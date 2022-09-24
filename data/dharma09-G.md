## 1.DEFAULT VALUE INITIALIZATION

### PROBLEM
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0, etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

### PROOF OF CONCEPT
Instances include:
[ERC20PermitPermissionedMint.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84) or use ++i instead of i++
```
  for (uint i; i < minters_array.length; ++i)
```
[frxETHMinter.sol#L129](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129)
```
for (uint256 i; i < numDeposits; ++i)
```
[OperatorRegistry.sol#L63](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63)
[OperatorRegistry.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84)
[OperatorRegistry.sol#L114](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114)

## 2.COMPARISONS: != IS MORE EFFICIENT THAN > IN REQUIRE (6 GAS LESS)
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)

For uints the minimum value would be 0 and never a negative value. Since it cannot be a negative value, then the check > 0 is essentially checking that the value is not equal to 0 therefore >0 can be replaced with !=0 which saves gas.

While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas.

## PROOF OF CONCEPT
Instances include:
[frxETHMinter.sol#L126](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126)
```
require(numDeposits =! 0, "Not enough ETH in contract");
```
[frxETHMinter.sol#L79](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79)
```
require(sfrxeth_recieved =! 0, 'No sfrxETH was returned');
```