# Whitelist Discount Fee

## Overview

To attract big market makers (actors who make a big volume of swaps) DEXes might offer them better swap costs at the expense of swap fees. Using the dedicated plugin we can configure Algebra pool(s) with a registry of preferred (‘whitelisted’) addresses that can make swaps with reduced fees.  This approach allows a trade-off between lower gains for LP providers (and protocol as well) from the single swap in favor of boosting the total swap volume.

## Details

We can base our setup on the concept of ‘Whitelist Registry’ that is essentially a smart contract maintaining the ledger of preferred addresses and their respective discounts for each pool:

<figure><img src="../../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

Discount defined above is applied to the swap fee before any redistribution happens. First the discount is applied and then the remaining fee is assigned to the LPs and protocol. This means that all parties lose their fee part in equal measure.&#x20;

Discount can be set up to 100% in which case the fee is essentially forfeited. Setting a discount value to 0% means removing the address from the whitelist. **The tx.origin parameter** is used to identify the user and calculate the discount. In our plugin implementation, the discount is applied to the dynamic fee.

## How to configure discount fee plugin

The owner or address having the role “FEE\_DISCOUNT\_MANAGER” is able to set the discount value in thousandths (1000 = 100%)

To set fee discount you need to call method of registry contract:

**setFeeDiscount(address user, address\[] memory pools, uint16\[] memory newDiscounts)**

To get the current fee discount for user and pool, you need to call:

**feeDiscounts(address user, address pool) returns (uint16 feeDiscount);**

If the fee discount registry contract is not sufficient to cover some use cases. Owner can develop and replace it with its own version of Fee Discount Registry. Fee Discount Registry should implement the  **feeDiscounts(address user, address pool) returns (uint16 feeDiscount)** method, which returns the discount value for the given user and pool.
