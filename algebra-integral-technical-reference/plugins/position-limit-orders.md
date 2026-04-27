# Position Limit Orders

## Overview

The Limit Orders Plugin is a sophisticated tool designed to facilitate advanced trading strategies by allowing users to place orders at specified price levels. With this plugin, traders can automate their buy and sell orders based on predetermined price thresholds, maximizing profit potential and minimizing risks.

The plugin consists of two contracts: **LimitOrderManager** and **LimitOrderPlugin**. **LimitOrderManager** contains the main functionality and is the contract the user interacts with. **LimitOrderPlugin** is the “proxy” contract through which the pool communicates with **LimitOrderManager**. **LimitOrderManager** is deployed once and used for all pools, while **LimitOrderPlugin** is deployed and set up for each pool separately.

In the **LimitOrderPlugin** architecture, a limit order is a one-sided position. When a user places a limit order, a one-sided position is created, the size of which is equal to the pool's tick spacing. The fact that a limit order is a position implies some restrictions on its placement:&#x20;

* A limit order can be placed for a limited set of prices, namely with a step equal to the pool's tick spacing. For example, in most pools the tick spacing is 60, which means that the price step will be 0.6%.
* A limit order can only be placed above the token's price in a selected pool.
* Limit order cannot be placed in the range (equal to the tick spacing of the pool) where the price is located
* All limit orders placed within the same range are considered one position.

To add a limit order, a user needs to approve tokens for the **LimitOrderManager** contract and call the place method, passing in the following: the pool where the position will be placed, the lower tick of the position, and the amount of liquidity. When this method is called, a mint will occur, and one-sided liquidity will be added to the pool.

Limit orders are closed when the price moves within the pool, specifically when the price completely passes or crosses through the position. **AfterSwapHook** is used to notify the manager about price change. After each swap, the **LimitOrderManager** is called, which provides information about the current pool tick. The **LimitOrderManager** stores the previous pool tick, allowing it to transition from prevTick to currentTick in increments defined by the tick spacing. If there were limit orders within this interval, the corresponding positions are burned. Upon burning, the tokens are transferred to the **LimitOrderManager** contract.

<figure><img src="../../.gitbook/assets/limit-order.svg" alt=""><figcaption></figcaption></figure>

After the limit order is executed, the user needs to call the **withdraw** method to claim their tokens. Since the limit order is a position, all fees collected by the limit order position are claimed by the user upon closing.

The user can also cancel their limit order at any time by calling the **kill** method. However, if the pool price is within the limit order position at the time of cancellation, the user will receive their deposit in two tokens, and the limit order will be partially executed.

## How to configure Limit Order Plugin

**LimitOrderManager** needs to have information about the tickspacing of the pool. This data is used when creating a position and searching for crossed limit order positions. However, since Algebra pools do not have a handler for tick spacing changes, the DEX owner must manually set the pool tick spacing in the manager's contract. The owner can also increase the minimum length of the limit order range to mitigate the possibility of swap DDoS attacks. To change the tick spacing, you need to call:

**function setTickSpacing(pool, tickSpacing)**

Changing the tick spacing of the **LimitOrderManager** is necessary if the tick spacing of the pool has increased or the prev tick spacing is not divisible evenly by the new pool tick spacing.&#x20;

After changing the tick spacing, all previously placed limit orders will not be taken into account by the plugin, so these positions will not be burned. Therefore, users will have to call kill method and place the limit order again.

## How to deploy Limit Order Plugin

The Limit Order plugin can be deployed in two ways:

1.  **As a Plugin for Base Pools**

    The plugin can be used in base pools (pools with a zero-address deployer).
2.  **As a Plugin for Custom Pools**

    The plugin paired with a custom pool alongside a base pool. Adding manual positions is not mandatory in the custom pool, as arbitrage bots will align the price with the market, enabling limit order execution. However, it is highly recommended to add a small amount of liquidity to the full range position when creating a custom pool to avoid tick movement to min/max bound after limit order execution.

