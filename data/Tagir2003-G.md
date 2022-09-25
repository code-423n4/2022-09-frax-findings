# GAS REPORT

## [GAS] Cache the following arrays size before the loop


Example: [OperatorRegistry.sol#L113](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L113)

## [GAS] Caching msg.sender is unnecessary


### Proof of concept:
- [frxETH_sfrxETH_combo.t.sol#L517](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L517)
- [frxETHMinter.t.sol#L367](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETHMinter.t.sol#L367)

## [GAS] Consider using custom errors
You can utilize customized errors in require statements to save gas.

### Proof of concept:
- [OperatorRegistry.sol#L181](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L181)
- [Owned.sol#L10](https://github.com/code-423n4/2022-09-frax/tree/main/src/Utils/Owned.sol#L10)

--