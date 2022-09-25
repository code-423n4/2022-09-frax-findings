## Table of Contents
Total of 11 issues found
- X = X + Y costs less gass than X += Y
- Duplicate require() Checks Should be a Modifier or a Function
- Change Function Visibility Public to External
- Use Calldata instead of Memory for Read Only Function Parameters
- Boolean Comparisons
- Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas
- Store Array's Length as a Variable
- ++i Costs Less Gas than i++
- != 0 costs less gass than > 0
- Keep The Revert Strings of Error Messages within 32 Bytes
- Use Custom Errors to Save Gas

&ensp;
### X = X + Y costs less gass than X += Y

#### Issue
X = X + Y costs less gass than X += Y for state variables.
It saves 8 gas.

#### PoC
Total of 2 instance found.

1. `moveWithheldETH` function of `frxETHMinter.sol`
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L168
Change it to
```
currentWithheldETH = currentWithheldETH - amount;
```

1. `_submit` function of `frxETHMinter.sol`
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L97
Change it to
```
currentWithheldETH = currentWithheldETH + withheld_amt;
```

&ensp;
### Duplicate require() Checks Should be a Modifier or a Function

#### Issue
Since below require checks are used more than once,
I recommend making these to a modifier or a function.

#### PoC
Total of 2 instances found.
```
./ERC20PermitPermissionedMint.sol:66:        require(minter_address != address(0), "Zero address detected");
./ERC20PermitPermissionedMint.sol:77:        require(minter_address != address(0), "Zero address detected");
```
```
./frxETHMinter.sol:171:        require(success, "Invalid transfer");
./frxETHMinter.sol:193:        require(success, "Invalid transfer");
```

#### Mitigation
I recommend making duplicate require statement into modifier or a function.

&ensp;
### Change Function Visibility Public to External

#### Issue
If the function is not called internally, it is cheaper to set your function visibility to external instead of public.

#### PoC
Total of 3 instances found.

1. sfrxETH.sol:pricePerShare()
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L54-L56

2. OperatorRegistry.sol:popValidators()
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L82-L89

3. OperatorRegistry.sol:removeValidators()
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L93-L122

#### Mitigation
Change the function visibility to external.

&ensp;
### Use Calldata instead of Memory for Read Only Function Parameters

#### Issue
It is cheaper gas to use calldata than memory if the function parameter is read only.
Calldata is a non-modifiable, non-persistent area where function arguments are stored, 
and behaves mostly like memory. More details on following link.
link: https://docs.soliditylang.org/en/v0.8.15/types.html#data-location

#### PoC
Total of 3 instances found.
```
./OperatorRegistry.sol:172:        bytes memory pubKey, 
./OperatorRegistry.sol:173:        bytes memory signature, 
./OperatorRegistry.sol:181:    function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
```

#### Mitigation
Change memory to calldata

&ensp;
### Boolean Comparisons

#### Issue
It is more gas expensive to compare boolean with "variable == true" or "variable == false" than 
directly checking the returned boolean value.

#### PoC
Total of 3 instances found.
```
./ERC20PermitPermissionedMint.sol:68:        require(minters[minter_address] == false, "Address already exists");
./ERC20PermitPermissionedMint.sol:46:       require(minters[msg.sender] == true, "Only minters");
./ERC20PermitPermissionedMint.sol:78:        require(minters[minter_address] == true, "Address nonexistant");
```

#### Mitigation
Simply check by returned boolean value.
Example:
```solidity
For false:
require(!somethinghere);
For true:
require(somethinghere);
```

&ensp;
### Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas

#### Issue
Since EVM operates on 32 bytes at a time, if the element is smaller than that, the EVM must use more operations
in order to reduce the elements from 32 bytes to specified size.

Reference: https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html

#### PoC
Total of 8 instances found.
```
./sfrxETH.sol:64:        uint8 v,
./sfrxETH.sol:80:        uint8 v,
./xERC4626.sol:48:        uint192 lastRewardAmount_ = lastRewardAmount;
./xERC4626.sol:49:        uint32 rewardsCycleEnd_ = rewardsCycleEnd;
./xERC4626.sol:50:        uint32 lastSync_ = lastSync;
./xERC4626.sol:79:        uint192 lastRewardAmount_ = lastRewardAmount;
./xERC4626.sol:80:        uint32 timestamp = block.timestamp.safeCastTo32();
./xERC4626.sol:89:        uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```

