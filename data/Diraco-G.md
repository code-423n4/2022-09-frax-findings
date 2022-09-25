For-Loop :
1 - An array’s length should be cached to save gas in for-loops
Reading array length at each iteration of the loop takes 6 gas
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
2 - Increments can be unchecked and ++i costs less gas compared to i++

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129
