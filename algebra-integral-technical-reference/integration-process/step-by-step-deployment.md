# Step by Step Deployment

Algebra protocol consists of 4 main parts:

* 💎 Contracts
* 💾 Subgraphs
* ⚙️ Backend
* 🎨 Frontend

To get the gears going you need to setup and connect together all of these 4 parts. Each section relies on the previous one, so, please, follow along this guide to get everything setup.

## 1. 💎 Contracts

First of all, you need to deploy contracts. Algebra Protocol Contracts divides onto 4 parts:

* Core contracts
* Farming contracts
* Periphery contracts
* Plugin

Contracts could be deployed to any EVM-compatible network. To deploy all four parts together, follow these steps:

1. npm run bootstrap in root directory (Requires npm >= 8.0.0)
2. add network to hardhat config
3. create .env file in root directory, add MNEMONIC(private key without 0x) to .env
4. Update partnerAddress and protocol fee settings in `src/core/scripts/deploy.js`
5. Update wnative address (line 12) and symbol (line 80) for NonfungibleTokenPositionDescriptor in `src/periphery/scripts/deploy.js`
6. npm run deploy --network `network_name`
7. npm run verify --network `network_name`

After contracts are deployed, you will get these contracts and their addresses in the console:

* AlgebraPoolDeployer
* AlgebraFactory
* AlgebraCommunityVault
* AlgebraVaultFactoryStub
* PluginFactory
* EntryPoint
* TickLens
* Quoter
* QuoterV2
* SwapRouter
* NonfungibleTokenPositionDescriptor
* Proxy
* NonfungiblePositionManager
* AlgebraInterfaceMulticall
* AlgebraEternalFarming
* FarmingCenter

Save these contracts addresses somewhere. You will need them in all next sections.

## 2. 💾 Subgraphs

Subgraphs are used for blockchain indexing. We use them for:

* Tracking DEX data (TVL, Volume, Tokens, Pools, Prices, Ticks, etc.)
* Tracking Farming data (Available Farmings, Positions on Farming, Participants Income, etc.)

You can deploy your Subgraph to any of TheGraph or GoldSky supported EVM networks. You can see what chains are supported here:

[TheGraph Supported Networks](https://thegraph.com/docs/en/#supported-networks)

[GoldSky Supported Networks](https://docs.goldsky.com/chains/supported-networks)

If your chain is not on a list, you can spin up your one TheGraph node and host it on your server. To do this, you can address TheGraph documentation in graph-node repository:

[graphprotocol/graph-node](https://github.com/graphprotocol/graph-node)

Usually, you can depend on supported networks without self-hosting.

Algebra Protocol Subgraphs consist of 3 parts:

* DEX info (Tokens, Pairs, Ticks, Prices)
* Farming (Available Farmings, Participants Positions)
* Blocklytics (Tracking blocks)

Refer to the Subgraph repository's README file for deployment instructions.

Save API endpoints for your Subgraphs, you will need them in the next steps.

## 3. ⚙️ Backend

Algebra Protocol uses backend to keep track of Farming statistics and Pool APRS. We expose these endpoints:

Farmings:

* APR
* MAX-APR
* TVL

Pools:

* APR
* MAX-APR

Here you will need to use Subgraphs APIs from the previous step. Refer to the backend repository's README file for deployment instructions.

For backend operations, 2 CPUs and 8 GB of memory should suffice. However, you may consider using 4 CPUs to accommodate potential increases in update frequency for performance.

If you can't use your own server for some reason, you can skip this step. But you will not have access to APR and TVL data on client side.

## 4. 🎨 Frontend

Frontend is divided into 4 parts:

* SDK
* Smart Router (Optional: Use a dedicated router for custom pools, or opt for a simpler one for base pools).
* Client Side Application (Trading, Liquidity Providing, Participation in Farmings, Statistics)
* Admin Panel Application (Farmings Creation, Farmings Management)

Here you will need everything from previous steps:

* Contract Addresses,
* Subgraph APIs,
* Backend APIs

### &#x20;4.1. SDK

* Update the addresses in `src/constants/addresses.ts`
* Modify chain name and ID in `src/constants/chainIds.ts`
* Update Wnative information in `src/entities/wnative.ts`
* Update the SDK package name and remove any version information (version will be set during publishing) from `package.json`.



### 4.2. Smart Router&#x20;

This router supports custom pools. If you do not require custom pools, you can skip this step. To disable the SmartRouter functionality and use a simpler router, set the `SmartRouter` flag to `false` in the `config/app-modules.ts` file  in the Client Side Application.

* Update addresses in `evm/constants/addresses.ts`  &#x20;
* Update subgraph endpoints in `evm/chains/src/subgraphs.ts`  and `evm/v3-router/providers/publicProviders.ts`
* Configure chain parameters in `evm/chains/src/chainId.ts`, `evm/chains/src/chainNames.ts` and `evm/v3-router/providers/publicProviders.ts`&#x20;
* Update `MULTICALL_ADDRESS` in `evm/multicall/src/constants/contracts.ts` &#x20;
* Update the SDK link; the router package name and remove any version information (version will be set during publishing) from `package.json`.
* To enable multihops, define the specific tokens to be used in the `evm/constants/tokens.ts` file.
* Use the global search feature (Ctrl+Shift+F) to locate all instances of `ChainId.BASE_SEPOLIA` in the project repository and replace them with your custom chain name previously configured in the SDK.

### &#x20;4.3. Client Side Application settings:

* Enable all necessary modules in `config/app-modules.ts`.
* Update deployed contract addresses in `config/contract-addresses.ts`.
* Configure chain parameters in `config/default-chain.ts` and `config/wagmi.ts`.
* Use the global search feature (Ctrl+Shift+F) to locate all instances of `ChainId.BaseSepolia` in the project repository and replace them with your custom chain name previously configured in the SDK.
* Update subgraph endpoints in `config/graphql-urls.ts`
* Add stablecoins to `config/tokens.ts`.
* Update APR endpoints in `config/apr-urls.ts`
* Update token addresses in `src/components/common/CurrencyLogo/index.tsx`.
* Update the SDK and router links in both `package.json`



### 4.4. Admin Panel

* Update subgraph endpoints in `.env` and `codegen.ts`
* Update addresses in `src/constants/addresses.ts`
* Configure chain parameters in `src/constants/default-chain-id.ts` and `src/wagmi.ts`
* Update the SDK link in `package.json`
* Use the global search feature (Ctrl+Shift+F) to locate all instances of `ChainId.BaseSepolia` in the project repository and replace them with your custom chain name previously configured in the SDK.





## 5. Finish

Now that all the steps have been completed, you have successfully set up the Algebra Protocol!

Now you can explore its features:

[Adaptive Fee](https://docs.algebra.finance/algebra-integral-documentation/algebra-integral-technical-reference/plugins/adaptive-fee)

[Farming](https://docs.algebra.finance/algebra-integral-documentation/algebra-integral-technical-reference/plugins/farming)

[Frequently used Subgraph queries](https://docs.algebra.finance/algebra-integral-documentation/algebra-integral-technical-reference/integration-process/subgraphs-and-analytics/examples-of-queries)

[Integration FAQ](https://docs.algebra.finance/algebra-integral-documentation/algebra-integral-technical-reference/integration-process/faq)
