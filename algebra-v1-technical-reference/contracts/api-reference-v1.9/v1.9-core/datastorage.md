# DataStorage

DataStorage

Provides price, liquidity, volatility data useful for a wide variety of system designs

_Developer note: Instances of stored dataStorage data, "timepoints", are collected in the dataStorage array Timepoints are overwritten when the full length of the dataStorage array is populated. The most recent timepoint is available by passing 0 to getSingleTimepoint()_

## Variables

### uint32 WINDOW constant
