1 - Pre-incrementing consumes lower gas than post-incrementing. (++i rather than i++):

https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L76
https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L83
https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L148


2 - It's better to cache length in "for" loops.

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

3 - The default value for variable uint or uint256 is "0". Reassigning it will cost gas.

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129
https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L148
https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L83
https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L148


