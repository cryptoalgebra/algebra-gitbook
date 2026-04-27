# Create Eternal Farming

## Classic parameters

For creation of Eternal Farming you need to specify these parameters:

* Pair
* Reward token
* Reward amount
* Bonus reward token
* Bonus reward amount
* Reward rate
* Bonus reward rate

Plus

* Tier farming
* Minimal tick range

Lets review every parameter in more details

### Pair

Token pair for which Eternal Farming will be created. To participate in farming, users should provide liqudity to this tokens pool.

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

Minimal range for position to being able to participate in farming. Algebra Protocol `TICK_SPACING` is `60`. It means, that position couldn't be narrower, than 60. And its range is always 60 multiples. If you want to limit range to at least 5 ticks, you should set this parameter to 300 (5 \* 60).

### Tier farming

Toggler for switching between Tier and classic farming.

## Tier Farming parameters

Tier Farming is special type of farming, where you can specify token that users can lock and get pre-defined multiplier to their rewards.

Tier Farming has its own additional parameters:

### Locked token

Token to lock

### Tier limits (in tokens)

Amount of tokens for participants to lock

1. Low tier limit
2. Mid tier limit
3. High tier limit

### Tier multipliers (in %)

Multiplier for participants rewards based on selected tier

1. Low tier multiplier
2. Mid tier multiplier
3. High tier multiplier

## Create Eternal Farming

When you have all parameters specified, you need to:

1. Approve Reward Token
2. Approve Bonus Reward Token
3. Create Farming

When you create farming, specified amount of token as rewards is being send to FarmingCenter contract.
