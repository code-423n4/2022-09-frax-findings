# QA REPORT

## [QA 00] Use safeApprove instead approve in the following locations


### Proof of concept:
- [frxETH_sfrxETH_combo.t.sol#L1019](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L1019)
- [frxETH_sfrxETH_combo.t.sol#L904](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L904)
- [frxETH_sfrxETH_combo.t.sol#L992](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L992)
- [frxETH_sfrxETH_combo.t.sol#L852](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L852)
- [frxETH_sfrxETH_combo.t.sol#L1065](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L1065)

## [QA 01] Missing MIT licence for the following contracts


### Proof of concept:
- [deployGoerli.s.sol](https://github.com/code-423n4/2022-09-frax/tree/main/script/deployGoerli.s.sol)
- [AddValidators.s.sol](https://github.com/code-423n4/2022-09-frax/tree/main/script/AddValidators.s.sol)

## [QA 02] The following require statements are missing an error message


### Proof of concept:
- [frxETH_sfrxETH_combo.t.sol#L943](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L943)
- [frxETH_sfrxETH_combo.t.sol#L1081](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L1081)
- [frxETH_sfrxETH_combo.t.sol#L877](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L877)
- [frxETH_sfrxETH_combo.t.sol#L875](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L875)
- [frxETH_sfrxETH_combo.t.sol#L1030](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L1030)

## [QA 03] Remove the name from the following unused function parameters


### Proof of concept:
- [OperatorRegistry.sol#L40](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L40)
- [frxETH.sol#L41](https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETH.sol#L41)
- [sfrxETH.sol#L45](https://github.com/code-423n4/2022-09-frax/tree/main/src/sfrxETH.sol#L45)
