# Technical Reference

### Functions

Transactions to the `OmegaRouter` all go through the `execute` function:

* `execute(bytes calldata commands, bytes[] calldata inputs, uint256 deadline)`
* `execute(bytes calldata commands, bytes[] calldata inputs)`

Both functions behave and process the commands exactly the same; the first one includes a deadline. The function without the deadline parameter will not revert based on `block.timestamp`.

The `execute` function behaves like a minimal virtual machine. It interprets a list of 1-byte commands and their corresponding ABI-encoded inputs and executes them sequentially.

### Command Structure

Each command byte uses the following bit structure:

| 0 | 1 2 | 3 4 5 6 7 |
| - | --- | --------- |
| f | r   | command   |

#### f

A single bit flag that signals whether or not the command should be allowed to revert without the whole transaction failing.

* If `f` is `0` (false) and the command reverts, then the entire transaction will revert and none of the commands will be executed.
* If `f` is `1` (true) and the command reverts, then the transaction will continue, allowing partial fills. If using this flag, include further commands that will remove any funds that could be left unused in the `OmegaRouter` contract.

#### r

2 unused bits, reserved for future use. Leaving these 2 bits as `0` will save gas, but any value passed into the contract will be ignored. Later versions of the `OmegaRouter` may expand the 5 bits used for `command` to use at least 1 of these bits.

#### command

A 5-bit unique identifier for the command that should be carried out. The values of these commands can be found within Commands.sol (link below), or can be viewed in the table in the Supported Commands section.

Commands not defined (placeholders) will revert with `InvalidCommandType` if provided.

### Key Features

| Feature                      | Status                                                                             |
| ---------------------------- | ---------------------------------------------------------------------------------- |
| Algebra Integral swaps       | ✅ `INTEGRAL_SWAP_EXACT_IN`, `INTEGRAL_SWAP_EXACT_OUT`                              |
| Integral position management | ✅ `INTEGRAL_POSITION_MANAGER_CALL`, `INTEGRAL_MINT`, `INTEGRAL_INCREASE_LIQUIDITY` |
| Uniswap V3 swaps             | ✅ `UNISWAP_V3_SWAP_EXACT_IN`, `UNISWAP_V3_SWAP_EXACT_OUT`                          |
| Uniswap V2 swaps             | ✅ `V2_SWAP_EXACT_IN`, `V2_SWAP_EXACT_OUT`                                          |
| Permit2-based transfers      | ✅ Extended with batch transfers                                                    |
| ERC4626 vault support        | ✅ `ERC4626_WRAP`, `ERC4626_UNWRAP`                                                 |
| Sub-plan execution           | ✅ `EXECUTE_SUB_PLAN`                                                               |

### Supported Commands

| Command | Name                               |
| ------: | ---------------------------------- |
|  `0x00` | `INTEGRAL_SWAP_EXACT_IN`           |
|  `0x01` | `INTEGRAL_SWAP_EXACT_OUT`          |
|  `0x02` | `PERMIT2_TRANSFER_FROM`            |
|  `0x03` | `PERMIT2_PERMIT_BATCH`             |
|  `0x04` | `SWEEP`                            |
|  `0x05` | `TRANSFER`                         |
|  `0x06` | `PAY_PORTION`                      |
|  `0x07` | `ERC4626_WRAP`                     |
|  `0x08` | `V2_SWAP_EXACT_IN`                 |
|  `0x09` | `V2_SWAP_EXACT_OUT`                |
|  `0x0a` | `PERMIT2_PERMIT`                   |
|  `0x0b` | `WRAP_ETH`                         |
|  `0x0c` | `UNWRAP_WETH`                      |
|  `0x0d` | `PERMIT2_TRANSFER_FROM_BATCH`      |
|  `0x0e` | `BALANCE_CHECK_ERC20`              |
|  `0x0f` | `ERC4626_UNWRAP`                   |
|  `0x10` | `UNISWAP_V3_SWAP_EXACT_IN`         |
|  `0x11` | `UNISWAP_V3_SWAP_EXACT_OUT`        |
|  `0x12` | `INTEGRAL_POSITION_MANAGER_CALL`   |
|  `0x13` | `INTEGRAL_MINT`                    |
|  `0x14` | `INTEGRAL_POSITION_MANAGER_PERMIT` |
|  `0x15` | `INTEGRAL_INCREASE_LIQUIDITY`      |
|  `0x21` | `EXECUTE_SUB_PLAN`                 |

