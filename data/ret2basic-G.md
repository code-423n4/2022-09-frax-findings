# Frax Ether Liquid Staking Contest Gas Optimization Report

## Summary

The following gas optimization issues were found during the code audit:

1. Use `calldata` instead of `memory` (6 instances)
2. Cache `<array>.length` (1 instance)
3. Use `unchecked{}` to suppress overflow/underflow check (7 instances)
4. Long `require()`/`revert()` string (9 instances)
5. Using `bool`s for storage incurs overhead (4 instances)
6. Use `!= 0` instead of `> 0` when comparing uint (2 instances)
7. Empty blocks should be removed (2 instances)
8. Don't initialize variables with default value (7 instances)
9. Use `++i`/`--i` instead of `i++`/`i--` (3 instances)
10. Use uint256/int256 instead of other variations (16 instances)
11. Use `private` instead of `public` for constants (2 instances)
12. Use custom errors instead of `revert()`/`require()` strings (22 instances)

Total 81 instances of 12 issues.

## 1. Use `calldata` instead of `memory` (6 instances)

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for loop to copy each index of the `calldata` to the `memory` index. This overhead can be optimized by using `calldata` directly.

```solidity
src/DepositContract.sol::46 => function get_deposit_count() external view returns (bytes memory);

src/DepositContract.sol::97 => function get_deposit_count() override external view returns (bytes memory) {

src/DepositContract.sol::165 => function to_little_endian_64(uint64 value) internal pure returns (bytes memory ret) {

src/IsfrxETH.sol::24 => function name() external view returns (string memory);

src/IsfrxETH.sol::34 => function symbol() external view returns (string memory);

src/OperatorRegistry.sol::181 => function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
```

## 2. Cache `<array>.length` (1 instance)

If `<array>.length` is used as for loop termination condition, then the `.length` method will be called in each iteration. Caching it in a local variable can save gas.

```solidity
src/OperatorRegistry.sol::114 => for (uint256 i = 0; i < original_validators.length; ++i) {
```

## 3. Use `unchecked{}` to suppress overflow/underflow check (7 instances)

Starting from version 0.8.0, Solidity does overflow/underflow checks by default. It is a good feature to prevent vulnerabilities but it has a significant overhead, especially when used in for loop. When using uint256/int256, it is extremely hard to trigger overflow, so it makes sense to skip these checks. To suppress the overflow/underflow checks, use `unchecked {}`. For increment situations, just use `unchecked {}` directly; for decrement situations, add a `require()` statement before decrementing to prevent underflow.

```solidity
src/DepositContract.sol::76 => for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH - 1; height++)

src/DepositContract.sol::83 => for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {

src/DepositContract.sol::148 => for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {

src/frxETHMinter.sol::129 => for (uint256 i = 0; i < numDeposits; ++i) {

src/OperatorRegistry.sol::63 => for (uint256 i = 0; i < arrayLength; ++i) {

src/OperatorRegistry.sol::84 => for (uint256 i = 0; i < times; ++i) {

src/OperatorRegistry.sol::114 => for (uint256 i = 0; i < original_validators.length; ++i) {
```

## 4. Long `require()`/`revert()` string (9 instances)

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

```solidity
src/DepositContract.sol::108 => require(pubkey.length == 48, "DepositContract: invalid pubkey length");

src/DepositContract.sol::109 => require(withdrawal_credentials.length == 32, "DepositContract: invalid withdrawal_credentials length");

src/DepositContract.sol::110 => require(signature.length == 96, "DepositContract: invalid signature length");

src/DepositContract.sol::113 => require(msg.value >= 1 ether, "DepositContract: deposit value too low");

src/DepositContract.sol::114 => require(msg.value % 1 gwei == 0, "DepositContract: deposit value not multiple of gwei");

src/DepositContract.sol::116 => require(deposit_amount <= type(uint64).max, "DepositContract: deposit value too high");

src/DepositContract.sol::140 => require(node == deposit_data_root, "DepositContract: reconstructed DepositData does not match supplied deposit_data_root");

src/DepositContract.sol::143 => require(deposit_count < MAX_DEPOSIT_COUNT, "DepositContract: merkle tree full");

src/frxETHMinter.sol::167 => require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```

## 5. Using `bool`s for storage incurs overhead (4 instances)

Use `uint256(1)` and `uint256(2)` for true/false. Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
src/frxETHMinter.sol::49 => bool public submitPaused;

src/frxETHMinter.sol::50 => bool public depositEtherPaused;

src/sfrxETH.sol::63 => bool approveMax,

src/sfrxETH.sol::79 => bool approveMax,
```

## 6. Use `!= 0` instead of `> 0` when comparing uint (2 instances)

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```solidity
src/frxETHMinter.sol::79 => require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

src/frxETHMinter.sol::126 => require(numDeposits > 0, "Not enough ETH in contract");
```

## 7. Empty blocks should be removed (2 instances)

Empty blocks exist in the code. The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

```solidity
src/frxETH.sol::41 => {}

src/sfrxETH.sol::45 => {}
```

## 8. Don't initialize variables with default value (7 instances)

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```solidity
src/DepositContract.sol::76 => for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH - 1; height++)

