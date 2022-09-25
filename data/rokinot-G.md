### Calldata length should not be cached

Calldata access has the same cost as memory access already, so caching calldata -> memory is a waste of gas.

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L62

### No need to compare boolean variables as true

Instead of `require(minters[minter_address] == true, "Address nonexistant");` simply write `require(minters[minter_address], "Address nonexistant");`

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L46