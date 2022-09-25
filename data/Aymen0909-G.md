# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Optimize the `removeValidator` function to save gas  |  1 |
| 2      | Use `unchecked` blocks to save gas  |  1 |
| 3      | Using `bool` for storage incurs overhead   |  2 |
| 4      | `<array>.length` should not be looked up in every loop in a for loop |  2 |
| 5      | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |  2 |
| 6      | Use custom errors rather than `require()`/`revert()` strings to save deployments gas  |  20 |
| 7      | It costs more gas to initialize variables to zero than to let the default of zero be applied    |  8  |
| 8      | Use of `++i` cost less gas than `i++` in for loops    |  1  |
| 9      | `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops |  5  |
| 10      | Using private rather than public for constants, saves gas |  2 |
| 11     | Functions guaranteed to revert when called by normal users can be marked payable |  17 |

## Findings

### 1- Optimize the `removeValidator` function to save gas :

In the `removeValidator` function when `dont_care_about_ordering` is set to false, the function will delete the whole `validators` array and then reset it all over again. 

This could be optimized by shifting the element to be removed to the last and then using pop on it, in this way the `validators` array will be reset only if the deleted element is the first one but in all other cases the for loop will not run through the whole `validators` array, so the number of iterations will be less than in the reset method thus saving gas.

The instance occurs in the code below :

File: src/frxETHMinter.sol [Line 106-118](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L106-L118)

```
else {
    // Save the original validators
    Validator[] memory original_validators = validators;

    // Clear the original validators list
    delete validators;

    // Fill the new validators array with all except the value to remove
    for (uint256 i = 0; i < original_validators.length; ++i) {
        if (i != remove_idx) {
            validators.push(original_validators[i]);
        }
    }
```

This should be modified as follow :

```
else {
    uint256 len = validators.length - 1;
    for (uint256 i = remove_idx; i < len; i++){
            validators[i] = validators[i+1];
        }
    validators.pop();
    }
```

### 2- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There is 1 instance of this issue:

File: src/frxETHMinter.sol [Line 168](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L168)

```
currentWithheldETH -= amount;
```

The above operation cannot underflow due to the check [require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167) and should be modified as follows :

```
unchecked {
    currentWithheldETH -= amount;
}
```

### 3- Using `bool` for storage incurs overhead :

Use `uint256(1)` and `uint256(2)` instead of `true/false` to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from `false` to `true`, after having been `true` in the past

There are 2 instances of this issue:

File: src/frxETHMinter.sol [Line 49](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L49)

```
bool public submitPaused;
```

File: src/frxETHMinter.sol [Line 50](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L50)

```
bool public depositEtherPaused;
```

### 4- `<array>.length` should not be looked up in every loop in a for loop :

There are 2 instances of this issue:

File: src/ERC20/ERC20PermitPermissionedMint.sol [Line 84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

```
for (uint i = 0; i < minters_array.length; i++)
```

File: src/OperatorRegistry.sol [Line 114](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114)

```
for (uint256 i = 0; i < original_validators.length; ++i)
```

### 5- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

There are 2 instances of this issue:

File: src/frxETHMinter.sol [Line 97](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L97)
```
currentWithheldETH += withheld_amt;
```

File: src/frxETHMinter.sol [Line 168](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L168)
```
currentWithheldETH -= amount;
```

### 6- Use custom errors rather than `require()`/`revert()` strings to save deployments gas :

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information.

There are 20 instances of this issue :

```
File: src/ERC20/ERC20PermitPermissionedMint.sol

41       require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
46       require(minters[msg.sender] == true, "Only minters");
66       require(minter_address != address(0), "Zero address detected");
68       require(minters[minter_address] == false, "Address already exists")
77       require(minter_address != address(0), "Zero address detected");
78       require(minters[minter_address] == true, "Address nonexistant"); 
95       require(_timelock_address != address(0), "Zero address detected"); 

File: src/frxETHMinter.sol

79       require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
87       require(!submitPaused, "Submit is paused");
88       require(msg.value != 0, "Cannot submit 0");
122      require(!depositEtherPaused, "Depositing ETH is paused");
126      require(numDeposits > 0, "Not enough ETH in contract");
140      require(!activeValidators[pubKey], "Validator already has 32 ETH");
160      require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");
167      require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
193      require(success, "Invalid transfer");

File: src/OperatorRegistry.sol

46       require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock"); 
137      require(numVals != 0, "Validator stack is empty");
182      require(numValidators() == 0, "Clear validator array first");
203      require(_timelock_address != address(0), "Zero address detected");
```

### 7- It costs more gas to initialize variables to zero than to let the default of zero be applied (saves ~3 gas per instance) :

If a variable is not set/initialized, it is assumed to have the default value (0 for uint or int, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

There are 8 instances of this issue:

File: src/ERC20/ERC20PermitPermissionedMint.sol [Line 84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

```
for (uint i = 0; i < minters_array.length; i++)
```

File: src/OperatorRegistry.sol [Line 63](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63)

```
for (uint256 i = 0; i < arrayLength; ++i)
```

File: src/OperatorRegistry.sol [Line 84](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84)

```
for (uint256 i = 0; i < times; ++i)
```

File: src/OperatorRegistry.sol [Line 114](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114)

```
for (uint256 i = 0; i < original_validators.length; ++i)
```

File: src/frxETHMinter.sol [Line 63](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L63)

```
withholdRatio = 0;
```

File: src/frxETHMinter.sol [Line 64](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L64)

```
currentWithheldETH = 0;
```

File: src/frxETHMinter.sol [Line 94](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94)

```
uint256 withheld_amt = 0;
```

File: src/frxETHMinter.sol [Line 129](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129)

```
for (uint256 i = 0; i < numDeposits; ++i)
```

### 8- Use of ++i cost less gas than i++/i=i+1 in for loops :

There is 1 instance of this issue:

File: src/ERC20/ERC20PermitPermissionedMint.sol [Line 84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

```
for (uint i = 0; i < minters_array.length; i++)
```

### 9- ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 5 instances of this issue:

File: src/ERC20/ERC20PermitPermissionedMint.sol [Line 84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

```
for (uint i = 0; i < minters_array.length; i++)
```

File: src/frxETHMinter.sol [Line 129]()

```
for (uint256 i = 0; i < numDeposits; ++i)
```

File: src/OperatorRegistry.sol [Line 63](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63)

```
for (uint256 i = 0; i < arrayLength; ++i)
```

File: src/OperatorRegistry.sol [Line 84](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84)

```
for (uint256 i = 0; i < times; ++i)
```

File: src/OperatorRegistry.sol [Line 114](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114)

```
for (uint256 i = 0; i < original_validators.length; ++i)
```

### 10- Using private rather than public for constants, saves gas :

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

There are 2 instances of this issue:

File: src/frxETHMinter.sol [Line 38](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L38)

```
uint256 public constant DEPOSIT_SIZE = 32 ether;
```

File: src/frxETHMinter.sol [Line 39](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L39)

```
uint256 public constant RATIO_PRECISION = 1e6;
```

### 11- Functions guaranteed to revert when called by normal users can be marked `payable` :

If a function modifier such as `onlyAdmin` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for the owner because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are : 

CALLVALUE(gas=2), DUP1(gas=3), ISZERO(gas=3), PUSH2(gas=3), JUMPI(gas=10), PUSH1(gas=3), DUP1(gas=3), REVERT(gas=0), JUMPDEST(gas=1), POP(gas=2). 
Which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There are 17 instances of this issue:

```
File: src/ERC20/ERC20PermitPermissionedMint.sol

65       function addMinter(address minter_address) public onlyByOwnGov
76       function removeMinter(address minter_address) public onlyByOwnGov
94       function setTimelock(address _timelock_address) public onlyByOwnGov

File: src/frxETHMinter.sol

159      function setWithholdRatio(uint256 newRatio) external onlyByOwnGov
166      function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov
177      function togglePauseSubmits() external onlyByOwnGov
184      function togglePauseDepositEther() external onlyByOwnGov
191      function recoverEther(uint256 amount) external onlyByOwnGov
199      function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyByOwnGov

File: src/OperatorRegistry.sol

53       function addValidator(Validator calldata validator) public onlyByOwnGov
61       function addValidators(Validator[] calldata validatorArray) external onlyByOwnGov
69       function swapValidator(uint256 from_idx, uint256 to_idx) public onlyByOwnGov
82       function popValidators(uint256 times) public onlyByOwnGov
93       function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov
181      function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov
190      function clearValidatorArray() external onlyByOwnGov
202      function setTimelock(address _timelock_address) external onlyByOwnGov
```