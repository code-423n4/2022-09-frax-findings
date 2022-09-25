### G-01 ++I or I++ SHOULD BE UNCHECKED{++I} or UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

*Number of Instances Identified: 5*

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```
63: for (uint256 i = 0; i < arrayLength; ++i) {
84: for (uint256 i = 0; i < times; ++i) {
114: for (uint256 i = 0; i < original_validators.length; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
129: for (uint256 i = 0; i < numDeposits; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```
84: for (uint i = 0; i < minters_array.length; i++){
```

### G-02 UNCHECKING ARITHMETICS OPERATIONS THAT CANT UNDERFLOW or OVERFLOW

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an `unchecked` block: [Solidity Doc](https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic)

*Number of Instances Identified: 12*

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```
100: swapValidator(remove_idx, validators.length - 1);
```

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol

```
55: return storedTotalAssets_ + lastRewardAmount_;
60: uint256 unlockedRewards = (lastRewardAmount_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
61: storedTotalAssets_ + unlockedRewards;
67: storedTotalAssets -= amount;
72: storedTotalAssets += amount;
85: uint256 nextRewards = asset.balanceOf(address(this)) - storedTotalAssets_ - lastRewardAmount_;
87: storedTotalAssets = storedTotalAssets_ + lastRewardAmount_;
89: uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
97: currentWithheldETH += withheld_amt;
125: uint256 numDeposits = (address(this).balance - currentWithheldETH) / DEPOSIT_SIZE;
168: currentWithheldETH -= amount;

```


### G-03 IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

*Number of Instances Identified: 8*

If a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: `for (uint256 i = 0; i < numIterations; ++i) {` should be replaced with `for (uint256 i; i < numIterations; ++i) {`

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```
63: for (uint256 i = 0; i < arrayLength; ++i) {
84: for (uint256 i = 0; i < times; ++i) {
114: for (uint256 i = 0; i < original_validators.length; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
63: withholdRatio = 0;
64: currentWithheldETH = 0;
94: uint256 withheld_amt = 0;
129: for (uint256 i = 0; i < numDeposits; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```
84: for (uint i = 0; i < minters_array.length; i++){
```

### G-04 ++I COSTS LESS GAS COMPARED TO I++ OR I += 1 (SAME FOR --I VS I-- OR I -= 1)

*Number of Instances Identified: 1*

Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

-   `i += 1` is the most expensive form
-   `i++` costs 6 gas less than `i += 1`
-   `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

-   `i -= 1` is the most expensive form
-   `i--` costs 11 gas less than `i -= 1`
-   `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)


https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

```
for (uint i = 0; i < minters_array.length; i++){
```


### G-05 USE CUSTOM ERRORS RATHER THAN REVERT() or REQUIRE() STRINGS TO SAVE GAS

*Number of Instances Identified: 22*

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```
46: require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
137: require(numVals != 0, "Validator stack is empty");
182: require(numValidators() == 0, "Clear validator array first");
203: require(_timelock_address != address(0), "Zero address detected");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```
41: require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
46: require(minters[msg.sender] == true, "Only minters");
66: require(minter_address != address(0), "Zero address detected");
68: require(minters[minter_address] == false, "Address already exists");
77: require(minter_address != address(0), "Zero address detected");
78: require(minters[minter_address] == true, "Address nonexistant");
95: require(_timelock_address != address(0), "Zero address detected");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
79: require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
87: require(!submitPaused, "Submit is paused");
88: require(msg.value != 0, "Cannot submit 0");
122: require(!depositEtherPaused, "Depositing ETH is paused");
126: require(numDeposits > 0, "Not enough ETH in contract");
140: require(!activeValidators[pubKey], "Validator already has 32 ETH");
160: require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");
167: require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
171: require(success, "Invalid transfer");
193: require(success, "Invalid transfer");
200: require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```



### G-06 FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

*Number of Instances Identified: 19*


If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2), `DUP1`(3), `ISZERO`(3), `PUSH2`(3), `JUMPI`(10), `PUSH1`(3), `DUP1`(3), `REVERT`(0), `JUMPDEST`(1), `POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost


https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```
53: function addValidator(Validator calldata validator) public onlyByOwnGov {
61: function addValidators(Validator[] calldata validatorArray) external onlyByOwnGov {
69: function swapValidator(uint256 from_idx, uint256 to_idx) public onlyByOwnGov {
82: function popValidators(uint256 times) public onlyByOwnGov {
93: function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
181: function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
190: function clearValidatorArray() external onlyByOwnGov {
202:  function setTimelock(address _timelock_address) external onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```
53: function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {
59: function minter_mint(address m_address, uint256 m_amount) public onlyMinters {
65: function addMinter(address minter_address) public onlyByOwnGov {
76: function removeMinter(address minter_address) public onlyByOwnGov {
94: function setTimelock(address _timelock_address) public onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
159: function setWithholdRatio(uint256 newRatio) external onlyByOwnGov {
166: function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
177: function togglePauseSubmits() external onlyByOwnGov {
184: function togglePauseDepositEther() external onlyByOwnGov {
191: function recoverEther(uint256 amount) external onlyByOwnGov {
199: function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyByOwnGov {
```


### G-07 USAGE OF UINTS or INTS SMALLER THAN 32 BYTES - 256 BITS INCURS OVERHEAD

*Number of Instances Identified: 15*

> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol

```
42: constructor(ERC20 _underlying, uint32 _rewardsCycleLength)
64: uint8 v,
80: uint8 v,
```

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol

```
24: uint32 public immutable rewardsCycleLength;
27: uint32 public lastSync;
30: uint32 public rewardsCycleEnd;
33: uint192 public lastRewardAmount;
35: uint256 internal storedTotalAssets;
37: constructor(uint32 _rewardsCycleLength) {
48: uint192 lastRewardAmount_ = lastRewardAmount;
49: uint32 rewardsCycleEnd_ = rewardsCycleEnd;
50: uint32 lastSync_ = lastSync;
79: uint192 lastRewardAmount_ = lastRewardAmount;
80: uint32 timestamp = block.timestamp.safeCastTo32();
89: uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```


### G-08 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Number of Instances Identified: 4*

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol

```
67: storedTotalAssets -= amount;
72: storedTotalAssets += amount;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
97: currentWithheldETH += withheld_amt;
168:  currentWithheldETH -= amount;
```

### G-09 REQUIRE STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

*Number of Instances Identified: 1*

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167


```
require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```


### G-10 USING BOOLS FOR STORAGE INCURS OVERHEAD

*Number of Instances Identified: 2*


```
// Booleans are more expensive than uint256 or any type that takes up a full    // word because each write operation emits an extra SLOAD to first read the    // slot's contents, replace the bits taken up by the boolean, and then write    // back. This is the compiler's defense against contract upgrades and    // pointer aliasing, and it cannot be disabled.
```


https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L151

```
 activeValidators[pubKey] = true;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L69

```
minters[minter_address] = true;
```

### G-11 DUPLICATED REQUIRE() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

*Number of Instances Identified: 3*

The following require checks across all the contracts are used several times and can be refactored to a modifier

```
- require(minter_address != address(0), "Zero address detected");
- require(_timelock_address != address(0), "Zero address detected");
- require(success, "Invalid transfer");
```

### G-12 USING `> 0` COSTS MORE GAS THAN `!= 0` WHEN USED ON A `UINT` IN A `REQUIRE()` STATEMENT

*Number of Instances Identified: 2*

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
79: require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
126:  require(numDeposits > 0, "Not enough ETH in contract");
```

### G-13 Using both named returns and a return statement isn’t necessary

*Number of Instances Identified: 3*

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L70

```
function submitAndDeposit(address recipient) external payable returns (uint256 shares) {
```


https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol

```
67: external nonReentrant returns (uint256 shares) {
83: external nonReentrant returns (uint256 assets) {
```

### G-14 EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING


*Number of Instances Identified: 2*

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty `if`-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`)


https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L42-L45

```
constructor(ERC20 _underlying, uint32 _rewardsCycleLength) 
        ERC4626(_underlying, "Staked Frax Ether", "sfrxETH")
        xERC4626(_rewardsCycleLength)
    {} 
```


https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#L36-L41

```
   constructor( 
      address _creator_address,
      address _timelock_address
    ) 
    ERC20PermitPermissionedMint(_creator_address, _timelock_address, "Frax Ether", "frxETH") 
    {}

}
```


### G-15 ARRAY.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR- LOOP

*Number of Instances Identified: 2*

Reading array length at each iteration of the loop consumes more gas than necessary.

In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Here, Consider storing the array’s length in a variable before the for-loop, and use this new variable instead.

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

```
for (uint256 i = 0; i < original_validators.length; ++i) {
```


https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

```
for (uint i = 0; i < minters_array.length; i++){
```


### G-16 DONT COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

*Number of Instances Identified: 3*

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```
46: require(minters[msg.sender] == true, "Only minters");
68: require(minters[minter_address] == false, "Address already exists");
78: require(minters[minter_address] == true, "Address nonexistant");
```

