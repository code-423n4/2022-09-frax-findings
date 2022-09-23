1.	Use `>` instead of `!=0` to save gas.
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L88
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L95
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L137
2.	Use ++i instead of i++ for loops. 
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
3.	Cache `original_validators.length` to variable to save gas.
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114
