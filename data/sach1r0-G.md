# Functions that are not called within the contract must set its visibility to `external` instead of `public`

## Details
Setting function's visibility to external when it is only called externally can save gas because external function’s parameters are not copied into memory and are instead read from calldata directly.
see reference: https://github.com/code-423n4/2021-06-gro-findings/issues/37

## Mitigation
Set function visibility to `external`

## Line of code
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L54-L56
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L53-L56
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L59-L62
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L65-L73
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L76-L89
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L93-L119

___
# No need to explicitly initialize variables with their default values

## Details
When variables are not set, it is assumed to have it's default value(0 for uint, false for bool, address(0) for address). Explicitly initializing it with its default value is an anti-pattern and wastes gas.
see reference: https://code4rena.com/reports/2022-02-jpyc/ [G-07] GENERAL RECOMMENDATIONS

## Mitigation 
change `uint i = 0;` to `uint i;`


## Line of code
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L129
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114

___
# Solidity compiler will always read the length of the array during each iteration
## Details
.length in a loop can be extracted into a variable and used where necessary to reduce the number of storage reads
see reference: https://github.com/code-423n4/2021-10-union-findings/issues/92
## Mitigation:
This extra costs can be avoided by caching the array length. 
Example:
`uint minters_arrayLength = minters_array.length;`
`for (uint i = 0; i < minters_arrayLength; i++) {
}`
## Line of code
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114

___
# Pre-increment cost less gas than post-increment

## Details
`i++` costs more gas than `++i` , for uint pre-decrement is cheaper than post-decrement
see reference: https://github.com/code-423n4/2021-12-nftx-findings/issues/195

## Mitigation
change `i++` to `++i`

## Line of code
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84

___
# `!=` is cheaper in gas compared to `>` for uint

## Details
`!= 0` costs less gas compared to `> 0` for unsigned integers in require statements with the optimizer enabled (6 gas)
see reference: https://github.com/code-423n4/2021-12-maple-findings/issues/75

## Mitigation
use `!= 0` instead of `> 0`

## Line of code
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L126