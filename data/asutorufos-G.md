G-1 Public function visibility can be made external
If a function is never called from the contract it should be marked as external. This will save gas.

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L171
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L197

G-2 use of custom errors rather than revert() / require() error message
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L46
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L137
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L182
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L203
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L87
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L88

G-3 use != rather than >0 for unsigned integers in require() statements
When the optimizer is enabled, gas is wasted by doing a greater-than operation, rather than a not-equals operation inside require() statements. When Using != , the optimizer is able to avoid the EQ, ISZERO, and associated operations, by relying on the JUMPI that comes afterwards, which itself checks for zero.
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126

G-4 In for loops is not needed to initialize indexes to 0 as it is the default uint/int value. This saves gas.

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

G-5 .length should no be looked up in every loop of a for-loop
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
