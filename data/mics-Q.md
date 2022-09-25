Table Of Content
================

* [QA REPORT](#qa-report)
        * [Contract should have pause/unpause functionality](#contract-should-have-pauseunpause-functionality)
        * [Missing 0 address check at transfer](#missing-0-address-check-at-transfer)
        * [Use safeTransfer() instead transfer()](#use-safetransfer-instead-transfer)
        * [Missing zero address check in a state variable setter function](#missing-zero-address-check-in-a-state-variable-setter-function)
        * [Should approve(0) first](#should-approve0-first)
        * [Array access is out of bounds](#array-access-is-out-of-bounds)
        * [SPDX license not provided in source file](#spdx-license-not-provided-in-source-file)
        * [Avoid floating pragma](#avoid-floating-pragma)
        * [Make sure the following functions has to be payable](#make-sure-the-following-functions-has-to-be-payable)
        * [Not indexed events](#not-indexed-events)
        * [Consider adding constant variables instead of hardcoded strings](#consider-adding-constant-variables-instead-of-hardcoded-strings)
        * [Several functions are declaring named returns but then are using return statements. I suggest choosing only one for readability reasons.](#several-functions-are-declaring-named-returns-but-then-are-using-return-statements-i-suggest-choosing-only-one-for-readability-reasons)
        * [Magical number should be documented and explained. Use a constant instead](#magical-number-should-be-documented-and-explained-use-a-constant-instead)
        * [Add event to the following functions](#add-event-to-the-following-functions)

# QA REPORT

## Contract should have pause/unpause functionality
In case a hack is occuring or an exploit is discovered, the team (or validators in this case) should be able to pause
functionality until the necessary changes are made to the system. Additionally, the gravity.sol contract should be manged by proxy so that upgrades can be made by the validators.

Because an attack would probably span a number of blocks, a method for pausing the contract would be able to interrupt any such attack if discovered.)

For instance, [frxETH_sfrxETH_combo.t.sol](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol)

## Missing 0 address check at transfer
Some contracts does not support 0 transfer, then the transaction will revert with no explanation. We recommend to add a require statement that the amount is not 0.

### Code Instances:
- [frxETH_sfrxETH_combo.t.sol#L716](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L716)
- [frxETH_sfrxETH_combo.t.sol#L169](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L169)
- [frxETH_sfrxETH_combo.t.sol#L76](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L76)
- [frxETH_sfrxETH_combo.t.sol#L727](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L727)

## Use safeTransfer() instead transfer()
Use openzeppelin safeTransfer() method instead of transfer() in the following locations.

### Code Instances:
- [frxETH_sfrxETH_combo.t.sol#L474](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L474)
- [frxETH_sfrxETH_combo.t.sol#L203](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L203)
- [frxETH_sfrxETH_combo.t.sol#L430](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L430)
- [frxETH_sfrxETH_combo.t.sol#L509](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L509)

## Missing zero address check in a state variable setter function
A state variable of type 'address' is set without a non-zero verification. This can lead to undesired behavior.

### Code Instances:
- [OperatorRegistry.sol#L202](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L202)
- [OperatorRegistry.sol#L40](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L40)
- [Owned.sol#L10](https://github.com/code-423n4/2022-09-frax/tree/main/src/Utils/Owned.sol#L10)

## Should approve(0) first
Some tokens (like USDT L199) do not work when changing the allowance from an existing non-zero allowance value.
They must first be approved by zero and then the actual allowance must be approved.

### Code Instances:
- [frxETH_sfrxETH_combo.t.sol#L956](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L956)
- [frxETH_sfrxETH_combo.t.sol#L974](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L974)
- [frxETH_sfrxETH_combo.t.sol#L992](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L992)
- [frxETH_sfrxETH_combo.t.sol#L443](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L443)

## Array access is out of bounds
There is no check for the access to be in the array bounds.

### Code Instances:
- [OperatorRegistry.sol#L69](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L69)
- [frxETHMinter.t.sol#L201](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETHMinter.t.sol#L201)
- [frxETHMinter.t.sol#L78](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETHMinter.t.sol#L78)
- [frxETHMinter.t.sol#L162](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETHMinter.t.sol#L162)

## SPDX license not provided in source file
Before publishing, consider adding a comment containing 'SPDX-License-Identifier: MIT' at the beginning of each source file.

### Code Instances:
- [deployGoerli.s.sol](https://github.com/code-423n4/2022-09-frax/tree/main/script/deployGoerli.s.sol)
- [AddValidators.s.sol](https://github.com/code-423n4/2022-09-frax/tree/main/script/AddValidators.s.sol)

## Avoid floating pragma
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively. (SWC-103)

### Code Instances:
- [frxETH_sfrxETH_combo.t.sol](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol)
- [OperatorRegistry.sol](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol)
- [SigUtils.sol](https://github.com/code-423n4/2022-09-frax/tree/main/src/Utils/SigUtils.sol)
- [Owned.sol](https://github.com/code-423n4/2022-09-frax/tree/main/src/Utils/Owned.sol)

## Make sure the following functions has to be payable
I didn't see a use of using payable in the following functions, consider changing it.

For instance, [frxETH_sfrxETH_combo.t.sol#L816](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L816)

## Not indexed events
The emitted event is not indexed, making off-chain scripts such as front-ends of dApps difficult to filter the events efficiently.



### Code Instances:
- [OperatorRegistry.sol#L212](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L212)
- [frxETHMinter.sol#L208](https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L208)
- [frxETH_sfrxETH_combo.t.sol#L62](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L62)
- [OperatorRegistry.sol#L213](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L213)

## Consider adding constant variables instead of hardcoded strings
A good practice is to use constant variables instead of hardcoded strings in the code.

### Code Instances:
- [frxETH_sfrxETH_combo.t.sol#L913](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L913)
- [frxETH_sfrxETH_combo.t.sol#L861](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L861)
- [frxETH_sfrxETH_combo.t.sol#L923](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L923)
- [frxETH_sfrxETH_combo.t.sol#L649](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L649)

## Several functions are declaring named returns but then are using return statements. I suggest choosing only one for readability reasons.
Using both named returns and a return statement isn't necessary. Removing one of those can improve code clarity.

### Code Instances:
- [sfrxETH.sol#L67](https://github.com/code-423n4/2022-09-frax/tree/main/src/sfrxETH.sol#L67)
- [sfrxETH.sol#L83](https://github.com/code-423n4/2022-09-frax/tree/main/src/sfrxETH.sol#L83)

## Magical number should be documented and explained. Use a constant instead


### Code Instances:
- [frxETHMinter.t.sol#L218](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETHMinter.t.sol#L218)
- [frxETHMinter.t.sol#L187](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETHMinter.t.sol#L187)
- [frxETHMinter.t.sol#L213](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETHMinter.t.sol#L213)
- [frxETHMinter.t.sol#L220](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETHMinter.t.sol#L220)

## Add event to the following functions


### Code Instances:
- [deployGoerli.s.sol#L22](https://github.com/code-423n4/2022-09-frax/tree/main/script/deployGoerli.s.sol#L22)
- [frxETH_sfrxETH_combo.t.sol#L32](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L32)
- [SigUtils.sol#L8](https://github.com/code-423n4/2022-09-frax/tree/main/src/Utils/SigUtils.sol#L8)