Commands not listed are placeholders and will revert if called.

### Command Inputs

Each command requires its own input structure. Inputs are encoded using `abi.encode(...)` and placed in `inputs[i]` to match `commands[i]`. For example: (use `abi.encode(...)` for each command's parameters)

### Swap Commands

#### Algebra Integral Swaps

**0x00 – INTEGRAL\_SWAP\_EXACT\_IN**

Parameters:

* `address recipient`
* `uint256 amountIn`
* `uint256 amountOutMin`
* `bytes path`
* `bool payerIsUser`

Calls: `integralSwapExactInput(...)` in IntegralSwapModule\
Usage: Swaps exact input amount on Algebra Integral pools.

**0x01 – INTEGRAL\_SWAP\_EXACT\_OUT**

Parameters:

* `address recipient`
* `uint256 amountOut`
* `uint256 amountInMax`
* `bytes path`
* `bool payerIsUser`

Calls: `integralSwapExactOutput(...)`\
Usage: Swaps to receive exact output amount on Algebra Integral pools with optional ERC4626 wrap/unwrap operations.

For exactOut swaps, the path MUST use boosted encoding to specify wrap/unwrap actions:\
<kbd>tokenOut|wrapOut|poolTokenOut|deployer|poolTokenIn|wrapIn|tokenIn</kbd>

WrapAction Values:

* 0x00 - NONE: No wrap/unwrap operation
* 0x01 - WRAP: Wrap underlying token to vault token
* 0x02 - UNWRAP: Unwrap vault token to underlying

Important Rules:

* Always use boosted path format for exactOut, even if no wrap/unwrap needed (use NONE)
* Regular path encoding is NOT supported for exactOut swaps

Examples:

No Wrap (USDC -> WETH):\
WETH | NONE(0x00) | WETH | deployer | USDC | NONE(0x00) | USDC

With Wrap/Unwrap (USDC -> wmUSDC  -> waWETH -> WETH):\
WETH | UNWRAP(0x02) | waWETH | deployer | wmUSDC | WRAP(0x01) | USDC

#### Uniswap V3 Swaps

**0x10 – UNISWAP\_V3\_SWAP\_EXACT\_IN**

Parameters:

* `address recipient`
* `uint256 amountIn`
* `uint256 amountOutMin`
* `bytes path`
* `bool payerIsUser`

Calls: `uniswapV3SwapExactInput(...)` in UniswapV3SwapModule\
Usage: Swaps exact input amount on Uniswap V3 pools.

**0x11 – UNISWAP\_V3\_SWAP\_EXACT\_OUT**

Parameters:

* `address recipient`
* `uint256 amountOut`
* `uint256 amountInMax`
* `bytes path`
* `bool payerIsUser`

Calls: `uniswapV3SwapExactOutput(...)`\
Usage: Swaps to receive exact output amount on Uniswap V3 pools.

#### Uniswap V2 Swaps

**0x08 – V2\_SWAP\_EXACT\_IN**

Parameters:

* `address recipient`
* `uint256 amountIn`
* `uint256 amountOutMin`
* `address[] path`
* `bool payerIsUser`

Calls: `v2SwapExactInput(...)` in V2SwapModule\
Usage: Simple Uniswap v2-style fixed input swap using token pairs.

**0x09 – V2\_SWAP\_EXACT\_OUT**

Parameters:

* `address recipient`
* `uint256 amountOut`
* `uint256 amountInMax`
* `address[] path`
* `bool payerIsUser`

Calls: `v2SwapExactOutput(...)`\
Usage: Swaps to get an exact output amount with limited token budget.

### ERC4626 Vault Commands

**0x07 – ERC4626\_WRAP**

Parameters:

* `address vault`
* `address recipient`
* `uint256 amount`

Calls: `ERC4626.deposit(...)`\
Usage: Deposits assets into an ERC4626 vault and mints shares.

**0x0f – ERC4626\_UNWRAP**

Parameters:

* `address vault`
* `address recipient`
* `uint256 amount`

Calls: `ERC4626.redeem(...)`\
Usage: Redeems shares from an ERC4626 vault for underlying assets.

### Permit2 Commands

**About Permit2:** Permit2 is Uniswap's signature-based approval contract that enables gasless token approvals. The Permit2 contract must be deployed on the chain (Uniswap has already deployed it on major networks; if not available, OmegaRouter can deploy it).

**Workflow:**

1. **One-time approval:** User approves the Permit2 contract to spend their tokens. This approval can be reused across different protocols on the same network.
2. **Generate signature:** User generates an off-chain signature via MetaMask, specifying:
   * Token address and amount
   * Target contract (OmegaRouter)
   * Expiration time
3. **Execute with signature:** The signature is passed to Permit2 commands before swap or liquidity operations, allowing the router to transfer tokens without additional on-chain approvals.

**0x02 – PERMIT2\_TRANSFER\_FROM**

Parameters:

* `address token`
* `address recipient`
* `uint160 amount`

Calls: `permit2TransferFrom(...)`\
Usage: Transfers a single token using Permit2 allowances. Always pulls from `msg.sender`.

**0x03 – PERMIT2\_PERMIT\_BATCH**

Parameters:

* `PermitBatch permitBatch`
* `bytes signature`

Calls: `PERMIT2.permit(...)`\
Usage: Sets approval for multiple tokens in one signature.

**0x0a – PERMIT2\_PERMIT**

Parameters:

* `PermitSingle permitSingle`
* `bytes signature`

Calls: `PERMIT2.permit(...)`\
Usage: Sets approval for one token, often before `PERMIT2_TRANSFER_FROM`.

**0x0d – PERMIT2\_TRANSFER\_FROM\_BATCH**

Parameters:

* `AllowanceTransferDetails[] batch`

Calls: `permit2TransferFrom(...)`\
Usage: Transfers many tokens in one call from a user to one or more destinations.

### Payment & Balance Commands

**0x04 – SWEEP**

Parameters:

* `address token`
* `address recipient`
* `uint256 amountMin`

Calls: `Payments.sweep(...)`\
Usage: Clears out all router-held ETH or ERC20 tokens to a destination address.

**0x05 – TRANSFER**

Parameters:

* `address token`
* `address recipient`
* `uint256 amount`

Calls: `Payments.pay(...)`\
Usage: Transfers a specific amount (not full balance) from the router.

**0x06 – PAY\_PORTION**

Parameters:

* `address token`
* `address recipient`
* `uint256 bips`

Calls: `Payments.payPortion(...)`\
Usage: Sends a % of the token balance (e.g., 2500 = 25%).

**0x0e – BALANCE\_CHECK\_ERC20**

Parameters:

* `address owner`
* `address token`
* `uint256 minBalance`

Calls: view-only `balanceOf(...)`\
Usage: Ensures required token balance exists; useful for conditional workflows.

### ETH & WETH

**0x0b – WRAP\_ETH**

Parameters:

* `address recipient`
* `uint256 amount`

Calls: `Payments.wrapETH(...)` → WETH.deposit()\
Usage: Converts ETH held by router into WETH and optionally sends it.

**0x0c – UNWRAP\_WETH**

Parameters:

* `address recipient`
* `uint256 amountMin`

Calls: `Payments.unwrapWETH9(...)`\
Usage: Converts router-held WETH into ETH and sends it.

### Algebra Integral Position Management

These commands interact with Algebra Integral's NonfungiblePositionManager to create and manage concentrated liquidity positions.

**Router Balance Feature:** `INTEGRAL_MINT` and `INTEGRAL_INCREASE_LIQUIDITY` were specifically designed to work with the router's token balance. This is essential for ERC4626 vault tokens, where the exact amount received from wrapping cannot be predicted due to variable exchange rates. Instead of specifying exact amounts, these commands use whatever token balance the router holds.

**0x12 – INTEGRAL\_POSITION\_MANAGER\_CALL**

Parameters:

* `bytes callData`

Calls: Arbitrary call to Algebra Integral NonfungiblePositionManager\
Usage: Executes position operations like `burn`, `collect`, `decreaseLiquidity` on Integral NFT positions.

**0x13 – INTEGRAL\_MINT**

Parameters:

* `address token0`
* `address token1`
* `int24 tickLower`
* `int24 tickUpper`
* `uint256 amount0Desired`
* `uint256 amount1Desired`
* `uint256 amount0Min`
* `uint256 amount1Min`
* `address recipient`

Calls: `NonfungiblePositionManager.mint(...)`\
Usage: Creates a new liquidity position on Algebra Integral.

**0x14 – INTEGRAL\_POSITION\_MANAGER\_PERMIT**

Parameters:

* `address spender`
* `uint256 tokenId`
* `uint256 deadline`
* `uint8 v, bytes32 r, bytes32 s`

Calls: `NonfungiblePositionManager.permit(...)`\
Usage: Grants router permission to operate on a user's Integral position NFT via signature.

**0x15 – INTEGRAL\_INCREASE\_LIQUIDITY**

Parameters:

* `uint256 tokenId`
* `uint256 amount0Desired`
* `uint256 amount1Desired`
* `uint256 amount0Min`
* `uint256 amount1Min`

Calls: `NonfungiblePositionManager.increaseLiquidity(...)`\
Usage: Adds liquidity to an existing Integral position NFT.

### Composability

**0x21 – EXECUTE\_SUB\_PLAN**

Parameters:

* `bytes subCommands`
* `bytes[] subInputs`

Calls: `execute(...)` (reentrantly)\
Usage: Nested command execution for conditional or fallback logic. Used to group steps or allow selective reverts (via `f` flag).

**How EXECUTE\_SUB\_PLAN Works:** EXECUTE\_SUB\_PLAN performs a reentrant call to the router's `execute` function with a nested set of commands and inputs. This allows you to:

* Group related operations into logical units
* Create fallback execution paths
* Implement conditional logic using the ALLOW\_REVERT flag
* Build complex multi-step operations with partial execution guarantees

**Execution Flow:**

* Main command sequence encounters EXECUTE\_SUB\_PLAN
* Router calls itself with `subCommands` and `subInputs`
* Nested commands execute sequentially
* If any nested command fails and ALLOW\_REVERT is not set, the entire transaction reverts
* If ALLOW\_REVERT is set (0x80 | 0x21), the sub-plan can fail without reverting the main transaction
* Execution returns to the main command sequence

**Example: Vault Token Liquidity Addition**

```solidity
// Main commands:
// 1. PERMIT2_TRANSFER_FROM - get underlying tokens from user
// 2. EXECUTE_SUB_PLAN:
//    a. ERC4626_WRAP - wrap to vault tokens (amount uncertain)
//    b. INTEGRAL_MINT - add liquidity using router balance
// 3. SWEEP - return any leftover tokens to user
```

### Reverting Command Example

To allow a command to fail without reverting the entire transaction, set the high bit:

```solidity
command = 0x80 | <command_id>
// Examples:
0x80 | 0x00 = 0x80  // INTEGRAL_SWAP_EXACT_IN with ALLOW_REVERT
0x80 | 0x21 = 0xa1  // EXECUTE_SUB_PLAN with ALLOW_REVERT
0x80 | 0x13 = 0x93  // INTEGRAL_MINT with ALLOW_REVERT
```

* If `f = 0` (ALLOW\_REVERT not set): Command failure reverts the entire transaction
* If `f = 1` (ALLOW\_REVERT set): Command failure is caught, execution continues with next command

Be sure to follow such commands with cleanup logic (e.g., `SWEEP`) to handle unused ETH or tokens.

**Example: Multi-Route Swap with Best Execution**

```solidity
// Try multiple DEX routes, execute whichever succeeds first
commands = [
  0x02,  // PERMIT2_TRANSFER_FROM - get input tokens
  0x80,  // INTEGRAL_SWAP_EXACT_IN (Route A) with ALLOW_REVERT
  0x90,  // UNISWAP_V3_SWAP_EXACT_IN (Route B) with ALLOW_REVERT
  0x04   // SWEEP - send output to user
]
// If Route A succeeds, Route B is skipped (no input balance left)
// If Route A fails, Route B attempts the swap
// If both fail, SWEEP still executes (no tokens to sweep)
```

### References

* https://github.com/cryptoalgebra/omega-router
* https://github.com/cryptoalgebra/omega-router/blob/main/contracts/libraries/Commands.sol
* https://docs.algebra.finance/