#### Mitigation
I suggest using uint256 instead of anything smaller and downcast where needed.

&ensp;
### Store Array's Length as a Variable 

#### Issue
By storing an array's length as a variable before the for-loop,
can save 3 gas per iteration.

#### PoC
Total of 2 instances found.
```
./ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){ 
./OperatorRegistry.sol:114:            for (uint256 i = 0; i < original_validators.length; ++i) {
```

#### Mitigation
Store array's length as a variable before looping it.
For example, I suggest changing it to
```
uint length = minters_array.length;
for (uint i = 0; i < minters_array.length; i++){ 
```

&ensp;
### ++i Costs Less Gas than i++

#### Issue
Prefix increments/decrements (++i or --i) costs cheaper gas than 
postfix increment/decrements (i++ or i--).

#### PoC
Total of 1 instance found.
```
./ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){ 
```

#### Mitigation
Change it to postfix increments/decrements.
It saves 6 gas per loop.

&ensp;
### != 0 costs less gass than > 0

#### Issue
!= 0 costs less gas when optimizer is enabled and is used for unsigned integers in require statement.

#### PoC
Total of 2 instances found.
```
./frxETHMinter.sol:79:        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
./frxETHMinter.sol:126:        require(numDeposits > 0, "Not enough ETH in contract");
```

#### Mitigation
I suggest changing > 0 to != 0

&ensp;
### Keep The Revert Strings of Error Messages within 32 Bytes

#### Issue
Since each storage slot is size of 32 bytes, error messages that is longer than this will need
extra storage slot leading to more gas cost.

#### PoC
Total of 1 instance found.
```
./frxETHMinter.sol:167:        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```

#### Mitigation
Simply keep the error messages within 32 bytes to avoid extra storage slot cost.

&ensp;
### Use Custom Errors to Save Gas

#### Issue
Custom errors from Solidity 0.8.4 are cheaper than revert strings.
Details are explained here: https://blog.soliditylang.org/2021/04/21/custom-errors/

#### PoC
Total of 22 instances found.
```
./ERC20PermitPermissionedMint.sol:41:        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
./ERC20PermitPermissionedMint.sol:46:       require(minters[msg.sender] == true, "Only minters");
./ERC20PermitPermissionedMint.sol:66:        require(minter_address != address(0), "Zero address detected");
./ERC20PermitPermissionedMint.sol:68:        require(minters[minter_address] == false, "Address already exists");
./ERC20PermitPermissionedMint.sol:77:        require(minter_address != address(0), "Zero address detected");
./ERC20PermitPermissionedMint.sol:78:        require(minters[minter_address] == true, "Address nonexistant");
./ERC20PermitPermissionedMint.sol:95:        require(_timelock_address != address(0), "Zero address detected"); 
./frxETHMinter.sol:79:        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
./frxETHMinter.sol:87:        require(!submitPaused, "Submit is paused");
./frxETHMinter.sol:88:        require(msg.value != 0, "Cannot submit 0");
./frxETHMinter.sol:122:        require(!depositEtherPaused, "Depositing ETH is paused");
./frxETHMinter.sol:126:        require(numDeposits > 0, "Not enough ETH in contract");
./frxETHMinter.sol:140:            require(!activeValidators[pubKey], "Validator already has 32 ETH");
./frxETHMinter.sol:160:        require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");
./frxETHMinter.sol:167:        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
./frxETHMinter.sol:171:        require(success, "Invalid transfer");
./frxETHMinter.sol:193:        require(success, "Invalid transfer");
./frxETHMinter.sol:200:        require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
./OperatorRegistry.sol:46:        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
./OperatorRegistry.sol:137:        require(numVals != 0, "Validator stack is empty");
./OperatorRegistry.sol:182:        require(numValidators() == 0, "Clear validator array first");
./OperatorRegistry.sol:203:        require(_timelock_address != address(0), "Zero address detected");
```

#### Mitigation
I suggest implementing custom errors to save gas.

&ensp;