
## G-01 Don't compare boolean expressions to boolean literals

Comparing to a constant (`true` or `false`) is a bit more expensive than directly checking the returned boolean value. I suggest using `if(!directValue)` instead of `if(directValue == false)`

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

There are 3 instances of this issue

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```
File: src/ERC20/ERC20PermitPermissionedMint.sol  

46: require(minters[msg.sender] == true, "Only minters");
68: require(minters[minter_address] == false, "Address already exists");
78: require(minters[minter_address] == true, "Address nonexistant");
```

-------------

## G-02 Using greater than 0 costs more gas than != 0 when used on a uint in a require() statement

!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas). This change saves 6 gas per instance

There are 2 instances of this issue

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
File: src/frxETHMinter.sol

79: require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
126: require(numDeposits > 0, "Not enough ETH in contract");
```

--------------

## G-03 Use an unchecked block for calculations that cannot overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block:
https://docs.soliditylang.org/en/v0.8.7/control-structures.html#checked-or-unchecked-arithmetic

There are 12 instances of this issue

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```
File: src/OperatorRegistry.sol

100: swapValidator(remove_idx, validators.length - 1);
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
File: src/frxETHMinter.sol

97: currentWithheldETH += withheld_amt;
125: uint256 numDeposits = (address(this).balance - currentWithheldETH) / DEPOSIT_SIZE;
168: currentWithheldETH -= amount;
```

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol

```
File: corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol

