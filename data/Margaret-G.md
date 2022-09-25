# GAS REPORT

## [GAS 00] Declare as payable If the onlyOwner modifier is present
You can save gas by adding a payable modifier to functions that can be only called by protocol owners.

### Proof of concept:
- [OperatorRegistry.sol#L61](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L61)
- [OperatorRegistry.sol#L190](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L190)
- [OperatorRegistry.sol#L181](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L181)
- [OperatorRegistry.sol#L202](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L202)
- [OperatorRegistry.sol#L82](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L82)
