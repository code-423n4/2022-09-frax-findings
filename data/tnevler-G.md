# Report
## Gas Optimizations ## 

### [G-01]: X += Y (X -= Y) costs more gas than X = X + Y (X = X - Y)
**Context:** 

+ [currentWithheldETH += withheld_amt;](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L97)
+ [currentWithheldETH -= amount;](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L168)
+ [storedTotalAssets += amount;](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L72)
+ [storedTotalAssets -= amount;](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L67)

**Recommendation:**

Change X += Y (X -= Y) to X = X + Y (X = X - Y).

### [G-02]: Don't initialize variable with its default value 
**Context:** 

1. https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
2. https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129
3. https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
4. https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
5. https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114
6. https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L63
7. https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L64
8. https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94

**Description:**

Default value of uint is 0. It's unnecessary and costs more gas to initialize uint variavles to 0.

**Recommendation:**

1-5. Change "uint256 i = 0;" to "uint256 i;".

6-7. Remove the line.

8. Change "uint256 withheld_amt = 0;" to "uint256 withheld_amt".


### [G-03]: >0 costs more gas than !=0 
**Context:** 

+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126

**Description:**

uint256 type will never be less than 0.

**Recommendation:**

Change > 0 to !=0.

### [G-04]: i++ costs more gas than ++i 
**Context:**

```
for (uint i = 0; i < minters_array.length; i++){ 
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

**Recommendation:**

Change i++ to ++i.


### [G-05]: Unchecked arithmetic
**Context:**

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L34

**Description:**

Some gas can be saved by using an unchecked {} block if an overflow/underflow isn't possible because of a previous require() or if-statement.

**Recommendation:**

Place the arithmetic operations in an unchecked block.


### [G-06]: Use new variable instead of reading array length in every loop of a for-loop 
**Context:**

+ https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

**Description:**

If you read the length of the array at each iteration of the loop, this consumes a lot of gas.

**Recommendation:**

Store the array’s length in a variable before the for-loop, and use this new variable in the loop.


### [G-07]: Use custom errors instead of revert strings 

[Custom errors](https://blog.soliditylang.org/2021/04/21/custom-errors/) are more gas efficient than using require with a string explanation.

+ https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L40
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L46
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L66
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L68
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L77
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L95
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L87
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L88
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L122
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L140
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L140
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L171
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L193
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L200
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L46
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L137
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L182
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L203