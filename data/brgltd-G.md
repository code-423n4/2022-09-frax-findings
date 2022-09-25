# [G-01] Prefix increment costs less gas than postfix increment

There's 1 instance of this issue.

```
File: src/ERC20/ERC20PermitPermissionedMint.sol
84: for (uint i = 0; i < minters_array.length; i++){
```

# [G-02] The increment in for loop post condition can be made unchecked to save gas

There are 5 instances of this issue.

```
File: src/ERC20/ERC20PermitPermissionedMint.sol
84: for (uint i = 0; i < minters_array.length; i++){
```

```
File: src/OperatorRegistry.sol
63: for (uint256 i = 0; i < arrayLength; ++i) {
84: for (uint256 i = 0; i < times; ++i) {
114: for (uint256 i = 0; i < original_validators.length; ++i) {
```

```
File: src/frxETHMinter.sol
129: for (uint256 i = 0; i < numDeposits; ++i) {
```

# [G-03] Cache the length of the array before the loop

There are 2 instances of this issue.

```
File: src/ERC20/ERC20PermitPermissionedMint.sol
84: for (uint i = 0; i < minters_array.length; i++){
```

```
File: src/OperatorRegistry.sol
114: for (uint256 i = 0; i < original_validators.length; ++i) {
```

# [G-04] Initializing a variable with the default value wastes gas

There are 5 instances of this issue.

```
File: src/ERC20/ERC20PermitPermissionedMint.sol
84: for (uint i = 0; i < minters_array.length; i++){
```

```
File: src/OperatorRegistry.sol
63: for (uint256 i = 0; i < arrayLength; ++i) {
84: for (uint256 i = 0; i < times; ++i) {
114: for (uint256 i = 0; i < original_validators.length; ++i) {
```

```
File: src/frxETHMinter.sol
129: for (uint256 i = 0; i < numDeposits; ++i) {
```

# [G-05] Use != 0 instead of > 0 to save gas

Replace `> 0` with `!= 0` for unsigned integers.

There are 2 instances of this issue.

```
File: src/frxETHMinter.sol
79: require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
126: require(numDeposits > 0, "Not enough ETH in contract");
```

# [G-06] Use custom errors rather than require/revert strings to save gas

There are 21 instances of this issue.

```
File: src/ERC20/ERC20PermitPermissionedMint.sol
41: require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
46: require(minters[msg.sender] == true, "Only minters");
66: require(minter_address != address(0), "Zero address detected");
68: require(minters[minter_address] == false, "Address already exists");
77: require(minter_address != address(0), "Zero address detected");
78: require(minters[minter_address] == true, "Address nonexistant");
95: require(_timelock_address != address(0), "Zero address detected");
```

```
File: src/OperatorRegistry.sol
46: require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
137: require(numVals != 0, "Validator stack is empty");
182: require(numValidators() == 0, "Clear validator array first");
203: require(_timelock_address != address(0), "Zero address detected");
```

```
File: src/frxETHMinter.sol
79: require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
87: require(!submitPaused, "Submit is paused");
88: require(msg.value != 0, "Cannot submit 0");
122: require(!depositEtherPaused, "Depositing ETH is paused");
126: require(numDeposits > 0, "Not enough ETH in contract");
140: require(!activeValidators[pubKey], "Validator already has 32 ETH");
167: require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
171: require(success, "Invalid transfer");
193: require(success, "Invalid transfer");
200: require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```

# [G-07] Don’t compare boolean expressions to boolean literals

There are 3 instances of this issue.

```
File: src/ERC20/ERC20PermitPermissionedMint.sol
46: require(minters[msg.sender] == true, "Only minters");
68: require(minters[minter_address] == false, "Address already exists");
78: require(minters[minter_address] == true, "Address nonexistant");
```

# [G-08] Using private rather than public for constants, saves gas

The values can still be inspected on the source code if necessary.

There are 2 instances of this issue.

```
File: src/frxETHMinter.sol
38: uint256 public constant DEPOSIT_SIZE = 32 ether; // ETH 2.0 minimum deposit size
39: uint256 public constant RATIO_PRECISION = 1e6; // 1,000,000
```

# [G-09] x += y costs more gas than x = x + y for state variables

There are 4 instances of this issue.

```
File: lib/ERC4626/src/xERC4626.sol
67: storedTotalAssets -= amount;
72: storedTotalAssets += amount;
```

```
File: src/frxETHMinter.sol
97: currentWithheldETH += withheld_amt;
168: currentWithheldETH -= amount;
```
