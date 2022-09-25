# GAS
## Public function visibility can be made external
### Summary
Functions should have the strictest visibility possible. Public functions may lead to more gas usage by forcing the copy of their parameters to memory from calldata.
### Details
If a function is never called from the contract it should be marked as external. This will save gas.
### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L93-L122
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L76-L92
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L54-L56
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L94-L98
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L53-L56
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L65-L73
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L82-L89
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L59-L62


### Mitigation
Consider changing visibility from public to external.


## use of custom errors rather than revert() / require() error message
### Summary
Custom errors reduce 38 gas if the condition is met and 22 gas otherwise.
Also reduces contract size and deployment costs.
### Details
Since version 0.8.4 the use of custom errors rather than revert() / require() saves gas as noticed in
https://blog.soliditylang.org/2021/04/21/custom-errors/
https://github.com/code-423n4/2022-04-pooltogether-findings/issues/13

### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L41
        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L46
       require(minters[msg.sender] == true, "Only minters");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L66
        require(minter_address != address(0), "Zero address detected");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L68
        require(minters[minter_address] == false, "Address already exists");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L77
        require(minter_address != address(0), "Zero address detected");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L78
        require(minters[minter_address] == true, "Address nonexistant");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L95
        require(_timelock_address != address(0), "Zero address detected"); 

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L79
        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L87
        require(!submitPaused, "Submit is paused");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L88
        require(msg.value != 0, "Cannot submit 0");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L122
        require(!depositEtherPaused, "Depositing ETH is paused");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L126
        require(numDeposits > 0, "Not enough ETH in contract");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L140
            require(!activeValidators[pubKey], "Validator already has 32 ETH");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L167
        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L171
        require(success, "Invalid transfer");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L193
        require(success, "Invalid transfer");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L200
        require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20
 Transfer failed");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L46
        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L137
        require(numVals != 0, "Validator stack is empty");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L182
        require(numValidators() == 0, "Clear validator array first");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L203
        require(_timelock_address != address(0), "Zero address detected");


### Mitigation
replace each error message in a require by a custom error

## duplicated require() check should be refactored
### Summary
duplicated require() / revert() checks should be
refactored to a modifier or function to save gas
### Details
Event appears twice and can be reduced

### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L66
        require(minter_address != address(0), "Zero address detected");
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L77
        require(minter_address != address(0), "Zero address detected");
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L95
        require(_timelock_address != address(0), "Zero address detected"); 
- - - -
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L171
        require(success, "Invalid transfer");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L193
        require(success, "Invalid transfer");


### Mitigation
refactor this checks to different functions to save gas

## use != rather than >0 for unsigned integers in require() statements
### Summary
When the optimizer is enabled, gas is wasted by doing a greater-than operation, rather than a not-equals operation inside require() statements. When Using != , the optimizer is able to avoid the EQ, ISZERO, and associated operations, by relying on the JUMPI that comes afterwards, which itself checks for zero.
### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L79
        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L126
        require(numDeposits > 0, "Not enough ETH in contract");


### Mitigation
Use != 0 rather than > 0 for unsigned integers in require()  statements.

## Reduce the size of error messages (Long revert Strings)
### Summary
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L167

### Mitigation
Consider shortening the revert strings to fit in 32 bytes

## usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
### Summary
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

### Details
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 
Use a larger size than downcast where needed

### Github Permalinks
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L22-L33

### Mitigation
Consider using some data type that uses 32 bytes, for example uint256

## Using bools for storage incurs overhead { 
### Summary
Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

### Details
Here is one example of OpenZeppelin about this optimization
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L49
    bool public submitPaused;

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L50
    bool public depositEtherPaused;
### Mitigation
Consider using uint256 with values 0 and 1 rather than bool

## Using private rather than public for constants saves gas
### Summary
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L38
    uint256 public constant DEPOSIT_SIZE = 32 ether; // ETH 2.0 minimum deposit size

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L39
    uint256 public constant RATIO_PRECISION = 1e6; // 1,000,000 

### Mitigation
Consider replacing public for private in constants for gas saving.

