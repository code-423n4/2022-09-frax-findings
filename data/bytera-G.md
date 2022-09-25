### ERC20PermitPermissionedMint.sol
1. In the contract `ERC20PermitPermissionedMint`, line 84 loops through `minters_array` which is stored in storage. Unless the array will be less than 2 elements, it will be more gas efficient to store the length in memory and use that for the loop counter. Furthermore, using pre-increment instead of post-increment will save gas. One possible suggestion is to change line 84 to the following:
```
uint256 len = minters_array.length;
for (uint i = 0; i < len; ++i){
```
2. In the contract `ERC20PermitPermissionedMint`, line 77 checks if `minter_address` does not equal zero address. However, the only method of adding a minter address is with the function `addMinter()`, which already checks the added address is not zero address. Checking it in the `removeMinter()` function would be redundant and a waste of gas. Consider removing line 77 to reduce gas consumption.

### xERC4626.sol
1. In the contract `xERC4626`, the functions `beforeWithdraw()` and `afterDeposit()` calls the underlying function in the parent contract with the `super` keyword. However, since the underlying functions are not implemented, calling it with the `super` keyword would have no effect except costing more gas. Consider removing the lines 66 and 73 to reduce gas consumption.