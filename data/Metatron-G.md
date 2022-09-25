### [G-01] ++i costs less gas compared to i++

++i costs **about 5 gas less per iteration** compared to i++ for unsigned integer.
This statement is true even with the optimizer enabled.
Summarized my results where i is used in a loop, is unsigned integer, and you safely can be changed to ++i without changing any behavior,

I've found only 1 localtion:

```solidty
src/ERC20/ERC20PermitPermissionedMint.sol:
  83          // 'Delete' from the array by setting the address to 0x0
  84:         for (uint i = 0; i < minters_array.length; i++){ 
  85              if (minters_array[i] == minter_address) {
```

---------------------------------------------------------------------------

### [G-02] An arrays length should be cached to save gas in for-loops

An array’s length should be cached to save gas in for-loops
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset).
Caching the array length in the stack saves around **3 gas per iteration**.

I've found 2 locations in 2 files:

```solidty
src/OperatorRegistry.sol:
  113              // Fill the new validators array with all except the value to remove
  114:             for (uint256 i = 0; i < original_validators.length; ++i) {
  115                  if (i != remove_idx) {

src/ERC20/ERC20PermitPermissionedMint.sol:
  83          // 'Delete' from the array by setting the address to 0x0
  84:         for (uint i = 0; i < minters_array.length; i++){ 
  85              if (minters_array[i] == minter_address) {
```

---------------------------------------------------------------------------

### [G-03] Using default values is cheaper than assignment

If a variable is not set/initialized, it is assumed to have the default value 0 for uint, and false for boolean.
Explicitly initializing it with its default value is an anti-pattern and wastes gas.
For example: ```uint8 i = 0;``` should be replaced with ```uint8 i;```

I've found 6 locations in 3 files:

```
src/frxETHMinter.sol:
   93          // Track the amount of ETH that we are keeping
   94:         uint256 withheld_amt = 0;
   95          if (withholdRatio != 0) {

  128          // Give each deposit chunk to an empty validator
  129:         for (uint256 i = 0; i < numDeposits; ++i) {
  130              // Get validator information

src/OperatorRegistry.sol:
   62          uint arrayLength = validatorArray.length;
   63:         for (uint256 i = 0; i < arrayLength; ++i) {
   64              addValidator(validatorArray[i]);

   83          // Loop through and remove validator entries at the end
   84:         for (uint256 i = 0; i < times; ++i) {
   85              validators.pop();

  113              // Fill the new validators array with all except the value to remove
  114:             for (uint256 i = 0; i < original_validators.length; ++i) {
  115                  if (i != remove_idx) {

src/ERC20/ERC20PermitPermissionedMint.sol:
  83          // 'Delete' from the array by setting the address to 0x0
  84:         for (uint i = 0; i < minters_array.length; i++){ 
  85              if (minters_array[i] == minter_address) {
```

---------------------------------------------------------------------------

### [G-04] != 0 is cheaper than > 0

!= 0 costs less gas compared to > 0 for unsigned integers even when optimizer enabled
All of the following findings are uint (E&OE) so >0 and != have exactly the same effect.
** saves 6 gas ** each

I've found 2 locations in 1 file:

```
src/frxETHMinter.sol:
   78          uint256 sfrxeth_recieved = sfrxETHToken.deposit(msg.value, recipient);
   79:         require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
   80  

  125          uint256 numDeposits = (address(this).balance - currentWithheldETH) / DEPOSIT_SIZE;
  126:         require(numDeposits > 0, "Not enough ETH in contract");
  127  
```

---------------------------------------------------------------------------

### [G-05] Custom errors save gas

From solidity 0.8.4 and above,
Custom errors from are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)
Source: https://blog.soliditylang.org/2021/04/21/custom-errors/:
```Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.```

I've found 22 locations in 3 files:

```
src/frxETHMinter.sol:
  78          uint256 sfrxeth_recieved = sfrxETHToken.deposit(msg.value, recipient);
  79:         require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
  80  

   86          // Initial pause and value checks
   87:         require(!submitPaused, "Submit is paused");
   88:         require(msg.value != 0, "Cannot submit 0");
   89  

  121          // Initial pause check
  122:         require(!depositEtherPaused, "Depositing ETH is paused");
  123  

  125          uint256 numDeposits = (address(this).balance - currentWithheldETH) / DEPOSIT_SIZE;
  126:         require(numDeposits > 0, "Not enough ETH in contract");
  127  

  139              // until withdrawals are allowed
  140:             require(!activeValidators[pubKey], "Validator already has 32 ETH");
  141  

  159      function setWithholdRatio(uint256 newRatio) external onlyByOwnGov {
  160:         require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");
  161          withholdRatio = newRatio;

  166      function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
  167:         require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
  168          currentWithheldETH -= amount;

  170          (bool success,) = payable(to).call{ value: amount }("");
  171:         require(success, "Invalid transfer");
  172  

  192          (bool success,) = address(owner).call{ value: amount }("");
  193:         require(success, "Invalid transfer");
  194  

  199      function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyByOwnGov {
  200:         require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
  201  

src/OperatorRegistry.sol:
   45      modifier onlyByOwnGov() {
   46:         require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
   47          _;

  136          uint numVals = numValidators();
  137:         require(numVals != 0, "Validator stack is empty");
  138  

  181      function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
  182:         require(numValidators() == 0, "Clear validator array first");
  183          curr_withdrawal_pubkey = _new_withdrawal_pubkey;

  202      function setTimelock(address _timelock_address) external onlyByOwnGov {
  203:         require(_timelock_address != address(0), "Zero address detected");
  204          timelock_address = _timelock_address;

src/ERC20/ERC20PermitPermissionedMint.sol:
  40      modifier onlyByOwnGov() {
  41:         require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
  42          _;

  45      modifier onlyMinters() {
  46:        require(minters[msg.sender] == true, "Only minters");
  47          _;

  65      function addMinter(address minter_address) public onlyByOwnGov {
  66:         require(minter_address != address(0), "Zero address detected");
  67  
  68:         require(minters[minter_address] == false, "Address already exists");
  69          minters[minter_address] = true; 

  76      function removeMinter(address minter_address) public onlyByOwnGov {
  77:         require(minter_address != address(0), "Zero address detected");
  78:         require(minters[minter_address] == true, "Address nonexistant");
  79          

  94      function setTimelock(address _timelock_address) public onlyByOwnGov {
  95:         require(_timelock_address != address(0), "Zero address detected"); 
  96          timelock_address = _timelock_address;

```

---------------------------------------------------------------------------

### [G-06] Upgrade pragma to 0.8.16 to save gas?

Across the whole solution, the declared pragma is 0.8.0
Upgrading to 0.8.16 has many benefits, cheaper on gas is one of them.
The following information is regarding 0.8.15 over 0.8.14. Assume that over 0.8.0 the save is higher!
```
According to the release note of 0.8.15: https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/
The benchmark shows saving of 2.5-10% Bytecode size,
Saving 2-8% Deployment gas,
And saving up to 6.2% Runtime gas.
```
*0.8.16 saves even more over 0.8.15
