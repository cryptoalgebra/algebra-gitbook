---
icon: up-from-line
---

# Changes v1.2

Algebra Integral is located in the integral v1.2 branch of the repository: [Algebra Integral v1.2](https://github.com/cryptoalgebra/Algebra/tree/integral-v1.2)

### Plugin fee

Plugin builders can handle plugin fee receiving however they want. The plugin fee is accumulated in the pool and sent periodically along with the community fee. To receive the plugin fee a plugin must have a method **handlePluginFee(fee0, fee1)**. This method is called when the fee is sent to the plugin contract. Plugin fee is returned in **beforeSwapHook** and **beforeModifyPositionHook** (Burn only). The plugin fee is charged from the swap amount, so totalFee = pluginFee + overrideFee/poolFee. TotalFee cannot exceed 100%.

### Override fee

Added overrideFee mechanism which can make certain plugins with whatever dynamic fee cheaper. Now it is not necessary to write fee value in the storage via setFee(). Override fee is **returned in beforeSwapHook**. The fee value can be up to 100%. If the override fee obtained from beforeSwapHook is 0, the fee from globalState is used for the swap.

### Minor changes

* Added ability to pass arbitrary data to a plugin factory when deploying a pool.
* Disable hooks when plugin calls a pool to avoid recursion.
* Added plugin and override fee values for swap/burn events.
