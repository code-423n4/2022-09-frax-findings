


### [G01] State variables only set in the constructor should be declared `immutable`


#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::16 => address public timelock_address;
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::19 => address[] public minters_array; // Allowed to mint
2022-09-frax/src/OperatorRegistry.sol::38 => address public timelock_address;
2022-09-frax/src/frxETHMinter.sol::38 => uint256 public constant DEPOSIT_SIZE = 32 ether; // ETH 2.0 minimum deposit size
2022-09-frax/src/frxETHMinter.sol::39 => uint256 public constant RATIO_PRECISION = 1e6; // 1,000,000
2022-09-frax/src/frxETHMinter.sol::41 => uint256 public withholdRatio; // What we keep and don't deposit whenever someone submit()'s ETH
2022-09-frax/src/frxETHMinter.sol::42 => uint256 public currentWithheldETH; // Needed for internal tracking
2022-09-frax/src/frxETHMinter.sol::49 => bool public submitPaused;
2022-09-frax/src/frxETHMinter.sol::50 => bool public depositEtherPaused;
2022-09-frax/src/xERC4626.sol::27 => uint32 public lastSync;
2022-09-frax/src/xERC4626.sol::30 => uint32 public rewardsCycleEnd;
2022-09-frax/src/xERC4626.sol::33 => uint192 public lastRewardAmount;
```




### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor, avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper
#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::16 => address public timelock_address;
2022-09-frax/src/OperatorRegistry.sol::38 => address public timelock_address;
```


