---
icon: up-from-line
---

# Changes v1.2.1

Integral v1.2.1 code can be found in the [integral-v1.2.1](https://github.com/cryptoalgebra/Algebra/tree/integral-v1.2.1) branch

### Minor changes

* The communityFee is now collected not only from the fee, but also from the pluginFee.&#x20;
* In order for the plugin to be able to override the fees, the DYNAMIC\_FEE flag needs to be set in the pool.
* The plugin can no longer change fee in the pool via setFee() function.&#x20;

