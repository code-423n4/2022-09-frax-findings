
| | issue |
| ----------- | ----------- |
| 1 | [no reason to cache these variables, doing so will waste gas](#1-no-reason-to-cache-these-variables-doing-so-will-waste-gas) |
| 2 | [state variables should be cached in stack variables rather than re-reading them from storage](#2-state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage) |
| 3 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#3-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 4 | [not using the named return variables when a function returns, wastes deployment gas](#4-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 5 | [can make the variable outside the loop to save gas](#5-can-make-the-variable-outside-the-loop-to-save-gas) |
| 6 | [`<array>.length` should not be looked up in every loop of a for-loop](#6-arraylength-should-not-be-looked-up-in-every-loop-of-a-for-loop) |
| 7 | [`++i` costs less gas than `i++`, especially when it’s used in for-loops (--i/i-- too)](#7-i-costs-less-gas-than-i-especially-when-its-used-in-for-loops---ii---too) |
| 8 | [it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied](#8-it-costs-more-gas-to-initialize-non-constantnon-immutable-variables-to-zero-than-to-let-the-default-of-zero-be-applied) |
| 9 | [`++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops](#9--ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for-loop-and-while-loops) |
| 10 | [these variables are initially 0 so the is no need for these two lines](#10-these-variables-are-initially-0-so-the-is-no-need-for-these-two-lines) |
| 11 | [require()/revert() strings longer than 32 bytes cost extra gas](#11-requirerevert-strings-longer-than-32-bytes-cost-extra-gas) |
| 12 | [using > 0 costs more gas than != 0 when used on a uint in a require() statement](#12-using--0-costs-more-gas-than--0-when-used-on-a-uint-in-a-require-statement) |
| 13 | [use custom errors rather than revert()/require() strings to save deployment gas](#13-use-custom-errors-rather-than-revertrequire-strings-to-save-deployment-gas) |
| 14 | [its not safe to use pragma for solidity version, its highly recommended to test thoroughly on a more recent version of solidity and remove the pragma](#14-its-not-safe-to-use-pragma-for-solidity-version-its-highly-recommended-to-test-thoroughly-on-a-more-recent-version-of-solidity-and-remove-the-pragma) |
| 15 | [using `calldata` instead of `memory` for read-only arguments in external functions saves gas](#15-using-calldata-instead-of-memory-for-read-only-arguments-in-external-functions-saves-gas) |
| 16 | [using `bool` for storage incurs overhead](#16-using-bool-for-storage-incurs-overhead) |
| 17 | [usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#17-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 18 | [using private rather than public for constants, saves gas](#18-using-private-rather-than-public-for-constants-saves-gas) |
| 19 | [bytes constants are more efficient than string constants](#19-bytes-constants-are-more-efficient-than-string-constants) |


## 1. no reason to cache these variables, doing so will waste gas

- [xERC4626.sol#L47](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L47)
- [xERC4626.sol#L48](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L48)
- [xERC4626.sol#L60](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L60)


## 2. state variables should be cached in stack variables rather than re-reading them from storage

`rewardsCycleLength`
- [xERC4626.sol#L89](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L60)


## 3. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

- [xERC4626.sol#L67](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L67)
- [xERC4626.sol#L72](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L72)
- [xERC4626.sol#L92](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L92)

- [frxETHMinter.sol#L97](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L97)
- [frxETHMinter.sol#L168](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L168)


## 4. not using the named return variables when a function returns, wastes deployment gas

- [frxETHMinter.sol#L70](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L70)

- [sfrxETH.sol#L67](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L67)
- [sfrxETH.sol#L83](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L83)


## 5. can make the variable outside the loop to save gas

- [frxETHMinter.sol#L132](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L132)
- [frxETHMinter.sol#L133](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L133)
- [frxETHMinter.sol#L134](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L134)
- [frxETHMinter.sol#L135](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L135)


## 6. `<array>.length` should not be looked up in every loop of a for-loop

This reduce gas cost as show here https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/5

1- if it is a storage array, this is an extra sload operation (100 additional extra gas (EIP-2929 2) for each iteration except for the first),
2- if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first),
3- if it is a calldata array, this is an extra calldataload operation (3 additional gas for each iteration except for the first)

- [OperatorRegistry.sol#L114](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114)

- [ERC20PermitPermissionedMint.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)


## 7. `++i` costs less gas than `i++`, especially when it’s used in for-loops (--i/i-- too)
Saves 6 gas per loop

- [ERC20PermitPermissionedMint.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)


## 8. it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied

- [OperatorRegistry.sol#L63](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63)
- [OperatorRegistry.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84)
- [OperatorRegistry.sol#L114](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114)

- [frxETHMinter.sol#L63](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L63)
- [frxETHMinter.sol#L64](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L64)
- [frxETHMinter.sol#L94](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94)
- [frxETHMinter.sol#L129](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129)

- [ERC20PermitPermissionedMint.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)


## 9. ` ++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

- [OperatorRegistry.sol#L63](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63)
- [OperatorRegistry.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84)
- [OperatorRegistry.sol#L114](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114)

- [frxETHMinter.sol#L129](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129)

- [ERC20PermitPermissionedMint.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)


## 10. these variables are initially 0 so the is no need for these two lines

- [frxETHMinter.sol#L63](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L63)
- [frxETHMinter.sol#L64](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L64)


## 11. require()/revert() strings longer than 32 bytes cost extra gas

- [frxETHMinter.sol#L167](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167)


## 12. using > 0 costs more gas than != 0 when used on a uint in a require() statement

- [frxETHMinter.sol#L79](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79)
- [frxETHMinter.sol#L126](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126)


## 13. use custom errors rather than revert()/require() strings to save deployment gas

https://blog.soliditylang.org/2021/04/21/custom-errors/

- [OperatorRegistry.sol#L46](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L46)
- [OperatorRegistry.sol#L137](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L137)
- [OperatorRegistry.sol#L182](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L182)
- [OperatorRegistry.sol#L203](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L203)

- [frxETHMinter.sol#L79](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79)
- [frxETHMinter.sol#L87](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L87)
- [frxETHMinter.sol#L88](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L88)
- [frxETHMinter.sol#L122](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L122)
- [frxETHMinter.sol#L126](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126)
- [frxETHMinter.sol#L140](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L140)
- [frxETHMinter.sol#L167](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167)
- [frxETHMinter.sol#L171](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L171)
- [frxETHMinter.sol#L193](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L193)
- [frxETHMinter.sol#L200](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L200)

- [ERC20PermitPermissionedMint.sol#L41](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L41)
- [ERC20PermitPermissionedMint.sol#L46](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L46)
- [ERC20PermitPermissionedMint.sol#L66](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L66)
- [ERC20PermitPermissionedMint.sol#L68](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L68)
- [ERC20PermitPermissionedMint.sol#L77](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L77)
- [ERC20PermitPermissionedMint.sol#L78](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78)
- [ERC20PermitPermissionedMint.sol#L95](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L95)


## 14. its not safe to use pragma for solidity version, its highly recommended to test thoroughly on a more recent version of solidity and remove the pragma

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have `external` calls skip contract existence checks if the external call has a return value	
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

- [frxETH.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol)
- [sfrxETH.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol)
- [ERC20PermitPermissionedMint.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol)
- [frxETHMinter.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol)
- [OperatorRegistry.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol)
- [xERC4626.sol](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol)


## 15. using `calldata` instead of `memory` for read-only arguments in external functions saves gas

- [OperatorRegistry.sol#L172](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L172)
- [OperatorRegistry.sol#L173](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L173)
- [OperatorRegistry.sol#L181](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L181)


## 16. using `bool` for storage incurs overhead

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

- [OperatorRegistry.sol#L93](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L93)

- [frxETHMinter.sol#L43](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L43)
- [frxETHMinter.sol#L49](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L49)
- [frxETHMinter.sol#L50](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L50)
- [frxETHMinter.sol#L170](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L170)
- [frxETHMinter.sol#L192](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L192)

- [ERC20PermitPermissionedMint.sol#L20](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L20)

- [sfrxETH.sol#L63](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L63)
- [sfrxETH.sol#L79](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L79)


## 17. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

- [xERC4626.sol#L33](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L33)
- [xERC4626.sol#L48](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L48)
- [xERC4626.sol#L79](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L79)
- [xERC4626.sol#L24](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L24)
- [xERC4626.sol#L27](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L27)
- [xERC4626.sol#L30](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L30)
- [xERC4626.sol#L37](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L37)
- [xERC4626.sol#L49](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L49)
- [xERC4626.sol#L50](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L50)
- [xERC4626.sol#L80](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L80)
- [xERC4626.sol#L89](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L89)

- [sfrxETH.sol#L42](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L42)
- [sfrxETH.sol#L64](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L64)
- [sfrxETH.sol#L80](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L80)


## 18. using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

- [frxETHMinter.sol#L38](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L38)
- [frxETHMinter.sol#L39](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L39)


## 19. bytes constants are more efficient than string constants

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

- [ERC20PermitPermissionedMint.sol#L27](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L27)
- [ERC20PermitPermissionedMint.sol#L28](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L28)
