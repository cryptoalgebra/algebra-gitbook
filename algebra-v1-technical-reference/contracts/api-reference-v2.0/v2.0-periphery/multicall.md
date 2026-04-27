# Multicall

Multicall

Enables calling multiple methods in a single call to the contract

## Functions

### multicall

`function multicall(bytes[] data) external payable returns (bytes[] results)` payable external

Call multiple functions in the current contract and return the data from all of them if they all succeed _Developer note: The \`msg.value\` should not be trusted for any method callable from multicall._

| Name | Type     | Description                                                              |
| ---- | -------- | ------------------------------------------------------------------------ |
| data | bytes\[] | The encoded function data for each of the calls to make to this contract |

**Returns:**

| Name    | Type     | Description                                           |
| ------- | -------- | ----------------------------------------------------- |
| results | bytes\[] | The results from each of the calls passed in via data |
