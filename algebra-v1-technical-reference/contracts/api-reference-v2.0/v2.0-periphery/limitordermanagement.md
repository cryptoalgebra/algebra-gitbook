# LimitOrderManagement

Limit orders management functions

Internal functions for safely managing limit orders in Algebra V2

## Functions

### algebraMintCallback

`function algebraMintCallback(uint256 amount0Owed, uint256 amount1Owed, bytes data) external` external

Called to \`msg.sender\` after minting liquidity to a position from IAlgebraPool#mint. _Developer note: In the implementation you must pay the pool tokens owed for the minted liquidity. The caller of this method must be checked to be a AlgebraPool deployed by the canonical AlgebraFactory._

| Name        | Type    | Description                                                                 |
| ----------- | ------- | --------------------------------------------------------------------------- |
| amount0Owed | uint256 | The amount of token0 due to the pool for the minted liquidity               |
| amount1Owed | uint256 | The amount of token1 due to the pool for the minted liquidity               |
| data        | bytes   | Any data passed through by the caller via the IAlgebraPoolActions#mint call |
