---
icon: up-from-line
---

# Changes v1.2.2

Integral v1.2.2 code can be found in the [integral-v1.2.2 ](https://github.com/cryptoalgebra/Algebra/tree/integral-v1.2.2)branch

In version 1.2, with the addition of plugin and override fee functionality, the Swap and Burn pool events were modified, which caused integration issues with some trackers.

Minor changes:

* The Swap event is split into a swap event and swapFee event that contains overrideFee and pluginFee
* The Burn event is split into a burn event and burnFee event that contains overrideFee
