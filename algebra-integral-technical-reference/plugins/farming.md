# Farming

## Overview

The farming is designed for positions created via AlgebraPositionManager. It will not work with ALM positions, which are added directly to the pool.

In each pool, you can have one farming instance. Rewards may be distributed in one or two tokens, with options to add or withdraw rewards as needed. Distribution occurs every second, allocated to all in-range positions according to their liquidity in the current tick. NFT of a position is not transferred on enter.

***

## Architecture

Farming consists of three contracts that work together:

| Contract                  | Role                                                                                                                                           |
| ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **FarmingCenter**         | Entry point for users. Verifies NFT ownership, routes calls to AlgebraEternalFarming, connects virtual pools to pool plugins.                  |
| **AlgebraEternalFarming** | Core farming logic. Stores incentives and per-position farm records, calculates and distributes rewards.                                       |
| **EternalVirtualPool**    | A "virtual" pool deployed for each incentive. Mirrors the tick and liquidity state of the real pool for the purpose of reward accounting only. |

The **FarmingPlugin** is a plugin connected to the Algebra pool. On every swap, it notifies the EternalVirtualPool about tick crossings, keeping the virtual pool in sync with the real pool.

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

***

## Reward Calculation

Rewards are distributed continuously, every second, proportional to each position's **in-range liquidity**.

The EternalVirtualPool maintains a global accumulator `totalRewardGrowth` that increases over time:

$$\Delta\text{totalRewardGrowth} = \frac{\text{rewardRate} \times \Delta t}{\text{currentLiquidity}}$$

where `currentLiquidity` is the total liquidity of all farming positions whose range currently covers the active tick.

Each farm (per-NFT entry) stores the value of the inner reward growth accumulator at the moment of entry. On exit or collect, the accrued reward is:

$$\text{reward} = (\text{innerGrowth}_\text{now} - \text{innerGrowth}_\text{entry}) \times \text{positionLiquidity}$$

This means:

* **Out-of-range positions earn nothing**: only positions whose tick range covers the current price accumulate rewards.
* **Larger liquidity, larger share**: reward is proportional to the position's contribution to `currentLiquidity`.
* Rewards stop accruing automatically when the `rewardReserve` in the virtual pool is exhausted.

***

## Minimal Position Width

The **Minimal range** parameter sets the minimum allowed width `(tickUpper - tickLower)` for a position to enter farming.

This is a protection against **phantom liquidity attacks**. Without a minimum width, an attacker with a highly concentrated in-range position could:

1. Monitor the mempool for large incoming swaps.
2. Remove liquidity just before the swap to avoid impermanent loss.
3. Re-add the same liquidity right after the swap.

The attacker's position spends most of its time "in farming" and collecting rewards, but disappears exactly when a real swap needs it. This is phantom liquidity: it shows up in the accounting but provides no real market-making function.

By requiring a minimum position width, the protocol makes this attack impractical.

{% hint style="info" %}
The default `TICK_SPACING` in Algebra is `60`, but it can be changed per pool. The position width is always a multiple of `TICK_SPACING`. To require at least 5 tick spacings with the default value, set Minimal range to `300` (5 x 60).
{% endhint %}

***

## Anti-JIT Farming Buffer

Since v1.2.3, `AlgebraEternalFarming` adds a second protection against phantom liquidity / JIT farming: rewards claimed too soon after a position's liquidity was added are forfeited instead of paid out.

### Vesting timestamp

Each farm stores `enteredTimestamp`, updated on every liquidity change:

* **First entry**: `enteredTimestamp` is set to the current block timestamp.
* **Increasing liquidity** (top-up): `enteredTimestamp` is recalculated as a liquidity-weighted blend of the old timestamp and the current time — a small top-up on a large position barely moves it, a large top-up on a small position pulls it close to "now".
* **Decreasing liquidity**: `enteredTimestamp` is left unchanged.

### Buffer

* `defaultFarmingBuffer` is the global buffer duration in seconds; `poolFarmingBuffer(pool)` is an optional per-pool override (`0` falls back to the default). Both are set via `setFarmingBuffer` and capped at 7 days.
* If a reward is collected, or a position exits, less than `buffer` seconds after its `enteredTimestamp`, that reward is forfeited: it's credited to a protocol-owned bucket (`rewards(address(0), token)`) instead of the position owner, and `RewardsForfeited` is emitted instead of `RewardsCollected`.
* Forfeited rewards are withdrawn via `withdrawForfeitedRewards`, which emits `ForfeitedRewardsWithdrawn`.

### Buffer exemption

Trusted addresses can be exempted from the buffer entirely via `setBufferExempt(owner, true)` — for example, ALM vaults, which must mint a new position on every rebalance and would otherwise look freshly-entered on every claim.

{% hint style="info" %}
Setting `defaultFarmingBuffer` to `0` disables forfeiture globally. A per-pool buffer of `0` means "use the default".
{% endhint %}

