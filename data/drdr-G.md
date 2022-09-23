In `ERC20PermitPermissionedMint` contract consider using mapping from `adress` to `uint` instead of `bool` (https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L20). Its value would be the index in `minters_array` where the minter stays. Value 0 means: not a minter.

It would be set in `addMinter` function as the length of `minters_array`, after the minter is pushed. 

Thanks to that, you do not have to iterate over an array (https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84) but simply add:
```
minters_array[minters[minter_address]] = address(0)
```
Remember to delete the minter from mapping (https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L81) after this update.