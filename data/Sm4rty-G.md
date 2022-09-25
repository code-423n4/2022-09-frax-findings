

## 1. Pre-increment costs less gas as compared to Post-increment :

++i costs less gas as compared to i++ for unsigned integer, as per-increment is cheaper(its about 5 gas per iteration cheaper)

i++ increments i and returns initial value of i. Which means

```
uint i =  1;
i++; // ==1 but i ==2
```

But ++i returns the actual incremented value:

```
uint i = 1;
++i; // ==2 and i ==2 , no need for temporary variable here
```

In the first case, the compiler has create a temporary variable (when used) for returning 1 instead of 2.

### Instances:

```
src/ERC20/ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){ 
``` 

### Recommendations:
Change post-increment to pre-increment.

-----

## 2. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

### Instances:

```
src/ERC20/ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){ 

``` 

### Reference:

[https://code4rena.com/reports/2022-05-cally#g-11-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops](https://code4rena.com/reports/2022-05-cally#g-11-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops)

-----

## 3. Using > 0 costs more gas than != 0 when used on a uint in a require() statement

> 0 is less efficient than != 0 for unsigned integers (with proof)
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas) Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas. You can see this tweet for more proof: https://twitter.com/gzeon/status/1485428085885640706

### Instances

```
src/frxETHMinter.sol:79:        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
src/frxETHMinter.sol:126:        require(numDeposits > 0, "Not enough ETH in contract");
``` 

### Reference:

[https://twitter.com/gzeon/status/1485428085885640706](https://twitter.com/gzeon/status/1485428085885640706)

### Remediation:

I suggest changing > 0 with != 0. Also, please enable the Optimizer.

-----

## 4. An array's length should be cached to save gas in for-loops

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.

### Instances:

```
src/ERC20/ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){ 
src/OperatorRegistry.sol:114:            for (uint256 i = 0; i < original_validators.length; ++i) {
``` 


### Remediation:
Here, I suggest storing the array's length in a variable before the for-loop, and use it instead.

-----

## 5. Custom Errors instead of Revert Strings to save Gas

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met). Starting from Solidity v0.8.4,there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");),but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.
Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

### Instances

```
src/frxETHMinter.sol:79:        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
src/frxETHMinter.sol:160:        require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");
src/frxETHMinter.sol:87:        require(!submitPaused, "Submit is paused");
src/frxETHMinter.sol:88:        require(msg.value != 0, "Cannot submit 0");
src/frxETHMinter.sol:122:        require(!depositEtherPaused, "Depositing ETH is paused");
src/frxETHMinter.sol:126:        require(numDeposits > 0, "Not enough ETH in contract");
src/frxETHMinter.sol:140:            require(!activeValidators[pubKey], "Validator already has 32 ETH");
src/frxETHMinter.sol:167:        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
src/frxETHMinter.sol:171:        require(success, "Invalid transfer");
src/frxETHMinter.sol:193:        require(success, "Invalid transfer");
src/frxETHMinter.sol:200:        require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
src/ERC20/ERC20PermitPermissionedMint.sol:41:        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
src/ERC20/ERC20PermitPermissionedMint.sol:46:       require(minters[msg.sender] == true, "Only minters");
src/ERC20/ERC20PermitPermissionedMint.sol:66:        require(minter_address != address(0), "Zero address detected");
src/ERC20/ERC20PermitPermissionedMint.sol:68:        require(minters[minter_address] == false, "Address already exists");
src/ERC20/ERC20PermitPermissionedMint.sol:77:        require(minter_address != address(0), "Zero address detected");
src/ERC20/ERC20PermitPermissionedMint.sol:78:        require(minters[minter_address] == true, "Address nonexistant");
src/ERC20/ERC20PermitPermissionedMint.sol:95:        require(_timelock_address != address(0), "Zero address detected"); 
src/OperatorRegistry.sol:46:        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
src/OperatorRegistry.sol:137:        require(numVals != 0, "Validator stack is empty");
src/OperatorRegistry.sol:182:        require(numValidators() == 0, "Clear validator array first");
src/OperatorRegistry.sol:203:        require(_timelock_address != address(0), "Zero address detected");
``` 


