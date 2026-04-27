# Specification and API of contracts

A conceptual, higher-level description of the Core part logic can be found in [the corresponding section of this documentation](../core-logic/).

This subsection contains a condensed API specification of the most important protocol contracts. A more detailed API specification, including all contracts, is located in the protocol repository: [the Algebra Integral protocol repository](https://github.com/cryptoalgebra/Algebra/tree/integral-v1.2.2/docs). This specification is generated automatically using natspec annotations of functions and state variables of smart contracts.

* [AlgebraPool](https://github.com/cryptoalgebra/Algebra/blob/integral-v1.2.2/docs/Contracts/Core/AlgebraPool.md) - liquidity pool of Algebra Integral
* [AlgebraFactory](https://github.com/cryptoalgebra/Algebra/blob/integral-v1.2.2/docs/Contracts/Core/AlgebraFactory.md) - factory of liquidity pools (and access manager)
* [SwapRouter](https://github.com/cryptoalgebra/Algebra/blob/integral-v1.2.2/docs/Contracts/Periphery/SwapRouter.md) - default peripheral router for swaps
* [NonfungiblePositionManager](https://github.com/cryptoalgebra/Algebra/blob/integral-v1.2.2/docs/Contracts/Periphery/NonfungiblePositionManager.md) - default position management contract (with NFT)
* [Quoter](https://github.com/cryptoalgebra/Algebra/blob/integral-v1.2.2/docs/Contracts/Periphery/lens/Quoter.md) - allows getting the expected amount out or amount in for a given swap without executing the swap (also off-chain)
* [QuoterV2](https://github.com/cryptoalgebra/Algebra/blob/integral-v1.2.2/docs/Contracts/Periphery/lens/QuoterV2.md) - version of Quoter returning more data
* [TickLens](https://github.com/cryptoalgebra/Algebra/blob/integral-v1.2.2/docs/Contracts/Periphery/lens/TickLens.md) - a contract that allows conveniently fetch information about ticks in the pool

Information about custom errors that the pool can throw:

[IAlgebraPoolErrors](https://github.com/cryptoalgebra/Algebra/blob/integral-v1.2.2/docs/Contracts/Core/interfaces/pool/IAlgebraPoolErrors.md)

The information above references documentation for **version 1.2.2** of Algebra Integral. If you're working with a different protocol version, please refer to the documentation in the corresponding branch for your version.

* [Integral v1.0](https://github.com/cryptoalgebra/Algebra/tree/integral-v1.0/docs/Contracts)
* [Integral v1.1](https://github.com/cryptoalgebra/Algebra/tree/integral-v1.1/docs/Contracts)
* [Integral v1.2](https://github.com/cryptoalgebra/Algebra/tree/integral-v1.2/docs/Contracts)
