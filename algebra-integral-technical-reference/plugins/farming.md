# Farming

## Overview

The farming is designed for positions created via AlgebraPositionManager. It will not work with ALM positions, which are added directly to the pool.

In each pool, you can have one farming instance. Rewards may be distributed in one or two tokens, with options to add or withdraw rewards as needed. Distribution occurs every second, allocated to all in-range positions according to their liquidity in the current tick. NFT of a position is not transferred on enter.

## Managing Farming in admin panel

For creation of Eternal Farming you need to specify these parameters:

* Pair
* Reward token
* Reward amount
* Bonus reward token
* Bonus reward amount
* Reward rate
* Bonus reward rate
* Minimal tick range

Lets review every parameter in more details

### Pair

Token pair for which Eternal Farming will be created. To participate in farming, users should provide liqudity to this pool.

### Reward token

This token will be used as an incentive to participate in farming.

### Reward amount

Total amount of rewards to distribute between participants. Without decimals.

### Bonus reward token

One more token as an incentive to participate in farming. Bonus reward is optional. If you don't want to use additional token, you need to select the same token as in `Reward token` field.

### Bonus reward amount

Total amount of rewards to distribute between participants. Without decimals. If you don't want to use additional token, you need to enter 0 as `Bonus reward amount`.

### Reward rate

Reward rate to be distributed **per second**. Without decimals.

### Bonus reward rate

Bonus reward rate to be distributed **per second**. Without decimals.

### Minimal range

Minimal range for position to being able to participate in farming. Algebra Protocol `TICK_SPACING` is `60`. The position cannot be narrower than 60, and its range is always in multiples of 60. If you want to limit range to at least 5 ticks, you should set this parameter to 300 (5 \* 60).

## Create Eternal Farming

When you have all parameters specified, you need to:

1. Approve Reward Token
2. Approve Bonus Reward Token
3. Create Farming

When you create farming, specified amount of token as rewards is being send to FarmingCenter contract.

## How to turn Eternal Farming off?

To turn Eternal Farming off you can set `Reward rate` and `Bonus Reward Rate` to 0.

## Detaching and Attaching

If you want to cancel your farming and create the new one, you can Detach it.

NFTs and rewards will remain on detached farming.

When farming is being detached, you need to create some way for participants to get their NFTs back, because now they don't see detached farming in UI.

If you accidentally pressed Detach button, don’t worry, as there would be the Attach button.
