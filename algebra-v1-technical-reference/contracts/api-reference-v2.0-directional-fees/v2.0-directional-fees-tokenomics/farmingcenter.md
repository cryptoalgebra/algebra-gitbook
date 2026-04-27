# FarmingCenter

Algebra main farming contract

_Developer note: Manages farmings and performs entry, exit and other actions._

## Modifiers

### isOwner

`modifier isOwner(uint256 tokenId)` internal

| Name    | Type    | Description |
| ------- | ------- | ----------- |
| tokenId | uint256 |             |

## Variables

### contract IAlgebraEternalFarming eternalFarming immutable

The eternal farming contract

### contract INonfungiblePositionManager nonfungiblePositionManager immutable

The nonfungible position manager with which this farming contract is compatible

### address algebraPoolDeployer immutable

The Algebra poolDeployer contract

### mapping(address => address) virtualPoolAddresses

_Developer note: saves addresses of virtual pools for pool_

### mapping(uint256 => bytes32) deposits

_Developer note: deposits\[tokenId] => incentiveId_

### mapping(bytes32 => struct IncentiveKey) incentiveKeys

## Functions

### constructor

`constructor(contract IAlgebraEternalFarming _eternalFarming, contract INonfungiblePositionManager _nonfungiblePositionManager) public` public

| Name                         | Type                                 | Description |
| ---------------------------- | ------------------------------------ | ----------- |
| \_eternalFarming             | contract IAlgebraEternalFarming      |             |
| \_nonfungiblePositionManager | contract INonfungiblePositionManager |             |

### enterFarming

`function enterFarming(struct IncentiveKey key, uint256 tokenId) external` external

Enters in incentive (time-limited or eternal farming) with NFT-position token _Developer note: token must be deposited in FarmingCenter_

| Name    | Type                | Description             |
| ------- | ------------------- | ----------------------- |
| key     | struct IncentiveKey | The incentive event key |
| tokenId | uint256             | The id of position NFT  |

### exitFarming

`function exitFarming(struct IncentiveKey key, uint256 tokenId) external` external

Exits from incentive (time-limited or eternal farming) with NFT-position token

| Name    | Type                | Description             |
| ------- | ------------------- | ----------------------- |
| key     | struct IncentiveKey | The incentive event key |
| tokenId | uint256             | The id of position NFT  |

### applyLiquidityDelta

`function applyLiquidityDelta(uint256 tokenId, int256 liquidityDelta) external` external

Report a change of liquidity in position

| Name           | Type    | Description                                            |
| -------------- | ------- | ------------------------------------------------------ |
| tokenId        | uint256 | The ID of the token for which liquidity is being added |
| liquidityDelta | int256  | The amount of added liquidity                          |

### increaseLiquidity

`function increaseLiquidity(uint256 tokenId, uint256 liquidityDelta) external` external

| Name           | Type    | Description |
| -------------- | ------- | ----------- |
| tokenId        | uint256 |             |
| liquidityDelta | uint256 |             |

### decreaseLiquidity

`function decreaseLiquidity(uint256 tokenId, uint256 liquidityDelta) external returns (bool success)` external

| Name           | Type    | Description |
| -------------- | ------- | ----------- |
| tokenId        | uint256 |             |
| liquidityDelta | uint256 |             |

**Returns:**

| Name    | Type | Description |
| ------- | ---- | ----------- |
| success | bool |             |

### burnPosition

`function burnPosition(uint256 tokenId) external returns (bool success)` external

Report a burn of position token

| Name    | Type    | Description                               |
| ------- | ------- | ----------------------------------------- |
| tokenId | uint256 | The ID of the token which is being burned |

**Returns:**

| Name    | Type | Description |
| ------- | ---- | ----------- |
| success | bool |             |

### collectRewards

`function collectRewards(struct IncentiveKey key, uint256 tokenId) external returns (uint256 reward, uint256 bonusReward)` external

Used to collect reward from eternal farming. Then reward can be claimed.

| Name    | Type                | Description             |
| ------- | ------------------- | ----------------------- |
| key     | struct IncentiveKey | The incentive event key |
| tokenId | uint256             | The id of position NFT  |

**Returns:**

| Name        | Type    | Description                          |
| ----------- | ------- | ------------------------------------ |
| reward      | uint256 | The amount of collected reward       |
| bonusReward | uint256 | The amount of collected bonus reward |

### claimReward

`function claimReward(contract IERC20Minimal rewardToken, address to, uint256 amountRequested) external returns (uint256 reward)` external

Used to claim and send rewards from farming(s) _Developer note: can be used via static call to get current rewards for user_

| Name            | Type                   | Description                        |
| --------------- | ---------------------- | ---------------------------------- |
| rewardToken     | contract IERC20Minimal | The token that is a reward         |
| to              | address                | The address to be rewarded         |
| amountRequested | uint256                | Amount to claim in eternal farming |

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
