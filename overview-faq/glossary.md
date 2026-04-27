---
description: >-
  Here's the basic package of all the definitions related to Algebra technology,
  its unique propositions, and more.
---

# 📚 Glossary

* **Concentrated Liquidity** is the liquidity allocated within a custom price range. With the concentrated liquidity mechanics, LPs can accumulate their capital to smaller price intervals than (0, ∞) enabling individualized price curves, higher capital efficiency & deeper liquidity for traders.
* **Dynamic Fees, or Volatility-based Fees** is the feature presenting only 1 pool with a special model calculating the fee depending on numerous factors: the risk, volatility of an asset, trading, and pool’s volume.
* **Price range** defines your expected min & max price limits of your position. Algebra-built DEXes offer custom range options for optimal liquidity, reducing slippage and loss while boosting LP fees.
* **DEX Engine** is the V3 codebase that Algebra provides to all the integrated DEXes, with the features we present. Thus, decentralized exchanges within the Algebra DEX Ecosystem are built on the Algebra DEX Engine, sharing part of trading fees to the Algebra dev team.
* **Core** is a crucial part of the Integral codebase that stores the pair’s liquidity and implements the swap logic
* **Plugin** is a smart contract that can be held by the pool both before and after major actions (such as swap, liquidity provision, or flash loan), and the pool can reference it. It can execute various actions on the blockchain, calling other contracts, adjusting the pool's commission, implement Farming, Limit Orders, and more.
* **Hooks** are calls from a pool before or after specific actions.
* **Impermanent Loss** is the change in the assets’ prices. It occurs when you provide liquidity to a liquidity pool and the price of your deposited assets change compared to the moment you deposited them.
* **Built-in Farming** is a feature withing the Algebra DEX Engine allowing users to deposit their assets & earn significantly more, all in one place, without external platforms.
* **Staking** is another Algebra feature, available on our platform. Algebra has its native token ALGB which allows you to earn high interest rates by taking part in staking, getting a share of the trading fees obtained from the integrated DEXes.
* **Limit Order** is the feature reducing price impact and create opportunities for market makers and traders, aggregators and users. These on-chain orders use tick-based mechanics, complementing newer smart contracts.
* **Customizable Tickspacing, or Tickspacing Adjustment,** empowers DEXes to fine-tune tickspacing for pools, boosting efficiency, especially for stablecoin pairs. This customization helps integrated DEXes compete effectively with other projects by enabling narrower liquidity settings.
* **Rebase Tokens Support** is a unique feature in the Algebra codebase, enhancing support for rebase & similar tokens by distributing excess balance to active liquidity providers as a fee.
* **Integral** is the Algebra tech, bringing seamless updates without liquidity transfers, tackles high gas issues, bolsters flexibility, ensures security, and more for DEXes. It's powered by its Modular Architecture, featuring a stable core and upgradable plugins. Learn more [here](https://medium.com/@crypto_algebra/algebra-integral-empowering-decentralized-exchanges-with-modular-architecture-199d7a540efe).