src/DepositContract.sol::83 => for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {

src/DepositContract.sol::148 => for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {

src/frxETHMinter.sol::129 => for (uint256 i = 0; i < numDeposits; ++i) {

src/OperatorRegistry.sol::63 => for (uint256 i = 0; i < arrayLength; ++i) {

src/OperatorRegistry.sol::84 => for (uint256 i = 0; i < times; ++i) {

src/OperatorRegistry.sol::114 => for (uint256 i = 0; i < original_validators.length; ++i) {
```

## 9. Use `++i`/`--i` instead of `i++`/`i--` (3 instances)

Using `++i`/`--i` saves 6 gas per loop.

```solidity
src/DepositContract.sol::76 => for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH - 1; height++)

src/DepositContract.sol::83 => for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {

src/DepositContract.sol::148 => for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {
```

## 10. Use uint256/int256 instead of other variations (16 instances)

Using smaller data types such as uint8/int8 is more expensive than using uint256/int256. The EVM works with 256bit/32byte words. Every operation is based on these base units. If the data is smaller, further operations are needed to downscale from 256 bits to 8 bits, and this is more expensive than using uint256/int256.

```solidity
src/DepositContract.sol::92 => to_little_endian_64(uint64(deposit_count)),

src/DepositContract.sol::98 => return to_little_endian_64(uint64(deposit_count));

src/DepositContract.sol::116 => require(deposit_amount <= type(uint64).max, "DepositContract: deposit value too high");

src/DepositContract.sol::119 => bytes memory amount = to_little_endian_64(uint64(deposit_amount));

src/DepositContract.sol::125 => to_little_endian_64(uint64(deposit_count))

src/DepositContract.sol::165 => function to_little_endian_64(uint64 value) internal pure returns (bytes memory ret) {

src/IsfrxETH.sol::13 => function decimals() external view returns (uint8);

src/IsfrxETH.sol::15 => function depositWithSignature(uint256 assets, address receiver, uint256 deadline, bool approveMax, uint8 v, bytes32 r, bytes32 s) external returns (uint256 shares);

src/IsfrxETH.sol::17 => function lastSync() external view returns (uint32);

src/IsfrxETH.sol::23 => function mintWithSignature(uint256 shares, address receiver, uint256 deadline, bool approveMax, uint8 v, bytes32 r, bytes32 s) external returns (uint256 assets);

src/IsfrxETH.sol::26 => function permit(address owner, address spender, uint256 value, uint256 deadline, uint8 v, bytes32 r, bytes32 s) external;

src/IsfrxETH.sol::32 => function rewardsCycleEnd() external view returns (uint32);

src/IsfrxETH.sol::33 => function rewardsCycleLength() external view returns (uint32);

src/sfrxETH.sol::42 => constructor(ERC20 _underlying, uint32 _rewardsCycleLength)

src/sfrxETH.sol::64 => uint8 v,

src/sfrxETH.sol::80 => uint8 v,
```

## 11. Use `private` instead of `public` for constants (2 instances)

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

```solidity
src/frxETHMinter.sol::38 => uint256 public constant DEPOSIT_SIZE = 32 ether; // ETH 2.0 minimum deposit size

src/frxETHMinter.sol::39 => uint256 public constant RATIO_PRECISION = 1e6; // 1,000,000
```

## 12. Use custom errors instead of `revert()`/`require()` strings (22 instances)

Using `require()`/`revert()` strings is expensive. Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors.

Custom errors decrease both deploy and runtime gas costs. Note that runtime gas cost is only relevant when the revert condition is met.

```solidity
src/DepositContract.sol::108 => require(pubkey.length == 48, "DepositContract: invalid pubkey length");

src/DepositContract.sol::109 => require(withdrawal_credentials.length == 32, "DepositContract: invalid withdrawal_credentials length");

src/DepositContract.sol::110 => require(signature.length == 96, "DepositContract: invalid signature length");

src/DepositContract.sol::113 => require(msg.value >= 1 ether, "DepositContract: deposit value too low");

src/DepositContract.sol::114 => require(msg.value % 1 gwei == 0, "DepositContract: deposit value not multiple of gwei");

src/DepositContract.sol::116 => require(deposit_amount <= type(uint64).max, "DepositContract: deposit value too high");

src/DepositContract.sol::140 => require(node == deposit_data_root, "DepositContract: reconstructed DepositData does not match supplied deposit_data_root");

src/DepositContract.sol::143 => require(deposit_count < MAX_DEPOSIT_COUNT, "DepositContract: merkle tree full");

src/frxETHMinter.sol::87 => require(!submitPaused, "Submit is paused");

src/frxETHMinter.sol::88 => require(msg.value != 0, "Cannot submit 0");

src/frxETHMinter.sol::122 => require(!depositEtherPaused, "Depositing ETH is paused");

src/frxETHMinter.sol::126 => require(numDeposits > 0, "Not enough ETH in contract");

src/frxETHMinter.sol::140 => require(!activeValidators[pubKey], "Validator already has 32 ETH");

src/frxETHMinter.sol::160 => require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");

src/frxETHMinter.sol::167 => require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");

src/frxETHMinter.sol::171 => require(success, "Invalid transfer");

src/frxETHMinter.sol::193 => require(success, "Invalid transfer");

src/frxETHMinter.sol::200 => require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");

src/OperatorRegistry.sol::46 => require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

src/OperatorRegistry.sol::137 => require(numVals != 0, "Validator stack is empty");

src/OperatorRegistry.sol::182 => require(numValidators() == 0, "Clear validator array first");

src/OperatorRegistry.sol::203 => require(_timelock_address != address(0), "Zero address detected");
```
