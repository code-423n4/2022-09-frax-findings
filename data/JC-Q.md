
## Summary


L-01 Unspecific Compiler Version Pragma | 5 instances

N-01 Event is missing indexed fields | 3 instances
N-02 Use a more recent version of solidity | 6 instances

Total: 14 instances in 3 issues



## L-01 Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

5 instances in 5 files:

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#L2


## N-01 Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

3 instances in 2 files:

OperatorRegistry.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L212
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L214

frxETHMinter.sol
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L207


## N-02 Use a more recent version of solidity

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


