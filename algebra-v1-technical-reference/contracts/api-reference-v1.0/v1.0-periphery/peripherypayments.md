# PeripheryPayments

## PeripheryPayments

### Functions

#### receive

`receive()` payable external

#### unwrapWNativeToken

`unwrapWNativeToken(uint256,address)` payable external

Unwraps the contract's WNativeToken balance and sends it to recipient as NativeToken.

| Name          | Type    | Description                                  |
| ------------- | ------- | -------------------------------------------- |
| amountMinimum | uint256 | The minimum amount of WNativeToken to unwrap |
| recipient     | address | The address receiving NativeToken            |

#### sweepToken

`sweepToken(address,uint256,address)` payable external

Transfers the full amount of a token held by this contract to recipient

| Name          | Type    | Description                                                                  |
| ------------- | ------- | ---------------------------------------------------------------------------- |
| token         | address | The contract address of the token which will be transferred to \`recipient\` |
| amountMinimum | uint256 | The minimum amount of token required for a transfer                          |
| recipient     | address | The destination address of the token                                         |

#### refundNativeToken

`refundNativeToken()` payable external

Refunds any NativeToken balance held by this contract to the \`msg.sender\`

***

## PeripheryPayments

### Functions

#### receive

`receive()` payable external

#### unwrapWNativeToken

`unwrapWNativeToken(uint256,address)` payable external

Unwraps the contract's WNativeToken balance and sends it to recipient as NativeToken.

| Name          | Type    | Description                                  |
| ------------- | ------- | -------------------------------------------- |
| amountMinimum | uint256 | The minimum amount of WNativeToken to unwrap |
| recipient     | address | The address receiving NativeToken            |

#### sweepToken

`sweepToken(address,uint256,address)` payable external

Transfers the full amount of a token held by this contract to recipient

| Name          | Type    | Description                                                                  |
| ------------- | ------- | ---------------------------------------------------------------------------- |
| token         | address | The contract address of the token which will be transferred to \`recipient\` |
| amountMinimum | uint256 | The minimum amount of token required for a transfer                          |
| recipient     | address | The destination address of the token                                         |

#### refundNativeToken

`refundNativeToken()` payable external

Refunds any NativeToken balance held by this contract to the \`msg.sender\`

***
