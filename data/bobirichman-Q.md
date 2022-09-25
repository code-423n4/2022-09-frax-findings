# QA REPORT

## [LOW] Missing nonReentrancy modifier
The following functions allows attackers to try reentrancy since they are calling to external contracts / transferring eth. Consider adding a nonReentrancy modifier.

### Proof of concept:
- [frxETH_sfrxETH_combo.t.sol#L76](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L76)
- [frxETH_sfrxETH_combo.t.sol#L949](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L949)

## [LOW] The project is compiled with different solidity versions


## [LOW] Not verified input
At the following functions you should verify the parameters that are being assigned to a state variable.

### Proof of concept:
- [OperatorRegistry.sol#L40](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L40)
- [OperatorRegistry.sol#L202](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L202)

## [LOW] Use safeApprove
Use safeApprove in the following locations

### Proof of concept:
- [frxETH_sfrxETH_combo.t.sol#L956](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L956)
- [frxETH_sfrxETH_combo.t.sol#L443](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L443)

## [LOW] Approve 0 first
At some tokens you can approve an amount (at USDT for instance) only after approving to 0. Consider using increase/decrease approve notation instead.

### Proof of concept:
- [frxETH_sfrxETH_combo.t.sol#L443](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L443)
- [frxETH_sfrxETH_combo.t.sol#L904](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L904)

## [LOW] Missing pause functionality


Example: [frxETH_sfrxETH_combo.t.sol](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol)

## [NON CRITICAL] The following events are not indexed


### Proof of concept:
- [frxETHMinter.sol#L205](https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L205)
- [frxETHMinter.t.sol#L398](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETHMinter.t.sol#L398)

## [NON CRITICAL] Consider emitting an event at the following functions


### Proof of concept:
- [OperatorRegistry.sol#L40](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L40)
- [frxETH_sfrxETH_combo.t.sol#L32](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L32)

## [NON CRITICAL] Floating pragma
Floating pragma is a bad practice, since it does not guaranty the same version at future deployments.

### Proof of concept:
- [sfrxETH.sol](https://github.com/code-423n4/2022-09-frax/tree/main/src/sfrxETH.sol)
- [Owned.sol](https://github.com/code-423n4/2022-09-frax/tree/main/src/Utils/Owned.sol)

## [NON CRITICAL] Missing function spec comments


### Proof of concept:
- [OperatorRegistry.sol#L160](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L160)
- [frxETH_sfrxETH_combo.t.sol#L776](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L776)
