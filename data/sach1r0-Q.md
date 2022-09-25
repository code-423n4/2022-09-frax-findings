# Lack of zero-address check in the constructor

## Details
 Lack of zero-address checks may lead to infunctional protocol especially in the case wherein variable is immutable like the  `depositContract` .
 
## Mitigation
Consider adding zero-address checks such as: `require(depositContractAddress != address(0));`

## Line of code
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L52-L65

___
# Non-Library/Interface files should use fixed compiler versions, not floating ones

## Details
Contracts should be deployed with the same compiler version/flags where they have been tested with. Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version that might introduce bugs that affect the contract system negatively.
see reference: https://github.com/code-423n4/2021-11-unlock-findings/issues/15, https://code4rena.com/reports/2022-03-paladin/ and https://swcregistry.io/docs/SWC-103

## Mitigation
I suggest removing `^` in `pragma solidity ^0.8.0` and change it to `pragma solidity 0.8.15`.

## Line of code
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETH.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L2

___
