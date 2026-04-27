# POOL\_INIT\_CODE\_HASH

````
const POOL_INIT_CODE_HASH = ```0x6ec6c9c8091d160c0aa74b2b14ba9c1717e95093bd3ac085cee99a49aab294a4```;
````

`POOL_INIT_CODE_HASH` is the hash of AlgebraPool.sol bytecode contract.

You should not worry about managing it until you change something in AlgebraPool.sol contract. If you do, you will need to take keccak256 hash from its bytecode and change `POOL_INIT_CODE_HASH` everywhere.
