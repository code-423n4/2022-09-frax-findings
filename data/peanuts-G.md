## TABLE OF CONTENTS

- [G-01] ++I costs less gas as compared to I++ or I+= 1
- [G-02] Increments can be unchecked
- [G-03] Store array length in for-loops
- [G-04] Duplicated require() / revert() checks should be refactored to a modifier or function
- [G-05] Use private rather than public for constants, saves gas
- [G-06] Using > 0 costs more gas than != 0 when used on a uint in a require() statement
- [G-07] x += y costs more gas than x = x + y for state variables
- [G-08] Updating solidity version to the latest saves gas

## [G-01] ++I costs less gas as compared to I++ or I+= 1

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

```
for (uint256 i = 0; i < length; i++) {
   ...
  }
```
can become
```
for (uint256 i = 0; i < length; ++i) {
   ...
  }
```

File: ERC20PermitPermissionedMint.sol [Line 84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

## [G-02] No need to explicitly initialize variables with default value

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas. In the similar code above, the initialization of uint256 i = 0 can be simplified to uint256 i.

File: ERC20PermitPermissionedMint.sol [Line 84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

File: frxETHMinter.sol [Line 129](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L129)

File: OperatorRegistry.sol [Line 63](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63), [Line 84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84), [Line 114](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114)

## [G-03] Store array length in for-loops

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration.

Here, I suggest storing the array’s length in a variable before the for-loop, and use it instead

```
for (uint i = 0; i < minters_array.length; i++){ 
            if (minters_array[i] == minter_address) {
                minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
                break;
            }
        }
```

File: ERC20PermitPermissionedMint.sol [Line 84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

## [G-04] Duplicated require() / revert() checks should be refactored to a modifier or function

Saves deployment costs

File: ERC20PermitPermissionedMint.sol [Line 66](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L66), Line 77 and Line 95

## [G-05] Use private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

File: frxETHMinter.sol [Line 38-39](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L38)

## [G-06] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

`!= 0` costs 6 less GAS compared to `> 0` for unsigned integers in require statements with the optimizer enabled. [Detailed Explanation](https://twitter.com/gzeon/status/1485428085885640706)

File: frxETHMinter.sol [Line 125-126](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L125-L126)

## [G-07] x += y costs more gas than x = x + y for state variables

File: frxETHMinter.sol [Line 97](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L97)

## [G-08] Updating solidity version to the latest saves gas

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.
