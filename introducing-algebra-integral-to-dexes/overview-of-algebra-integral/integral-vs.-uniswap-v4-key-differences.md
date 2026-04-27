---
icon: left-right
---

# Integral vs. Uniswap V4: Key Differences

The main competitor of Integral will be Uniswap V4 with the same modular concept. Because of the business license, it will be impossible to fork it, currently making only Algebra Integral-based DEXes and Uniswap V4 competing with each other. Here is how V4 and Integral differentiate from each other:

* **Philosophy:** Uni V4 allows anyone to create a plugin and launch it on their pool. Integral, on the other hand, does not grant these rights to just anyone; DEX administrators or the DAO control plugin setup, with one pool for one pair, thus, eliminating liquidity fragmentation issues.
* **Singleton:** Uni V4 consolidates all liquidity in one contract, while Integral keeps pools separated. Singleton is more efficient for multihops and pool creation but carries the risk of having all tokens in one place. Also, the advantages of Singleton can be covered in Algebra Integral with multiple plugins connected to one pool: this will be possible thanks to the currently developing Proxy Plugin.
* **The main difference** is that we provide the ability to change Plugins connected to the pool. As the original idea is to eliminate fragmentation, we offer the option to change these Plugins on top since they may become outdated or simply unnecessary. This way, DEXs don’t need to migrate liquidity to new pools when making updates. Additionally, there is also the possibility to place not just one but multiple Plugins on top, following all the necessary security procedures.
