# DataStorageOperator

Algebra timepoints data operator

This contract stores timepoints and calculates adaptive fee and statistical averages

## Modifiers

### onlyPool

`modifier onlyPool()` internal

## Variables

### struct DataStorage.Timepoint\[65536] timepoints

Returns data belonging to a certain timepoint

_Developer note: There is more convenient function to fetch a timepoint: getTimepoints(). Which requires not an index but seconds_

### struct AlgebraFeeConfiguration feeConfigZtO

### struct AlgebraFeeConfiguration feeConfigOtZ

### bytes32 FEE\_CONFIG\_MANAGER constant

_Developer note: The role can be granted in AlgebraFactory_

## Functions

### constructor

`constructor(address _pool) public` public

| Name   | Type    | Description |
| ------ | ------- | ----------- |
| \_pool | address |             |

### initialize

`function initialize(uint32 time, int24 tick) external` external

Initialize the dataStorage array by writing the first slot. Called once for the lifecycle of the timepoints array

| Name | Type   | Description                                                                         |
| ---- | ------ | ----------------------------------------------------------------------------------- |
| time | uint32 | The time of the dataStorage initialization, via block.timestamp truncated to uint32 |
| tick | int24  | Initial tick                                                                        |

### changeFeeConfiguration

`function changeFeeConfiguration(bool zto, struct AlgebraFeeConfiguration _config) external` external

Changes fee configuration for the pool

| Name     | Type                           | Description |
| -------- | ------------------------------ | ----------- |
| zto      | bool                           |             |
| \_config | struct AlgebraFeeConfiguration |             |

### getSingleTimepoint

`function getSingleTimepoint(uint32 time, uint32 secondsAgo, int24 tick, uint16 lastIndex) external view returns (int56 tickCumulative, uint112 volatilityCumulative)` view external

_Developer note: Reverts if a timepoint at or before the desired timepoint timestamp does not exist. 0 may be passed as \`secondsAgo' to return the current cumulative values. If called with a timestamp falling between two timepoints, returns the counterfactual accumulator values at exactly the timestamp between the two timepoints._

| Name       | Type   | Description                                                                       |
| ---------- | ------ | --------------------------------------------------------------------------------- |
| time       | uint32 | The current block timestamp                                                       |
| secondsAgo | uint32 | The amount of time to look back, in seconds, at which point to return a timepoint |
| tick       | int24  | The current tick                                                                  |
| lastIndex  | uint16 |                                                                                   |

**Returns:**

| Name                 | Type    | Description                                                                                |
| -------------------- | ------- | ------------------------------------------------------------------------------------------ |
| tickCumulative       | int56   | The cumulative tick since the pool was first initialized, as of \`secondsAgo\`             |
| volatilityCumulative | uint112 | The cumulative volatility value since the pool was first initialized, as of \`secondsAgo\` |

### getTimepoints

`function getTimepoints(uint32[] secondsAgos) external view returns (int56[] tickCumulatives, uint112[] volatilityCumulatives)` view external

Returns the accumulator values as of each time seconds ago from the given time in the array of \`secondsAgos\` _Developer note: Reverts if \`secondsAgos\` > oldest timepoint_

| Name        | Type      | Description                                                                        |
| ----------- | --------- | ---------------------------------------------------------------------------------- |
| secondsAgos | uint32\[] | Each amount of time to look back, in seconds, at which point to return a timepoint |

**Returns:**

| Name                  | Type       | Description                                                                                      |
| --------------------- | ---------- | ------------------------------------------------------------------------------------------------ |
| tickCumulatives       | int56\[]   | The cumulative tick since the pool was first initialized, as of each \`secondsAgo\`              |
| volatilityCumulatives | uint112\[] | The cumulative volatility values since the pool was first initialized, as of each \`secondsAgo\` |

### write

`function write(uint16 index, uint32 blockTimestamp, int24 tick) external returns (uint16 indexUpdated, uint16 newFeeZtO, uint16 newFeeOtZ)` external

Writes a dataStorage timepoint to the array _Developer note: Writable at most once per block. Index represents the most recently written element. index must be tracked externally._

| Name           | Type   | Description                                                                       |
| -------------- | ------ | --------------------------------------------------------------------------------- |
| index          | uint16 | The index of the timepoint that was most recently written to the timepoints array |
| blockTimestamp | uint32 | The timestamp of the new timepoint                                                |
| tick           | int24  | The active tick at the time of the new timepoint                                  |

**Returns:**

| Name         | Type   | Description                                                                 |
| ------------ | ------ | --------------------------------------------------------------------------- |
| indexUpdated | uint16 | The new index of the most recently written element in the dataStorage array |
| newFeeZtO    | uint16 | The fee for ZtO swaps in hundredths of a bip, i.e. 1e-6                     |
| newFeeOtZ    | uint16 | The fee for OtZ swaps in hundredths of a bip, i.e. 1e-6                     |

### prepayTimepointsStorageSlots

`function prepayTimepointsStorageSlots(uint16 startIndex, uint16 amount) external` external

Fills uninitialized timepoints with nonzero value _Developer note: Can be used to reduce the gas cost of future swaps_

| Name       | Type   | Description                                                       |
| ---------- | ------ | ----------------------------------------------------------------- |
| startIndex | uint16 | The start index, must be not initialized                          |
| amount     | uint16 | of slots to fill, startIndex + amount must be <= type(uint16).max |
