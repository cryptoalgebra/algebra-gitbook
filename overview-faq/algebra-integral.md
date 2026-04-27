# 🧑‍🔬 Algebra Integral

### Introduction to Integral’s Modular Architecture

<figure><img src="https://lh7-us.googleusercontent.com/6loJqu9uAcIUR8blz1Tzhrr8GFeGV_FMQDV3PjaaVUFe4Hte1PTMfAiksQTHO55Ntc9UqEU12X7aGvqsjw2jz730BnGlEdQjWS5KYdDusBfZYzBchH0ef1B69iCkv4AZoQy1t-pS03SYc3KGzQ5vGy8" alt=""><figcaption></figcaption></figure>

Until now, each DEX had its source code in the form of a single immutable monolith, and updating such architecture required liquidity migration and associated additional expenses. Existing technology inside DEXes can quickly become outdated, failing to keep pace with DeFi trends, resulting in low liquidity effectiveness.

With the advent of Integral, it became possible to separate the most vulnerable part (liquidity storage) and peripheral functionality modules (e.g. oracles, fee calculation) from each other. Critical functionality of liquidity storage and swap calculations is kept at the unchangeable Core, while peripheral functionality of Plugins can be updated when needed without liquidity migration. This approach allows DEXes to develop faster.&#x20;

### Plugins

Plugin is a smart contract that connects to a Pool’s contract and expands its functionality. Currently, only one Plugin can be initialized per each Pool. A Plugin can be disconnected or replaced by another plugin. (However, the architecture allows for connecting several plugins to one Pool through a Proxy Plugin, which can be developed in the future).

What a Plugin can do: do arbitrary actions on the blockchain (including calling other contracts), change the fee in the pool, do additional checks, etc.

What a Plugin cannot do: take someone’s liquidity or tokens (directly), change the logic (mathematics) of calculations.

#### Interaction between Liquidity Pools and Plugins.

Plugins interact with the Pools via Hooks.

Hook is a function connecting the Plugin and the Pool, which is triggered before or after a certain key event, allowing the Plugin and the Pool to exchange information. The set of possible Hooks corresponds to these events (which are: initialization of a Pool, liquidity mint or burn, swap, flash loan):

* beforeInitialize – a call before initialization of a Pool
* afterInitialize – a call after initialization of a Pool
* beforeModifyPosition – a call before changing any liquidity position. The call can occur before a mint or liquidity burn.
* afterModifyPosition –  a call after changing any liquidity position. The call can occur after a mint or liquidity burn.
* beforeSwap – a call before swap
* afterSwap – a call after swap
* beforeFlash – a call before flash loan
* afterFlash – a call after flash loan

#### Existing Plugins

Currently the Standard Plugin is a default Plugin, available right upon deploying ‘Integral’. It contains several Modules:&#x20;

* TWAP Oracle

In the current implementation, Plugins can act as Oracles, providing access to historical price and volatility data.

* Adaptive Fee

The main advantage of Algebra over other DEXes is the dynamic fee. In the Integral version, the adaptive fee functionality was moved to a Plugin. Having information from TWAP-oracle about price changes, the Plugin calculates the fee based on the volatility of the pair.&#x20;

* Farming&#x20;

In the current implementation of Farming there is a Virtual Pool containing information about the liquidity and state of the original Pool. For the Farming Module to work correctly, continuous synchronization between the Virtual and regular Pools is required. Therefore, the Farming Proxy Module was introduced.

#### Future possibilities

Integral's innovative approach to DEX architecture and its extensible plugin system pave the way for future developments, including the introduction of the following features:

* Dynamic & elastic fees&#x20;
* A whitelist / blacklist / KYC system
* Gas discount, or compensation mechanisms
* Solutions for JIT liquidity issues
* LVR (Loss-Versus-Rebalancing) / Impermanent Loss reduction
* Perpetual Contracts

### Architectural benefits and gas efficiency

* The architecture eliminates the need to migrate liquidity during updates
* Integral allows external developers and DEXes to bring innovation into life
*   Integral’s optimized core saves 7-22% on gas costs on average compared to Uniswap V3:<br>

    <figure><img src="https://lh7-us.googleusercontent.com/qg4llRQLxrl9U1USOOqr_DPKqMwM-oloS1ZFo40xGuuuGgSx382aTB7XquFZDaos2_NJCZZeKFtMovVX4YOD3HLHLkRguRdGORkNROPGBBkrrMLvkdpi9o8FOSUF8-c0aDmQWRY64cVuBST-YSmQEoE" alt=""><figcaption></figcaption></figure>
