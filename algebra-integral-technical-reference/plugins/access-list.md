# Access List

### Overview <a href="#overview" id="overview"></a>

The purpose of the Access List Plugin is to restrict pool access to approved users only.

Briefly, a pool that uses this plugin checks `tx.origin` against an access list registry before allowing the following actions:

* swaps
* adding liquidity
* flash loans
* pool initialization

Liquidity removal is always allowed. This ensures that verification is enforced for entry and active usage, without preventing users from exiting an existing position.

### Details <a href="#details" id="details"></a>

The access list module is designed to work through the following Algebra plugin hooks:

* `beforeSwap`
* `beforeFlash`
* `beforeModifyPosition`
* `afterInitialize`

For liquidity modification, only positive `liquidityDelta` values are checked, so adding liquidity is gated while removing liquidity is not.

The plugin uses `tx.origin` as the end-user identifier. In practice, this means the registry validates the externally owned account (EOA) that initiated the transaction, rather than an intermediate router or helper contract.

Some "whitelist systems" solve this by keeping a `msgSender()` method on supported routers, which returns the original EOA. This approach can work well, but it depends on router-level integration. In this plugin, a simple `sender` parameter check is not sufficient. since pool actions are typically called through peripheral contracts such as routers or position managers.&#x20;

For this reason, **the implementation relies on `tx.origin`**.  This approach is simpler and does not require custom router support, but it comes with trade-offs:

* weaker compatibility with account abstraction and smart wallets
* limited support for relayers and meta-transactions

If required, a router-based approach can be added later by adding `msgSender()`-style methods to the position manager and swap routers, then requiring third-party integrations to support the same interface.

**The plugin does not perform identity verification on-chain**. The expected flow is:

1. The user completes verification via a website or another off-chain process.
2. A backend or operator decides whether the wallet should be approved.
3. An `ACCESS_LIST_MANAGER` account updates the `AccessListRegistry`.
4. Pool actions are allowed or reverted based on the registry state.

### Access List Registry <a href="#kyc-registry" id="kyc-registry"></a>

`AccessListRegistry` is the single source of truth for whitelist status.

It provides the following management methods:

* `setWhitelisted(address user, bool status)`
* `setWhitelistedBatch(address[] users, bool[] statuses)`
* `pause()`
* `unpause()`

When the registry is paused, all access list checks are bypassed. This serves as an emergency mechanism, alowing the protocol to temporarily disable access list enforcement without updating plugin configuration on every pool.

`AccessListRegistry` does not store per-pool state. It manages users globally. However, different pools may use different registries if their plugins point to different registry contracts.\
\
If the registry address is not set, all access list checks are bypassed.

### How To Configure the Access List Plugin <a href="#how-to-configure-kyc-plugin" id="how-to-configure-kyc-plugin"></a>

Whitelist users who should be allowed to interact with access-list-protected pools by calling: `setWhitelisted(address user, bool status)` or \
`setWhitelistedBatch(address[] users, bool[] statuses)`.

Operational notes:

* setting the plugin registry to `address(0)` disables access list for that plugin
* pausing the registry bypasses checks for all plugins pointing to that registry

### Roles <a href="#roles" id="roles"></a>

There is access list-specific role in `AccessListRegistry` :

* `ACCESS_LIST_MANAGER`

`ACCESS_LIST_MANAGER` can:

* add or remove a single user from the whitelist
* batch update whitelist statuses
* pause verification
* unpause verification

\
The registry enforces access via `IAlgebraFactory(algebraFactory).hasRoleOrOwner(...)`, which allows the Algebra factory owner to act as an administrative fallback (if suppported by the factory implementation).<br>

The plugin has a separate admin role:

* `ALGEBRA_BASE_PLUGIN_MANAGER` can call `AccessListRegistry``(address)`
