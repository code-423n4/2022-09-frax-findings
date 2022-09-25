## [G-01] STATE VARIABLES THAT ARE ACCESSED FOR MULTIPLE TIMES CAN BE CACHED IN MEMORY
State variables that are accessed for multiple times can be cached in memory, and the cached variable value can then be used. This can replace multiple `sload` operations with one `sload`, one `mstore`, and multiple `mload` operations to save gas.

`withholdRatio` can be cached, and the cached value can then be used in the following `_submit` function.
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L85-L101
```solidity
    function _submit(address recipient) internal nonReentrant {
        ...

        // Track the amount of ETH that we are keeping
        uint256 withheld_amt = 0;
        if (withholdRatio != 0) {
            withheld_amt = (msg.value * withholdRatio) / RATIO_PRECISION;
            currentWithheldETH += withheld_amt;
        }

        emit ETHSubmitted(msg.sender, recipient, msg.value, withheld_amt);
    }
```

`submitPaused` can be cached, and the cached value can then be used in the following `togglePauseSubmits` function.
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L177-L181
```solidity
    function togglePauseSubmits() external onlyByOwnGov {
        submitPaused = !submitPaused;

        emit SubmitPaused(submitPaused);
    }
```

`depositEtherPaused` can be cached, and the cached value can then be used in the following `togglePauseDepositEther` function.
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L184-L188
```solidity
    function togglePauseDepositEther() external onlyByOwnGov {
        depositEtherPaused = !depositEtherPaused;

        emit DepositEtherPaused(depositEtherPaused);
    }
```

## [G-02] ARRAY LENGTH CAN BE CACHED OUTSIDE OF LOOP
Caching the array length outside of the loop and using the cached length in the loop costs less gas than reading the array length for each iteration. For example, `minters_array.length` in the following code can be cached outside of the loop like `uint256 mintersArrayLength = minters_array.length`, and `i < mintersArrayLength` can be used for each iteration.

```solidity
  src\OperatorRegistry.sol
    114: for (uint256 i = 0; i < original_validators.length; ++i) {
  
  src\ERC20\ERC20PermitPermissionedMint.sol
    84: for (uint i = 0; i < minters_array.length; i++){
```

## [G-03] ARITHMETIC OPERATIONS THAT DO NOT OVERFLOW CAN BE UNCHECKED
Explicitly unchecking arithmetic operations that do not overflow by wrapping these in `unchecked {}` costs less gas than implicitly checking these.

For the following loops, `unchecked {++i}` at the end of the loop block can be used.
```solidity
src\frxETHMinter.sol
  129: for (uint256 i = 0; i < numDeposits; ++i) {

src\OperatorRegistry.sol
  63: for (uint256 i = 0; i < arrayLength; ++i) {
  84: for (uint256 i = 0; i < times; ++i) {
  114: for (uint256 i = 0; i < original_validators.length; ++i) {

src\ERC20\ERC20PermitPermissionedMint.sol
  84: for (uint i = 0; i < minters_array.length; i++){
```

## [G-04] VARIABLE DOES NOT NEED TO BE INITIALIZED TO ITS DEFAULT VALUE
Explicitly initializing a variable with its default value costs more gas than uninitializing it. For example, `uint256 i` can be used instead of `uint256 i = 0` in the following code.

```solidity
src\frxETHMinter.sol
  129: for (uint256 i = 0; i < numDeposits; ++i) {

src\OperatorRegistry.sol
  63: for (uint256 i = 0; i < arrayLength; ++i) {
  84: for (uint256 i = 0; i < times; ++i) {
  114: for (uint256 i = 0; i < original_validators.length; ++i) {

src\ERC20\ERC20PermitPermissionedMint.sol
  84: for (uint i = 0; i < minters_array.length; i++){
```

## [G-05] ++VARIABLE CAN BE USED INSTEAD OF VARIABLE++
++variable costs less gas than variable++. For example, `i++` can be changed to `++i` in the following code.

```solidity
src\ERC20\ERC20PermitPermissionedMint.sol
  84: for (uint i = 0; i < minters_array.length; i++){
```

## [G-06] X = X + Y OR X = X - Y CAN BE USED INSTEAD OF X += Y OR X -= Y
x = x + y or x = x - y costs less gas than x += y or x -= y. For example, `currentWithheldETH += withheld_amt` can be changed to `currentWithheldETH = currentWithheldETH + withheld_amt` in the following code.

```solidity
src\frxETHMinter.sol
  97: currentWithheldETH += withheld_amt;
  168: currentWithheldETH -= amount;
```

## [G-07] REVERT REASON STRINGS CAN BE SHORTENED TO FIT IN 32 BYTES
Revert reason strings that are longer than 32 bytes need more memory space and more `mstore` operation than these that are shorter than 32 bytes when reverting. Please consider shortening the following revert reason string.

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167
```solidity
        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```

## [G-08] REVERT WITH CUSTOM ERROR CAN BE USED INSTEAD OF REQUIRE() WITH REASON STRING
`revert` with custom error can cost less gas than `require()` with reason string. Please consider using `revert` with custom error to replace the following `require()`.

```solidity
src\frxETHMinter.sol
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

src\OperatorRegistry.sol
  46: require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
  137: require(numVals != 0, "Validator stack is empty");
  182: require(numValidators() == 0, "Clear validator array first");
  203: require(_timelock_address != address(0), "Zero address detected");

src\ERC20\ERC20PermitPermissionedMint.sol
  41: require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
  46: require(minters[msg.sender] == true, "Only minters");
  66: require(minter_address != address(0), "Zero address detected");
  68: require(minters[minter_address] == false, "Address already exists");
  77: require(minter_address != address(0), "Zero address detected");
  78: require(minters[minter_address] == true, "Address nonexistant");
  95: require(_timelock_address != address(0), "Zero address detected"); 
```