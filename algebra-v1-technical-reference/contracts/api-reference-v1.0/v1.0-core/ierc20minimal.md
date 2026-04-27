# IERC20Minimal

Minimal ERC20 interface for Algebra Contains a subset of the full ERC20 interface that is used in Algebra

## Events

### Transfer

`Transfer(address,address,uint256)`

Event emitted when tokens are transferred from one address to another, either via \`#transfer\` or \`#transferFrom\`.

| Name  | Type    | Description                                                             |
| ----- | ------- | ----------------------------------------------------------------------- |
| from  | address | The account from which the tokens were sent, i.e. the balance decreased |
| to    | address | The account to which the tokens were sent, i.e. the balance increased   |
| value | uint256 | The amount of tokens that were transferred                              |

### Approval

`Approval(address,address,uint256)`

Event emitted when the approval amount for the spender of a given owner's tokens changes.

| Name    | Type    | Description                                               |
| ------- | ------- | --------------------------------------------------------- |
| owner   | address | The account that approved spending of its tokens          |
| spender | address | The account for which the spending allowance was modified |
| value   | uint256 | The new allowance from the owner to the spender           |

## Functions

### balanceOf

`balanceOf(address)` view external

Returns the balance of a token

| Name    | Type    | Description                                                                    |
| ------- | ------- | ------------------------------------------------------------------------------ |
| account | address | The account for which to look up the number of tokens it has, i.e. its balance |

**Returns:**

| Name | Type    | Description |
| ---- | ------- | ----------- |
| \[0] | uint256 |             |

### transfer

`transfer(address,uint256)` external

Transfers the amount of token from the \`msg.sender\` to the recipient

| Name      | Type    | Description                                                   |
| --------- | ------- | ------------------------------------------------------------- |
| recipient | address | The account that will receive the amount transferred          |
| amount    | uint256 | The number of tokens to send from the sender to the recipient |

**Returns:**

| Name | Type | Description |
| ---- | ---- | ----------- |
| \[0] | bool |             |

### allowance

`allowance(address,address)` view external

Returns the current allowance given to a spender by an owner

| Name    | Type    | Description                      |
| ------- | ------- | -------------------------------- |
| owner   | address | The account of the token owner   |
| spender | address | The account of the token spender |

**Returns:**

| Name | Type    | Description |
| ---- | ------- | ----------- |
| \[0] | uint256 |             |

### approve

`approve(address,uint256)` external

Sets the allowance of a spender from the \`msg.sender\` to the value \`amount\`

| Name    | Type    | Description                                                                    |
| ------- | ------- | ------------------------------------------------------------------------------ |
| spender | address | The account which will be allowed to spend a given amount of the owners tokens |
| amount  | uint256 | The amount of tokens allowed to be used by \`spender\`                         |

**Returns:**

| Name | Type | Description |
| ---- | ---- | ----------- |
| \[0] | bool |             |

### transferFrom

`transferFrom(address,address,uint256)` external

Transfers \`amount\` tokens from \`sender\` to \`recipient\` up to the allowance given to the \`msg.sender\`

| Name      | Type    | Description                                           |
| --------- | ------- | ----------------------------------------------------- |
| sender    | address | The account from which the transfer will be initiated |
| recipient | address | The recipient of the transfer                         |
| amount    | uint256 | The amount of the transfer                            |

**Returns:**

| Name | Type | Description |
| ---- | ---- | ----------- |
| \[0] | bool |             |

***
