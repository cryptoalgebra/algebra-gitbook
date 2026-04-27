# Omega Quoter

## Overview

OmegaQuoter is a stateless off-chain quoting contract that provides accurate price quotes for swaps on Algebra Integral, Uniswap V3, and Uniswap V2 protocols without executing actual trades. It simulates swap operations to determine expected output amounts, price impact, and gas estimates.

## Functions

### Main Execution

```solidity
function execute(bytes calldata commands, bytes[] calldata inputs) 
    external 
    returns (bytes[] memory outputs)
```

Executes a sequence of quote commands and returns encoded results for each command.

Parameters:

* `commands`: Concatenated 1-byte command identifiers
* `inputs`: ABI-encoded parameters for each command

Returns:

* `outputs`: Array of ABI-encoded results (varies by command type)

## Supported Commands

### Algebra Integral Quotes

#### 0x00 – INTEGRAL\_SWAP\_EXACT\_IN

Parameters:

* `uint256 amountIn`
* `bytes path`

Returns:

* `uint256 amountOut`
* `uint160[] sqrtPriceX96AfterList`
* `uint256 gasEstimate`

Usage: Gets quote for exact input swap on Algebra Integral pools.

#### 0x01 – INTEGRAL\_SWAP\_EXACT\_OUT

Parameters:

* `uint256 amountOut`
* `bytes path`

Returns:

* `uint256 amountIn`
* `uint160[] sqrtPriceX96AfterList`
* `uint256 gasEstimate`

Usage: Gets quote for exact output swap on Algebra Integral pools with optional ERC4626 wrap/unwrap operations. For exactOut swaps, the path MUST use boosted encoding to specify wrap/unwrap actions.

### Uniswap V3 Quotes

#### 0x10 – UNISWAP\_V3\_SWAP\_EXACT\_IN

Parameters:

* `uint256 amountIn`
* `bytes path`

Returns:

* `uint256 amountOut`
* `uint160[] sqrtPriceX96AfterList`
* `uint256 gasEstimate`

#### 0x11 – UNISWAP\_V3\_SWAP\_EXACT\_OUT

Parameters:

* `uint256 amountOut`
* `bytes path`

Returns:

* `uint256 amountIn`
* `uint160[] sqrtPriceX96AfterList`
* `uint256 gasEstimate`

### Uniswap V2 Quotes

#### 0x08 – V2\_SWAP\_EXACT\_IN

Parameters:

* `uint256 amountIn`
* `address[] path`

Returns:

* `uint256 amountOut`

#### 0x09 – V2\_SWAP\_EXACT\_OUT

Parameters:

* `uint256 amountOut`
* `address[] path`

Returns:

* `uint256 amountIn`

### ERC4626 Vault Quotes

#### 0x07 – ERC4626\_WRAP

Parameters:

* `address vault`
* `uint256 amount`

Returns:

* `uint256 amountOut`

Usage: Quotes the number of vault shares received when depositing assets.

#### 0x0f – ERC4626\_UNWRAP

Parameters:

* `address vault`
* `uint256 amount`

Returns:

* `uint256 amountOut`

Usage: Quotes the number of underlying assets received when redeeming vault shares.

## Chain Execution with CONTRACT\_BALANCE

The quoter supports chaining multiple operations by using `CONTRACT_BALANCE` (a special constant) as the `amountIn` parameter. When encountered, the quoter uses the output from the previous command as input to the current command.

## References

* [Source Code](https://github.com/cryptoalgebra/omega-router)
