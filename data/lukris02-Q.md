# QA Report for Frax Ether Liquid Staking contest

## Overview
During the audit, 3 low and 4 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | [Large number of elements may cause out-of-gas error](#l-1-large-number-of-elements-may-cause-out-of-gas-error) | Low | 3
L-2 | [Check zero denominator](#l-2-check-zero-denominator) | Low | 1
L-3 | [Missing check](#l-3-missing-check) | Low | 1
NC-1 | [Order of Layout](#nc-1-order-of-layout) | Non-Critical | 3
NC-2 | [Floating pragma](#nc-2-floating-pragma) | Non-Critical | 6
NC-3 | [Missing NatSpec](#nc-3-missing-natspec) | Non-Critical | 7
NC-4 | [Public functions can be external](#nc-4-public-functions-can-be-external) | Non-Critical | 10

## Low Risk Findings (3)
### L-1. Large number of elements may cause out-of-gas error
##### Description
[Loops](https://docs.soliditylang.org/en/develop/security-considerations.html#gas-limit-and-loops) that do not have a fixed number of iterations, for example, loops that depend on storage values, have to be used carefully: Due to the block gas limit, transactions can only consume a certain amount of gas. Either explicitly or just due to normal operation, the number of iterations in a loop can grow beyond the block gas limit, which can cause the complete contract to be stalled at a certain point. 

##### Instances
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

##### Recommendation
Restrict the maximum number of elements.

#
### L-2. Check zero denominator
##### Description 
If the input parameter is equal to zero, this will cause the call failure on division.

##### Instances
- https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L40

##### Recommendation
Add the check to prevent function call failure.

#
### L-3. Missing check
##### Description
No check that ```times``` <= validators.length. Without check, the function will try to pop more elements than there are in the array.

##### Instances
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L82-L86

##### Recommendation
Add require statement or custom error - ```times <= validators.length```.

## Non-Critical Risk Findings (4)
### NC-1. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions

##### Instances
Events should not be at the end of the contract:
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

##### Recommendation
Place events before modifiers.

#
### NC-2. Floating pragma
##### Description
Contracts should be deployed with the same compiler version. It helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

##### Instances
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol
- https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol
- https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

##### Recommendation
According to [SWC-103](https://swcregistry.io/docs/SWC-103), pragma version should be locked.

#
### NC-3. Missing NatSpec
##### Description 
NatSpec is missing for 7 functions in 2 contracts.

##### Instances
- [5 functions in ERC20PermitPermissionedMint.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol)
- [2 functions in xERC4626.sol](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol)

##### Recommendation
Add NatSpec for all functions.

#
### NC-4. Public functions can be external
##### Description
If functions are not called by the contract where they are defined, they can be declared external.

##### Instances
- https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L54
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L53
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L59
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L65
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L76
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L94
- https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L45
- https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L78
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L82
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L93

##### Recommendation
Make public functions external, where possible.