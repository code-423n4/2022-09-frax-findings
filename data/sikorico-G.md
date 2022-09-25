# GAS REPORT

## [GAS 00] Unnecessary caching of msg.sender


### Proof of concept:
- [frxETHMinter.t.sol#L371](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETHMinter.t.sol#L371)
- [frxETH_sfrxETH_combo.t.sol#L607](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L607)
- [frxETHMinter.t.sol#L307](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETHMinter.t.sol#L307)
- [deployGoerli.s.sol#L30](https://github.com/code-423n4/2022-09-frax/tree/main/script/deployGoerli.s.sol#L30)
- [frxETH_sfrxETH_combo.t.sol#L984](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L984)