### [G03] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::84 => for (uint i = 0; i < minters_array.length; i++){
2022-09-frax/src/OperatorRegistry.sol::114 => for (uint256 i = 0; i < original_validators.length; ++i) {
```




### [G04] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops


#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::84 => for (uint i = 0; i < minters_array.length; i++){
2022-09-frax/src/OperatorRegistry.sol::63 => for (uint256 i = 0; i < arrayLength; ++i) {
2022-09-frax/src/OperatorRegistry.sol::84 => for (uint256 i = 0; i < times; ++i) {
2022-09-frax/src/OperatorRegistry.sol::114 => for (uint256 i = 0; i < original_validators.length; ++i) {
2022-09-frax/src/frxETHMinter.sol::129 => for (uint256 i = 0; i < numDeposits; ++i) {
```




### [G05] `require()`/`revert()` strings longer than 32 bytes cost extra gas


#### Findings:
```
2022-09-frax/src/frxETHMinter.sol::167 => require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```




### [G06] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement


#### Findings:
```
2022-09-frax/src/frxETHMinter.sol::79 => require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
2022-09-frax/src/frxETHMinter.sol::126 => require(numDeposits > 0, "Not enough ETH in contract");
```




### [G07] It costs more gas to initialize variables to zero than to let the default of zero be applied


#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::84 => for (uint i = 0; i < minters_array.length; i++){
2022-09-frax/src/OperatorRegistry.sol::63 => for (uint256 i = 0; i < arrayLength; ++i) {
2022-09-frax/src/OperatorRegistry.sol::84 => for (uint256 i = 0; i < times; ++i) {
2022-09-frax/src/OperatorRegistry.sol::114 => for (uint256 i = 0; i < original_validators.length; ++i) {
2022-09-frax/src/frxETHMinter.sol::63 => withholdRatio = 0; // No ETH is withheld initially
2022-09-frax/src/frxETHMinter.sol::64 => currentWithheldETH = 0;
2022-09-frax/src/frxETHMinter.sol::94 => uint256 withheld_amt = 0;
2022-09-frax/src/frxETHMinter.sol::129 => for (uint256 i = 0; i < numDeposits; ++i) {
```




### [G08] `++i` costs less gas than `i++`, especially when it’s used in forloops (`--i`/`i--` too)


#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::84 => for (uint i = 0; i < minters_array.length; i++){
```


### [G09] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed
#### Findings:
```
2022-09-frax/src/sfrxETH.sol::42 => constructor(ERC20 _underlying, uint32 _rewardsCycleLength)
2022-09-frax/src/sfrxETH.sol::64 => uint8 v,
2022-09-frax/src/sfrxETH.sol::80 => uint8 v,
2022-09-frax/src/xERC4626.sol::24 => uint32 public immutable rewardsCycleLength;
2022-09-frax/src/xERC4626.sol::27 => uint32 public lastSync;
2022-09-frax/src/xERC4626.sol::30 => uint32 public rewardsCycleEnd;
2022-09-frax/src/xERC4626.sol::33 => uint192 public lastRewardAmount;
2022-09-frax/src/xERC4626.sol::37 => constructor(uint32 _rewardsCycleLength) {
2022-09-frax/src/xERC4626.sol::48 => uint192 lastRewardAmount_ = lastRewardAmount;
2022-09-frax/src/xERC4626.sol::49 => uint32 rewardsCycleEnd_ = rewardsCycleEnd;
2022-09-frax/src/xERC4626.sol::50 => uint32 lastSync_ = lastSync;
2022-09-frax/src/xERC4626.sol::79 => uint192 lastRewardAmount_ = lastRewardAmount;
2022-09-frax/src/xERC4626.sol::80 => uint32 timestamp = block.timestamp.safeCastTo32();
2022-09-frax/src/xERC4626.sol::89 => uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```




### [G10] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function


#### Findings:
```

2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::66 => require(minter_address != address(0), "Zero address detected");
2022-09-frax/src/frxETHMinter.sol::171 => require(success, "Invalid transfer");
```




### [G11] `require()` or `revert()` statements that check input arguments should be at the top of the function

#### Impact
Checks that involve constants should come before checks that involve state variables
#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::66 => require(minter_address != address(0), "Zero address detected");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::77 => require(minter_address != address(0), "Zero address detected");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::95 => require(_timelock_address != address(0), "Zero address detected");
2022-09-frax/src/OperatorRegistry.sol::203 => require(_timelock_address != address(0), "Zero address detected");
```




### [G12] Use custom errors rather than `revert()`/`require()` strings to save deployment gas


#### Findings:
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
2022-09-frax/src/frxETHMinter.sol::79 => require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
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




### [G13] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
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
2022-09-frax/src/frxETHMinter.sol::166 => function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
2022-09-frax/src/frxETHMinter.sol::177 => function togglePauseSubmits() external onlyByOwnGov {
2022-09-frax/src/frxETHMinter.sol::184 => function togglePauseDepositEther() external onlyByOwnGov {
2022-09-frax/src/frxETHMinter.sol::191 => function recoverEther(uint256 amount) external onlyByOwnGov {
2022-09-frax/src/frxETHMinter.sol::199 => function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyByOwnGov {
```




### [G14] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.10 to have external calls skip
 contract existence checks if the external call has a return value
#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/OperatorRegistry.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETH.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETHMinter.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/sfrxETH.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/xERC4626.sol::4 => pragma solidity ^0.8.0;
```




### [G15] Use simple comparison in trinary logic

#### Impact
The comparison operators >= and <= use more gas than >, 
<, or ==. Replacing the  >= and ≤ operators with a comparison 
operator that has an opcode in the EVM saves gas.

### Recommended Mitigation Steps

Replace the comparison operator and reverse the logic to save gas using the suggestions above.

#### Findings:
```
2022-09-frax/src/sfrxETH.sol::50 => if (block.timestamp >= rewardsCycleEnd) { syncRewards(); }
2022-09-frax/src/xERC4626.sol::52 => if (block.timestamp >= rewardsCycleEnd_) {
```




### [G16] Use `calldata` instead of `memory` for function parameters

#### Impact
Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.

There are several cases of function arguments using memory instead of calldata
#### Findings:
```
2022-09-frax/src/OperatorRegistry.sol::181 => function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
```



### [G17] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done at some places, it’s not consistently done in the solution.
#### Findings:
```
2022-09-frax/src/frxETHMinter.sol::200 => require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```


### [G18] Remove unused local variable


#### Findings:
```
2022-09-frax/src/frxETHMinter.sol::170 => (bool success,) = payable(to).call{ value: amount }("");
2022-09-frax/src/frxETHMinter.sol::192 => (bool success,) = address(owner).call{ value: amount }("");
```




### [G19] Using `bools` for storage incurs overhead


#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::20 => mapping(address => bool) public minters; // Mapping is also used for faster verification
2022-09-frax/src/frxETHMinter.sol::43 => mapping(bytes => bool) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them
2022-09-frax/src/frxETHMinter.sol::49 => bool public submitPaused;
2022-09-frax/src/frxETHMinter.sol::50 => bool public depositEtherPaused;
```


### [G20] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code. Savings are due to the compiler not having to create non-payable 
getter functions for deployment calldata, and not adding another entry 
to the method ID table
#### Findings:
```
2022-09-frax/src/frxETHMinter.sol::38 => uint256 public constant DEPOSIT_SIZE = 32 ether; // ETH 2.0 minimum deposit size
2022-09-frax/src/frxETHMinter.sol::39 => uint256 public constant RATIO_PRECISION = 1e6; // 1,000,000
```




### [G21] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
2022-09-frax/src/frxETH.sol::41 => {}
2022-09-frax/src/sfrxETH.sol::45 => {}
```


### [G22] Don’t compare boolean expressions to boolean literals

#### Impact
if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)
#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::46 => require(minters[msg.sender] == true, "Only minters");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::68 => require(minters[minter_address] == false, "Address already exists");
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::78 => require(minters[minter_address] == true, "Address nonexistant");
```




### [G23] Optimize names to save gas

#### Impact
public/external function names and public member variable names can be optimized to save gas. See this
 link for an example of how it works. Below are the interfaces/abstract 
contracts that can be optimized so that the most frequently-called 
functions use the least amount of gas possible during method lookup. 
Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, per sorted position shifted
#### Findings:
```
2022-09-frax/src/xERC4626.sol::20 => abstract contract xERC4626 is IxERC4626, ERC4626 {
```


