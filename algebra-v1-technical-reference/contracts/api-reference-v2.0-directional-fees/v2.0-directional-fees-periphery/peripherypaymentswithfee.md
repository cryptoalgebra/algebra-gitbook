# PeripheryPaymentsWithFee

_Developer note: Credit to Uniswap Labs under GPL-2.0-or-later license: https://github.com/Uniswap/v3-periphery_

## Functions

### unwrapWNativeTokenWithFee

`function unwrapWNativeTokenWithFee(uint256 amountMinimum, address recipient, uint256 feeBips, address feeRecipient) public payable` payable public

Unwraps the contract's WNativeToken balance and sends it to recipient as NativeToken, with a percentage between 0 (exclusive), and 1 (inclusive) going to feeRecipient _Developer note: The amountMinimum parameter prevents malicious contracts from stealing WNativeToken from users._

| Name          | Type    | Description |
| ------------- | ------- | ----------- |
| amountMinimum | uint256 |             |
| recipient     | address |             |
| feeBips       | uint256 |             |
| feeRecipient  | address |             |

### sweepTokenWithFee

`function sweepTokenWithFee(address token, uint256 amountMinimum, address recipient, uint256 feeBips, address feeRecipient) public payable` payable public

Transfers the full amount of a token held by this contract to recipient, with a percentage between 0 (exclusive) and 1 (inclusive) going to feeRecipient _Developer note: The amountMinimum parameter prevents malicious contracts from stealing the token from users_

| Name          | Type    | Description |
| ------------- | ------- | ----------- |
| token         | address |             |
| amountMinimum | uint256 |             |
| recipient     | address |             |
| feeBips       | uint256 |             |
| feeRecipient  | address |             |
