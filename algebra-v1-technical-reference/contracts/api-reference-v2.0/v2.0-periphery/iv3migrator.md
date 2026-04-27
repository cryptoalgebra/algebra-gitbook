# IV3Migrator

Algebra Migrator

Enables migration of liquidity from Uniswap v2-compatible pairs into Algebra pools

_Developer note: Credit to Uniswap Labs under GPL-2.0-or-later license: https://github.com/Uniswap/v3-periphery_

## Functions

### migrate

`function migrate(struct IV3Migrator.MigrateParams params) external` external

Migrates liquidity to Algebra by burning v2 liquidity and minting a new position for Algebra _Developer note: Slippage protection is enforced via \`amount{0,1}Min\`, which should be a discount of the expected values of the maximum amount of Algebra liquidity that the v2 liquidity can get. For the special case of migrating to an out-of-range position, \`amount{0,1}Min\` may be set to 0, enforcing that the position remains out of range_

| Name   | Type                             | Description                                                                            |
| ------ | -------------------------------- | -------------------------------------------------------------------------------------- |
| params | struct IV3Migrator.MigrateParams | The params necessary to migrate v2 liquidity, encoded as \`MigrateParams\` in calldata |
