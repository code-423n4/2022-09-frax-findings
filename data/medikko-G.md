### G-01 Using of custom errors than are came from 0.8.4 instead of `require()`/`revert()` strings will save deployment gas

Contracts use version 0.8.14 and can use custom errors that will save of deployment gas also when the trasaction failed duo to return the gas will be less.

_There have **2** istance of this issues:_


```
require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79

```
require(numDeposits > 0, "Not enough ETH in contract");
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L126

### G-02 Use `uncheck()` in for loops whenere overflow and undeflow is not possible

Using of `uncheck(i++)`/`uncheck(++i)` will save about 30 gas per iteration because compiler not save check everytime. This feature come from 0.8

_There have **5** istance of this issues:_

```
for (uint i = 0; i < minters_array.length; i++){
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84

```
for (uint256 i = 0; i < arrayLength; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63

```
for (uint256 i = 0; i < times; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84

```
for (uint256 i = 0; i < original_validators.length; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114

```
for (uint256 i = 0; i < numDeposits; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L129

### G-03 = + cost less than += for state variables

_There have **5** istance of this issues:_

```
currentWithheldETH += withheld_amt;
```
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#97

### G-04 i++, i += 1 and i = i + 1 IN FOR LOOPS COSTS MORE GAS COMPARED TO ++i

++i will save about 5 gas for each iteration

_There have **1** istance of this issues:_

```
for (uint i = 0; i < minters_array.length; i++){
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84

### G-05 Recomend to use `uint256(1)`/`uint256(2)` for true and false

If you don't use boolean for storage you will avoid Gwarmaccess 100 gas. Also boolean from true to false cost 20000 gas rather than uint256(2) to uint256(1) that cost less.

_There have **4** istance of this issues:_

File: ERC20PermitPermissionedMint.sol line 20
```
mapping(address => bool) public minters; // Mapping is also used for faster verification
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L20

File: frxETHMinter.sol lines 43,49,50

```
mapping(bytes => bool) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L43

```
bool public submitPaused;
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L49

```
bool public depositEtherPaused;
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L50

### G-06 Recomend to use one operator for comparison

If you use `>=` or `<=` this will cost more gas because in the EVM there is no implementation of Opcodes for `>=` and `<= and two operations are done.

_There have **1** istance of this issues:_

```
if (block.timestamp >= rewardsCycleEnd) { syncRewards(); } 
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L50

### G-07 `!=` cost 6 gas less than `>` in require

_There have **2** istance of this issues:_

File: frxETHMinter.sol lines 79, 126

```
require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79

```
require(numDeposits > 0, "Not enough ETH in contract");
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L126

### G-08 Recommend to use short require string to save deployment gas

If you use longer require strings than 32 bytes that will cost more expensive than short require string.

File : frxETHMinter.sol line 167

_There have **1** istance of this issues:_

```
require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L167

### G-09 Use defualt value rather than overwriite variable with their default value.

Overriting varibles with defualt values with their default value will waste only gas and not necessary.

_There have **5** istance of this issues:_

```
for (uint i = 0; i < minters_array.length; i++){
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84

```
for (uint256 i = 0; i < arrayLength; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63

```
for (uint256 i = 0; i < times; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84

```
for (uint256 i = 0; i < original_validators.length; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114

```
for (uint256 i = 0; i < numDeposits; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L129

### G-10 In for loop use outside variable for array length

For loop written like this`for (uint256 i; i < array.length; ++i) {` will cost more gas than `for (uint256 i; i < _lengthOfArray; ++i) {` because for every iteration we use mload and memory_offset
that will cost about 6 gas

_There are **2** instances of this issue:_

```
for (uint i = 0; i < minters_array.length; i++){
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84

```
for (uint256 i = 0; i < original_validators.length; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114
