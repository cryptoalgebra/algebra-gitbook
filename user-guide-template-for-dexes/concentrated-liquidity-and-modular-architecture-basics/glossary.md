---
icon: arrow-down-a-z
---

# Glossary

{% hint style="info" %}
**Note for DEX Teams:**

This glossary breaks down **essential terminology** used throughout the guide, enabling your users to speak the same language when working with an Algebra-powered DEX product.
{% endhint %}

Switching from a traditional V2 DEX to a Modular Concentrated Liquidity V4 DEX like one powered by Algebra Integral introduces several key changes. Users will now interact with more customizable liquidity positions, experience greater capital efficiency, and may encounter new features such as dynamic fees or built-in farming. This evolution means learning new concepts and tools not present in V2 models. Below is a glossary of the essential terms to understand this shift.

## V2 Concepts (Familiar terms)

* **Liquidity Pool** is a smart contract that holds a pair of tokens and enables users to trade between them using automated pricing.
* **AMM (Automated Market Maker)** is a protocol that uses a mathematical formula to price assets in a liquidity pool, removing the need for order books.
* **Swap** is a trade between two tokens using the liquidity in a pool, where one token is exchanged for another.
* **Slippage** is the difference between the expected price and the actual price at which a trade is executed due to price movement or low liquidity.
* **Impermanent Loss** is the change in the assets’ prices. It occurs when you provide liquidity to a liquidity pool and the price of your deposited assets change compared to the moment you deposited them.
* **Price Impact** is how much a large trade moves the market price due to limited liquidity or depth in the pool.
* **Liquidity Provider (LP)** is a user who deposits token pairs into a liquidity pool to enable trading and earn a share of the trading fees.
* **Pool Share** is the percentage of the total pool reserves owned by an LP, which determines how much of the fees and assets they can claim.
* **LP Tokens** are fungible tokens that represent a user’s share of a V2 liquidity pool and their claim on pool assets and fees.
* **Reserves** are the total amounts of each token held in a liquidity pool, used to determine price and enable swaps.

## V3 & V4 Modular CLAMM Concepts

* **Concentrated Liquidity** is the liquidity allocated within a custom price range. With the concentrated liquidity mechanics, LPs can accumulate their capital to smaller price intervals than (0, ∞) enabling individualized price curves, higher capital efficiency & deeper liquidity for traders.
* **DEX Engine** is the V3 codebase that Algebra provides to all the integrated DEXes, with the features we present. Thus, decentralized exchanges within the Algebra DEX Ecosystem are built on the Algebra DEX Engine, sharing part of trading fees to the Algebra dev team.
* **Capital Efficiency** is A measure of how effectively capital is used to generate returns. Greater capital efficiency means a trader is maximizing the potential return on their invested capital.
* **Price Range** defines your expected min & max price limits of your position. Algebra-built DEXes offer custom range options for optimal liquidity, reducing slippage and loss while boosting LP fees
* **Custom Liquidity Ranges** are specific price intervals selected by liquidity providers to focus their capital. These ranges are set according to market conditions and ensure liquidity is available in targeted areas for trading.
* **Active Liquidity** is the portion of an LP's liquidity that is currently within the market price range and earning fees.
* **Out-of-Range Liquidity** is the liquidity that sits outside the current market price range and is inactive—it earns no fees until the price returns to that range.
* **Tick** is a discrete price level used to define ranges in V3; liquidity is concentrated between ticks chosen by the LP.
* **Tick Spacing**: tick spacing sets the intervals between price points (or “ticks”) in a liquidity pool. For example, with a tick spacing of 8, each tick is 0.08% apart from the next price level, while a spacing of 128 means each tick is 1.28% apart.
* **Customizable Tickspacing, or Tickspacing Adjustment**, empowers DEXes to fine-tune tickspacing for pools, boosting efficiency, especially for stablecoin pairs. This customization helps integrated DEXes compete effectively with other projects by enabling narrower liquidity settings.
* **Position (NFT)** is a unique liquidity position in V3 defined by a token pair, price range, and amount—represented as a non-fungible token (NFT).
* **NFT LP / NFT Position** is same as above: refers to a V3 liquidity position held as an NFT instead of fungible LP tokens.
* **Dynamic Fees, or Volatility-based Fees** is the feature presenting only 1 pool with a special model calculating the fee depending on numerous factors: the risk, volatility of an asset, trading, and pool’s volume.
* **Farming** is a feature within the Algebra DEX Engine allowing users to deposit their assets & earn significantly more, all in one place, without external platforms.
* **Staking** is another Algebra feature, available on our platform. Algebra has its native token ALGB which allows you to earn high interest rates by taking part in staking, getting a share of the trading fees obtained from the integrated DEXes.
* **Limit Order** is the feature reducing price impact and creating opportunities for market makers and traders, aggregators and users. These on-chain orders use tick-based mechanics, complementing newer smart contracts.
* **Rebase Tokens Support** is a unique feature in the Algebra codebase, enhancing support for rebase & similar tokens by distributing excess balance to active liquidity providers as a fee.
* **Integral** is the Algebra tech, bringing seamless updates without liquidity transfers, tackles high gas issues, bolsters flexibility, ensures security, and more for DEXes. It's powered by its Modular Architecture, featuring a stable core and upgradable plugins.&#x20;
* **Core** is a crucial smart contract, a part of the Integral codebase that stores the pair’s liquidity and implements the swap logic
* **Plugin (Periphery Contract)** is a smart contract that can be held by the pool both before and after major actions (such as swap, liquidity provision, or flash loan), and the pool can reference it. It can execute various actions on the blockchain, calling other contracts, adjusting the pool's commission, implement Farming, Limit Orders, and more.
* **Hooks** are calls from a pool to a Plugin before or after specific actions.<br>
