## 1. `++i` costs less gas compared to `i++` or `i += 1`, same for `--i/i--`. Especially in for loops

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments i and returns the initial value of `i`.

```
uint i = 1;  
i++; // == 1 but i == 2
```
But ++i returns the actual incremented value:
```
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```
In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

I suggest using ++i instead of i++ to increment the value of an uint variable.

If done inside for loop, saves 6 gas per loop.

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol
```
84	for (uint i = 0; i < minters_array.length; i++){
```

## 2. Use a more recent version of solidity

- Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath  
- Use a solidity version of at least 0.8.2 to get compiler automatic inlining  
- Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads  
- Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings  
- Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

```
All contracts
```

## 3. No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: `for (uint256 i = 0; i < numIterations; ++i) {` should be replaced with for `(uint256 i; i < numIterations; ++i) {`

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol
```
94	uint256 withheld_amt = 0;
129	for (uint256 i = 0; i < numDeposits; ++i) {
```
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol
```
84	for (uint i = 0; i < minters_array.length; i++){
```
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol
```
63	for (uint256 i = 0; i < arrayLength; ++i) {
84	for (uint256 i = 0; i < times; ++i) {
114	for (uint256 i = 0; i < original_validators.length; ++i) {
```

## 4. Use custom errors instead of revert strings to save Gas


Custom errors from Solidity 0.8.4 are cheaper than revert string (cheaper deployment cost and runtime cost when the revert condition is met)

Source: https://blog.soliditylang.org/2021/04/21/custom-errors/:

> Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol
```
41	require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
46	require(minters[msg.sender] == true, "Only minters");
66	require(minter_address != address(0), "Zero address detected");
68	require(minters[minter_address] == false, "Address already exists");
77	require(minter_address != address(0), "Zero address detected");
78	require(minters[minter_address] == true, "Address nonexistant");
95	require(_timelock_address != address(0), "Zero address detected"); 
```
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol
```
79	require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
87	require(!submitPaused, "Submit is paused");
88	require(msg.value != 0, "Cannot submit 0");
122	require(!depositEtherPaused, "Depositing ETH is paused");
126	require(numDeposits > 0, "Not enough ETH in contract");
140	require(!activeValidators[pubKey], "Validator already has 32 ETH");
160	require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");
167	require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
171	require(success, "Invalid transfer");
193	require(success, "Invalid transfer");
200	require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol
```
46	require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
137	require(numVals != 0, "Validator stack is empty");
182	require(numValidators() == 0, "Clear validator array first");
203	require(_timelock_address != address(0), "Zero address detected");
```
## 5. Increments can be unchecked


In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline. This saves 30-40 gas PER LOOP.

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol
```
for (uint i = 0; i < minters_array.length; i++){ 
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol
```
129	for (uint256 i = 0; i < numDeposits; ++i) {
```
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol
```
63	for (uint256 i = 0; i < arrayLength; ++i) {
84	for (uint256 i = 0; i < times; ++i) {
114	for (uint256 i = 0; i < original_validators.length; ++i) {
```

## 6.\<array>.length should not be looked up in every loop of a for-loop

The overheads outlined below are PER LOOP, excluding the first loop

- storage arrays incur a Gwarmaccess (100 gas)
- memory arrays use MLOAD (3 gas)
- calldata arrays use CALLDATALOAD (3 gas)

Caching the length changes each of these to a DUP\<N> (3 gas), and gets rid of the extra DUP\<N> needed to store the stack offset


https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol
```
for (uint i = 0; i < minters_array.length; i++){ 
```
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol
```
114	for (uint256 i = 0; i < original_validators.length; ++i) {
```
## 7. require()/revert() strings longer than 32 bytes cost extra gas


https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol
```
167	require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```

## 8. Boolean comparisons


Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using if(!directValue) instead of if(directValue == false)

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol
```
68	require(minters[minter_address] == false, "Address already exists");
78	require(minters[minter_address] == true, "Address nonexistant");
```

## 9. Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.


## 10. Using bools for storage incurs overhead


// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and 
// pointer aliasing, and it cannot be disabled.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

Use uint256(1) and uint256(2) for true/false

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol
```
63	bool approveMax,
79	bool approveMax,
```
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol
```
20	mapping(address => bool) public minters; // Mapping is also used for faster verification
```
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol
```
43	mapping(bytes => bool) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them
49	bool public submitPaused;
50	bool public depositEtherPaused;
```

## 11. Not using the named return variables when a function returns, wastes deployment gas

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol
```
70	return (deposit(assets, receiver));
86	return (mint(shares, receiver));
```
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol
```
55	return storedTotalAssets_ + lastRewardAmount_;
61	return storedTotalAssets_ + unlockedRewards;
```
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol
```
176	return Validator(pubKey, signature, depositDataRoot);
```





