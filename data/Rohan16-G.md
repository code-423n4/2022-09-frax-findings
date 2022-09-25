# Findings
## 1. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)
### Bugs
[L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

```

src/ERC20/ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){ 
```

[L63,L84,L114](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol)
```
src/OperatorRegistry.sol:63:        for (uint256 i = 0; i < arrayLength; ++i) {
src/OperatorRegistry.sol:84:        for (uint256 i = 0; i < times; ++i) {
src/OperatorRegistry.sol:114:            for (uint256 i = 0; i < original_validators.length; ++i) {
```

------
## 2. Variables: No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

We can use `uint number;` instead of `uint number = 0;`

### Bugs

[L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

```

src/ERC20/ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){ 
```

[L63,L84,L114](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol)
```
src/OperatorRegistry.sol:63:        for (uint256 i = 0; i < arrayLength; ++i) {
src/OperatorRegistry.sol:84:        for (uint256 i = 0; i < times; ++i) {
src/OperatorRegistry.sol:114:            for (uint256 i = 0; i < original_validators.length; ++i) {
```

----

## 3. Pre-increment costs less gas as compared to Post-increment :

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

### Bug

[L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)
```

src/ERC20/ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){ 

```

### Recommendations:

Change post-increment to pre-increment.

---

## 4. Using > 0 costs more gas than != 0 when used on a uint in a require() statement

> 0 is less efficient than != 0 for unsigned integers (with proof)
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas) Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas. You can see this tweet for more proof: https://twitter.com/gzeon/status/1485428085885640706
> 

### Bugs

[L79,L126](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol)
```

src/frxETHMinter.sol:79:        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
src/frxETHMinter.sol:126:        require(numDeposits > 0, "Not enough ETH in contract");

```


### Suggestion

I suggest changing > 0 with != 0. Also, please enable the Optimizer.

---

## 5. Custom Errors instead of Revert Strings to save Gas

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met). Starting from Solidity v0.8.4,there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");),but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.
Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

### Bugs
[frxETHMinter.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol)
 ```   
src/frxETHMinter.sol:79:        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
src/frxETHMinter.sol:87:        require(!submitPaused, "Submit is paused");
src/frxETHMinter.sol:88:        require(msg.value != 0, "Cannot submit 0");
src/frxETHMinter.sol:122:        require(!depositEtherPaused, "Depositing ETH is paused");
src/frxETHMinter.sol:126:        require(numDeposits > 0, "Not enough ETH in contract");
src/frxETHMinter.sol:140:            require(!activeValidators[pubKey], "Validator already has 32 ETH");
src/frxETHMinter.sol:160:        require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");
src/frxETHMinter.sol:167:        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
src/frxETHMinter.sol:171:        require(success, "Invalid transfer");
src/frxETHMinter.sol:193:        require(success, "Invalid transfer");
src/frxETHMinter.sol:200:        require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```

[ERC20PermitPermissionedMint.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol)
```

src/ERC20/ERC20PermitPermissionedMint.sol:41:        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
src/ERC20/ERC20PermitPermissionedMint.sol:46:       require(minters[msg.sender] == true, "Only minters");
src/ERC20/ERC20PermitPermissionedMint.sol:66:        require(minter_address != address(0), "Zero address detected");
src/ERC20/ERC20PermitPermissionedMint.sol:68:        require(minters[minter_address] == false, "Address already exists");
src/ERC20/ERC20PermitPermissionedMint.sol:77:        require(minter_address != address(0), "Zero address detected");
src/ERC20/ERC20PermitPermissionedMint.sol:78:        require(minters[minter_address] == true, "Address nonexistant");
src/ERC20/ERC20PermitPermissionedMint.sol:95:        require(_timelock_address != address(0), "Zero address detected"); 
src/OperatorRegistry.sol:46:        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
```
[OperatorRegistry.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol)
```
src/OperatorRegistry.sol:137:        require(numVals != 0, "Validator stack is empty");
src/OperatorRegistry.sol:182:        require(numValidators() == 0, "Clear validator array first");
src/OperatorRegistry.sol:203:        require(_timelock_address != address(0), "Zero address detected");

```
### Recommendation:

I suggest replacing revert strings with custom errors.

---
## 6. x += y costs more gas than x = x + y for state variables

### Bugs

```

lib/ERC4626/src/xERC4626.sol:71:        storedTotalAssets += amount;
src/frxETHMinter.sol:97:            currentWithheldETH += withheld_amt;
```

### References:

[https://github.com/code-423n4/2022-05-backd-findings/issues/108](https://github.com/code-423n4/2022-05-backd-findings/issues/108)


---

## 7. Using bools for storage incurs overhead

`
Booleans are more expensive than uint256 or any type that takes up a full
word because each write operation emits an extra SLOAD to first read the
slot's contents, replace the bits taken up by the boolean, and then write
back. This is the compiler's defense against contract upgrades and
pointer aliasing, and it cannot be disabled.
`



[Refer Here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past


```
src/ERC20/ERC20PermitPermissionedMint.sol:20:    mapping(address => bool) public minters; // Mapping is also used for faster verification
src/sfrxETH.sol:63:        bool approveMax,
src/sfrxETH.sol:79:        bool approveMax,

src/frxETHMinter.sol:43:    mapping(bytes => bool) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them
src/frxETHMinter.sol:49:    bool public submitPaused;
src/frxETHMinter.sol:50:    bool public depositEtherPaused;
```

### Refer Here:

[https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead](https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead)

---





