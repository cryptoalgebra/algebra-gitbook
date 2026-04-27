# Getting data from pools

## How to determine which token is token0 and which is token1?

The order of the tokens is determined in such a way that:

&#x20;`address(token0) < address(token1)`

## How to get address of liquidity pool?

To obtain the liquidity pool address, you can use one of the following methods:

**1. Use `poolByPair` mapping in AlgebraFactory**

```solidity
/// @inheritdoc IAlgebraFactory
mapping(address tokenA => mapping(address tokenB => address pool)) public poolByPair;
```

Token addresses must be passed as parameters (in any order). If the pool exists, you will get its address. If such a pool has not yet been created, you will receive `address(0)`.

**2. Use `computePoolAdress` function in AlgebraFactory**

```solidity
/// @inheritdoc IAlgebraFactory
function computePoolAddress(address token0, address token1) public view returns (address pool);
```

Token addresses must be passed as parameters ( `address(token0)` < `address(token1)` ). You will get address of pool even if it hasn't been created yet.

**3. Calculate pool address using `create2` mechanism**

The previous options require making an external call to the AlgebraFactory contract. A cheaper option in terms of gas would be to calculate the address yourself. To do this, you can use the same logic as in method `computePoolAddress`:

```solidity
function computePoolAddress(address token0, address token1) public view returns (address pool) {
  pool = address(uint160(uint256(keccak256(abi.encodePacked(hex'ff', poolDeployer, keccak256(abi.encode(token0, token1)), POOL_INIT_CODE_HASH)))));
}
```

You need to know `POOL_INIT_CODE_HASH` constant: you can get it from AlgebraFactory contract .&#x20;

It is important to note that the calculation uses the address not of the AlgebraFactory, but of the separate pool deployer contract (`poolDeployer`), you also can get this constant from AlgebraFactory contract.

Also, important to remember that it is important to maintain the order of the tokens: address of token0 must be less than address of token1 (`address(token0)` < `address(token1)`)

<mark style="color:orange;">Note: pool address calculation can be different in some blockchains (like zkSync Era).</mark>

### Custom Pools

Starting from version 1.1, Algebra Integral supports custom pools that use custom deployers and plugins:

#### 1. Use customPoolByPair mapping in AlgebraFactory

```solidity
/// @inheritdoc IAlgebraFactory
mapping(address customDeployer => mapping(address tokenA => mapping(address tokenB => address pool))) public customPoolByPair;
```

You need to provide the custom deployer address and token addresses (in any order). If the custom pool exists, you will get its address.

#### 2. Use computeCustomPoolAddress function in AlgebraFactory

```solidity
/// @inheritdoc IAlgebraFactory
function computeCustomPoolAddress(address customDeployer, address token0, address token1) external view returns (address customPool);
```

You need to provide the custom deployer address and token addresses ( `address(token0) < address(token1)` ).

#### 3. Calculate custom pool address using create2 mechanism

For custom pools, the calculation is slightly different:

```solidity
function computeCustomPoolAddress(address customDeployer, address token0, address token1) public view returns (address customPool) {
  customPool = address(uint160(uint256(keccak256(abi.encodePacked(hex'ff', poolDeployer, keccak256(abi.encode(customDeployer, token0, token1)), POOL_INIT_CODE_HASH)))));
}
```

For custom pools, the salt includes the `customDeployer` address as the first parameter.

<mark style="color:orange;">Note: When interacting with peripheral contracts(such as Quoter, SwapRouter, etc), for base pools the plugin deployer parameter should be passed as</mark> `address(0)`

## Price

### How to get current price in pool?

Information about the current instant price of the tokens in pool is in the `globalState` structure:

```solidity
// AlgebraPool contract

struct GlobalState {
  uint160 price; // The square root of the current price in Q64.96 format
  int24 tick; // The current tick (price(tick) <= current price)
  uint16 lastFee; // The last fee in hundredths of a bip, i.e. 1e-6 (so 100 is 0.01%)
  uint8 pluginConfig; // The current plugin config as a bitmap
  uint16 communityFee; // The community fee represented as a percent of all collected fee in thousandths (1e-3) (so 100 is 10%)
  bool unlocked; // Reentrancy lock flag, true if the contract is unlocked, otherwise - false
}

/// @inheritdoc IAlgebraPoolState
GlobalState public override globalState;
```

And the safest way to get price and other data is to use special getter function:

<pre class="language-solidity"><code class="lang-solidity">/// @dev safe from read-only reentrancy getter function
<strong>function safelyGetStateOfAMM() external view override returns (
</strong>    uint160 sqrtPrice, 
    int24 tick, 
    uint16 lastFee, 
    uint8 pluginConfig, 
    uint128 activeLiquidity, 
    int24 nextTick, 
    int24 previousTick
)
</code></pre>

```solidity
(uint160 price, , , , , ,) = IAlgebraPool(poolAddress).safelyGetStateOfAMM();
```

<mark style="color:orange;">You can get price directly from</mark> <mark style="color:orange;"></mark><mark style="color:orange;">`globalState`</mark> <mark style="color:orange;"></mark><mark style="color:orange;">struct, but beware of read-only reentrancy, if you are doing it on-chain!</mark>

Next, it is important to understand what exactly is meant by price in liquidity pools. The pool stores the **square root** of the price of token0 relative to token1 in format [`Q64.96`](https://en.wikipedia.org/wiki/Q_\(number_format\)). So, in pseudocode, price value in pool can be expressed as:

```python
price_pool = sqrt(reserve_1_virtual / reserve_0_virtual) * 2**96
```

To get the usual instant price from the price value in the pool, you need to make the following transformations:

```python
price_instant = (price_pool / 2**96)**2
```

Further, if you need to bring the price to a human-readable form, it is necessary to take into account the difference in token decimals, if any.

### Can i use "price" value in pool as real price?

Shortly: <mark style="color:red;">**no**</mark>, you should not rely on this raw value for anything other than internal pool calculations. This value in the pool reflects only its internal state at the current moment in time, can change and is subject to manipulation using swaps. In addition, when swapping, the actual execution price will differ from this value due to price impact and fee charged.&#x20;

To get the real price of the swap, see the paragraph [**How to get actual execution price for swap?**](getting-data-from-pools.md#how-to-get-actual-execution-price-for-swap)

If you need to get the average price in a pool for a certain period of time, then for this you can use data from a plugin with a TWAP-oracle, **if it is connected to the pool**.

### How to get actual execution price for swap?

To do this, you can use a special peripheral contract called Quoter. For example, here is description of `quoteExactInputSingle`:

```solidity
/// @inheritdoc IQuoter
function quoteExactInputSingle(
  address tokenIn,
  address tokenOut,
  address deployer; // Available since v1.1: supports custom pool deployers
  uint256 amountIn,
  uint160 limitSqrtPrice
) public override returns (uint256 amountOut, uint16 fee) {
...
}
```

You can do a **static call** to this method _(and it is better not to use it on-chain due to gas costs)_. As a result, the number of tokens at the output and the real pool fee will be obtained.