55: return storedTotalAssets_ + lastRewardAmount_;
60: uint256 unlockedRewards = (lastRewardAmount_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
61: return storedTotalAssets_ + unlockedRewards;
67: storedTotalAssets -= amount;
72: storedTotalAssets += amount;
85: uint256 nextRewards = asset.balanceOf(address(this)) - storedTotalAssets_ - lastRewardAmount_;
87: storedTotalAssets = storedTotalAssets_ + lastRewardAmount_;
89: uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```

------------

## G-04 Increments can be unchecked

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

Prior to Solidity 0.8.0, arithmetic operations would always wrap in case of under- or overflow leading to widespread use of libraries that introduce additional checks.

Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

To obtain the previous behaviour, an unchecked block can be used

There are 5 instances of this issue

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
File: src/frxETHMinter.sol

129: for (uint256 i = 0; i < numDeposits; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```
File: src/OperatorRegistry.sol

63:  for (uint256 i = 0; i < arrayLength; ++i) {
84:  for (uint256 i = 0; i < times; ++i) {
114: for (uint256 i = 0; i < original_validators.length; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```
File: src/ERC20/ERC20PermitPermissionedMint.sol  

84: for (uint i = 0; i < minters_array.length; i++){
```

-----------

## G-05 It costs more gas to initialize variables to zero than to let the default of zero be applied

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

There are 8 instances of this issue

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
File: src/frxETHMinter.sol

63:  withholdRatio = 0;
64:  currentWithheldETH = 0;
94:  uint256 withheld_amt = 0;
129: uint256 withheld_amt = 0;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```
File: src/OperatorRegistry.sol

63:  for (uint256 i = 0; i < arrayLength; ++i) {
84:  for (uint256 i = 0; i < times; ++i) {
114: for (uint256 i = 0; i < original_validators.length; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```
File: src/ERC20/ERC20PermitPermissionedMint.sol  

84: for (uint i = 0; i < minters_array.length; i++){
```

--------------------

## G-06 x += y costs more gas than x = x+y for state variables (same for x -= y)

There are 4 instances of this issue

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
File: src/frxETHMinter.sol

97:  currentWithheldETH += withheld_amt;
168: currentWithheldETH -= amount;
```

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol

```
File: corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol

67: storedTotalAssets -= amount;
72: storedTotalAssets += amount;
```

-----------

## G-07 Use custom errors rather than revert() or require() strings to save deployment gas

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

Source: [https://blog.soliditylang.org/2021/04/21/custom-errors/](https://blog.soliditylang.org/2021/04/21/custom-errors/):

> Starting from [Solidity v0.8.4](https://github.com/ethereum/solidity/releases/tag/v0.8.4), there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., `revert("Insufficient funds.");`), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the `error` statement, which can be used inside and outside of contracts (including interfaces and libraries).

There are 22 instances of this issue

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
File: src/frxETHMinter.sol

79:  require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
87:  require(!submitPaused, "Submit is paused");
88:  require(msg.value != 0, "Cannot submit 0");
122: require(!depositEtherPaused, "Depositing ETH is paused");
126: require(numDeposits > 0, "Not enough ETH in contract");
140: require(!activeValidators[pubKey], "Validator already has 32 ETH");
160: require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");
167: require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
171: require(success, "Invalid transfer");
193: require(success, "Invalid transfer");
200: require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```
File: src/OperatorRegistry.sol

46:  require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
137: require(numVals != 0, "Validator stack is empty");
182: require(numValidators() == 0, "Clear validator array first");
203: require(_timelock_address != address(0), "Zero address detected");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

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

-------------------------

## G-08 Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

There are 19 instances of this issue

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
File: src/frxETHMinter.sol

159: function setWithholdRatio(uint256 newRatio) external onlyByOwnGov {
166: function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
177: function togglePauseSubmits() external onlyByOwnGov {
184: function togglePauseDepositEther() external onlyByOwnGov {
191: function recoverEther(uint256 amount) external onlyByOwnGov {
199: function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```
File: src/OperatorRegistry.sol

53:  function addValidator(Validator calldata validator) public onlyByOwnGov {
61:  function addValidators(Validator[] calldata validatorArray) external onlyByOwnGov {
69:  function swapValidator(uint256 from_idx, uint256 to_idx) public onlyByOwnGov {
82:  function popValidators(uint256 times) public onlyByOwnGov {
93:  function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
181: function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
190: function clearValidatorArray() external onlyByOwnGov {
202: function setTimelock(address _timelock_address) external onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```
File: src/ERC20/ERC20PermitPermissionedMint.sol  

53: function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {
59: function minter_mint(address m_address, uint256 m_amount) public onlyMinters {
65: function addMinter(address minter_address) public onlyByOwnGov {
76: function removeMinter(address minter_address) public onlyByOwnGov {
94: function setTimelock(address _timelock_address) public onlyByOwnGov {
```

--------------

## G-09 Usage of uints or ints smaller than 32 bytes (26 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

There are 14 instances of this issue

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol

```
File: src/sfrxETH.sol 

42: constructor(ERC20 _underlying, uint32 _rewardsCycleLength)
64: uint8 v,
80: uint8 v,
```

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol

```
File: corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol

24: uint32 public immutable rewardsCycleLength;
27: uint32 public lastSync;
30: uint32 public rewardsCycleEnd;
33: uint192 public lastRewardAmount;
37: constructor(uint32 _rewardsCycleLength) {
48: uint192 lastRewardAmount_ = lastRewardAmount;
49: uint32 rewardsCycleEnd_ = rewardsCycleEnd;
50: uint32 lastSync_ = lastSync;
79: uint192 lastRewardAmount_ = lastRewardAmount;
80: uint32 timestamp = block.timestamp.safeCastTo32();
89: uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```

-------------

## G-10 ++i costs less gas than i++ especially when it's used in for loops (Same applies for --i , i-- too)

Prefix increments are cheaper than postfix increments.  

Further more, using unchecked {++i} is even more gas efficient, and the gas saving accumulates every iteration and can make a real change  
There is no risk of overflow caused by incrementing the iteration index in for loops (the `++i` in `for (uint i; i < minters_array.length; i++i) {`).  
But increments perform overflow checks that are not necessary in this case.  
These functions use not using prefix increments (`++i`) or not using the unchecked keyword

There is 1 instance of this issue

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```
File: src/ERC20/ERC20PermitPermissionedMint.sol  

84: for (uint i = 0; i < minters_array.length; i++){
```

-----------------

## G-11 Using both named returns and a return statement isn’t necessary

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

There are 3 instances of this issue

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol

```
File: src/sfrxETH.sol

59-71: function depositWithSignature(
        uint256 assets,
        address receiver,
        uint256 deadline,
        bool approveMax,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external nonReentrant returns (uint256 shares) {
        uint256 amount = approveMax ? type(uint256).max : assets;
        asset.permit(msg.sender, address(this), amount, deadline, v, r, s);
        return (deposit(assets, receiver));
    }
75-87: function mintWithSignature(
        uint256 shares,
        address receiver,
        uint256 deadline,
        bool approveMax,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external nonReentrant returns (uint256 assets) {
        uint256 amount = approveMax ? type(uint256).max : previewMint(shares);
        asset.permit(msg.sender, address(this), amount, deadline, v, r, s);
        return (mint(shares, receiver));
    }
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
File: src/frxETHMinter.sol

70-81: function submitAndDeposit(address recipient) external payable returns (uint256 shares) {
        // Give the frxETH to this contract after it is generated
        _submit(address(this));

        // Approve frxETH to sfrxETH for staking
        frxETHToken.approve(address(sfrxETHToken), msg.value);

        // Deposit the frxETH and give the generated sfrxETH to the final recipient
        uint256 sfrxeth_recieved = sfrxETHToken.deposit(msg.value, recipient);
        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

        return sfrxeth_recieved; 
```

-----------------

## G-12 Duplicated require() or revert() checks should be refactored to a modifier or function

There are 6 instances of this issue:

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
File: src/frxETHMinter.sol

171: require(success, "Invalid transfer");
193: require(success, "Invalid transfer");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```
File: src/OperatorRegistry.sol

203: require(_timelock_address != address(0), "Zero address detected");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```
File: src/ERC20/ERC20PermitPermissionedMint.sol  

66: require(minter_address != address(0), "Zero address detected");
77: require(minter_address != address(0), "Zero address detected");
95: require(_timelock_address != address(0), "Zero address detected"); 
```

--------------

## G-13 An array's length should be cached to save gas in for-loops

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration.

It is suggested to store the array’s length in a variable before the for-loop, and use it instead

There is 1 instance of this issue

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```
File: src/ERC20/ERC20PermitPermissionedMint.sol  

84: for (uint i = 0; i < minters_array.length; i++){ 
```

----------


## G-14 Require() or revert() strings longer than 32 bytes cost extra gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

I suggest shortening the revert strings to fit in 32 bytes, or that using custom errors as described next.

There are 2 instances of this issue

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
File: src/frxETHMinter.sol

167: require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```
File: src/OperatorRegistry.sol

114: for (uint256 i = 0; i < original_validators.length; ++i) {
```

---------

