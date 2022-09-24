## `++i` COSTS LESS GAS COMPARED TO `i++` OR `i += 1`

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments `i` and returns the initial value of `i`. Which means:

`uint i = 1;
i++; // == 1 but i == 2  `
But `++i` returns the actual incremented value:

`uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable `
In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`

Instance include:

[`src/ERC20/ERC20PermitPermissionedMint.sol:84:`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)
I suggest using ++i instead of i++ to increment the value of a uint variable.

___
## Cache array length in for loops can save gas
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.

Instances include:

[`src/ERC20/ERC20PermitPermissionedMint.sol:84:`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)
[`src/OperatorRegistry.sol:114:`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114)
___
## In `require()`, Use `!= 0` Instead of `> 0` With Uint Values

In a require, when checking a uint, using `!= 0` instead of `> 0` saves 6 gas. This will jump over or avoid an extra `ISZERO` opcode.

Instances include:
[`src/frxETHMinter.sol:79`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79)
[`src/frxETHMinter.sol:126`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126)