# ERC20Permit

_Developer note: Implementation of the ERC20 Permit extension allowing approvals to be made via signatures, as defined in https://eips.ethereum.org/EIPS/eip-2612\[EIP-2612]. Adds the {permit} method, which can be used to change an account's ERC20 allowance (see {IERC20-allowance}) by presenting a message signed by the account. By not relying on \`{IERC20-approve}\`, the token holder account doesn't need to send a transaction, and thus is not required to hold Ether at all. Available since v3.4._

## Functions

### permit

`permit(address,address,uint256,uint256,uint8,bytes32,bytes32)` public

| Name     | Type    | Description |
| -------- | ------- | ----------- |
| owner    | address |             |
| spender  | address |             |
| value    | uint256 |             |
| deadline | uint256 |             |
| v        | uint8   |             |
| r        | bytes32 |             |
| s        | bytes32 |             |

### nonces

`nonces(address)` view public

| Name  | Type    | Description |
| ----- | ------- | ----------- |
| owner | address |             |

**Returns:**

| Name | Type    | Description |
| ---- | ------- | ----------- |
| \[0] | uint256 |             |

### DOMAIN\_SEPARATOR

`DOMAIN_SEPARATOR()` view external

**Returns:**

| Name | Type    | Description |
| ---- | ------- | ----------- |
| \[0] | bytes32 |             |

***
