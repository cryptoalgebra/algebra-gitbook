# Liquidity Mining Overview

### Introduction

After the user has provided liquidity for a certain range, he receives an ERC-721 token tied to this range. This token can be used, not only as a proof of the provided liquidity, but also as a tool for yield-farming.

To receive rewards, a liquidity provider can participate in one of the farming Campaigns. We have two types of farming: limit and eternal.

#### Limit farming

Limit campaign is a time-limited, pool-based incentive program that contains a certain amount of reward tokens. To participate in the Campaign, one needs to send an ERC-721 token to the farming contract, which will be stored on it until the end of the Campaign. If there is an opportunity to increase the amount of liquidity in farming, the user can additionally lock the specified token and get a multiplier of their liquidity, and therefore receive more rewards.

After the end of the Campaign, all reward tokens will be distributed among the liquidity providers who participated in the Campaign, and all ERC-721 tokens will be returned back. The amount of the reward that a particular liquidity provider will receive depends on the time while her liquidity was in the price range in which the trades took place.

For proper reward calculation for each Campaign participant we invented an entity called a Virtual pool. A Virtual pool is a simplified copy of a real pool; it is used to track the time while the liquidity of different providers is active. The calculation of the time when liquidity was active for a certain period is performed as follows:

`𝑠𝑒𝑐𝑜𝑛𝑑𝑠𝑃𝑒𝑟𝐿𝑖𝑞𝑢𝑖𝑑𝑖𝑡𝑦 = 𝑟𝑎𝑛𝑔𝑒𝑆𝑒𝑐𝑜𝑛𝑑𝑠𝐼𝑛𝑠𝑖𝑑𝑒 / 𝑟𝑎𝑛𝑔𝑒𝐿𝑖𝑞𝑢𝑖𝑑𝑖𝑡𝑦`

Where rangeSecondsInside is the time while the price was at the range of provided liquidity, and rangeLiquidity is all of the liquidity on that range.

The time while liquidity was active on a given range is calculated as:

`𝑝𝑜𝑠𝑖𝑡𝑖𝑜𝑛𝑆𝑒𝑐𝑜𝑛𝑑𝑠𝐼𝑛𝑠𝑖𝑑𝑒 = 𝑠𝑒𝑐𝑜𝑛𝑑𝑠𝑃𝑒𝑟𝐿𝑖𝑞𝑢𝑖𝑑𝑖𝑡𝑦 * 𝑝𝑜𝑠𝑖𝑡𝑖𝑜𝑛𝐿𝑖𝑞𝑢𝑖𝑑𝑖𝑡𝑦`

Where positionLiquidity is the value that represents position’s liquidity.

Now, knowing the time while the liquidity was active, we can calculate the reward for the given Campaign participant:

`𝑟𝑒𝑤𝑎𝑟𝑑 = 𝑡𝑜𝑡𝑎𝑙𝑅𝑒𝑤𝑎𝑟𝑑 * 𝑝𝑜𝑠𝑖𝑡𝑖𝑜𝑛𝑆𝑒𝑐𝑜𝑛𝑑𝑠𝐼𝑛𝑠𝑖𝑑𝑒 / 𝑐𝑎𝑚𝑝𝑎𝑖𝑔𝑛𝐷𝑢𝑟𝑎𝑡𝑖𝑜𝑛`

Every time there is a swap in the real pool the Virtual pool recalculates the rewards for each participant of the Campaign.

#### Eternal Farming

Eternal farming is more like classic liquidity mining, the user can exit or enter farming at any time. It uses a logic similar to limit farming. Only the distribution mechanism differs. The number of distributed rewards is set using the rate (number of tokens per second) and can be changed during farming.

### Dev part

#### Incentive Maker

Since in the current implementation only one farming of every type can be active for one pool and it is possible to increase / decrease the number of rewards, the ability to create farms and manage rewards has been moved to a separate role that can be set by the contract owner.

