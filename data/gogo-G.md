# 2022-09-FRAX

## Gas Optimizations Report

### The usage of `++i` will cost less gas than `i++`. The same change can be applied to `i--` as well.

This change would save up to 6 gas per instance/loop.

_There are **1** instances of this issue:_

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

84:   for (uint i = 0; i < minters_array.length; i++){
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

### State variables should be cached in stack variables rather than re-reading them.

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

_There are **1** instances of this issue:_

```solidity
File: src/OperatorRegistry.sol

      /// @audit Cache `validators`. Used 6 times in `removeValidator`
95:   bytes memory removed_pubkey = validators[remove_idx].pubKey;
100:  swapValidator(remove_idx, validators.length - 1);
103:  validators.pop();
108:  Validator[] memory original_validators = validators;
111:  delete validators;
116:  validators.push(original_validators[i]);
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

### Using `!= 0` on `uints` costs less gas than `> 0`.

This change saves 3 gas per instance/loop

_There are **2** instances of this issue:_

```solidity
File: src/frxETHMinter.sol

79:   require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

126:  require(numDeposits > 0, "Not enough ETH in contract");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

### It costs more gas to initialize non-`constant`/non-`immutable` variables to zero than to let the default of zero be applied

Not overwriting the default for stack variables saves 8 gas. Storage and memory variables have larger savings

_There are **6** instances of this issue:_

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

84:   for (uint i = 0; i < minters_array.length; i++){
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```solidity
File: src/frxETHMinter.sol

94:   uint256 withheld_amt = 0;

129:  for (uint256 i = 0; i < numDeposits; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```solidity
File: src/OperatorRegistry.sol

63:   for (uint256 i = 0; i < arrayLength; ++i) {

84:   for (uint256 i = 0; i < times; ++i) {

114:  for (uint256 i = 0; i < original_validators.length; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

### Using `private` rather than `public` for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

_There are **2** instances of this issue:_

```solidity
File: src/frxETHMinter.sol

38:   uint256 public constant DEPOSIT_SIZE = 32 ether;

39:   uint256 public constant RATIO_PRECISION = 1e6;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

### Functions that are access-restricted from most users may be marked as `payable`

Marking a function as `payable` reduces gas cost since the compiler does not have to check whether a payment was provided or not. This change will save around 21 gas per function call.

_There are **11** instances of this issue:_

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

65:   function addMinter(address minter_address) public onlyByOwnGov {

76:   function removeMinter(address minter_address) public onlyByOwnGov {

94:   function setTimelock(address _timelock_address) public onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```solidity
File: src/OperatorRegistry.sol

53:   function addValidator(Validator calldata validator) public onlyByOwnGov {

61:   function addValidators(Validator[] calldata validatorArray) external onlyByOwnGov {

69:   function swapValidator(uint256 from_idx, uint256 to_idx) public onlyByOwnGov {

82:   function popValidators(uint256 times) public onlyByOwnGov {

93:   function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {

181:  function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {

190:  function clearValidatorArray() external onlyByOwnGov {

202:  function setTimelock(address _timelock_address) external onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

### `<array>.length` should not be looked up in every loop of a `for`-loop

The overheads outlined below are PER LOOP, excluding the first loop \ - storage arrays incur a Gwarmaccess (100 gas) \ - memory arrays use `MLOAD` (3 gas) \ - calldata arrays use `CALLDATALOAD` (3 gas)

Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

_There are **1** instances of this issue:_

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

84:   for (uint i = 0; i < minters_array.length; i++){
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

### `++i`/`i++` should be `unchecked{++I}`/`unchecked{I++}` in `for`-loops

When an increment or any arithmetic operation is not possible to overflow it should be placed in `unchecked{}` block. \This is because of the default compiler overflow and underflow safety checks since Solidity version 0.8.0. \In for-loops it saves around 30-40 gas **per loop**

_There are **5** instances of this issue:_

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

84:   for (uint i = 0; i < minters_array.length; i++){
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```solidity
File: src/frxETHMinter.sol

129:  for (uint256 i = 0; i < numDeposits; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```solidity
File: src/OperatorRegistry.sol

63:   for (uint256 i = 0; i < arrayLength; ++i) {

84:   for (uint256 i = 0; i < times; ++i) {

114:  for (uint256 i = 0; i < original_validators.length; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

### `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

_There are **2** instances of this issue:_

```solidity
File: src/frxETHMinter.sol

97:   currentWithheldETH += withheld_amt;

168:  currentWithheldETH -= amount;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

### Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead

'When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.' \ https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html \ Use a larger size then downcast where needed

_There are **3** instances of this issue:_

```solidity
File: src/sfrxETH.sol

42:   constructor(ERC20 _underlying, uint32 _rewardsCycleLength)

64:   uint8 v,

80:   uint8 v,
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol

### Don't compare boolean expressions to boolean literals

Use `if(x)`/`if(!x)` instead of `if(x == true)`/`if(x == false)`.

_There are **3** instances of this issue:_

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

46:   require(minters[msg.sender] == true, "Only minters");

68:   require(minters[minter_address] == false, "Address already exists");

78:   require(minters[minter_address] == true, "Address nonexistant");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

### Use `calldata` instead of `memory` for function parameters

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

_There are **3** instances of this issue:_

```solidity
File: src/OperatorRegistry.sol

172:  bytes memory pubKey,

173:  bytes memory signature,

181:  function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

### Replace `x <= y` with `x < y + 1`, and `x >= y` with `x > y - 1`

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas

_There are **3** instances of this issue:_

```solidity
File: src/frxETHMinter.sol

160:  require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");

167:  require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```solidity
File: src/sfrxETH.sol

50:   if (block.timestamp >= rewardsCycleEnd) { syncRewards(); }
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol

### Use `immutable` & `constant` for state variables that do not change their value

_There are **2** instances of this issue:_

```solidity
File: src/frxETHMinter.sol

49:   bool public submitPaused;

50:   bool public depositEtherPaused;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

### `require()/revert()` strings longer than 32 bytes cost extra gas

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

_There are **1** instances of this issue:_

```solidity
File: src/frxETHMinter.sol

167:  require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

### Use custom errors rather than `revert()`/`require()` strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

_There are **21** instances of this issue:_

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

41:   require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

46:   require(minters[msg.sender] == true, "Only minters");

66:   require(minter_address != address(0), "Zero address detected");

68:   require(minters[minter_address] == false, "Address already exists");

77:   require(minter_address != address(0), "Zero address detected");

78:   require(minters[minter_address] == true, "Address nonexistant");

95:   require(_timelock_address != address(0), "Zero address detected");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```solidity
File: src/frxETHMinter.sol

79:   require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

87:   require(!submitPaused, "Submit is paused");

88:   require(msg.value != 0, "Cannot submit 0");

122:  require(!depositEtherPaused, "Depositing ETH is paused");

126:  require(numDeposits > 0, "Not enough ETH in contract");

140:  require(!activeValidators[pubKey], "Validator already has 32 ETH");

167:  require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");

171:  require(success, "Invalid transfer");

193:  require(success, "Invalid transfer");

200:  require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```solidity
File: src/OperatorRegistry.sol

46:   require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

137:  require(numVals != 0, "Validator stack is empty");

182:  require(numValidators() == 0, "Clear validator array first");

203:  require(_timelock_address != address(0), "Zero address detected");
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

### Using bools for storage variables incurs overhead

Use `uint256(1)` and `uint256(2)` for `true`/`false` to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

_There are **2** instances of this issue:_

```solidity
File: src/frxETHMinter.sol

49:   bool public submitPaused;

50:   bool public depositEtherPaused;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

### Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

_There are **5** instances of this issue:_

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

2:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```solidity
File: src/frxETH.sol

2:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol

```solidity
File: src/frxETHMinter.sol

2:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```solidity
File: src/OperatorRegistry.sol

2:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```solidity
File: src/sfrxETH.sol

2:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol
