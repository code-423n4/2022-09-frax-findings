# Gas optimizations

## [G-01] Useless explicit initialization of variable to default value

Explicitly initializing a variable with its default value wastes gas.

### Files links :

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

### Findings :

```solidity
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:84: 	 for (uint i = 0; i < minters_array.length; i++){
2022-09-frax/src/OperatorRegistry.sol:63: 	 for (uint256 i = 0; i < arrayLength; ++i) {
2022-09-frax/src/OperatorRegistry.sol:84: 	 for (uint256 i = 0; i < times; ++i) {
2022-09-frax/src/OperatorRegistry.sol:114: 	 for (uint256 i = 0; i < original_validators.length; ++i) {
2022-09-frax/src/frxETHMinter.sol:94: 	 uint256 withheld_amt = 0;
2022-09-frax/src/frxETHMinter.sol:129: 	 for (uint256 i = 0; i < numDeposits; ++i) {
```

## [G-02] Cache Array Length Outside of Loop

Reading array length at each iteration of a loop uses 6 gas per loop (3 for mload and 3 to place memory_offset in the stack).

### Files links :

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

### Findings :

```solidity
2022-09-frax/src/OperatorRegistry.sol:114: 	 for (uint256 i = 0; i < original_validators.length; ++i) {
```

## [G-03] `i++` instead of `++i`

`i++` costs 5 more gas per loop than `++i`.

### Files links :

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

### Findings :

```solidity
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:84:   for (uint i = 0; i < minters_array.length; i++){
```

## [G-05] Inline `unchecked{++i;}` could be used

The increment of a loop can be inlined and unchecked since there is no risk of overflow/underflow, which cost less gas.

### Files links :

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

### Findings :

```solidity
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:84: 	 for (uint i = 0; i < minters_array.length; i++){
2022-09-frax/src/frxETHMinter.sol:129:   for (uint256 i = 0; i < numDeposits; ++i) {
2022-09-frax/src/OperatorRegistry.sol:63:   for (uint256 i = 0; i < arrayLength; ++i) {
2022-09-frax/src/OperatorRegistry.sol:84:   for (uint256 i = 0; i < times; ++i) {
2022-09-frax/src/OperatorRegistry.sol:114:   for (uint256 i = 0; i < original_validators.length; ++i) {
```

## [G-06] Boolean comparison

Using if(bool) or if(!bool) instead of if(bool == true) or if(bool == false) costs less gas.

### Files links :

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

### Findings :

```solidity
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:46: 	 require(minters[msg.sender] == true, "Only minters");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:68: 	 require(minters[minter_address] == false, "Address already exists");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:78: 	 require(minters[minter_address] == true, "Address nonexistant");
```

## [G-7] Use custom errors instead of revert string

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met).

### Files links :

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

### Findings :

```solidity
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:41: 	 require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:46: 	 require(minters[msg.sender] == true, "Only minters");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:66: 	 require(minter_address != address(0), "Zero address detected");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:68: 	 require(minters[minter_address] == false, "Address already exists");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:77: 	 require(minter_address != address(0), "Zero address detected");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:78: 	 require(minters[minter_address] == true, "Address nonexistant");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:95: 	 require(_timelock_address != address(0), "Zero address detected");
2022-09-frax/src/OperatorRegistry.sol:46: 	 require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
2022-09-frax/src/OperatorRegistry.sol:137: 	 require(numVals != 0, "Validator stack is empty");
2022-09-frax/src/OperatorRegistry.sol:182: 	 require(numValidators() == 0, "Clear validator array first");
2022-09-frax/src/OperatorRegistry.sol:203: 	 require(_timelock_address != address(0), "Zero address detected");
2022-09-frax/src/frxETHMinter.sol:79: 	 require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
2022-09-frax/src/frxETHMinter.sol:87: 	 require(!submitPaused, "Submit is paused");
2022-09-frax/src/frxETHMinter.sol:88: 	 require(msg.value != 0, "Cannot submit 0");
2022-09-frax/src/frxETHMinter.sol:122: 	 require(!depositEtherPaused, "Depositing ETH is paused");
2022-09-frax/src/frxETHMinter.sol:126: 	 require(numDeposits > 0, "Not enough ETH in contract");
2022-09-frax/src/frxETHMinter.sol:140: 	 require(!activeValidators[pubKey], "Validator already has 32 ETH");
2022-09-frax/src/frxETHMinter.sol:160: 	 require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");
2022-09-frax/src/frxETHMinter.sol:167: 	 require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
2022-09-frax/src/frxETHMinter.sol:171: 	 require(success, "Invalid transfer");
2022-09-frax/src/frxETHMinter.sol:193: 	 require(success, "Invalid transfer");
2022-09-frax/src/frxETHMinter.sol:200: 	 require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```