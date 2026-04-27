# Automated Liquidity Management (A-ALM)

## Overview

Algebra’s Pools have its dedicated plugins, individual smart contracts.&#x20;

Algebra ALM rebalancing is performed via such a plugin (ALM Plugin) through “afterSwap” hook execution.&#x20;

ALM Plugin works jointly with Volatility Oracle Plugin and interacts with Rebalance Manager. The last one performs main calculations, checks requirements and decides how and whether or not to do a rebalancing.&#x20;

Rebalancing is performed by AlgebraVault, which holds LP NFT and pending deposit assets.

<figure><img src="../../.gitbook/assets/Снимок экрана 2025-12-24 в 17.32.52 (1).png" alt=""><figcaption></figcaption></figure>

### ALM Plugin

This is a simple contract which passes data from Volatility Oracle to Rebalance Manager.&#x20;

The essential data is current tick, slow and fast TWAPs. Depending on some checks it might not call Rebalance Manager:

* if there is no Rebalance Manager set yet
* if there is not enough time passed since the Pool initialization. So it cannot calculate slow TWAP

Configuration:

* slow TWAP period (e.g. 3600 seconds)
* fast TWAP period (e.g. 300 seconds)
* Rebalance Manager address

### Rebalance Manager

Rebalance Manager is the core smart contract of the rebalancing process.&#x20;

It decides whether to do a rebalance or not, on which ranges to add a position, or pause the rebalancing process.

**Configuration:**

* Algebra Vault address
* minimal time between rebalances
* how much of pending deposits in Deposit Token should be on Algebra Vault to rebalance
* minimal percentage of Deposit Token to retain Over Inventory state (e.g. 93%)
* maximum percentage of Deposit Token to retain Normal state (e.g. 91% or 93% as well)
* minimal percentage of Deposit Token to retain Normal state (e.g. 80%)
* maximum percentage of Deposit Token to retain Under Inventory state (e.g. 77%)
* required price change percentage to rebalance while retaining the same state
* extreme volatility percentage
* high volatility percentage
* some volatility percentage
* width of the actual liquidity position owned by Algebra Vault

### Algebra Vault

This contract is responsible for the execution of the rebalancing process. It burns/mints liquidity positions, accepts deposits, allows withdrawals and distributes fees.

Algebra Vaults are deployed via Algebra Vault Factory and deposits are made via Algebra Vault Deposit Guard. At the same time withdrawals are made directly from an Algebra Vault.

<figure><img src="../../.gitbook/assets/Снимок экрана 2025-12-24 в 17.33.08.png" alt=""><figcaption></figcaption></figure>

Fees are deducted from the fees earned by Algebra Vault's liquidity position (from LP fees).

There are two fees being deducted by the ALM: ammFee and baseFee. The sum of ammFee and baseFee is always less than 100%. ammFee goes to ammFeeRecipient.

baseFee might be split between feeRecipient and affiliate. Split proportion is controlled by baseFeeSplit parameter.

<figure><img src="../../.gitbook/assets/Снимок экрана 2025-12-24 в 17.33.18.png" alt=""><figcaption></figcaption></figure>

**Configuration:**

* ﻿﻿on each Algebra Vault:
* ﻿ammFeeRecipient address
* ﻿﻿affiliate address\
  Rebalance Manager address maximum amount of deposit token\
  maximum price change percentage allowed to pass the price manipulation\
  check (e.g. 0.5%)
* ﻿﻿periods of 2 TWAPs used in the price manipulation check (default is 60 and 15 minutes)

on Algebra Vault Factory:

* feeRecipient address (for the part of baseFee)
* ammFee percentage (eg. 0%)
* baseFee percentage (e.g. 20%)
* baseFee Split percentage (e.g. 50%. If affiliate is not set on a Algebra Vault baseFeeSplit is ignored)

### Gas Costs

As was mentioned earlier, the rebalancing process is performed through the "afterSwap" hook. It means that the one who swaps paying the gas fees. When the rebalance is indeed executed (not skipped due to not passing checks) a swap will cost additional 1M gas.

This extra amount of gas might seem significant but practice shows that there is always an arbitrageur whose profits are covering the costs of such swap which includes rebalancing.

When rebalance is not triggered the plugin burns around 100K gas on every swap.
