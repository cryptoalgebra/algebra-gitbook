# Permissioned Pools

### Overview <a href="#overview" id="overview"></a>

The purpose of the Permissioned Pools Plugin is to restrict pool access to approved users only, for pools that contain compliance-restricted tokens (e.g. RWA or security tokens) where the token issuer needs to control who can trade or provide liquidity for the token through the pool.

Briefly, a pool that uses this plugin checks the real end user against a per-token allowlist checker before allowing:

* swaps (requires the `SWAP_ALLOWED` flag)
* adding liquidity (requires the `LIQUIDITY_ALLOWED` flag)

Removing liquidity, flash loans, and pool initialization are not gated by this plugin.

### Details <a href="#details" id="details"></a>

The permissioned pools module is designed to work through the following Algebra plugin hooks:

* `beforeSwap`
* `beforeModifyPosition` (only when `liquidityDelta` is positive, i.e. adding liquidity)

Both tokens of a pool are checked independently, token0 before token1, even if only one side of the pair is a permissioned token. A token with no checker assigned always passes.

Unlike the [Access List](access-list.md) plugin, this plugin doesn't use `tx.origin` or the raw hook `sender` directly. Every caller must be a registered router: the plugin calls `sender.msgSender()` and trusts the address the router reports back as the real end user, reverting with `RouterNotAllowed` if `sender` was not approved via `setRouterAllowed`.

`tx.origin` always resolves to the EOA that submitted the transaction, but this also means it breaks for account abstraction and smart-contract wallets. Router-reported `msg.sender` fixes this by letting each router resolve the real user through its own logic, at the cost of requiring every router to implement `IMsgSender` and be separately approved.

Trusted routers are a **per-pool** decision - each plugin keeps its own `allowedRouters` mapping, approved by that pool's own `ALGEBRA_BASE_PLUGIN_MANAGER`, rather than one shared registry across every permissioned pool.

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

### Allowlist Checker Registry <a href="#allowlist-checker-registry" id="allowlist-checker-registry"></a>

`IAllowlistChecker` is a pluggable, per-token eligibility check:

* `checkAllowlist(address account, address token) returns (PermissionFlag)` - a bitmask combining `SWAP_ALLOWED`, `LIQUIDITY_ALLOWED`, `NONE`, or `ALL_ALLOWED`

`AllowlistCheckerRegistry` is the governance-controlled `token -> checker` registry:

* `getChecker(address token)` - the checker assigned to `token`, or `address(0)` if none
* `setChecker(address token, address checker)` - assigns (or clears, with `address(0)`) the checker for `token`

`setChecker` is a single, direct governance action, gated by `PERMISSIONED_POOL_MANAGER`. A token with no checker assigned is simply unpermissioned.

`OnchainIdAllowlistChecker` is the provided reference implementation, gating eligibility on [OnchainID](https://github.com/onchain-id) claims: an account is eligible if its OnchainID identity holds a valid, non-revoked claim of the required topic from a trusted issuer.

### How To Configure the Permissioned Pools Plugin <a href="#how-to-configure-permissioned-pools-plugin" id="how-to-configure-permissioned-pools-plugin"></a>

1. Deploy an `IAllowlistChecker` implementation for the token - either `OnchainIdAllowlistChecker`, or a custom one.
2. An account with the `PERMISSIONED_POOL_MANAGER` role calls `AllowlistCheckerRegistry.setChecker(token, checker)`. This alone makes the token permissioned.
3. Whoever administers that specific checker manages actual eligibility (for `OnchainIdAllowlistChecker`: `setTrustedIssuer` / `setTrustedIssuersBatch`, `setRequiredTopic`).
4. Any periphery/router contract that pool users are expected to swap or add liquidity through must be updated to implement `IMsgSender.msgSender()`, reporting the real end user for the current call.
5. An account with the pool's `ALGEBRA_BASE_PLUGIN_MANAGER` role approves that router via `setRouterAllowed(router, true)` on the pool's plugin. Without this step, the router's self-reported `msgSender()` isn't trusted
6. An account with `ALGEBRA_BASE_PLUGIN_MANAGER` points the pool's plugin at the registry via `setAllowlistCheckerRegistry(registry)`.

### Roles <a href="#roles" id="roles"></a>

`PERMISSIONED_POOL_MANAGER` is enforced via `IAlgebraFactory(algebraFactory).hasRoleOrOwner(...)` on `AllowlistCheckerRegistry`, shared across the whole registry. It can:

* assign or clear the checker for any token

`ALGEBRA_BASE_PLUGIN_MANAGER` is a per-pool authorization on the plugin itself. It can:

* approve or revoke a trusted router for that pool
* point that pool's plugin at a different `AllowlistCheckerRegistry`

A checker's own admin role (e.g. `OnchainIdAllowlistChecker.admin`, set at deployment, independent of any protocol role) manages that checker's own eligibility rules - who counts as a trusted issuer, and which claim topic is required.
