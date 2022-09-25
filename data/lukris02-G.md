# Gas Optimizations Report for Frax Ether Liquid Staking contest

## Overview
During the audit, 8 gas issues were found.

№ | Title | Instance Count
--- | --- | --- 
G-1 | [Postfix increment](#g-1-postfix-increment) | 1
G-2 | [<>.length in loops](#g-2-length-in-loops) | 2
G-3 | [Initializing variables with default value](#g-3-initializing-variables-with-default-value) | 8
G-4 | [> 0 is more expensive than =! 0](#g-4--0-is--more-expensive-than--0) | 2
G-5 | [x += y is more expensive than x = x + y](#g-5-x--y-is--more-expensive-than-x--x--y) | 3
G-6 | [Using unchecked blocks saves gas](#g-6-using-unchecked-blocks-saves-gas) | 5
G-7 | [Public is more expensive than private for constants](#g-7-public-is-more-expensive-than-private-for-constants) | 2
G-8 | [Custom errors may be used](#g-8-custom-errors-may-be-used) | 22

## Gas Optimizations Findings (8)
### G-1. Postfix increment
##### Description
Prefix increment costs less gas than postfix.

##### Instances
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

##### Recommendation
Consider using prefix increment  where it is relevant. 

#
### G-2. <>.length in loops
##### Description
Reading the length of an array at each iteration of the loop consumes extra gas.

##### Instances
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

##### Recommendation
Store the length of an array in a variable before the loop, and use it.

#
### G-3. Initializing variables with default value
##### Description
It costs gas to initialize integer variables with 0 or bool variables with false but it is not necessary.

##### Instances
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L63
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L64
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

##### Recommendation
Remove initialization for default values.  
For example:
``` for (uint256 i; i < array.length; ++i) {```

#
### G-4. ```> 0``` is  more expensive than ```=! 0```
##### Instances
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126

##### Recommendation
Use ```=! 0``` instead of ```> 0```, where possible.

#
### G-5. ```x += y``` is  more expensive than ```x = x + y```
##### Instances
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L168
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L97
- https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L72

##### Recommendation
Use ```x = x + y``` instead of ```x += y```.
Use ```x = x - y``` instead of ```x -= y```.

#
### G-6. Using unchecked blocks saves gas
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. When an overflow or underflow isn’t possible, some gas can be saved by using unchecked blocks.

##### Instances
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

##### Recommendation
Change:
```
for (uint256 i; i < n; ++i) {
 // ...
}
```
to:
```
for (uint256 i; i < n;) { 
 // ...
 unchecked { ++i; }
}
```

#
### G-7. ```Public``` is more expensive than ```private``` for constants
##### Instances
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L38
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L39

##### Recommendation
Use private constants instead of public, where possible.

#
### G-8. Custom errors may be used
##### Description
[Custom errors from Solidity 0.8.4 are cheaper than revert strings.](https://blog.soliditylang.org/2021/04/21/custom-errors/)

##### Instances
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L41
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L46
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L66
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L68
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L77
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78
- https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L95
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L87
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L88
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L122
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L140
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L160
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L171
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L193
- https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L200
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L46
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L137
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L182
- https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L203

##### Recommendation
Use pragma 0.8.4+, and for example, change:
```
require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
```
to:
```
error NotOwnerOrTimelock();
...
if (!(msg.sender == timelock_address || msg.sender == owner)) revert NotOwnerOrTimelock();
```