## Explicit initialization
### Summary
It is not needed to initialize variables to the default value. Explicitly initializing it with its default value is an anti-pattern and wastes gas.
### Details
If a variable is not set/initialized, it is assumed to have the default value ( 0 for uint, false for bool, address(0) for address…). 

### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L94
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L63
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L64
### Mitigation
Don't initialize variables to default value

## Index initialized in for loop
### Summary
In for loops is not needed to initialize indexes to 0 as it is the default uint/int value. This saves gas.

### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L84
        for (uint i = 0; i < minters_array.length; i++){ 

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L129
        for (uint256 i = 0; i < numDeposits; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L63
        for (uint256 i = 0; i < arrayLength; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L84
        for (uint256 i = 0; i < times; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L114
            for (uint256 i = 0; i < original_validators.length; ++i) {


### Mitigation
Don't initialize variables to default value


## use of i++ in loop rather than ++i
### Summary
++i costs less gas than i++, especially when it's used in for loops
### Details
using ++i doesn't affect the flow of regular for loops and improves gas cost

### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L84
        for (uint i = 0; i < minters_array.length; i++){ 
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L71


### Mitigation
Substitute to ++i 

## increments can be unchecked in loops
### Summary
Unchecked operations as the ++i on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:

    for (uint256 i; i < numIterations; i++) {
    // ...
    }

    to

    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.

### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L84
        for (uint i = 0; i < minters_array.length; i++){ 

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L129
        for (uint256 i = 0; i < numDeposits; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L63
        for (uint256 i = 0; i < arrayLength; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L84
        for (uint256 i = 0; i < times; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L114
            for (uint256 i = 0; i < original_validators.length; ++i) {

### Mitigation
Add unchecked ++i at the end of all the for loop where it's not expected to overflow and remove them from the for header


## <array>.length should no be looked up in every loop of a for-loop
### Summary
In loops not assigning the length to a variable so memory accessed a lot (caching local variables)

### Details
The overheads outlined below are PER LOOP, excluding the first loop
storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)

### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L84
        for (uint i = 0; i < minters_array.length; i++){ 

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L114
            for (uint256 i = 0; i < original_validators.length; ++i) {


### Mitigation
Assign the length of the array.length to a local variable in loops for gas savings



## <X> += <Y> costs more gas than <X> = <X> + <Y> for state variables
### Summary
x+=y costs more gas than x=x+y for state variables
Same happens with -=
### Github Permalinks
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L72
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L97
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L67
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L72
### Mitigation
Don't use += for state variables as it cost more gas.

## Use calldata instead of memory for function parameters
### Summary
It is generally cheaper to load variables directly from calldata, rather than copying them to memory. 
### Details
Only use memory if the variable needs to be modified
### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L171-L177
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L181-L186

### Mitigation
Use calldata rather than memory in external functions where the parameter is not modified but only read

## Unused named returns
### Summary
Using both named returns and a return statement isn’t necessary. Removing one of those can improve code clarity 
### Details
Also as returns variable is ignored, it wastes extra gas

### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L70
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L70
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L86
### Mitigation
Remove return or returns when both used

## Retrieve internal variables directly
### Summary
Save ~30 gas per call to each function

### Details
Rather than calling from inside the code the function, it can be directly read as for example `validators.length` rather than `numValidators()` in order to save gas
### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L136
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L182
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L197-L199
### Mitigation
Retrieve internal variables directly rather than calling the retrieving function

## Variables should be cached when used several times
### Summary
Variables read more than once improves gas usage when cached into local variable
### Details
In loops or state variables, this is even more gas saving

### Github Permalinks
`submitPaused`
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L178-L180

`depositEtherPaused`
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L185-L187
### Mitigation
Cache variables used more than one into a local variable.


## Make immutable state variables that do not change but assigned in the constructor
### Summary
State variables which value isn't changed by any function in the contract but constructor, can be declared as a immutable state variable to save some gas during deployment.

### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L42
### Mitigation
- Add immutable to state variables that do not change but which value is assigned in constructor
