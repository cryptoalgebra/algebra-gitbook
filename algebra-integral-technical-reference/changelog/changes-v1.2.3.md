---
icon: up-from-line
---

# Changes v1.2.3

Integral v1.2.3 code can be found in the [integral-v1.2.3](https://github.com/cryptoalgebra/Algebra/tree/integral-v1.2.3) branch

### Pools

* `AlgebraFactory` now emits the `Pool` event for every pool, including custom pools. Custom pools additionally emit `CustomPool` (with deployer param) right after. Previously custom pools only emitted `CustomPool`, which caused integration issues.

### Farming

* `AlgebraEternalFarming` now includes an anti-JIT farming buffer: rewards claimed too soon after a position's liquidity-weighted vesting timestamp are forfeited to a protocol-owned address instead of being paid out. The buffer duration is configurable both globally and on a per-pool basis, and trusted addresses (e.g., ALM vaults) can be exempted. See [Anti-JIT Farming Buffer](../plugins/farming.md#anti-jit-farming-buffer) in the Farming page for details.
