## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 3 | 360 |
| [G&#x2011;02] | Avoid contract existence checks by using solidity version 0.8.10 or later | 1 | 100 |
| [G&#x2011;03] | State variables should be cached in stack variables rather than re-reading them from storage | 11 | 1067 |
| [G&#x2011;04] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables | 4 | 452 |
| [G&#x2011;05] | `<array>.length` should not be looked up in every loop of a `for`-loop | 2 | 200 |
| [G&#x2011;06] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 5 | 300 |
| [G&#x2011;07] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 1 | - |
| [G&#x2011;08] | Optimize names to save gas | 5 | 110 |
| [G&#x2011;09] | Using `bool`s for storage incurs overhead | 4 | 80000 |
| [G&#x2011;10] | Use a more recent version of solidity | 6 | - |
| [G&#x2011;11] | Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement | 2 | 12 |
| [G&#x2011;12] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 1 | 10 |
| [G&#x2011;13] | Using `private` rather than `public` for constants, saves gas | 3 | - |
| [G&#x2011;14] | Don't compare boolean expressions to boolean literals | 3 | 27 |
| [G&#x2011;15] | Use custom errors rather than `revert()`/`require()` strings to save gas | 21 | - |
| [G&#x2011;16] | Functions guaranteed to revert when called by normal users can be marked `payable` | 19 | 399 |

Total: 91 instances over 16 issues with **83037 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values


## Gas Optimizations

### [G&#x2011;01]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 3 instances of this issue:*
```solidity
File: src/OperatorRegistry.sol

/// @audit pubKey
/// @audit signature
171       function getValidatorStruct(
172           bytes memory pubKey, 
173           bytes memory signature, 
174           bytes32 depositDataRoot
175:      ) external pure returns (Validator memory) {

/// @audit _new_withdrawal_pubkey
181:      function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/OperatorRegistry.sol#L171-L175

### [G&#x2011;02]  Avoid contract existence checks by using solidity version 0.8.10 or later
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value

*There is 1 instance of this issue:*
```solidity
File: src/frxETHMinter.sol

/// @audit transfer()
200:          require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L200

### [G&#x2011;03]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 11 instances of this issue:*
```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

/// @audit minters_array on line 84
85:               if (minters_array[i] == minter_address) {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L85

```solidity
File: src/frxETHMinter.sol

/// @audit withholdRatio on line 95
96:               withheld_amt = (msg.value * withholdRatio) / RATIO_PRECISION;

/// @audit submitPaused on line 178
180:          emit SubmitPaused(submitPaused);

/// @audit depositEtherPaused on line 185
187:          emit DepositEtherPaused(depositEtherPaused);

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L96

```solidity
File: src/OperatorRegistry.sol

/// @audit validators on line 71
72:           Validator memory toVal = validators[to_idx];

/// @audit validators on line 95
100:              swapValidator(remove_idx, validators.length - 1);

/// @audit validators on line 100
103:              validators.pop();

/// @audit validators on line 103
108:              Validator[] memory original_validators = validators;

/// @audit validators on line 108
111:              delete validators;

/// @audit validators on line 111
116:                      validators.push(original_validators[i]);

/// @audit validators on line 140
141:          validators.pop();

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/OperatorRegistry.sol#L72

### [G&#x2011;04]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

*There are 4 instances of this issue:*
```solidity
File: lib/ERC4626/src/xERC4626.sol

67:           storedTotalAssets -= amount;

72:           storedTotalAssets += amount;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/lib/ERC4626/src/xERC4626.sol#L67

```solidity
File: src/frxETHMinter.sol

97:               currentWithheldETH += withheld_amt;

168:          currentWithheldETH -= amount;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L97

### [G&#x2011;05]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There are 2 instances of this issue:*
```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

84:           for (uint i = 0; i < minters_array.length; i++){ 

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L84

```solidity
File: src/OperatorRegistry.sol

114:              for (uint256 i = 0; i < original_validators.length; ++i) {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/OperatorRegistry.sol#L114

### [G&#x2011;06]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 5 instances of this issue:*
```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

84:           for (uint i = 0; i < minters_array.length; i++){ 

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L84

```solidity
File: src/frxETHMinter.sol

129:          for (uint256 i = 0; i < numDeposits; ++i) {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L129

```solidity
File: src/OperatorRegistry.sol

63:           for (uint256 i = 0; i < arrayLength; ++i) {

84:           for (uint256 i = 0; i < times; ++i) {

114:              for (uint256 i = 0; i < original_validators.length; ++i) {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/OperatorRegistry.sol#L63

### [G&#x2011;07]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There is 1 instance of this issue:*
```solidity
File: src/frxETHMinter.sol

167:          require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L167

### [G&#x2011;08]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 5 instances of this issue:*
```solidity
File: lib/ERC4626/src/xERC4626.sol

/// @audit syncRewards()
20:   abstract contract xERC4626 is IxERC4626, ERC4626 {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/lib/ERC4626/src/xERC4626.sol#L20

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

/// @audit minter_burn_from(), minter_mint(), addMinter(), removeMinter(), setTimelock()
14:   contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L14

```solidity
File: src/frxETHMinter.sol

/// @audit submitAndDeposit(), submit(), submitAndGive(), depositEther(), setWithholdRatio(), moveWithheldETH(), togglePauseSubmits(), togglePauseDepositEther(), recoverEther()
37:   contract frxETHMinter is OperatorRegistry, ReentrancyGuard {    

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L37

```solidity
File: src/OperatorRegistry.sol

/// @audit addValidator(), addValidators(), swapValidator(), popValidators(), removeValidator(), getValidator(), getValidatorStruct(), setWithdrawalCredential(), clearValidatorArray(), numValidators(), setTimelock()
28:   contract OperatorRegistry is Owned {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/OperatorRegistry.sol#L28

```solidity
File: src/sfrxETH.sol

/// @audit pricePerShare(), depositWithSignature(), mintWithSignature()
40:   contract sfrxETH is xERC4626, ReentrancyGuard {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/sfrxETH.sol#L40

### [G&#x2011;09]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There are 4 instances of this issue:*
```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

20:       mapping(address => bool) public minters; // Mapping is also used for faster verification

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L20

```solidity
File: src/frxETHMinter.sol

43:       mapping(bytes => bool) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them

49:       bool public submitPaused;

50:       bool public depositEtherPaused;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L43

### [G&#x2011;10]  Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

*There are 6 instances of this issue:*
```solidity
File: lib/ERC4626/src/xERC4626.sol

4:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/lib/ERC4626/src/xERC4626.sol#L4

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L2

```solidity
File: src/frxETHMinter.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L2

```solidity
File: src/frxETH.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETH.sol#L2

```solidity
File: src/OperatorRegistry.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/OperatorRegistry.sol#L2

```solidity
File: src/sfrxETH.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/sfrxETH.sol#L2

### [G&#x2011;11]  Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement
This change saves **[6 gas](https://aws1.discourse-cdn.com/business6/uploads/zeppelin/original/2X/3/363a367d6d68851f27d2679d10706cd16d788b96.png)** per instance. The optimization works until solidity version [0.8.13](https://gist.github.com/IllIllI000/bf2c3120f24a69e489f12b3213c06c94) where there is a regression in gas costs.

*There are 2 instances of this issue:*
```solidity
File: src/frxETHMinter.sol

79:           require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

126:          require(numDeposits > 0, "Not enough ETH in contract");

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L79

### [G&#x2011;12]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There is 1 instance of this issue:*
```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

84:           for (uint i = 0; i < minters_array.length; i++){ 

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L84

### [G&#x2011;13]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 3 instances of this issue:*
```solidity
File: lib/ERC4626/src/xERC4626.sol

24:       uint32 public immutable rewardsCycleLength;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/lib/ERC4626/src/xERC4626.sol#L24

```solidity
File: src/frxETHMinter.sol

38:       uint256 public constant DEPOSIT_SIZE = 32 ether; // ETH 2.0 minimum deposit size

39:       uint256 public constant RATIO_PRECISION = 1e6; // 1,000,000 

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L38

### [G&#x2011;14]  Don't compare boolean expressions to boolean literals
`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

*There are 3 instances of this issue:*
```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

46:          require(minters[msg.sender] == true, "Only minters");

68:           require(minters[minter_address] == false, "Address already exists");

78:           require(minters[minter_address] == true, "Address nonexistant");

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L46

### [G&#x2011;15]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 21 instances of this issue:*
```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

41:           require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

46:          require(minters[msg.sender] == true, "Only minters");

66:           require(minter_address != address(0), "Zero address detected");

68:           require(minters[minter_address] == false, "Address already exists");

77:           require(minter_address != address(0), "Zero address detected");

78:           require(minters[minter_address] == true, "Address nonexistant");

95:           require(_timelock_address != address(0), "Zero address detected"); 

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L41

```solidity
File: src/frxETHMinter.sol

79:           require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

87:           require(!submitPaused, "Submit is paused");

88:           require(msg.value != 0, "Cannot submit 0");

122:          require(!depositEtherPaused, "Depositing ETH is paused");

126:          require(numDeposits > 0, "Not enough ETH in contract");

140:              require(!activeValidators[pubKey], "Validator already has 32 ETH");

160:          require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");

167:          require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");

171:          require(success, "Invalid transfer");

193:          require(success, "Invalid transfer");

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L79

```solidity
File: src/OperatorRegistry.sol

46:           require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

137:          require(numVals != 0, "Validator stack is empty");

182:          require(numValidators() == 0, "Clear validator array first");

203:          require(_timelock_address != address(0), "Zero address detected");

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/OperatorRegistry.sol#L46

### [G&#x2011;16]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 19 instances of this issue:*
```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

53:       function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {

59:       function minter_mint(address m_address, uint256 m_amount) public onlyMinters {

65:       function addMinter(address minter_address) public onlyByOwnGov {

76:       function removeMinter(address minter_address) public onlyByOwnGov {

94:       function setTimelock(address _timelock_address) public onlyByOwnGov {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L53

```solidity
File: src/frxETHMinter.sol

159:      function setWithholdRatio(uint256 newRatio) external onlyByOwnGov {

166:      function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {

177:      function togglePauseSubmits() external onlyByOwnGov {

184:      function togglePauseDepositEther() external onlyByOwnGov {

191:      function recoverEther(uint256 amount) external onlyByOwnGov {

199:      function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyByOwnGov {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L159

```solidity
File: src/OperatorRegistry.sol

53:       function addValidator(Validator calldata validator) public onlyByOwnGov {

61:       function addValidators(Validator[] calldata validatorArray) external onlyByOwnGov {

69:       function swapValidator(uint256 from_idx, uint256 to_idx) public onlyByOwnGov {

82:       function popValidators(uint256 times) public onlyByOwnGov {

93:       function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {

181:      function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {

190:      function clearValidatorArray() external onlyByOwnGov {

202:      function setTimelock(address _timelock_address) external onlyByOwnGov {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/OperatorRegistry.sol#L53

