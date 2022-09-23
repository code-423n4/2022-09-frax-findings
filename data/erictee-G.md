### [G-01] Using bools for storage incurs overhead.


#### Impact
 
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 Use ```uint256(1)``` and ```uint256(2)``` for true/false to avoid a Gwarmaccess ([100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past


#### Findings:
```
src/frxETHMinter.sol:L43    mapping(bytes => bool) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them

src/frxETHMinter.sol:L49    bool public submitPaused;

src/frxETHMinter.sol:L50    bool public depositEtherPaused;

src/ERC20/ERC20PermitPermissionedMint.sol:L20    mapping(address => bool) public minters; // Mapping is also used for faster verification

```
###  [G-02] Cache Array Length Outside of Loop


#### Impact
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.


#### Findings:
```

src/OperatorRegistry.sol:L114            for (uint256 i = 0; i < original_validators.length; ++i) {

src/ERC20/ERC20PermitPermissionedMint.sol:L84        for (uint i = 0; i < minters_array.length; i++){ 

```
### [G-03] Use custom errors rather than ```revert()```/```require()``` string to save gas


#### Impact
Custom errors are available from solidity version 0.8.4, it saves around 50 gas each time they are hit by avoiding having to allocate and store the revert string.


#### Findings:
```
src/frxETHMinter.sol:L79        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

src/frxETHMinter.sol:L87        require(!submitPaused, "Submit is paused");

src/frxETHMinter.sol:L88        require(msg.value != 0, "Cannot submit 0");

src/frxETHMinter.sol:L122        require(!depositEtherPaused, "Depositing ETH is paused");

src/frxETHMinter.sol:L126        require(numDeposits > 0, "Not enough ETH in contract");

src/frxETHMinter.sol:L140            require(!activeValidators[pubKey], "Validator already has 32 ETH");

src/frxETHMinter.sol:L167        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");

src/frxETHMinter.sol:L171        require(success, "Invalid transfer");

src/frxETHMinter.sol:L193        require(success, "Invalid transfer");

src/frxETHMinter.sol:L200        require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");

src/OperatorRegistry.sol:L46        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

src/OperatorRegistry.sol:L137        require(numVals != 0, "Validator stack is empty");

src/OperatorRegistry.sol:L182        require(numValidators() == 0, "Clear validator array first");

src/OperatorRegistry.sol:L203        require(_timelock_address != address(0), "Zero address detected");

src/ERC20/ERC20PermitPermissionedMint.sol:L41        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

src/ERC20/ERC20PermitPermissionedMint.sol:L46       require(minters[msg.sender] == true, "Only minters");

src/ERC20/ERC20PermitPermissionedMint.sol:L66        require(minter_address != address(0), "Zero address detected");

src/ERC20/ERC20PermitPermissionedMint.sol:L68        require(minters[minter_address] == false, "Address already exists");

src/ERC20/ERC20PermitPermissionedMint.sol:L77        require(minter_address != address(0), "Zero address detected");

src/ERC20/ERC20PermitPermissionedMint.sol:L78        require(minters[minter_address] == true, "Address nonexistant");

src/ERC20/ERC20PermitPermissionedMint.sol:L95        require(_timelock_address != address(0), "Zero address detected"); 

```
### [G-04] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
src/frxETH.sol:L41    {}

src/sfrxETH.sol:L45    {}

```
### [G-05] Using ```> 0``` costs more gas than ```!= 0``` when used on a uint in a ```require()``` statement.


#### Impact
When working with unsigned integer types, comparisons with != 0 are cheaper than with > 0 . This changes saves 6 gas per instance.


#### Findings:
```
src/frxETHMinter.sol:L79        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

src/frxETHMinter.sol:L126        require(numDeposits > 0, "Not enough ETH in contract");

```
### [G-06] Use a more recent version of solidity.


#### Impact
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.


#### Findings:
```
src/frxETH.sol:L2      pragma solidity ^0.8.0;

src/frxETHMinter.sol:L2      pragma solidity ^0.8.0;

src/sfrxETH.sol:L2      pragma solidity ^0.8.0;

src/OperatorRegistry.sol:L2      pragma solidity ^0.8.0;

src/lib/xERC4626.sol:L4      pragma solidity ^0.8.0;

src/ERC20/ERC20PermitPermissionedMint.sol:L2      pragma solidity ^0.8.0;

```
### [G-07] Functions guaranteed to revert when called by normal users can be declared as payable.


#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.


#### Findings:
```
src/frxETHMinter.sol:L159    function setWithholdRatio(uint256 newRatio) external onlyByOwnGov {

src/frxETHMinter.sol:L177    function togglePauseSubmits() external onlyByOwnGov {

src/frxETHMinter.sol:L184    function togglePauseDepositEther() external onlyByOwnGov {

src/frxETHMinter.sol:L191    function recoverEther(uint256 amount) external onlyByOwnGov {

src/frxETHMinter.sol:L199    function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyByOwnGov {

src/OperatorRegistry.sol:L53    function addValidator(Validator calldata validator) public onlyByOwnGov {

src/OperatorRegistry.sol:L61    function addValidators(Validator[] calldata validatorArray) external onlyByOwnGov {

src/OperatorRegistry.sol:L69    function swapValidator(uint256 from_idx, uint256 to_idx) public onlyByOwnGov {

src/OperatorRegistry.sol:L82    function popValidators(uint256 times) public onlyByOwnGov {

src/OperatorRegistry.sol:L93    function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {

src/OperatorRegistry.sol:L181    function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {

src/OperatorRegistry.sol:L190    function clearValidatorArray() external onlyByOwnGov {

src/OperatorRegistry.sol:L202    function setTimelock(address _timelock_address) external onlyByOwnGov {

src/ERC20/ERC20PermitPermissionedMint.sol:L53    function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {

src/ERC20/ERC20PermitPermissionedMint.sol:L59    function minter_mint(address m_address, uint256 m_amount) public onlyMinters {

src/ERC20/ERC20PermitPermissionedMint.sol:L65    function addMinter(address minter_address) public onlyByOwnGov {

src/ERC20/ERC20PermitPermissionedMint.sol:L76    function removeMinter(address minter_address) public onlyByOwnGov {

src/ERC20/ERC20PermitPermissionedMint.sol:L94    function setTimelock(address _timelock_address) public onlyByOwnGov {

```
### [G-08] ```X += Y``` costs more gas than ```X = X + Y``` for state variables.


#### Impact
Consider changing ```X += Y``` to ```X = X + Y``` to save gas.


#### Findings:
```
src/frxETHMinter.sol:L97            currentWithheldETH += withheld_amt;

src/lib/xERC4626.sol:L72        storedTotalAssets += amount;

```
### [G-09] ++i costs less gas than i++, especially when it's used in for loops.


#### Impact
Saves 6 gas per loop.


#### Findings:
```
src/ERC20/ERC20PermitPermissionedMint.sol:L84        for (uint i = 0; i < minters_array.length; i++){ 

```
### [G-10] Using private rather than public for constants to saves gas.


#### Impact
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.


#### Findings:
```
src/frxETHMinter.sol:L38    uint256 public constant DEPOSIT_SIZE = 32 ether; // ETH 2.0 minimum deposit size

src/frxETHMinter.sol:L39    uint256 public constant RATIO_PRECISION = 1e6; // 1,000,000 

```

### [G-11] Revert message greater than 32 bytes


#### Impact
Keep revert message lower than or equal to 32 bytes to save gas.


#### Findings:
```
src/frxETHMinter.sol:L167        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");

```

### [G-12] Explicit initialization with zero not required


#### Impact
Explicit initialization with zero is not required for variable declaration because uints are 0 by default. Removing this will reduce contract size and save a bit of gas.


#### Findings:
```
src/frxETHMinter.sol:L94        uint256 withheld_amt = 0;

src/frxETHMinter.sol:L129        for (uint256 i = 0; i < numDeposits; ++i) {

src/OperatorRegistry.sol:L63        for (uint256 i = 0; i < arrayLength; ++i) {

src/OperatorRegistry.sol:L84        for (uint256 i = 0; i < times; ++i) {

src/OperatorRegistry.sol:L114            for (uint256 i = 0; i < original_validators.length; ++i) {

src/ERC20/ERC20PermitPermissionedMint.sol:L84        for (uint i = 0; i < minters_array.length; i++){ 

```

