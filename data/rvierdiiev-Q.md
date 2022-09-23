1.	Check `address` param for `0` address.
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L60-L62
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L41
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L34
2.	Do not initialize variables with default values
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L63-L64
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94
3.	Change message here to `Validator stack is not empty`
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L137
4.	Use `require(!minters[minter_address], "Address already exists")` instead.
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L68
5.	Use `require(minters[minter_address], "Address nonexistant")` instead
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78
