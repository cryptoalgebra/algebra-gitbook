# API Reference v1.9

Welcome to Algebra Protocol API v1.9 Reference!

## Algebra V1.9 API changelog

This document briefly describes major changes to Algebra V1.9 contracts that may be important for projects integrating the protocol.

[Algebra V1.9 branch](https://github.com/cryptoalgebra/Algebra/tree/v1-modified)

### AlgebraPool changes

#### Customizable Tickpacing

In the first version of the protocol, tickspacing was an immutable constant within pools. In version 2, tickspacing can be changed at any time. This allows the protocol to more accurately and efficiently set up pools for different kinds of tokens. The default tickspacing is 60. Contracts interacting with Algebra V2 pools must take into account the possibility that tickspacing will change. MAX\_LIQUIDITY\_PER\_TICK is fixed based on tickspacing value 1.

#### Default Community Fee

You can also now configure what value of `communityFee` will be set in new pools by default. This makes it easier to set up new pools.

### Farming changes

#### Decrease Rewards

In version 1.9 the owner have the ability to withdraw some of the rewards from farms.
