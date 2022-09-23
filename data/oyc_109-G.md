## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::84 => for (uint i = 0; i < minters_array.length; i++){
2022-09-frax/src/OperatorRegistry.sol::63 => for (uint256 i = 0; i < arrayLength; ++i) {
2022-09-frax/src/OperatorRegistry.sol::84 => for (uint256 i = 0; i < times; ++i) {
2022-09-frax/src/OperatorRegistry.sol::114 => for (uint256 i = 0; i < original_validators.length; ++i) {
2022-09-frax/src/frxETHMinter.sol::129 => for (uint256 i = 0; i < numDeposits; ++i) {
```

## [G-02] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::84 => for (uint i = 0; i < minters_array.length; i++){
2022-09-frax/src/OperatorRegistry.sol::114 => for (uint256 i = 0; i < original_validators.length; ++i) {
```

## [G-03] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance

```
2022-09-frax/src/frxETHMinter.sol::79 => require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
2022-09-frax/src/frxETHMinter.sol::126 => require(numDeposits > 0, "Not enough ETH in contract");
```

## [G-04] Long Revert Strings

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

```
2022-09-frax/src/frxETHMinter.sol::167 => require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```

## [G-05] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2022-09-frax/lib/xERC4626.sol::24 => uint32 public immutable rewardsCycleLength;
2022-09-frax/src/frxETHMinter.sol::45 => IDepositContract public immutable depositContract; // ETH 2.0 deposit contract
2022-09-frax/src/frxETHMinter.sol::46 => frxETH public immutable frxETHToken;
2022-09-frax/src/frxETHMinter.sol::47 => IsfrxETH public immutable sfrxETHToken;
```

## [G-06] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::53 => function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::59 => function minter_mint(address m_address, uint256 m_amount) public onlyMinters {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::65 => function addMinter(address minter_address) public onlyByOwnGov {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::76 => function removeMinter(address minter_address) public onlyByOwnGov {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::94 => function setTimelock(address _timelock_address) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::53 => function addValidator(Validator calldata validator) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::61 => function addValidators(Validator[] calldata validatorArray) external onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::69 => function swapValidator(uint256 from_idx, uint256 to_idx) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::82 => function popValidators(uint256 times) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::93 => function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::181 => function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::190 => function clearValidatorArray() external onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::202 => function setTimelock(address _timelock_address) external onlyByOwnGov {
2022-09-frax/src/frxETHMinter.sol::159 => function setWithholdRatio(uint256 newRatio) external onlyByOwnGov {
2022-09-frax/src/frxETHMinter.sol::177 => function togglePauseSubmits() external onlyByOwnGov {
2022-09-frax/src/frxETHMinter.sol::184 => function togglePauseDepositEther() external onlyByOwnGov {
2022-09-frax/src/frxETHMinter.sol::191 => function recoverEther(uint256 amount) external onlyByOwnGov {
2022-09-frax/src/frxETHMinter.sol::199 => function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyByOwnGov {
```

## [G-07] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 

```
2022-09-frax/src/frxETH.sol::41 => {}
2022-09-frax/src/sfrxETH.sol::45 => {}
```

## [G-08] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2022-09-frax/lib/xERC4626.sol::24 => uint32 public immutable rewardsCycleLength;
2022-09-frax/lib/xERC4626.sol::27 => uint32 public lastSync;
2022-09-frax/lib/xERC4626.sol::30 => uint32 public rewardsCycleEnd;
2022-09-frax/lib/xERC4626.sol::49 => uint32 rewardsCycleEnd_ = rewardsCycleEnd;
2022-09-frax/lib/xERC4626.sol::50 => uint32 lastSync_ = lastSync;
2022-09-frax/lib/xERC4626.sol::80 => uint32 timestamp = block.timestamp.safeCastTo32();
2022-09-frax/lib/xERC4626.sol::89 => uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```

## [G-09] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::20 => mapping(address => bool) public minters; // Mapping is also used for faster verification
2022-09-frax/src/frxETHMinter.sol::43 => mapping(bytes => bool) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them
2022-09-frax/src/frxETHMinter.sol::49 => bool public submitPaused;
2022-09-frax/src/frxETHMinter.sol::50 => bool public depositEtherPaused;
```

## [G-10] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::84 => for (uint i = 0; i < minters_array.length; i++){
2022-09-frax/src/OperatorRegistry.sol::63 => for (uint256 i = 0; i < arrayLength; ++i) {
2022-09-frax/src/OperatorRegistry.sol::84 => for (uint256 i = 0; i < times; ++i) {
2022-09-frax/src/OperatorRegistry.sol::114 => for (uint256 i = 0; i < original_validators.length; ++i) {
2022-09-frax/src/frxETHMinter.sol::129 => for (uint256 i = 0; i < numDeposits; ++i) {
```

## [G-11] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2022-09-frax/lib/xERC4626.sol::67 => storedTotalAssets -= amount;
2022-09-frax/lib/xERC4626.sol::72 => storedTotalAssets += amount;
2022-09-frax/src/frxETHMinter.sol::97 => currentWithheldETH += withheld_amt;
2022-09-frax/src/frxETHMinter.sol::168 => currentWithheldETH -= amount;
```

## [G-12] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::41 => require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::46 => require(minters[msg.sender] == true, "Only minters");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::66 => require(minter_address != address(0), "Zero address detected");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::68 => require(minters[minter_address] == false, "Address already exists");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::77 => require(minter_address != address(0), "Zero address detected");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::78 => require(minters[minter_address] == true, "Address nonexistant");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::95 => require(_timelock_address != address(0), "Zero address detected");
2022-09-frax/src/OperatorRegistry.sol::46 => require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
2022-09-frax/src/OperatorRegistry.sol::137 => require(numVals != 0, "Validator stack is empty");
2022-09-frax/src/OperatorRegistry.sol::182 => require(numValidators() == 0, "Clear validator array first");
2022-09-frax/src/OperatorRegistry.sol::203 => require(_timelock_address != address(0), "Zero address detected");
2022-09-frax/src/frxETHMinter.sol::87 => require(!submitPaused, "Submit is paused");
2022-09-frax/src/frxETHMinter.sol::88 => require(msg.value != 0, "Cannot submit 0");
2022-09-frax/src/frxETHMinter.sol::122 => require(!depositEtherPaused, "Depositing ETH is paused");
2022-09-frax/src/frxETHMinter.sol::126 => require(numDeposits > 0, "Not enough ETH in contract");
2022-09-frax/src/frxETHMinter.sol::140 => require(!activeValidators[pubKey], "Validator already has 32 ETH");
2022-09-frax/src/frxETHMinter.sol::160 => require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");
2022-09-frax/src/frxETHMinter.sol::167 => require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
2022-09-frax/src/frxETHMinter.sol::171 => require(success, "Invalid transfer");
2022-09-frax/src/frxETHMinter.sol::193 => require(success, "Invalid transfer");
2022-09-frax/src/frxETHMinter.sol::200 => require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```

## [G-13] Use a more recent version of solidity

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

```
2022-09-frax/lib/xERC4626.sol::4 => pragma solidity ^0.8.0;
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/OperatorRegistry.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETH.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETHMinter.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/sfrxETH.sol::2 => pragma solidity ^0.8.0;
```

## [G-14] Prefix increments cheaper than Postfix increments

++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
Saves 5 gas PER LOOP

```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::84 => for (uint i = 0; i < minters_array.length; i++){
```

## [G-15] Don't compare boolean expressions to boolean literals

The extran comparison wastes gas
if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::46 => require(minters[msg.sender] == true, "Only minters");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::78 => require(minters[minter_address] == true, "Address nonexistant");
```

## [G-16] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2022-09-frax/lib/xERC4626.sol::45 => function totalAssets() public view override returns (uint256) {
2022-09-frax/lib/xERC4626.sol::78 => function syncRewards() public virtual {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::53 => function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::59 => function minter_mint(address m_address, uint256 m_amount) public onlyMinters {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::65 => function addMinter(address minter_address) public onlyByOwnGov {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::76 => function removeMinter(address minter_address) public onlyByOwnGov {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::94 => function setTimelock(address _timelock_address) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::82 => function popValidators(uint256 times) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::93 => function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
2022-09-frax/src/sfrxETH.sol::54 => function pricePerShare() public view returns (uint256) {
```

## [G-17] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
2022-09-frax/src/frxETHMinter.sol::70 => function submitAndDeposit(address recipient) external payable returns (uint256 shares) {
2022-09-frax/src/sfrxETH.sol::67 => ) external nonReentrant returns (uint256 shares) {
2022-09-frax/src/sfrxETH.sol::83 => ) external nonReentrant returns (uint256 assets) {
```

## [G-18] Use assembly to check for address(0)

Saves 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

instances:

```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::66 => require(minter_address != address(0), "Zero address detected");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::77 => require(minter_address != address(0), "Zero address detected");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::95 => require(_timelock_address != address(0), "Zero address detected");
2022-09-frax/src/OperatorRegistry.sol::203 => require(_timelock_address != address(0), "Zero address detected");
```

## [G-19] Use selfbalance()

Use selfbalance() instead of address(this).balance when getting your contract's balance of ETH to save gas.

```
2022-09-frax/src/frxETHMinter.sol::125 => uint256 numDeposits = (address(this).balance - currentWithheldETH) / DEPOSIT_SIZE;
```

## [G-20] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

```
2022-09-frax/src/OperatorRegistry.sol::71 => Validator memory fromVal = validators[from_idx];
2022-09-frax/src/OperatorRegistry.sol::72 => Validator memory toVal = validators[to_idx];
2022-09-frax/src/OperatorRegistry.sol::95 => bytes memory removed_pubkey = validators[remove_idx].pubKey;
2022-09-frax/src/OperatorRegistry.sol::140 => Validator memory popped = validators[numVals - 1];
2022-09-frax/src/OperatorRegistry.sol::161 => Validator memory v = validators[i];
```