***

## Roles

Two privileged roles control farming administration:

| Role                          | Permissions                                                                                                                                                                                                                       |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `INCENTIVE_MAKER_ROLE`        | Create a new farming (`createEternalFarming`), manually deactivate it (`deactivateIncentive`), change reward rates (`setRates`), withdraw forfeited rewards (`withdrawForfeitedRewards`), set buffer exemptions (`setBufferExempt`). |
| `FARMINGS_ADMINISTRATOR_ROLE` | Withdraw excess rewards (`decreaseRewardsAmount`), activate emergency withdraw mode (`setEmergencyWithdrawStatus`), update the FarmingCenter address, set the anti-JIT farming buffer (`setFarmingBuffer`).                     |

Any address can **add** rewards to an existing active farming via `addRewards` (no special role required).

Roles are managed by the Algebra Factory (role-based access control inherited from the core).

***

## Deactivation and Emergency Withdraw

### Deactivation

An incentive can be deactivated in two ways:

**Manual**: `INCENTIVE_MAKER_ROLE` calls `deactivateIncentive`. This immediately sets rates to 0, marks the incentive as stopped, and disconnects the virtual pool from the plugin. Existing positions can still exit and claim their accumulated rewards.

**Automatic**: the EternalVirtualPool deactivates itself if it detects a desynchronization with the real pool (the real pool's current tick is on the wrong side of the virtual pool's nearest initialized tick). This can happen in edge cases after upgrades or unusual pool conditions. After automatic deactivation the farming behaves the same as after a manual one.

Once deactivated, no new positions can enter, and rates cannot be set to non-zero again. A new farming can be created for the same pool with a new nonce.

### Emergency Withdraw

`FARMINGS_ADMINISTRATOR_ROLE` can activate emergency withdraw mode via `setEmergencyWithdrawStatus(true)`.

When active:

* No new positions can enter farming.
* Positions can exit **without reward calculation**: rewards accumulated up to that point are forfeited.
* This is a last-resort tool intended for critical situations (e.g. a bug in the reward accounting logic). It should not be used in normal operations.

***

## Behavior on Liquidity Changes

When a user **adds or removes liquidity** from an NFT that is currently in farming, the NonfungiblePositionManager automatically notifies FarmingCenter via `applyLiquidityDelta`. FarmingCenter then:

{% stepper %}
{% step %}
**Exits** the position from farming, crediting all rewards accrued up to that moment.
{% endstep %}

{% step %}
**Re-enters** the position with the updated liquidity value (if the incentive is still active and the position still has liquidity > 0).
{% endstep %}
{% endstepper %}

This ensures the reward share is always proportional to the actual current liquidity of the position.

When an NFT is **burned** (liquidity reaches 0), the exit is triggered automatically and the position is removed from farming entirely.

{% hint style="info" %}
Because exit + re-enter happens atomically on every liquidity change, there is no need to manually exit farming before adjusting a position. The re-enter step is also where the vesting timestamp used by the [Anti-JIT Farming Buffer](#anti-jit-farming-buffer) gets recalculated.
{% endhint %}

***

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

***

## Create Eternal Farming

When you have all parameters specified, you need to:

{% stepper %}
{% step %}
### Approve Reward Token

Approve the reward token for transfer to the FarmingCenter contract.
{% endstep %}

{% step %}
### Approve Bonus Reward Token

Approve the bonus reward token (if used).
{% endstep %}

{% step %}
### Create Farming

Submit the transaction. The specified reward amount is sent to the FarmingCenter contract at this point.
{% endstep %}
{% endstepper %}

***

## Two-Step Reward Claiming

Claiming farming rewards is a two-step process:

{% stepper %}
{% step %}
### collectRewards

Called on `FarmingCenter`. Calculates the rewards accrued since the last collection and credits them to an internal balance. The NFT **stays in farming** and continues to earn rewards.
{% endstep %}

{% step %}
### claimReward

Called on `FarmingCenter`. Transfers the accumulated token balance to the specified address.
{% endstep %}
{% endstepper %}

This separation means a user can collect rewards as often as they like without exiting farming, and can direct the token transfer to any address.

{% hint style="warning" %}
`getRewardInfo` on AlgebraEternalFarming returns a snapshot that may be slightly outdated. For the exact current amount, use a static call to `collectRewards` on FarmingCenter.
{% endhint %}

***

## How to turn Eternal Farming off?

To turn Eternal Farming off you can set `Reward rate` and `Bonus Reward Rate` to 0.

***

## Detaching and Attaching

If you want to cancel your farming and create the new one, you can Detach it.

NFTs and rewards will remain on detached farming.

When farming is being detached, you need to create some way for participants to get their NFTs back, because now they don't see detached farming in UI.

If you accidentally pressed Detach button, don't worry, as there would be the Attach button.
