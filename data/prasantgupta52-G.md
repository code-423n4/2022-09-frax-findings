# 1. UNCHECKED{++I} INSTEAD OF I++ (OR USE ASSEMBLY WHEN APPLICABLE)
### Description
Use `++i` instead of `i++`. This is especially useful in for loops but this optimization can be used anywhere in your code. You can also use `unchecked{++i;}` for even more gas savings but this will not check to see if `i` overflows. For extra safety if you are worried about this, you can add a require statement after the loop checking if `i` is equal to the final incremented value. For best gas savings, use inline assembly, however this limits the functionality you can achieve. For example you cant use Solidity syntax to internally call your own contract within an assembly block and external calls must be done with the `call()` or `delegatecall()` instruction. However when applicable, inline assembly will save much more gas.
### Links to github files
[ERC20PermitPermissionedMint.sol:L84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)
### Instances
```
src/ERC20/ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){ 
```
----
# 2.`<ARRAY>.LENGTH` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
### Description
The overheads outlined below are `PER LOOP`, excluding the first loop
storage arrays incur a Gwarmaccess (100 gas) memory arrays use `MLOAD` (3 gas)
calldata arrays use `CALLDATALOAD` (3 gas) Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset
### Links to github files
[ERC20PermitPermissionedMint.sol:L84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)
[OperatorRegistry.sol:L114](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114)
### Instances
```
src/ERC20/ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){
src/OperatorRegistry.sol:114:            for (uint256 i = 0; i < original_validators.length; ++i) {
```
----
# 3. Variables: No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

We can use `uint number;` instead of `uint number = 0;`
### Links to github files
[frxETHMinter.sol:L94](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L94)
### Instances
```
src/frxETHMinter.sol:94:        uint256 withheld_amt = 0;
```
### Recommendation:
I suggest removing explicit initializations for default values.

----
# 4. IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for `(uint256 i = 0; i < numIterations; ++i)` { should be replaced with for `(uint256 i; i < numIterations; ++i) {`
### Links to github files
[frxETHMinter.sol:L129](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L129)
[ERC20PermitPermissionedMint.sol:L84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)
[OperatorRegistry.sol:L63](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63)
[OperatorRegistry.sol:L84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84)
[OperatorRegistry.sol:L114](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114)
### Instances
```
src/frxETHMinter.sol:129:        for (uint256 i = 0; i < numDeposits; ++i) {
src/ERC20/ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){ 
src/OperatorRegistry.sol:63:        for (uint256 i = 0; i < arrayLength; ++i) {
src/OperatorRegistry.sol:84:        for (uint256 i = 0; i < times; ++i) {
src/OperatorRegistry.sol:114:            for (uint256 i = 0; i < original_validators.length; ++i) {
```
----
# 5. Comparisons: > 0 is less efficient than != 0 for unsigned integers (with proof)

`!= 0` costs less gas compared to `> 0` for unsigned integers in `require` statements with the optimizer enabled (6 gas)

Proof: While it may seem that `> 0` is cheaper than `!=`, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a `require` statement, this will save gas.
### Links to github files
[frxETHMinter.sol:L79](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79)
[frxETHMinter.sol:L126](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L126)
### Instances
```
src/frxETHMinter.sol:79:        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
src/frxETHMinter.sol:126:        require(numDeposits > 0, "Not enough ETH in contract");
```
### Recommended Mitigation Step
I suggest changing `> 0` with `!= 0` here:

