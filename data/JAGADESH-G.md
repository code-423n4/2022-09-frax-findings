## [G001] Don't Initialize Variables with Default Value

File: [src/DepositContract.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol)

```
76: for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {
148: for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {
```

File: [src/OperatorRegistry.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol)

```
63:     for (uint256 i = 0; i < arrayLength; ++i) {
84:     for (uint256 i = 0; i < times; ++i) {
114:    for (uint256 i = 0; i < original_validators.length; ++i) {
```

File: [src/frxETHMinter.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol)

```
63:        withholdRatio = 0; // No ETH is withheld initially
64:        currentWithheldETH = 0;
129:       for (uint256 i = 0; i < numDeposits; ++i) {
```

## [G002] `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops

File: [src/DepositContract.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol)

```
76:  for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {
148: for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {
```

File: [src/OperatorRegistry.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol)

```
63:     for (uint256 i = 0; i < arrayLength; ++i) {
84:     for (uint256 i = 0; i < times; ++i) {
114:    for (uint256 i = 0; i < original_validators.length; ++i) {
```

File: [src/frxETHMinter.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol)
```
129: for (uint256 i = 0; i < numDeposits; ++i) {
```

## [G003] ++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)

File: [src/DepositContract.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol)

```
76: for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {
148: for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {
```

## [G004] Gas optimizing through arithmetics
`<x> / 2` is the same as `<x> >> 1`. The `DIV` opcode costs **5 gas**, whereas `SHR` only costs **3 gas**

File: [src/DepositContract.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol)

```
88:             size /= 2;
154:            size /= 2;
```

## [G005] Use != 0 instead of > 0 for Unsigned Integer Comparison

`!= 0` costs less gas compared to `> 0` for unsigned integers in require statements with the optimizer enabled (6 gas)
For uints the minimum value would be 0 and never a negative value. Since it cannot be a negative value, then the check `> 0` is essentially checking that the value is not equal to 0 therefore `>0` can be replaced with `!=0` which saves gas.

File: [src/frxETHMinter.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol)

```
79:        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
126:       require(numDeposits > 0, "Not enough ETH in contract");
```

## [G006] Cache Array Length Outside of Loop

File: [src/OperatorRegistry.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol)

```
114:    for (uint256 i = 0; i < original_validators.length; ++i) {
```