# FarmingCenter

Algebra main farming contract

_Developer note: Manages farmings and performs entry, exit and other actions._

## Variables

### contract IAlgebraLimitFarming limitFarming immutable

### contract IAlgebraEternalFarming eternalFarming immutable

### contract INonfungiblePositionManager nonfungiblePositionManager immutable

The nonfungible position manager with which this farming contract is compatible

### contract IFarmingCenterVault farmingCenterVault immutable

### mapping(uint256 => struct FarmingCenter.Deposit) deposits

_Developer note: deposits\[tokenId] => Deposit_

### mapping(uint256 => struct FarmingCenter.L2Nft) l2Nfts

## Functions

### constructor

`constructor(contract IAlgebraLimitFarming _limitFarming, contract IAlgebraEternalFarming _eternalFarming, contract INonfungiblePositionManager _nonfungiblePositionManager, contract IFarmingCenterVault _farmingCenterVault) public` public

| Name                         | Type                                 | Description |
| ---------------------------- | ------------------------------------ | ----------- |
| \_limitFarming               | contract IAlgebraLimitFarming        |             |
| \_eternalFarming             | contract IAlgebraEternalFarming      |             |
| \_nonfungiblePositionManager | contract INonfungiblePositionManager |             |
| \_farmingCenterVault         | contract IFarmingCenterVault         |             |

### onERC721Received

`function onERC721Received(address, address from, uint256 tokenId, bytes) external returns (bytes4)` external

