# Summary
## Low
1. A floating pragma is set
2. Outdated compiler version
3. Missing checks for address(0x0) when assigning values to address state variables

## Non Critical
1. Event is missing indexed fields
2. Public functions that are not being used inside de contract should be external
3. File is missing NatSpec
4. Remove unused internal functions
5. Not using the named return variables anywhere in the function is confusing

# Low
## 1. A floating pragma is set
### Description
The contract VariableSupplyERC20Token have the pragma solidity directive ^0.8.0. It is recommended to specify a fixed compiler version to ensure that the bytecode produced 
does not vary between builds. 
This is especially important if you rely on bytecode-level verification of the code.

### Mitigation
Lock the pragma.

### Lines in the code
[frxETH.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETH.sol#L2)
[sfrxETH.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L2)
[ERC20PermitPermissionedMint.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L2)
[frxETHMinter.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L2)
[OperatorRegistry.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L2)
[xERC4626.sol#L4](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L4)

## 2. Outdated compiler version
### Description
It's a best practice to use the latest compiler version.
The specified minimum compiler version is quite old (0.8.0). Older compilers might be susceptible to some bugs. 
It's recommended changing the solidity version pragma to the latest version to enforce the use of an up-to-date compiler.

A list of known compiler bugs and their severity can be found here: https://etherscan.io/solcbuginfo

To check the bugfixed and improvements of latest versions see the following [link](https://github.com/ethereum/solidity/releases)

### Mitigation
Update the pragma to 0.8.17

### Lines in the code
[frxETH.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETH.sol#L2)
[sfrxETH.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L2)
[ERC20PermitPermissionedMint.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L2)
[frxETHMinter.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L2)
[OperatorRegistry.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L2)
[xERC4626.sol#L4](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L4)

## 3. Missing checks for address(0x0) when assigning values to address state variables
### Lines in the code
[ERC20PermitPermissionedMint.sol#L34](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L34)
[frxETHMinter.sol#L60-L62](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L60-L62)
[OperatorRegistry.sol#L41](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L41)

# Non Critical
## 1. Event is missing indexed fields
### Description
Index event fields make the field more quickly accessible to off-chain tools that parse events. 
However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). 
Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. 
If there are fewer than three fields, all of the fields should be indexed.

### Lines in the code
[ERC20PermitPermissionedMint.sol#L102-L106](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L102-L106)
[frxETHMinter.sol#L205-L212](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L205-L212)
[OperatorRegistry.sol#L208-L210](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L208-L210)
[OperatorRegistry.sol#L212-L214](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L212-L214)

## 2. Public functions that are not being used inside de contract should be external

### Lines in the code
[OperatorRegistry.sol#L82](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L82)
[OperatorRegistry.sol#L93](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L93)
[sfrxETH.sol#L54](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L54)
[ERC20PermitPermissionedMint.sol#L53](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L53)
[ERC20PermitPermissionedMint.sol#L59](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L59)
[ERC20PermitPermissionedMint.sol#L65](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L65)
[ERC20PermitPermissionedMint.sol#L76](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L76)
[ERC20PermitPermissionedMint.sol#L94](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L94)
[xERC4626.sol#L45](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L45)
[xERC4626.sol#L78](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L78)

## 3. File is missing NatSpec
### Lines in the code
[frxETH.sol](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETH.sol)
[sfrxETH.sol](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol)
[ERC20PermitPermissionedMint.sol](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol)
[frxETHMinter.sol](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol)
[OperatorRegistry.sol](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol)
[xERC4626.sol](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol)

## 4. Remove unused internal functions
Remove those functions that are internal and not are begin used inside the contract.

### Lines in the code
[sfrxETH.sol#L48](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L48)
[xERC4626.sol#L65](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L65)
[xERC4626.sol#L71](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L71)

## 5. Not using the named return variables anywhere in the function is confusing
### Description
Consider changing the variable to be an unnamed one

### Lines in the code
[frxETHMinter.sol#L70](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L70)
[sfrxETH.sol#L67](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L67)
[sfrxETH.sol#L83](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L83)

