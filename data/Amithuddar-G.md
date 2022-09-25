1)++i costs less gas than i++, especially when it’s used in for-loops (--i/i-- too)

Saves 6 gas PER LOOP


File: 2022-09-frax\src\ERC20\ERC20PermitPermissionedMint.sol
  84,52:         for (uint i = 0; i < minters_array.length; i++){ 
  
 


2) ++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow,
as is the case when used in for- and while-loops    


File: 2022-09-frax\src\frxETHMinter.sol:

  129:         for (uint256 i = 0; i < numDeposits; ++i) {
 

File: 2022-09-frax\src\OperatorRegistry.sol:

   63:         for (uint256 i = 0; i < arrayLength; ++i) {
 

   84:         for (uint256 i = 0; i < times; ++i) {

File: 2022-09-frax\src\ERC20\ERC20PermitPermissionedMint.sol:

  84:         for (uint i = 0; i < minters_array.length; i++){ 



  114:             for (uint256 i = 0; i < original_validators.length; ++i) {


3)It costs more gas to initialize variables to zero than to let the default of zero be applied

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {  
 

File: 2022-09-frax\src\frxETHMinter.sol:

  129:         for (uint256 i = 0; i < numDeposits; ++i) {
 

File: 2022-09-frax\src\OperatorRegistry.sol:

   63:         for (uint256 i = 0; i < arrayLength; ++i) {
 

   84:         for (uint256 i = 0; i < times; ++i) {

File: 2022-09-frax\src\ERC20\ERC20PermitPermissionedMint.sol:

  84:         for (uint i = 0; i < minters_array.length; i++){ 



  114:             for (uint256 i = 0; i < original_validators.length; ++i) {


4)X = X + Y IS CHEAPER THAN X += Y 


File: 2022-09-frax\src\frxETHMinter.sol:

  97:             currentWithheldETH += withheld_amt;


5)Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code.
Savings are due to the compiler not having to create non-payable getter
functions for deployment calldata, and not adding another entry to the method ID table   
  
  
File: 2022-09-frax\src\frxETHMinter.sol:
  37  contract frxETHMinter is OperatorRegistry, ReentrancyGuard {    
  38:     uint256 public constant DEPOSIT_SIZE = 32 ether; // ETH 2.0 minimum deposit size
  39:     uint256 public constant RATIO_PRECISION = 1e6; // 1,000,000 
  40  

6)USE A MORE RECENT VERSION OF SOLIDITY

Use a solidity version of at least 0.8.2 to get compiler automatic inlining

Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads

Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value



File: 2022-09-frax\src\frxETH.sol:
  1  // SPDX-License-Identifier: GPL-2.0-or-later
  2: pragma solidity ^0.8.0;
  3  

File: 2022-09-frax\src\frxETHMinter.sol:
  1  // SPDX-License-Identifier: AGPL-3.0-only
  2: pragma solidity ^0.8.0;
  3  

File: 2022-09-frax\src\OperatorRegistry.sol:
  1  // SPDX-License-Identifier: AGPL-3.0-only
  2: pragma solidity ^0.8.0;
  3  

File: 2022-09-frax\src\sfrxETH.sol:
  1  // SPDX-License-Identifier: AGPL-3.0-only
  2: pragma solidity ^0.8.0;
  3  

File: 2022-09-frax\src\ERC20\ERC20PermitPermissionedMint.sol:
  1  //SPDX-License-Identifier: Unlicense
  2: pragma solidity ^0.8.0;
  3  


7) USING > 0 COSTS MORE GAS THAN != 0 WHEN USED ON A UINT IN A REQUIRE() STATEMENT

This change saves 6 gas per instance

File: 2022-09-frax\src\frxETHMinter.sol
  79,34:         require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
  126,29:         require(numDeposits > 0, "Not enough ETH in contract"); 
    
File: 2022-09-frax\src\frxETHMinter.sol
  79,9:         require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
  87,9:         require(!submitPaused, "Submit is paused");
  88,9:         require(msg.value != 0, "Cannot submit 0");
  122,9:         require(!depositEtherPaused, "Depositing ETH is paused");
  126,9:         require(numDeposits > 0, "Not enough ETH in contract");
  140,13:             require(!activeValidators[pubKey], "Validator already has 32 ETH");
  167,9:         require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
  171,9:         require(success, "Invalid transfer");
  193,9:         require(success, "Invalid transfer");
  200,9:         require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");

File: 2022-09-frax\src\OperatorRegistry.sol
  46,9:         require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
  137,9:         require(numVals != 0, "Validator stack is empty");
  182,9:         require(numValidators() == 0, "Clear validator array first");
  203,9:         require(_timelock_address != address(0), "Zero address detected");

File: 2022-09-frax\src\ERC20\ERC20PermitPermissionedMint.sol
  41,9:         require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
  46,8:        require(minters[msg.sender] == true, "Only minters");
  66,9:         require(minter_address != address(0), "Zero address detected");
  68,9:         require(minters[minter_address] == false, "Address already exists");
  77,9:         require(minter_address != address(0), "Zero address detected");
  78,9:         require(minters[minter_address] == true, "Address nonexistant");
  95,9:         require(_timelock_address != address(0), "Zero address detected"); 

8)<ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
The overheads outlined below are PER LOOP, 

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset
 

File: 2022-09-frax\src\OperatorRegistry.sol
 
  114,56:             for (uint256 i = 0; i < original_validators.length; ++i) {

File: 2022-09-frax\src\ERC20\ERC20PermitPermissionedMint.sol
  84,43:         for (uint i = 0; i < minters_array.length; i++){ 
  

9)Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code.
Savings are due to the compiler not having to create non-payable getter
functions for deployment calldata, and not adding another entry to the method ID table


File: 2022-09-frax\src\frxETHMinter.sol
  38,13:     uint256 public constant DEPOSIT_SIZE = 32 ether; // ETH 2.0 minimum deposit size
  39,13:     uint256 public constant RATIO_PRECISION = 1e6; // 1,000,000 


10)STATE VARIABLES CAN BE PACKED INTO FEWER STORAGE SLOTS
If variables occupying the same slot are both written the same function or by the constructor,
avoids a separate Gsset (20000 gas). Reads of the variables are also cheaper

File: 2022-09-frax\src\sfrxETH.sol
  
60      uint256 assets,
        address receiver,
        uint256 deadline,
        bool approveMax,
        uint8 v,
        bytes32 r,
        bytes32 s
		
		Variable ordering with 5 slots instead of the current 6:
		uint256(32):assets,uint256(32):deadline, address(20):receiver,bool(1):approveMax,
		uint8(1):v,bytes32(32):r,bytes32(32):s
		
		
76		uint256 shares,
        address receiver,
        uint256 deadline,
        bool approveMax,
        uint8 v,
        bytes32 r,
        bytes32 s
		
		Variable ordering with 5 slots instead of the current 6:
		uint256(32):shares,uint256(32):deadline, address(20):receiver,bool(1):approveMax,
		uint8(1):v,bytes32(32):r,bytes32(32):s
		
		