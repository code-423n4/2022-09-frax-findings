# GAS REPORT

## [GAS] Use assembly opcodes iszero in the following locations


### Proof of concept:
- [frxETH_sfrxETH_combo.t.sol#L725](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L725)
- [frxETH_sfrxETH_combo.t.sol#L860](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L860)

## [GAS] Use abiEncodePacked()


Example: [SigUtils.sol#L30](https://github.com/code-423n4/2022-09-frax/tree/main/src/Utils/SigUtils.sol#L30)

## [GAS] Mark as payable If has onlyOwner modifier
In order to save gas you can put a payable modifier for functions that are called by protocol owners.

### Proof of concept:
- [OperatorRegistry.sol#L202](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L202)
- [OperatorRegistry.sol#L61](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L61)