[How to create farmings](https://docs.algebra.finance/en/docs/frontend/admin-panel/introduction).

#### Contracts

There are three main contracts:

[FarmingCenter](https://github.com/cryptoalgebra/Algebra/blob/master/src/tokenomics/contracts/FarmingCenter.sol)

[AlgebraEternalFarming](https://github.com/cryptoalgebra/Algebra/blob/master/src/tokenomics/contracts/farmings/eternalFarming/AlgebraEternalFarming.sol)

[AlgebraLimitFarming](https://github.com/cryptoalgebra/Algebra/blob/master/src/tokenomics/contracts/farmings/limitFarming/AlgebraLimitFarming.sol)

FarmingCenter contract on which users' NFTs are stored is used as a "proxy" for the interaction of the main and virtual pools. Interacts with contracts of limit and eternal farmings, allows to participate in two farmings at the same time.

To enter farming you need to transfer an NFT to the farming center to receive an Algebra Farming NFT-V2. After that you can participate in any type of farming for the pool, you provided liquidity to. After transfer, you need to call

[enterFarming](https://github.com/cryptoalgebra/Algebra/blob/master/src/tokenomics/contracts/FarmingCenter.sol#L99)

```solidity
    // IncentiveKey - is struct that represent campaign entity and contains following fields
    // rewardToken - The token being distributed as a reward
    // bonusRewardToken - The bonus token being distributed as a reward
    // pool - The Algebra pool
    // startTime - The time when the incentive program begins
    // endTime - The time when rewards stop accruing
    // tokenId - id of the token that will be used in farming
    // tokensLocked - amount of tokens that will be locked up to get the liquidity multiplier
    // isLimit - field indicating the type of farming
    function enterFarming(
        IncentiveKey memory key,
        uint256 tokenId,
        uint256 tokensLocked,
        bool isLimit
    )
```

After the enter, your liquidity will be recorded in the farming virtual pool and you will start earning rewards. While an NFT stays on the Farming Center, you can collect fees by calling the function

[collect](https://github.com/cryptoalgebra/Algebra/blob/master/src/tokenomics/contracts/FarmingCenter.sol#L158)

```solidity
    // CollectParams contains following fields:
    // tokenId - The ID of the NFT whose tokens are collected
    // recipient - The account that should receive the tokens
    // amount0Max - The maximum amount of token0 to collect
    // amount1Max - The maximum amount of token1 to collect
    function collect(CollectParams memory params) 
```

if the position is in an eternal farming, rewards can be collected by calling

[collectRewards](https://github.com/cryptoalgebra/Algebra/blob/master/src/tokenomics/contracts/FarmingCenter.sol#L171)

```solidity
    function collectRewards(IncentiveKey memory key, uint256 tokenId)
```

To exit the eternal farming or finished limit farming you can call function

[exitFarming](https://github.com/cryptoalgebra/Algebra/blob/master/src/tokenomics/contracts/FarmingCenter.sol#L132)

```solidity
    function exitFarming(
        IncentiveKey memory key,
        uint256 tokenId,
        bool isLimit
    )
```

The rewards are not automatically accrued upon exit. To receive rewards you need to call

[claimReward](https://github.com/cryptoalgebra/Algebra/blob/master/src/tokenomics/contracts/FarmingCenter.sol#L194)

```solidity
    // rewardToken - The token that is a reward
    // to - The account that should receive the tokens
    // amountRequestedIncentive - Amount to claim in limit farming
    // amountRequestedEternal - Amount to claim in eternal farming
    function claimReward(
        IERC20Minimal rewardToken,
        address to,
        uint256 amountRequestedIncentive,
        uint256 amountRequestedEternal
    )
```

After the exit from all farmings, you can withdraw your position from Farming Center by calling

[withdrawToken](https://github.com/cryptoalgebra/Algebra/blob/master/src/tokenomics/contracts/FarmingCenter.sol#L236)

```solidity
    // tokenId - The id of position NFT
    // to - The account that should receive the position NFT
    // data - The additional data for NonfungiblePositionManager
    function withdrawToken(
        uint256 tokenId,
        address to,
        bytes memory data
    )
```

AlgebraEternalFarming and AlgebraLimitFarming contracts used to create farms, deploy virtual pools and calculate rewards.

#### Graph

In order to get a list of active farmings or positions, we use a subgraph. Farming graph contains all the entities necessary for this:

LimitFarming - contains information about the start and the end of farming, rewards and tiers.\
EternalFarming - contains information about virtual pools, rewards, rewards rates and tiers.\
Deposit - contains farmings keys in which the position takes part and information about locked tokens amounts.\
Reward - contain information about rewards amounts.\
Token - сontains basic information about tokens that are used in farmings.

[Full Farming Schema](https://docs.algebra.finance/en/docs/subgraph/graphQL-schema/farming/farming-schema)

#### Query Examples

Query for fetching all limit farmings:

```
query fetchIncentive($incentiveId: ID) {
    incentives(where: { id: $incentiveId}) {
        id
        rewardToken
        bonusRewardToken
        pool
        startTime
        endTime
        reward
        bonusReward
        multiplierToken
        createdAtTimestamp
        level1multiplier
        level2multiplier
        level3multiplier
        algbAmountForLevel1
        algbAmountForLevel2
        algbAmountForLevel3
    }
}
```

Query for fetching all eternal farmings:

```
  query fetchEternalFarm {
    eternalFarmings {
      id
      rewardToken
      bonusRewardToken
      pool
      startTime
      endTime
      reward
      bonusReward
      rewardRate
      bonusRewardRate
      isDetached
    }
  }
```

Query for fetching all active deposits in eternal farming:

```
  query positionsOnEternalFarming ($account: Bytes) {
    deposits (orderBy: id, orderDirection: desc, where: { owner: $account, onFarmingCenter: true, eternalFarming_not: null }) {
      id
      owner
      pool
      L2tokenId
      eternalFarming
      onFarmingCenter
      enteredInEternalFarming
    }
  }
```
