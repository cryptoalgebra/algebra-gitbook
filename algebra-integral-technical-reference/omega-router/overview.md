# Overview

The `OmegaRouter` is an ETH and ERC20 swap router, designed to aggregate trades across multiple DEX protocols including Algebra Integral, Uniswap V3, and Uniswap V2. It provides users with highly flexible and composable transactions. The contract is unowned and non-upgradeable.

The flexible command-based architecture enables:

* Token swaps on Algebra Integral, Uniswap V3, and Uniswap V2
* Liquidity management for Algebra Integral positions
* Interaction with ERC4626 vaults for wrapping and unwrapping
* Partial fills of trades
* Wrapping and unwrapping of ETH (via WETH)
* Time-bound, signature-controlled token approvals using [Permit2](/broken/pages/180018a856bd08fa86ed15a76d887406c2012daa)
* Sub-plan execution and balance checks

{% hint style="info" %}
The `OmegaRouter` integrates with `Permit2` to eliminate the need for direct token approvals. See the [Permit2 documentation](/broken/pages/180018a856bd08fa86ed15a76d887406c2012daa) for details.
{% endhint %}

## Resources

* [OmegaRouter on GitHub](https://github.com/cryptoalgebra/omega-router)
