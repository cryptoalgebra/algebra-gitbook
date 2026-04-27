# FAQ



<details>

<summary>What are the difference between UniswapV4 Hooks and Algebra Plugins</summary>

First of all, here is the difference in presence of hooks:

| Uniswap Hooks                   | Algebra Hooks                         |
| ------------------------------- | ------------------------------------- |
| beforeInitialize                | beforeInitialize                      |
| afterInitialize                 | afterInitialize                       |
| beforeAddLiquidity              | beforeModifyPosition (positive delta) |
| afterAddLiquidity               | afterModifyPosition (positive delta)  |
| beforeRemoveLiquidity           | beforeModifyPosition (negative delta) |
| afterRemoveLiquidity            | afterModifyPosition (negative delta)  |
| beforeSwap                      | beforeSwap                            |
| afterSwap                       | afterSwap                             |
| beforeDonate                    | -                                     |
| afterDonate                     | -                                     |
| beforeSwapReturnDelta           | -                                     |
| afterSwapReturnDelta            | -                                     |
| afterAddLiquidityReturnDelta    | -                                     |
| afterRemoveLiquidityReturnDelta | -                                     |
| -                               | beforeFlash                           |
| -                               | afterFlash                            |

And then there are the conceptual differences:

* Hooks are not able to define a swap amount.
* In Algebra there is no ERC-6909 functionality or flash accounting. Plugins can only manipulate LP fees and append a plugin fee on top.
* Algebra's approach is that each pool has its own deployed plugin. Therefore, when developing it, there is no need to implement mappings by pools, etc. Instead, you need to develop a simple contract (PluginFactory) that can perform custom plugin deployment when a pool is created. Since Algebra Pools are configurable (it is possible to manually change the fee, tick spacing, and even the plugin), the PluginFactory is used further to manage the pools created through it. More in [this section](../guides/plugin-development.md)

</details>
