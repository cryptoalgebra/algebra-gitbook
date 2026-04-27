# Step by Step Deployment

Algebra protocol consists of 4 main parts:

* 💎 Contracts
* 💾 Subgraphs
* ⚙️ Backend
* 🎨 Frontend

To get the gears going you need to setup and connect together all of these 4 parts. Each section relies on the previous one, so, please, follow along this guide to get everything setup.

## 1. 💎 Contracts

First of all, you need to deploy contracts. Algebra Protocol Contracts divides onto 3 parts:

* Core contracts
* Periphery contracts
* Tokenomics (farming) contracts

Contracts could be deployed to any EVM-compatible network. To deploy all 3 parts in-one, please follow these instructions:

[Contracts Deployment](../contracts/contracts-setup/)

After contracts are deployed, you will get these contracts and their addresses in the console:

* AlgebraPoolDeployer
* AlgebraFactory
* Quoter
* SwapRouter
* NonfungibleTokenPositionDescriptor
* Proxy
* NonfungiblePositionManager
* AlgebraInterfaceMulticall
* V3Migrator
* AlgebraLimitFarming
* AlgebraEternalFarming
* FarmingCenter

Save these contracts addresses somewhere. You will need them in all next parts.

## 2. 💾 Subgraphs

Subgraphs are used for blockchain indexing. We use them for:

* Tracking DEX data (TVL, Volume, Tokens, Pools, Prices, Ticks, etc.)
* Tracking Farming data (Available Farmings, Positions on Farming, Participants Income, etc.)

You can deploy your Subgraph to any of TheGraph supported EVM networks. You can see what chains are supported here:

[TheGraph Supported Networks](https://thegraph.com/docs/en/#supported-networks)

If your chain is not on a list, you can spin up your one TheGraph node and host it on your server. To do this, you can address TheGraph documentation in graph-node repository:

[graphprotocol/graph-node](https://github.com/graphprotocol/graph-node)

Most of the time, you don't need to host anything by yourself, and you can rely on the supported networks.

Algebra Protocol Subgraphs consist of 3 parts:

* DEX info (Tokens, Pairs, Ticks, Prices)
* Farming (Available Farmings, Participants Positions)
* Blocklytics (Tracking blocks)

Here you will need to connect previously deployed contracts and Subgraphs. To do this follow this instruction:

[Subgraph Deployment](../subgraph/subgraph-setup/)

Save API endpoints for your Subgraphs, you will need them in the next steps.

## 3. ⚙️ Backend

Algebra Protocol uses backend to keep track of Farming statistics and Pool APRS. We expose these endpoints:

Farmings APRs:

* APR/eternalFarming
* APR/limitFarmings

Farmings TVL:

* TVL/limitFarmings
* TVL/eternalFarmings

Pools APR:

* APR/pools

Here you will need to use Subgraphs APIs from the previous step.

To host these APIs on your own server follow this instruction:

[Backend Deployment](../backend/backend-setup.md)

If you can't use your own server for some reason, you can skip this step. But you will not have access to APR and TVL data on client side.

## 4. 🎨 Frontend

Frontend is divided into 2 parts:

* Client Side Application (Trading, Liquidity Providing, Participation in Farmings, Statistics)
* Admin Panel Application (Farmings Creation, Farmings Management)

Here you will need everything from previous steps:

* Contract Addresses,
* Subgraph APIs,
* Backend APIs

To run and setup them follow these instructions:

[Admin Panel Application Setup](../frontend/admin-panel/)

## 5. Finish

Now that all the steps have been completed, you have successfully set up the Algebra Protocol!

Now you can explore its features:

### Adaptive Fee

[How to tweak Adaptive Fee formula behaviour?](../contracts/adaptive-fee/how-to-tweak-formula-behaviour.md)

[How to set specific Adaptive Fee formula behaviour?](../contracts/adaptive-fee/how-to-set-up-specific-formula-behaviour.md)

### Farming

[How to create Eternal Farming](../frontend/admin-panel/eternal-farmings/create-eternal-farming.md)

[How to create Limit Farming](../frontend/admin-panel/limit-farmings/create-limit-farming.md)

[How to manage Eternal Farming](../frontend/admin-panel/eternal-farmings/managing-eternal-farming.md)

### Subgraph

[Frequently used Subgraph queries](../subgraph/query-examples/query-examples.md)