### Remediation:
I suggest replacing revert strings with custom errors.


-----

## 6. Boolean Comparision

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here

### Instances:

```
src/ERC20/ERC20PermitPermissionedMint.sol:68:        require(minters[minter_address] == false, "Address already exists");
src/ERC20/ERC20PermitPermissionedMint.sol:46:       require(minters[msg.sender] == true, "Only minters");
src/ERC20/ERC20PermitPermissionedMint.sol:78:        require(minters[minter_address] == true, "Address nonexistant");
``` 
### References:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-04-fundingonlywhenpricenotflagged-boolean-comparison-147](https://code4rena.com/reports/2022-04-badger-citadel/#g-04-fundingonlywhenpricenotflagged-boolean-comparison-147)


-----


## 7. x += y costs more gas than x = x + y for state variables

### Instances:

```
src/frxETHMinter.sol:97:            currentWithheldETH += withheld_amt;
lib/ERC4626/src/xERC4626.sol:71:        storedTotalAssets += amount;
src/frxETHMinter.sol:168:        currentWithheldETH -= amount;
lib/ERC4626/src/xERC4626.sol:66:        storedTotalAssets -= amount;
``` 
### References:

[https://github.com/code-423n4/2022-05-backd-findings/issues/108](https://github.com/code-423n4/2022-05-backd-findings/issues/108)


-----

## 8. Variables: No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

We can use `uint number;` instead of `uint number = 0;`

### Instances:

```
src/frxETHMinter.sol:94:        uint256 withheld_amt = 0;
``` 
### Recommendation:

I suggest removing explicit initializations for default values.


-----



## 9. Reduce the size of error messages (Long revert Strings)

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### Instances:

```
src/frxETHMinter.sol:167:        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
``` 
### Recommendations:

I suggest shortening the revert strings to fit in 32 bytes, or using custom errors as described next.

Custom errors from Solidity 0.8.4 are cheaper than revert strings
(cheaper deployment cost and runtime cost when the revert condition is
met)

Source [Custom Errors](https://blog.soliditylang.org/2021/04/21/custom-errors/) in Solidity:


-----

## 10. Use calldata instead of memory in external functions

Use calldata instead of memory in external functions to save gas.

### Instances:

```
src/OperatorRegistry.sol:181:    function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
``` 
### Reference:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-12-stakedcitadeldepositall-use-calldata-instead-of-memory](https://code4rena.com/reports/2022-04-badger-citadel/#g-12-stakedcitadeldepositall-use-calldata-instead-of-memory)


-----

## 11. Using bools for storage incurs overhead

```
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
```

[Refer Here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### Instances:

```
src/frxETHMinter.sol:43:    mapping(bytes => bool) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them
src/frxETHMinter.sol:49:    bool public submitPaused;
src/frxETHMinter.sol:50:    bool public depositEtherPaused;
src/ERC20/ERC20PermitPermissionedMint.sol:20:    mapping(address => bool) public minters; // Mapping is also used for faster verification
``` 
### References:

[https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead](https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead)


-----

## 12. Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed

### Instances:

```
src/sfrxETH.sol:64:        uint8 v,
src/sfrxETH.sol:80:        uint8 v,
``` 
### References:

[https://code4rena.com/reports/2022-04-phuture#g-14-usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead](https://code4rena.com/reports/2022-04-phuture#g-14-usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead)


-----

## 13. Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

### Instances:

```
src/frxETHMinter.sol:2:pragma solidity ^0.8.0;
src/ERC20/ERC20PermitPermissionedMint.sol:2:pragma solidity ^0.8.0;
src/OperatorRegistry.sol:2:pragma solidity ^0.8.0;
src/sfrxETH.sol:2:pragma solidity ^0.8.0;
src/frxETH.sol:2:pragma solidity ^0.8.0;
lib/ERC4626/src/xERC4626.sol:4:pragma solidity ^0.8.0;
``` 
### References:

[https://code4rena.com/reports/2022-06-notional-coop/#10-use-a-more-recent-version-of-solidity](https://code4rena.com/reports/2022-06-notional-coop/#10-use-a-more-recent-version-of-solidity)


-----

