## Gas Optimizations
 *** 


### G-01: pre-increment `++i/--i` costs less gas than post-increment `i++/i--`
Saves 6 gas per loop in a for loop

Total instances of this issue: 1

instance #1
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
```solidity
src/ERC20/ERC20PermitPermissionedMint.sol

84:        for (uint i = 0; i < minters_array.length; i++){ 

```

 *** 


### G-02: `++i/i++` should be placed in unchecked blocks to save gas as it is impossible for them to overflow in for and while loops
Unchecked keyword is available in solidity version `0.8.0` or higher and can be applied to iterator variables to save gas. 
Saves more than `30 gas` per loop.

Total instances of this issue: 5

instance #1
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
```solidity
src/ERC20/ERC20PermitPermissionedMint.sol

84:        for (uint i = 0; i < minters_array.length; i++){ 

```

instance #2
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129
```solidity
src/frxETHMinter.sol

129:        for (uint256 i = 0; i < numDeposits; ++i) {

```

instance #3
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
```solidity
src/OperatorRegistry.sol

63:        for (uint256 i = 0; i < arrayLength; ++i) {

```

instance #4
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
```solidity
src/OperatorRegistry.sol

84:        for (uint256 i = 0; i < times; ++i) {

```

instance #5
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114
```solidity
src/OperatorRegistry.sol

114:            for (uint256 i = 0; i < original_validators.length; ++i) {

```

 *** 


### G-03: Length of the array (`<array>.length`) need not be looked up in every iteration of a for-loop
Reading array length at each iteration of the loop takes total 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the `array.length` saves around `3 gas` per iteration.

Total instances of this issue: 2

instance #1
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
```solidity
src/ERC20/ERC20PermitPermissionedMint.sol

84:        for (uint i = 0; i < minters_array.length; i++){ 

```

instance #2
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114
```solidity
src/OperatorRegistry.sol

114:            for (uint256 i = 0; i < original_validators.length; ++i) {

```

 *** 


### G-04: `x += y` costs more gas than `x = x + y` for state variables

Total instances of this issue: 2

instance #1
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L97
```solidity
src/frxETHMinter.sol

97:            currentWithheldETH += withheld_amt;

```

instance #2
Link: https://github.com/code-423n4/2022-09-frax/blob/main/lib/ERC4626/src/xERC4626.sol#L72
```solidity
lib/ERC4626/src/xERC4626.sol

72:        storedTotalAssets += amount;

```

 *** 


### G-05: Put subtractions operation in `unchecked {}` block where the variable cannot underflow because of a previous `require()`
`require(a <= b); x = b - a` ==> `require(a <= b); unchecked { x = b - a }`

Total instances of this issue: 1

instance #1
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L168
```solidity
src/frxETHMinter.sol

168:        currentWithheldETH -= amount;

```

 *** 


### G-06: No need to compare boolean expressions with boolean literals, directly use the expression
if (<x> == true) ==> if (<x>)
if (<x> == false) => if (!<x>)

Total instances of this issue: 3

instance #1
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L46
```solidity
src/ERC20/ERC20PermitPermissionedMint.sol

46:       require(minters[msg.sender] == true, "Only minters");

```

instance #2
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L68
```solidity
src/ERC20/ERC20PermitPermissionedMint.sol

68:        require(minters[minter_address] == false, "Address already exists");

```

instance #3
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78
```solidity
src/ERC20/ERC20PermitPermissionedMint.sol

78:        require(minters[minter_address] == true, "Address nonexistant");

```

 *** 


### G-07: No need to initialize non-constant/non-immutable variables to zero
Since the default value is already zero, overwriting is not required.
Saves `8 gas` per instance.

Total instances of this issue: 6

instance #1
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
```solidity
src/ERC20/ERC20PermitPermissionedMint.sol

84:        for (uint i = 0; i < minters_array.length; i++){ 

```

instance #2
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94
```solidity
src/frxETHMinter.sol

94:        uint256 withheld_amt = 0;

```

instance #3
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129
```solidity
src/frxETHMinter.sol

129:        for (uint256 i = 0; i < numDeposits; ++i) {

```

instance #4
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
```solidity
src/OperatorRegistry.sol

63:        for (uint256 i = 0; i < arrayLength; ++i) {

```