----
# 6. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
`<x> += <y>` costs more gas as compared to `<x> = <x> + <y>`
### Links to github files
[frxETHMinter.sol:L97](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L97)
[xERC4626.sol:L72](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L72)
### Instances
```
src/frxETHMinter.sol:97:            currentWithheldETH += withheld_amt;
lib/ERC4626/src/xERC4626.sol:72:        storedTotalAssets += amount;
```
`<x> -= <y>` costs more gas as compared to `<x> = <x> - <y>`
### Links to github files
[frxETHMinter.sol:L168](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L168)
[xERC4626.sol:L67](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L67)
### Instances
```
src/frxETHMinter.sol:168:        currentWithheldETH -= amount;
lib/ERC4626/src/xERC4626.sol:67:        storedTotalAssets -= amount;
```
----
# 7. Use a more recent version of solidity
### Description
Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value
### Links to github files
[sfrxETH.sol:L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L2)
[frxETHMinter.sol:L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L2)
[ERC20PermitPermissionedMint.sol:L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L2)
[frxETH.sol:L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETH.sol#L2)
[OperatorRegistry.sol:L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L2)
[xERC4626.sol:L4](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L4)
### Instances
```
src/sfrxETH.sol:2:pragma solidity ^0.8.0;
src/frxETHMinter.sol:2:pragma solidity ^0.8.0;
src/ERC20/ERC20PermitPermissionedMint.sol:2:pragma solidity ^0.8.0;
src/frxETH.sol:2:pragma solidity ^0.8.0;
src/OperatorRegistry.sol:2:pragma solidity ^0.8.0;
lib/ERC4626/src/xERC4626.sol:4:pragma solidity ^0.8.0;
```
----
# 8. Public functions not called by the contract should be declared external instead
### Description
public functions not called by the contract should be declared external instead to save some gas cost.
### Links to github files
[sfrxETH.sol:L54](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L54)
[ERC20PermitPermissionedMint.sol:L53](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L53)
[ERC20PermitPermissionedMint.sol:L65](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L65)
[ERC20PermitPermissionedMint.sol:L76](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L76)
[ERC20PermitPermissionedMint.sol:L94](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L94)
[OperatorRegistry.sol:L82](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L82)
[OperatorRegistry.sol:L93](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L93)
[xERC4626.sol:L45](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L45)
### Instances
```
src/sfrxETH.sol:54:    function pricePerShare() public view returns (uint256) {
src/ERC20/ERC20PermitPermissionedMint.sol:53:    function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {
src/ERC20/ERC20PermitPermissionedMint.sol:65:    function addMinter(address minter_address) public onlyByOwnGov {
src/ERC20/ERC20PermitPermissionedMint.sol:76:    function removeMinter(address minter_address) public onlyByOwnGov {
src/ERC20/ERC20PermitPermissionedMint.sol:94:    function setTimelock(address _timelock_address) public onlyByOwnGov {
src/OperatorRegistry.sol:82:    function popValidators(uint256 times) public onlyByOwnGov {
src/OperatorRegistry.sol:93:    function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
lib/ERC4626/src/xERC4626.sol:45:    function totalAssets() public view override returns (uint256) {
```
### Recommended Mitigation Steps:
declare functions as external instead of public

----
# 9. Custom Errors instead of Revert Strings to save Gas
### Description
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

Starting from Solidity v0.8.4,there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");),but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
### Links to github files
[frxETHMinter.sol:L87](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L87)
[frxETHMinter.sol:L88](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L88)
[frxETHMinter.sol:L122](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L122)
[frxETHMinter.sol:L126](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L126)
[frxETHMinter.sol:L140](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L140)
[frxETHMinter.sol:L160](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L160)
[frxETHMinter.sol:L167](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L167)
[frxETHMinter.sol:L171](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L171)
[frxETHMinter.sol:L193](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L193)
[frxETHMinter.sol:L200](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L200)
[ERC20PermitPermissionedMint.sol:L41](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L41)
[ERC20PermitPermissionedMint.sol:L46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L46)
[ERC20PermitPermissionedMint.sol:L66](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L66)
[ERC20PermitPermissionedMint.sol:L68](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L68)
[ERC20PermitPermissionedMint.sol:L77](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L77)
[ERC20PermitPermissionedMint.sol:L78](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L78)
[ERC20PermitPermissionedMint.sol:L95](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L95)
[OperatorRegistry.sol:L46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L46)
[OperatorRegistry.sol:L137](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L137)
[OperatorRegistry.sol:L182](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L182)
[OperatorRegistry.sol:L203](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L203)
### Instances
```
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
### Recommended Mitigation Steps:
I suggest replacing revert strings with custom errors.

----
# 10. Errors: Reduce the size of error messages (Long revert Strings)

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.
### Links to github files
[frxETHMinter.sol:L167](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L167)
### Instances
```	
src/frxETHMinter.sol:167:        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```
### Recommended Mitigation Step
I suggest shortening the revert strings to fit in 32 bytes, or that using custom errors as described next.

----
# 11. Using private rather than public for constants / immutable, saves gas
### Description
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves **3406-3606** gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table
### Links to github files
[frxETHMinter.sol:L38](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L38)
[frxETHMinter.sol:L39](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L39)
[frxETHMinter.sol:L45](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L45)
[frxETHMinter.sol:L46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L46)
[frxETHMinter.sol:L47](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L47)
[xERC4626.sol:L24](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L24)
### Instances
```            
src/frxETHMinter.sol:38:    uint256 public constant DEPOSIT_SIZE = 32 ether; // ETH 2.0 minimum deposit size
src/frxETHMinter.sol:39:    uint256 public constant RATIO_PRECISION = 1e6; // 1,000,000 
src/frxETHMinter.sol:45:    IDepositContract public immutable depositContract; // ETH 2.0 deposit contract
src/frxETHMinter.sol:46:    frxETH public immutable frxETHToken;
src/frxETHMinter.sol:47:    IsfrxETH public immutable sfrxETHToken;
lib/ERC4626/src/xERC4626.sol:24:    uint32 public immutable rewardsCycleLength;
```
----
# 12. Comparisons: Boolean comparisons
### Description
Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using if(borrowers) instead of if(borrowers == true) and if(!borrowers) instead of if(borrowers == false) here

### Links to github files
[ERC20PermitPermissionedMint.sol:L46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L46)
[ERC20PermitPermissionedMint.sol:L78](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L78)
[ERC20PermitPermissionedMint.sol:L68](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L68)

### Instances
```
src/ERC20/ERC20PermitPermissionedMint.sol:46:       require(minters[msg.sender] == true, "Only minters");
src/ERC20/ERC20PermitPermissionedMint.sol:78:        require(minters[minter_address] == true, "Address nonexistant");
src/ERC20/ERC20PermitPermissionedMint.sol:68:        require(minters[minter_address] == false, "Address already exists");
```