## (1) Use Pre Increment Instead of Post Increment

Pre-increment uses a little less gas.

***

for (uint i = 0; i < minters_array.length; i++){
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

## (2) Cache Array Length

Array length should be cached instead of requesting it over and over in a for loop.
The cost per iteration depends on the array type, 100 gas for storage, 3 each for memory and calldata.
Caching the value costs only 3 gas.

***

for (uint i = 0; i < minters_array.length; i++){
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

for (uint256 i = 0; i < original_validators.length; ++i) {
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

## (3) Use Custom Errors Instead of Strings for Revert and Require

Require strings and revert strings use about 50 more gas than custom errors.

***

require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L41

require(minters[msg.sender] == true, "Only minters");
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L46

require(minter_address != address(0), "Zero address detected");
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L66

require(minters[minter_address] == false, "Address already exists");
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L68

require(minter_address != address(0), "Zero address detected");
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L77

require(minters[minter_address] == true, "Address nonexistant");
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78

require(_timelock_address != address(0), "Zero address detected");
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L95

require(!submitPaused, "Submit is paused");
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L87

require(msg.value != 0, "Cannot submit 0");
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L88

require(!depositEtherPaused, "Depositing ETH is paused");
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L122

require(numDeposits > 0, "Not enough ETH in contract");
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126

require(!activeValidators[pubKey], "Validator already has 32 ETH");
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L140

require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L160

require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167

require(success, "Invalid transfer");
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L171

require(success, "Invalid transfer");
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L193

require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L200

require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L46

require(numVals != 0, "Validator stack is empty");
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L137

require(numValidators() == 0, "Clear validator array first");
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L182

require(_timelock_address != address(0), "Zero address detected");
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L203

## (4) Limit Message Strings for Revert and Require to 32 Bytes

Require strings and revert strings longer than 32 bytes need MSTORE (3 gas).
Try to use shorter descriptions. Custom errors would be even better (see above).

***

require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167

## (5) Boolean Variables Should not be Used for Storage

Using UINT256(1) for true and UINT256(2) for false costs less gas than using boolean variables.

***

mapping(address => bool) public minters; // Mapping is also used for faster verification
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L20

mapping(bytes => bool) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L43

bool public submitPaused;
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L49

bool public depositEtherPaused;
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L50

## (6) Do not Initialize to Default Value

Non-const variables should not be initialized to their default value.
Just using the default costs less gas.
For example use: uint256 abc;
Instead of: uint256 abc=0;

***

for (uint i = 0; i < minters_array.length; i++){
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

uint256 withheld_amt = 0;
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94

for (uint256 i = 0; i < numDeposits; ++i) {
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129

for (uint256 i = 0; i < arrayLength; ++i) {
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63

for (uint256 i = 0; i < times; ++i) {
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84

for (uint256 i = 0; i < original_validators.length; ++i) {
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114