Upon receiving a Algebra ERC721, creates the token deposit setting owner to \`from\`. \*Developer note: Whenever an {IERC721} \`tokenId\` token is transferred to this contract via {IERC721-safeTransferFrom} by \`operator\` from \`from\`, this function is called.

It must return its Solidity selector to confirm the token transfer. If any other value is returned or the interface is not implemented by the recipient, the transfer will be reverted.

The selector can be obtained in Solidity with \`IERC721.onERC721Received.selector\`.\*

| Name    | Type    | Description |
| ------- | ------- | ----------- |
|         | address |             |
| from    | address |             |
| tokenId | uint256 |             |
|         | bytes   |             |

**Returns:**

| Name | Type   | Description |
| ---- | ------ | ----------- |
| \[0] | bytes4 |             |

### enterFarming

`function enterFarming(struct IIncentiveKey.IncentiveKey key, uint256 tokenId, uint256 tokensLocked, bool isLimit) external` external

Enters in incentive (time-limited or eternal farming) with NFT-position token _Developer note: token must be deposited in FarmingCenter_

| Name         | Type                              | Description                                                           |
| ------------ | --------------------------------- | --------------------------------------------------------------------- |
| key          | struct IIncentiveKey.IncentiveKey | The incentive event key                                               |
| tokenId      | uint256                           | The id of position NFT                                                |
| tokensLocked | uint256                           | Amount of tokens to lock for liquidity multiplier (if tiers are used) |
| isLimit      | bool                              | Is incentive time-limited or eternal                                  |

### exitFarming

`function exitFarming(struct IIncentiveKey.IncentiveKey key, uint256 tokenId, bool isLimit) external` external

Exits from incentive (time-limited or eternal farming) with NFT-position token

| Name    | Type                              | Description                          |
| ------- | --------------------------------- | ------------------------------------ |
| key     | struct IIncentiveKey.IncentiveKey | The incentive event key              |
| tokenId | uint256                           | The id of position NFT               |
| isLimit | bool                              | Is incentive time-limited or eternal |

### collect

`function collect(struct INonfungiblePositionManager.CollectParams params) external returns (uint256 amount0, uint256 amount1)` external

Collects up to a maximum amount of fees owed to a specific position to the recipient _Developer note: "proxies" to NonfungiblePositionManager_

| Name                                                  | Type                                             | Description                                                     |
| ----------------------------------------------------- | ------------------------------------------------ | --------------------------------------------------------------- |
| params                                                | struct INonfungiblePositionManager.CollectParams | tokenId The ID of the NFT for which tokens are being collected, |
| recipient The account that should receive the tokens, |                                                  |                                                                 |
| amount0Max The maximum amount of token0 to collect,   |                                                  |                                                                 |
| amount1Max The maximum amount of token1 to collect    |                                                  |                                                                 |

**Returns:**

| Name    | Type    | Description                            |
| ------- | ------- | -------------------------------------- |
| amount0 | uint256 | The amount of fees collected in token0 |
| amount1 | uint256 | The amount of fees collected in token1 |

### collectRewards

`function collectRewards(struct IIncentiveKey.IncentiveKey key, uint256 tokenId) external returns (uint256 reward, uint256 bonusReward)` external

Used to collect reward from eternal farming. Then reward can be claimed.

| Name    | Type                              | Description             |
| ------- | --------------------------------- | ----------------------- |
| key     | struct IIncentiveKey.IncentiveKey | The incentive event key |
| tokenId | uint256                           | The id of position NFT  |

**Returns:**

| Name        | Type    | Description                          |
| ----------- | ------- | ------------------------------------ |
| reward      | uint256 | The amount of collected reward       |
| bonusReward | uint256 | The amount of collected bonus reward |

### claimReward

`function claimReward(contract IERC20Minimal rewardToken, address to, uint256 amountRequestedIncentive, uint256 amountRequestedEternal) external returns (uint256 reward)` external

Used to claim and send rewards from farming(s) _Developer note: can be used via static call to get current rewards for user_

| Name                     | Type                   | Description                                  |
| ------------------------ | ---------------------- | -------------------------------------------- |
| rewardToken              | contract IERC20Minimal | The token that is a reward                   |
| to                       | address                | The address to be rewarded                   |
| amountRequestedIncentive | uint256                | Amount to claim in incentive (limit) farming |
| amountRequestedEternal   | uint256                | Amount to claim in eternal farming           |

**Returns:**

| Name   | Type    | Description                           |
| ------ | ------- | ------------------------------------- |
| reward | uint256 | The summary amount of claimed rewards |

### connectVirtualPool

`function connectVirtualPool(contract IAlgebraPool pool, address newVirtualPool) external` external

Updates activeIncentive in AlgebraPool _Developer note: only farming can do it_

| Name           | Type                  | Description                                   |
| -------------- | --------------------- | --------------------------------------------- |
| pool           | contract IAlgebraPool | The AlgebraPool for which farming was created |
| newVirtualPool | address               |                                               |

### withdrawToken

`function withdrawToken(uint256 tokenId, address to, bytes data) external` external

Withdraw Algebra NFT-position token _Developer note: can be used via static call to get current rewards for user_

| Name    | Type    | Description                                        |
| ------- | ------- | -------------------------------------------------- |
| tokenId | uint256 | The id of position NFT                             |
| to      | address | New owner of position NFT                          |
| data    | bytes   | The additional data for NonfungiblePositionManager |

### cross

`function cross(int24 nextTick, bool zeroToOne) external` external

_Developer note: This function is called by the main pool when an initialized tick is crossed and two farmings are active at same time._

| Name      | Type  | Description      |
| --------- | ----- | ---------------- |
| nextTick  | int24 | The crossed tick |
| zeroToOne | bool  | The direction    |

### increaseCumulative

`function increaseCumulative(uint32 blockTimestamp) external returns (enum IAlgebraVirtualPool.Status status)` external

_Developer note: This function is called by the main pool at start of the swap if two farmings are active at same time._

| Name           | Type   | Description                            |
| -------------- | ------ | -------------------------------------- |
| blockTimestamp | uint32 | The current block timestamp, truncated |

**Returns:**

| Name   | Type                            | Description                   |
| ------ | ------------------------------- | ----------------------------- |
| status | enum IAlgebraVirtualPool.Status | The current farming(s) status |

### virtualPoolAddresses

`function virtualPoolAddresses(address pool) external view returns (address limitVP, address eternalVP)` view external

| Name | Type    | Description |
| ---- | ------- | ----------- |
| pool | address |             |

**Returns:**

| Name      | Type    | Description |
| --------- | ------- | ----------- |
| limitVP   | address |             |
| eternalVP | address |             |

### getApproved

`function getApproved(uint256 tokenId) public view returns (address)` view public

\*Developer note: Returns the account approved for \`tokenId\` token.

Requirements:

* \`tokenId\` must exist.\*

| Name    | Type    | Description |
| ------- | ------- | ----------- |
| tokenId | uint256 |             |

**Returns:**

| Name | Type    | Description |
| ---- | ------- | ----------- |
| \[0] | address |             |
