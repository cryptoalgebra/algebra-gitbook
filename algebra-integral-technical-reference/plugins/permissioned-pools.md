# Permissioned Pools

### Overview <a href="#overview" id="overview"></a>

The purpose of the Permissioned Pools Plugin is to restrict pool access to approved users only, for pools that contain compliance-restricted tokens (e.g. RWA or security tokens) where the token issuer needs to control who can trade or provide liquidity for the token through the pool.

Briefly, a pool that uses this plugin checks the real end user against a per-token allowlist before allowing the following actions:

* swaps
* flash loans
* adding liquidity
* pool initialization

Liquidity removal is always allowed.

### Details <a href="#details" id="details"></a>

The permissioned pools module is designed to work through the following Algebra plugin hooks:

* `beforeSwap`
* `beforeFlash`
* `beforeModifyPosition`
* `afterInitialize`

Both tokens of a pool are checked independently on every action, even if only one side of the pair is a permissioned token. A token without a registered `PermissionsAdapter` always passes.

Unlike the [Access List](access-list.md) plugin, this plugin doesn't use the raw hook data `sender` or tx.origin. Instead it resolves the real end user through a two-level check:

1. If the raw hook `sender` is **not** an approved router, no identity resolution happens - its own address is checked against the allowlist directly, exactly like any other candidate address.
2. If `sender` **is** an approved router (registered in the shared `allowedRouters` registry), the plugin calls `sender.msgSender()` and trusts the address the router reports back as the real end user. 

`tx.origin` always resolves to the EOA that submitted the transaction, but this also means it breaks for account abstraction, smart-contract wallets. Router-reported `msg.sender` fixes this by letting each router resolve the real user through its own logic, at the cost of requiring every router to implement `IMsgSender` and be separately approved.

### Permissions Adapter & Factory <a href="#permissions-adapter-and-factory" id="permissions-adapter-and-factory"></a>

`PermissionsAdapter` is the per-token allowlist and kill switch. One adapter is deployed per permissioned token, standalone, by the token issuer:

* `isAllowed(address account)` - whether `account` may swap/add liquidity involving the token
* `swappingEnabled()` - emergency kill switch for secondary trading; does not affect liquidity
* `setAllowed(address account, bool allowed)` / `setAllowedBatch(address[] accounts, bool[] allowed)`
* `setSwappingEnabled(bool enabled)`

`PermissionsAdapterFactory` is the shared registry distinguishing a **registered** adapter (self-serve, permissionless) from a **verified** one (approved by the `PERMISSIONED_POOL_MANAGER` role):

* `getAdapter(address token)` - the adapter registered for `token`, or `address(0)` if none
* `isVerified(address token)` - whether the registered adapter has been verified
* `registerAdapter(address token, address adapter)` - callable only by the adapter's own admin; replacing a registration resets verification to `false`
* `verifyAdapter(address token, bool verified)` - approval/revocation by the `PERMISSIONED_POOL_MANAGER` role
* `allowedRouters(address router)` - the shared trusted-router registry used by every permissioned pool to resolve the real sender behind a router

If the plugin's `PermissionsAdapterFactory` address is not set (`address(0)`), all permissioned pool checks are bypassed.

### How To Configure the Permissioned Pools Plugin <a href="#how-to-configure-permissioned-pools-plugin" id="how-to-configure-permissioned-pools-plugin"></a>

1. The token issuer deploys their own `PermissionsAdapter(token, admin)`.
2. The issuer (as the adapter's `admin`) calls `PermissionsAdapterFactory.registerAdapter(token, adapter)`.
3. An account with the `PERMISSIONED_POOL_MANAGER` role calls `verifyAdapter(token, true)` to approve the adapter. A pool cannot be initialized with this token until this step is done.
4. The issuer manages the allowlist via `setAllowed` / `setAllowedBatch`, and can independently pause secondary trading via `setSwappingEnabled(false)`.
5. Any periphery/router contract that pool users are expected to swap, add liquidity, or flash through must be updated to implement `IMsgSender.msgSender()`, reporting the real end user for the current call.
6. An account with the `PERMISSIONED_POOL_MANAGER` role approves that router via `setRouterAllowed(router, true)` on the factory. Without this step, the router's self-reported `msgSender()` is never trusted, and the router's own address is checked against the allowlist instead.

### Roles <a href="#roles" id="roles"></a>

The token issuer is the `admin` set at `PermissionsAdapter` deployment time. The issuer can:

* add or remove a single account from the allowlist
* batch update allowlist statuses
* enable or disable secondary trading for their token
* register their adapter for their token in the factory (must be called by the adapter's own admin)

There is a permissioned-pools-specific role on the Algebra factory, `PERMISSIONED_POOL_MANAGER`, enforced via `IAlgebraFactory(algebraFactory).hasRoleOrOwner(...)`. It can:

* verify or unverify a registered adapter
* approve or revoke a trusted router

The plugin has a separate admin role, `ALGEBRA_BASE_PLUGIN_MANAGER`, which can call `setPermissionsAdapterFactory(address)` on the plugin itself.
