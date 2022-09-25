## Gas Optimization
22/09/2022 16:07
[Frax](https://github.com/code-423n4/2022-09-frax)
* * *

**ERC20PermitPermissionedMint.sol**

**`i++` uses more gas than `++i`**

Line: [84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

**Cache storage variable `minters_array.length` to save gas**

Line: [84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

**frxETHMinter.sol**

**`withheld_atm` & `i` initialized to default value 0**

Lines: [94](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94)

[Reference](https://github.com/devanshbatham/Solidity-Gas-Optimization-Tips#3--there-is-no-need-to-initialize-variables-with-default-values)