- [Gas](#gas)
    - [**1. Don't use the length of an array for loops condition**](#1-dont-use-the-length-of-an-array-for-loops-condition)
    - [**2. There's no need to set default values for variables**](#2-theres-no-need-to-set-default-values-for-variables)
        - [Total gas saved: **8 * 7 = 56**](#total-gas-saved-8--7--56)
    - [**3. Reduce boolean comparison**](#3-reduce-boolean-comparison)
        - [Total gas saved: **18 * 2 + 15 * 1 = 51**](#total-gas-saved-18--2--15--1--51)
    - [**4. Avoid compound assignment operator in state variables**](#4-avoid-compound-assignment-operator-in-state-variables)
        - [Total gas saved: **13 * 4 = 52**](#total-gas-saved-13--4--52)
    - [**5. Use calldata instead of memory**](#5-use-calldata-instead-of-memory)
    - [**6. ++i costs less gas compared to i++ or i += 1**](#6-i-costs-less-gas-compared-to-i-or-i--1)
    - [**7. Reduce the size of error messages Long revert Strings**](#7-reduce-the-size-of-error-messages-long-revert-strings)
        - [Total gas saved: **18 * 1 = 18**](#total-gas-saved-18--1--18)
    - [**8. Use Custom Errors instead of Revert Strings to save Gas**](#8-use-custom-errors-instead-of-revert-strings-to-save-gas)
        - [Total gas saved: **9 * 22 = 198**](#total-gas-saved-9--22--198)
    - [**9. Optimize xERC4626.totalAssets**](#9-optimize-xerc4626totalassets)
    - [**10. Change bool to uint256 can save gas**](#10-change-bool-to-uint256-can-save-gas)
    - [**11. Argument not needed**](#11-argument-not-needed)

# Gas

## **1. Don't use the length of an array for loops condition**

It's cheaper to store the length of the array inside a local variable and iterate over it.

**Affected source code:**

- [ERC20PermitPermissionedMint.sol:84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)
- [OperatorRegistry.sol:114](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114)

## **2. There's no need to set default values for variables**

If a variable is not set/initialized, the default value is assumed (0, `false`, 0x0 ... depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testInit() public view returns (uint) { uint a = 0; return a; }
}

contract TesterB {
function testNoInit() public view returns (uint) { uint a; return a; }
}
```

Gas saving executing: **8 per entry**

```
TesterA.testInit:   21392
TesterB.testNoInit: 21384   
```

**Affected source code:**

- [ERC20PermitPermissionedMint.sol:84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)
- [OperatorRegistry.sol:63](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63)
- [OperatorRegistry.sol:84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84)
- [OperatorRegistry.sol:114](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114)
- [frxETHMinter.sol:63](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L63)
- [frxETHMinter.sol:64](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L64)
- [frxETHMinter.sol:94](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L94)

### Total gas saved: **8 * 7 = 56**

## **3. Reduce boolean comparison**

Compare a boolean value using `== true` or `== false` instead of using the boolean value is more expensive.

`NOT` opcode it's cheaper than using `EQUAL` or `NOTEQUAL` when the value it's false, or just the value without `== true` when it's true, because it will use less opcodes inside the VM.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.16;

contract TesterA {
function testEqual(bool a) public view returns (bool) { return a == true; }
}

contract TesterB {
function testNot(bool a) public view returns (bool) { return a; }
}
```

Gas saving executing: **18 per entry for == true**

```
TesterA.testEqual:   21814
TesterB.testNot:     21796   
```

```javascript
pragma solidity 0.8.16;

contract TesterA {
function testEqual(bool a) public view returns (bool) { return a == false; }
}

contract TesterB {
function testNot(bool a) public view returns (bool) { return !a; }
}
```

Gas saving executing: **15 per entry for == false**

```
TesterA.testEqual:   21814
TesterB.testNot:     21799
```

**Affected source code:**

Use the value instead of `== true`:

- [ERC20PermitPermissionedMint.sol:46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L46)
- [ERC20PermitPermissionedMint.sol:78](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L78)

Use the value instead of `== false`:

- [ERC20PermitPermissionedMint.sol:68](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L68)

### Total gas saved: **(18 * 2) + (15 * 1) = 51**

## **4. Avoid compound assignment operator in state variables**

Using compound assignment operators for state variables (like `State += X` or `State -= X` ...) it's more expensive than using operator assignment (like `State = State + X` or `State = State - X` ...).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
uint private _a;
function testShort() public {
_a += 1;
}
}

contract TesterB {
uint private _a;
function testLong() public {
_a = _a + 1;
}
}
```

Gas saving executing: **13 per entry**

```
TesterA.testShort: 43507
TesterB.testLong:  43494
```

**Affected source code:**

- [xERC4626.sol:67](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L67)
- [xERC4626.sol:72](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L72)
- [frxETHMinter.sol:97](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L97)
- [frxETHMinter.sol:168](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L168)

### Total gas saved: **13 * 4 = 52**

## **5. Use `calldata` instead of `memory`**

Some methods are declared as `external` but the arguments are defined as `memory` instead of as `calldata`.

By marking the function as `external` it is possible to use `calldata` in the arguments shown below and save significant gas.

**Recommended change:**

```diff
-   function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
+   function setWithdrawalCredential(bytes calldata _new_withdrawal_pubkey) external onlyByOwnGov {
        require(numValidators() == 0, "Clear validator array first");
        curr_withdrawal_pubkey = _new_withdrawal_pubkey;
        emit WithdrawalCredentialSet(_new_withdrawal_pubkey);
    }
```

**Affected source code:**

- [OperatorRegistry.sol:181-186](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L181-L186)

## **6. `++i` costs less gas compared to `i++` or `i += 1`**

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integers, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments `i` and returns the initial value of `i`. Which means:

```solidity
uint i = 1;
i++; // == 1 but i == 2
```

But `++i` returns the actual incremented value:

```solidity
uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable
```

In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`
I suggest using `++i` instead of `i++` to increment the value of an uint variable. Same thing for `--i` and `i--`

*Keep in mind that this change can only be made when we are not interested in the value returned by the operation, since the result is different, you only have to apply it when you only want to increase a counter.*

**Affected source code:**

- [ERC20PermitPermissionedMint.sol:84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

## **7. Reduce the size of error messages (Long revert Strings)**

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testShortRevert(bool path) public view {
require(path, "test error");
}
}

contract TesterB {
function testLongRevert(bool path) public view {
require(path, "test big error message, more than 32 bytes");
}
}
```

Gas saving executing: **18 per entry**

```
TesterA.testShortRevert: 21886   
TesterB.testLongRevert:  21904       
```

**Affected source code:**

- [frxETHMinter.sol:167](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L167)

### Total gas saved: **18 * 1 = 18**

## **8. Use Custom Errors instead of Revert Strings to save Gas**

Custom errors from Solidity `0.8.4` are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

**Source Custom Errors in Solidity:**

Starting from Solidity `v0.8.4`, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testRevert(bool path) public view {
 require(path, "test error");
}
}

contract TesterB {
error MyError(string msg);
function testError(bool path) public view {
 if(path) revert MyError("test error");
}
}
```

Gas saving executing: **9 per entry**

```
TesterA.testRevert: 21611
TesterB.testError:  21602     
```

**Affected source code:**

- [ERC20PermitPermissionedMint.sol:41](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L41)
- [ERC20PermitPermissionedMint.sol:46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L46)
- [ERC20PermitPermissionedMint.sol:66](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L66)
- [ERC20PermitPermissionedMint.sol:68](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L68)
- [ERC20PermitPermissionedMint.sol:77](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L77)
- [ERC20PermitPermissionedMint.sol:78](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L78)
- [ERC20PermitPermissionedMint.sol:95](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L95)
- [OperatorRegistry.sol:46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L46)
- [OperatorRegistry.sol:137](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L137)
- [OperatorRegistry.sol:182](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L182)
- [OperatorRegistry.sol:203](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L203)
- [frxETHMinter.sol:79](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79)
- [frxETHMinter.sol:87](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L87)
- [frxETHMinter.sol:88](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L88)
- [frxETHMinter.sol:122](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L122)
- [frxETHMinter.sol:126](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L126)
- [frxETHMinter.sol:140](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L140)
- [frxETHMinter.sol:160](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L160)
- [frxETHMinter.sol:167](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L167)
- [frxETHMinter.sol:171](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L171)
- [frxETHMinter.sol:193](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L193)
- [frxETHMinter.sol:200](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L200)

### Total gas saved: **9 * 22 = 198**

## **9. Optimize `xERC4626.totalAssets`**

It is possible to reduce the definition of the variables in this way:

```diff
    function totalAssets() public view override returns (uint256) {
-       // cache global vars
-       uint256 storedTotalAssets_ = storedTotalAssets;
-       uint192 lastRewardAmount_ = lastRewardAmount;
        uint32 rewardsCycleEnd_ = rewardsCycleEnd;
-       uint32 lastSync_ = lastSync;

        if (block.timestamp >= rewardsCycleEnd_) {
            // no rewards or rewards fully unlocked
            // entire reward amount is available
-           return storedTotalAssets_ + lastRewardAmount_;
+           return storedTotalAssets + lastRewardAmount;
        }

+       uint32 lastSync_ = lastSync;

        // rewards not fully unlocked
        // add unlocked rewards to stored total
-       uint256 unlockedRewards = (lastRewardAmount_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
-       return storedTotalAssets_ + unlockedRewards;
+       uint256 unlockedRewards = (lastRewardAmount * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
+       return storedTotalAssets + unlockedRewards;
    }
```

- [xERC4626.sol:45-61](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L45-L61)

## **10. Change `bool` to `uint256` can save gas**

Because each write operation requires an additional `SLOAD` to read the slot's contents, replace the bits occupied by the boolean, and then write back, `booleans` are more expensive than `uint256` or any other type that uses a complete word. This cannot be turned off because it is the compiler's defense against pointer aliasing and contract upgrades.

Reference:

- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol#L23-L27

Also, this is applicable to integer types different from `uint256` or `int256`.

**Affected source code for `booleans`:**

- [ERC20PermitPermissionedMint.sol:20](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L20)
- [frxETHMinter.sol:43](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L43)
- [frxETHMinter.sol:49](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L49)
- [frxETHMinter.sol:50](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L50)

## **11. Argument not needed**

The `approveMax` argument is not necessary since the maximum amount can already be set in the `assets` or `shares` argument by the caller.

**Affected source code:**

Remove `bool approveMax`:

- [sfrxETH.sol:63](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L63)
- [sfrxETH.sol:79](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L79)