instance #5
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
```solidity
src/OperatorRegistry.sol

84:        for (uint256 i = 0; i < times; ++i) {

```

instance #6
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114
```solidity
src/OperatorRegistry.sol

114:            for (uint256 i = 0; i < original_validators.length; ++i) {

```

 *** 


### G-08: Using uints/ints smaller than 256 bits increases overhead
Gas usage becomes higher with uint/int smaller than 256 bits because EVM operates on 32 bytes and uses additional operations to reduce the size from 32 bytes to the target size.

Total instances of this issue: 12

instance #1
Permalink: https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L42-L45
```solidity
src/sfrxETH.sol

42:    constructor(ERC20 _underlying, uint32 _rewardsCycleLength)
43:        ERC4626(_underlying, "Staked Frax Ether", "sfrxETH")
44:        xERC4626(_rewardsCycleLength)
45:    {}

```

instance #2
Link: https://github.com/code-423n4/2022-09-frax/blob/main/lib/ERC4626/src/xERC4626.sol#L24
```solidity
lib/ERC4626/src/xERC4626.sol

24:    uint32 public immutable rewardsCycleLength;

```

instance #3
Link: https://github.com/code-423n4/2022-09-frax/blob/main/lib/ERC4626/src/xERC4626.sol#L27
```solidity
lib/ERC4626/src/xERC4626.sol

27:    uint32 public lastSync;

```

instance #4
Link: https://github.com/code-423n4/2022-09-frax/blob/main/lib/ERC4626/src/xERC4626.sol#L30
```solidity
lib/ERC4626/src/xERC4626.sol

30:    uint32 public rewardsCycleEnd;

```

instance #5
Link: https://github.com/code-423n4/2022-09-frax/blob/main/lib/ERC4626/src/xERC4626.sol#L33
```solidity
lib/ERC4626/src/xERC4626.sol

33:    uint192 public lastRewardAmount;

```

instance #6
Link: https://github.com/code-423n4/2022-09-frax/blob/main/lib/ERC4626/src/xERC4626.sol#L37
```solidity
lib/ERC4626/src/xERC4626.sol

37:    constructor(uint32 _rewardsCycleLength) {

```

instance #7
Link: https://github.com/code-423n4/2022-09-frax/blob/main/lib/ERC4626/src/xERC4626.sol#L48
```solidity
lib/ERC4626/src/xERC4626.sol

48:        uint192 lastRewardAmount_ = lastRewardAmount;

```

instance #8
Link: https://github.com/code-423n4/2022-09-frax/blob/main/lib/ERC4626/src/xERC4626.sol#L49
```solidity
lib/ERC4626/src/xERC4626.sol

49:        uint32 rewardsCycleEnd_ = rewardsCycleEnd;

```

instance #9
Link: https://github.com/code-423n4/2022-09-frax/blob/main/lib/ERC4626/src/xERC4626.sol#L50
```solidity
lib/ERC4626/src/xERC4626.sol

50:        uint32 lastSync_ = lastSync;

```

instance #10
Link: https://github.com/code-423n4/2022-09-frax/blob/main/lib/ERC4626/src/xERC4626.sol#L79
```solidity
lib/ERC4626/src/xERC4626.sol

79:        uint192 lastRewardAmount_ = lastRewardAmount;

```

instance #11
Link: https://github.com/code-423n4/2022-09-frax/blob/main/lib/ERC4626/src/xERC4626.sol#L80
```solidity
lib/ERC4626/src/xERC4626.sol

80:        uint32 timestamp = block.timestamp.safeCastTo32();

```

instance #12
Link: https://github.com/code-423n4/2022-09-frax/blob/main/lib/ERC4626/src/xERC4626.sol#L89
```solidity
lib/ERC4626/src/xERC4626.sol

89:        uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;

```

 *** 


### G-09: Using `!= 0` costs less gas than `> 0` in a require() statement
Saves 6 gas per instance for solidity version less than 0.8.12.

Total instances of this issue: 2

instance #1
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79
```solidity
src/frxETHMinter.sol

79:        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

```

instance #2
Link: https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126
```solidity
src/frxETHMinter.sol

126:        require(numDeposits > 0, "Not enough ETH in contract");

```

