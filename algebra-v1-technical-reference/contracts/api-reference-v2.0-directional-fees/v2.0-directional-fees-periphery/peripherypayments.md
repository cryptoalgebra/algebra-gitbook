# PeripheryPayments

_Developer note: Credit to Uniswap Labs under GPL-2.0-or-later license: https://github.com/Uniswap/v3-periphery_

## Functions

### receive

`receive() external payable` payable external

### unwrapWNativeToken

`function unwrapWNativeToken(uint256 amountMinimum, address recipient) external payable` payable external

Unwraps the contract's WNativeToken balance and sends it to recipient as NativeToken. _Developer note: The amountMinimum parameter prevents malicious contracts from stealing WNativeToken from users._

| Name          | Type    | Description                                  |
| ------------- | ------- | -------------------------------------------- |
| amountMinimum | uint256 | The minimum amount of WNativeToken to unwrap |
| recipient     | address | The address receiving NativeToken            |

### sweepToken

`function sweepToken(address token, uint256 amountMinimum, address recipient) external payable` payable external

Transfers the full amount of a token held by this contract to recipient _Developer note: The amountMinimum parameter prevents malicious contracts from stealing the token from users_

| Name          | Type    | Description                                                                  |
| ------------- | ------- | ---------------------------------------------------------------------------- |
| token         | address | The contract address of the token which will be transferred to \`recipient\` |
| amountMinimum | uint256 | The minimum amount of token required for a transfer                          |
| recipient     | address | The destination address of the token                                         |

### refundNativeToken

`function refundNativeToken() external payable` payable external

Refunds any NativeToken balance held by this contract to the \`msg.sender\` _Developer note: Useful for bundling with mint or increase liquidity that uses ether, or exact output swaps that use ether for the input amount_
