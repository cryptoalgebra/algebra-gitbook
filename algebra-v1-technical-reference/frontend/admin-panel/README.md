# Admin Panel

Welcome to Algebra Protocol Admin Panel!

Admin Panel is being used for creating and managing farmings.

### Incentive maker

The only address that can perform any actions in Admin Panel is the Incentive Maker. You can set it in constants when deploying contracts, or you can change it using a method described in AlgebraFactory.sol.

### Basic Farming rules

When LP provides liquidity, they get an NFT, which represents their position. This NFT can be used to participate in farming and earn rewards.

Basic farming rules are:

1. Participants only get rewards if their positions are in range – this way their liquidity “is working”.
2. With one NFT participants can enter Infinite and Limit farming for the same pool.
3. When participating in Limit Farming, participants lock their NFTs for the farming period.
4. When participating in Infinite Farming, NFTs **are not** locked. Participants can withdraw their NFTs at any time they want.
5. There can be only one Limit Farming incentive and only one Infinite Farming incentive for one pair of tokens at a time.

How to create Infinite Farming?

How to manage Infinite Farming?
