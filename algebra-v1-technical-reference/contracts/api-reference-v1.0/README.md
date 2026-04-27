# API Reference v1.0

Welcome to Algebra Protocol API v1.0 Reference!

### Main Contracts

#### AlgebraFactory.sol

Deploys pools and data storages, manages ownership and dynamic fee configuration

#### AlgebraPool.sol

Main contract of pair with concentrated liquidity and and dynamic fee. It contains the logic of swaps, liquidity providing, flash loans. Allows you to swap deflationary tokens and has protection from “Just-In-Time” liquidity providing

#### AlgebraPoolDeployer.sol

A contract that constructs a pool must implement this to pass arguments to the pool. This is used to avoid having constructor arguments in the pool contract, which results in the init code hash of the pool being constant allowing the CREATE2 address of the pool to be cheaply computed on-chain

#### DataStorageOperator.sol

This contract is used for interacting with DataStorage library

#### AdaptiveFee.sol

Calculates fee based on combination of sigmoids

#### DataStorage.sol

DataStorage provides price, liquidity, volatility data useful for a wide variety of system designs. Mainly used to calculate dynamic fee. Instances of stored dataStorage data, "timepoints", are collected in the dataStorage array Timepoints are overwritten when the full length of the dataStorage array is populated. The most recent timepoint is available by passing 0 to getSingleTimepoint()

#### PriceMovementMath.sol

Library that used for computing the result of a swap

#### TickManager.sol

Library for managing tick processes and relevant calculations

#### TickTable.sol

Packed tick initialized state library. Stores a packed mapping of tick index to its initialized state. The mapping uses int16 for keys since ticks are represented as int24 and there are 256 (2^8) values per word

#### TokenDeltaMath.sol

TokenDeltaMath contains the math that uses square root of price as a Q64.96 and liquidity to compute deltas
