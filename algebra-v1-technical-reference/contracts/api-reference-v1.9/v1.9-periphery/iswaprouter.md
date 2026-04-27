# ISwapRouter

Router token swapping functionality

Functions for swapping tokens via Algebra

_Developer note: Credit to Uniswap Labs under GPL-2.0-or-later license: https://github.com/Uniswap/v3-periphery_

## Functions

### exactInputSingle

`function exactInputSingle(struct ISwapRouter.ExactInputSingleParams params) external payable returns (uint256 amountOut)` payable external

Swaps \`amountIn\` of one token for as much as possible of another token

| Name   | Type                                      | Description                                                                              |
| ------ | ----------------------------------------- | ---------------------------------------------------------------------------------------- |
| params | struct ISwapRouter.ExactInputSingleParams | The parameters necessary for the swap, encoded as \`ExactInputSingleParams\` in calldata |

**Returns:**

| Name      | Type    | Description                      |
| --------- | ------- | -------------------------------- |
| amountOut | uint256 | The amount of the received token |

### exactInput

`function exactInput(struct ISwapRouter.ExactInputParams params) external payable returns (uint256 amountOut)` payable external

Swaps \`amountIn\` of one token for as much as possible of another along the specified path

| Name   | Type                                | Description                                                                                  |
| ------ | ----------------------------------- | -------------------------------------------------------------------------------------------- |
| params | struct ISwapRouter.ExactInputParams | The parameters necessary for the multi-hop swap, encoded as \`ExactInputParams\` in calldata |

**Returns:**

| Name      | Type    | Description                      |
| --------- | ------- | -------------------------------- |
| amountOut | uint256 | The amount of the received token |

### exactOutputSingle

`function exactOutputSingle(struct ISwapRouter.ExactOutputSingleParams params) external payable returns (uint256 amountIn)` payable external

Swaps as little as possible of one token for \`amountOut\` of another token

| Name   | Type                                       | Description                                                                               |
| ------ | ------------------------------------------ | ----------------------------------------------------------------------------------------- |
| params | struct ISwapRouter.ExactOutputSingleParams | The parameters necessary for the swap, encoded as \`ExactOutputSingleParams\` in calldata |

**Returns:**

| Name     | Type    | Description                   |
| -------- | ------- | ----------------------------- |
| amountIn | uint256 | The amount of the input token |

### exactOutput

`function exactOutput(struct ISwapRouter.ExactOutputParams params) external payable returns (uint256 amountIn)` payable external

Swaps as little as possible of one token for \`amountOut\` of another along the specified path (reversed)

| Name   | Type                                 | Description                                                                                   |
| ------ | ------------------------------------ | --------------------------------------------------------------------------------------------- |
| params | struct ISwapRouter.ExactOutputParams | The parameters necessary for the multi-hop swap, encoded as \`ExactOutputParams\` in calldata |

**Returns:**

| Name     | Type    | Description                   |
| -------- | ------- | ----------------------------- |
| amountIn | uint256 | The amount of the input token |

### exactInputSingleSupportingFeeOnTransferTokens

`function exactInputSingleSupportingFeeOnTransferTokens(struct ISwapRouter.ExactInputSingleParams params) external returns (uint256 amountOut)` external

Swaps \`amountIn\` of one token for as much as possible of another along the specified path _Developer note: Unlike standard swaps, handles transferring from user before the actual swap._

| Name   | Type                                      | Description                                                                                  |
| ------ | ----------------------------------------- | -------------------------------------------------------------------------------------------- |
| params | struct ISwapRouter.ExactInputSingleParams | The parameters necessary for the multi-hop swap, encoded as \`ExactInputParams\` in calldata |

**Returns:**

| Name      | Type    | Description                      |
| --------- | ------- | -------------------------------- |
| amountOut | uint256 | The amount of the received token |
