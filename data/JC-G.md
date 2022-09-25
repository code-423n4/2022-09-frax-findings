
## Summary

G-01 Calldata instead of memory for read-only arguments in external functions | 3 instances
G-02 <x> += <y> costs more gas than <x> = <x> + <y> for state variables | 2 instances
G-03 internal functions only called once can be inlined to save gas | 1 instance
G-04 Cache array length before loop | 3 instances
G-05 ++i/i++ should be unchecked{++i}/unchecked{i++} | 4 instances 
G-06 Using bools for storage incurs overhead | 3 instances
G-07 Use a more recent version of solidity | 6 instances
G-08 Use != 0 instead of > 0 | 2 instances
G-09 Redundant zero initialization | 5 instances
G-10 Use prefix not postfix in loops | 4 instances
G-11 Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead | 14 instances
G-12 Using private rather than public for constants, saves gas | 2 instances
G-13 Don’t compare boolean expressions to boolean literals | 5 instances
G-14 Empty blocks should be removed or emit something | 2 instances
G-15 Public functions to external | 9 instances
G-16 Use calldata instead of memory for function parameters | 3 instances
G-17 Use simple comparison in trinary logic / in if statement (>=) | 2 instances
G-18 ++i costs less gas compared to i++ or i += 1 | 3 instances

Total: 73 instances in 18 issues


## G-01 Calldata instead of memory for read-only arguments in external functions

Using calldata instead of memory for read-only arguments in external functions saves gas. 
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. 
Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). 
Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. 

3 instances in 1 file:

OperatorRegistry.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L172
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L173
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L181


## G-02 <x> += <y> costs more gas than <x> = <x> + <y> for state variables

2 instances in 2 files:

frxETHMinter.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L97

xERC4626.sol
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L72


## G-03 internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

1 instance in 1 file:

OperatorRegistry.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L126


## G-04 Cache array length before loop  

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack. 
Caching the array length in the stack saves around 3 gas per iteration. 
I suggest storing the array’s length in a variable before the for-loop, and use it instead

3 instances in 2 files:

OperatorRegistry.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

ERC20PermitPermissionedMint.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84


## G-05 ++i/i++ should be unchecked{++i}/unchecked{i++} 

++i/i++ should be unchecked{++i}/unchecked{i++}  when it is not possible for them to overflow, as is the case when used in for- and while-loops
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

4 instances in 2 files:

OperatorRegistry.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

frxETHMinter.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129


## G-06 Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. 
This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled. 
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 

Recommandation:
Use uint256(1) and uint256(2) for true/false

3 instances in 1 file:

frxETHMinter.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L43
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L49
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L50


## G-07 Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get compiler automatic inlining 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value. 
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>). 
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

6 instances in 6 files:

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#L2
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L4


## G-08 Use != 0 instead of > 0

Using > 0 uses slightly more gas than using != 0. Use != 0 when comparing uint variables to zero, which cannot hold values below zero. This change saves 6 gas per instance

2 instances in 1 file:

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126



## G-09 Redundant zero initialization

Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas. 
There are several places where an int is initialized to zero, which looks like: uint256 amount = 0; 

Recommended Mitigation Steps: 
Remove the redundant zero initialization uint256 amount; ( / or: If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). 
Explicitly initializing it with its default value is an anti-pattern and wastes gas.

5 instances in 3 files:

OperatorRegistry.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

frxETHMinter.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129

ERC20PermitPermissionedMint.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84


## G-10 Use prefix not postfix in loops

Using a prefix increment (++i) instead of a postfix increment (i++) saves gas for each loop cycle and so can have a big gas impact when the loop executes on a large number of elements. Saves 6 gas PER LOOP

4 instances in 2 files:
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129


## G-11 Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. 
Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. 
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 

Recommandation:
Use a larger size then downcast where needed

14 instances in 2 files:

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L42
https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L64
https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L80

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L24
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L27
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L30
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L33
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L35
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L37
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L49
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L50
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L79
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L80
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L89


## G-12 Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

2 instances in 1 file:

rxETHMinter.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L38
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L39


## G-13 Don’t compare boolean expressions to boolean literals

if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

5 instances in 2 files:

frxETHMinter.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L151

ERC20PermitPermissionedMint.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L46
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L68
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L69
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78


## G-14 Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

2 instances in 2 files:

sfrxETH.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L45

frxETH.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#L41


## G-15 Public functions to external

Public functions not called by the contract should be declared external instead. 

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 
https://docs.soliditylang.org/en/latest/contracts.html#function-overriding

9 instances in 4 files:

OperatorRegistry.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L82
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L93

ERC20PermitPermissionedMint.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L53
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L59
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L65
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L76
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L94

sfrxETH.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L54

xERC4626.so
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L78


## G-16 Use calldata instead of memory for function parameters

Having function arguments use calldata instead of memory can save gas. 

Recommended Mitigation Steps: 
Change function arguments from memory to calldata.

3 instances in 1 file:

OperatorRegistry.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L172
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L173
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L181


## G-17 Use simple comparison in trinary logic / in if statement (>=)

The comparison operators >= and <= use more gas than >, <, or ==. 
Replacing the >= and ≤ operators with a comparison operator that has an opcode in the EVM saves gas. 

Recommended Mitigation Steps: 
Replace the comparison operator and reverse the logic to save gas using the suggestions above.

2 instances in 2 files:

sfrxETH.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L50

xERC4626.sol
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L52



## G-18 ++i costs less gas compared to i++ or i += 1

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). 
This statement is true even with the optimizer enabled. i++ increments i and returns the initial value of i. 
Which means: uint i = 1;  i++; // == 1 but i == 2 
But ++i returns the actual incremented value: uint i = 1;  ++i; // == 2 and i == 2 too, so no need for a temporary variable . 
In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2.

 I suggest using ++i instead of i++ to increment the value of an uint variable.

3 instances in 1 file:

OperatorRegistry.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114


