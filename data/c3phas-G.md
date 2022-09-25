## FINDINGS
NB: *Some functions have been truncated where necessary to just show affected parts of the code*

Throughout the report some places might be denoted with audit tags to show the actual place affected.

### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

NB: *Some functions have been truncated where neccessary to just show affected parts of the code*

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L85-L101
### frxETHMinter.sol.\_submit(): withholdRatio should be cached(Saves 1 SLOAD)
```solidity
File: /src/frxETHMinter.sol
85:    function _submit(address recipient) internal nonReentrant {

95:        if (withholdRatio != 0) { //@audit: withholdRatio SLOAD 1
96:            withheld_amt = (msg.value * withholdRatio) / RATIO_PRECISION; //@audit: withholdRatio SLOAD 2
97:            currentWithheldETH += withheld_amt;
98:        }

101:    }
```

```diff
diff --git a/src/frxETHMinter.sol b/src/frxETHMinter.sol
index 4565883..c49f0a6 100644
--- a/src/frxETHMinter.sol
+++ b/src/frxETHMinter.sol
@@ -92,8 +92,9 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {

         // Track the amount of ETH that we are keeping
         uint256 withheld_amt = 0;
-        if (withholdRatio != 0) {
-            withheld_amt = (msg.value * withholdRatio) / RATIO_PRECISION;
+        uint256 tempWithholdRatio = withholdRatio;
+        if (tempWithholdRatio != 0) {
+            withheld_amt = (msg.value * tempWithholdRatio) / RATIO_PRECISION;
             currentWithheldETH += withheld_amt;
         }
```
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L166-L174
### frxETHMinter.sol.moveWithheldETH(): currentWithheldETH should be cached(Saves 1 SLOAD)
```solidity
File: /src/frxETHMinter.sol
166:    function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
167:        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
168:        currentWithheldETH -= amount;

174:    }
```

```diff
diff --git a/src/frxETHMinter.sol b/src/frxETHMinter.sol
index 4565883..dc3f2a7 100644
--- a/src/frxETHMinter.sol
+++ b/src/frxETHMinter.sol
@@ -164,8 +164,9 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {

     /// @notice Give the withheld ETH to the "to" address
     function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
-        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
-        currentWithheldETH -= amount;
+        uint256 tempWithheldEth = currentWithheldETH;
+        require(amount <= tempWithheldEth, "Not enough withheld ETH in contract");
+        currentWithheldETH = tempWithheldEth - amount;

         (bool success,) = payable(to).call{ value: amount }("");
         require(success, "Invalid transfer");
```

### Internal/Private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L126-L148
```solidity
File: /src/OperatorRegistry.sol
126:    function getNextValidator()
127:        internal
128:        returns (
129:            bytes memory pubKey,
130:            bytes memory withdrawalCredentials,
131:            bytes memory signature,
132:            bytes32 depositDataRoot
133:        )
```

The above function is only called once on a child contract at [Line 136](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L136)

### Use calldata instead of memory for function parameters
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L171-L177
```solidity
File: /src/OperatorRegistry.sol

//@audit: Use calldata on pubKey and signature
171:    function getValidatorStruct(
172:        bytes memory pubKey, 
173:        bytes memory signature, 
174:        bytes32 depositDataRoot
175:    ) external pure returns (Validator memory) {
176:        return Validator(pubKey, signature, depositDataRoot);
177:    }
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L181-L186
```solidity
File: /src/OperatorRegistry.sol

//@audit: Use calldata on _new_withdrawal_pubkey
181:    function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
182:        require(numValidators() == 0, "Clear validator array first");
183:        curr_withdrawal_pubkey = _new_withdrawal_pubkey;

185:        emit WithdrawalCredentialSet(_new_withdrawal_pubkey);
186:    }
```

### Refactor the code to save on gas
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L78-L97
```solidity
File: /src/xERC4626.sol
78:    function syncRewards() public virtual {
79:        uint192 lastRewardAmount_ = lastRewardAmount;
80:        uint32 timestamp = block.timestamp.safeCastTo32();
 
82        if (timestamp < rewardsCycleEnd) revert SyncError();
           ...
97:    }
```

In the above function, we could move the `uint192 lastRewardAmount_ = lastRewardAmount;` to be run after the if statement. This way if the check on the if statement doesn't succeed then we don't need to incur the gas for the assignment of  `lastRewardAmount_` which involves reading from storage

```diff
diff --git a/src/xERC4626.sol b/src/xERC4626.sol
index a8a4726..83c6738 100644
--- a/src/xERC4626.sol
+++ b/src/xERC4626.sol
@@ -76,10 +76,10 @@ abstract contract xERC4626 is IxERC4626, ERC4626 {
     /// @notice Distributes rewards to xERC4626 holders.
     /// All surplus `asset` balance of the contract over the internal balance becomes queued for the next cycle.
     function syncRewards() public virtual {
-        uint192 lastRewardAmount_ = lastRewardAmount;
         uint32 timestamp = block.timestamp.safeCastTo32();

         if (timestamp < rewardsCycleEnd) revert SyncError();
+                uint192 lastRewardAmount_ = lastRewardAmount;
```

### Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct


https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L71
```solidity
File: /src/OperatorRegistry.sol
71:        Validator memory fromVal = validators[from_idx];
72:        Validator memory toVal = validators[to_idx];

140:        Validator memory popped = validators[numVals - 1];

161:        Validator memory v = validators[i];
```

### Avoid contract existence checks by using solidity version 0.8.10 or later

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L78
```solidity
File: /src/frxETHMinter.sol
78:        uint256 sfrxeth_recieved = sfrxETHToken.deposit(msg.value, recipient);
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L200
```solidity
File: /src/frxETHMinter.sol
200:        require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```

### x += y costs more gas than x = x + y for state variables

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L97
```solidity
File: /src/frxETHMinter.sol
97:            currentWithheldETH += withheld_amt;
```

Modify the above as 
```solidity
            currentWithheldETH = currentWithheldETH + withheld_amt;
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L168
```solidity
File: /src/frxETHMinter.sol
168:        currentWithheldETH -= amount;
```

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L67
```solidity
File: /src/xERC4626.sol
67:        storedTotalAssets -= amount;
```

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L72
```solidity
File: /src/xERC4626.sol
72:        storedTotalAssets += amount;
```

### Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L59-L67
```solidity
File: /src/sfrxETH.sol

//@audit:  uint8 v,
59:    function depositWithSignature(
60:        uint256 assets,
61:        address receiver,
62:        uint256 deadline,
63:        bool approveMax,
64:        uint8 v,
65:        bytes32 r,
66:        bytes32 s
67:    ) external nonReentrant returns (uint256 shares) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L75-L83
```solidity
File: /src/sfrxETH.sol
//@audit: Missing @param shares,@param receiver,@param deadline,@param approveMax,@param v,@param r,@param s
//@audit: Missing @return
75:    function mintWithSignature(
76:        uint256 shares,
77:        address receiver,
78:        uint256 deadline,
79:        bool approveMax,
80:        uint8 v,
81:        bytes32 r,
82:        bytes32 s
83:    ) external nonReentrant returns (uint256 assets) {
```

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L37
```solidity
File: /src/xERC4626.sol

//@audit: uint32 _rewardsCycleLength
37:    constructor(uint32 _rewardsCycleLength) {


48:        uint192 lastRewardAmount_ = lastRewardAmount;
49:        uint32 rewardsCycleEnd_ = rewardsCycleEnd;
50:        uint32 lastSync_ = lastSync;

79:        uint192 lastRewardAmount_ = lastRewardAmount;
80:        uint32 timestamp = block.timestamp.safeCastTo32();

89:        uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```

### No need to initialize variables with their default values
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). If you explicitly initialize it with its default value, you are just wasting gas.
It costs more gas to initialize variables to zero than to let the default of zero be applied
Declaring uint256 i = 0; means doing an MSTORE of the value 0 Instead you could just declare uint256 i to declare the variable without assigning it’s default value, saving 3 gas per declaration

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L94
```solidity
File: /src/frxETHMinter.sol
94:        uint256 withheld_amt = 0;

//@audit: uint256 i = 0
129:        for (uint256 i = 0; i < numDeposits; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84
```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

//@audit: uint i = 0
84:        for (uint i = 0; i < minters_array.length; i++){ 

```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63
```solidity
File: /src/OperatorRegistry.sol
63:        for (uint256 i = 0; i < arrayLength; ++i) {

84:        for (uint256 i = 0; i < times; ++i) {

114:            for (uint256 i = 0; i < original_validators.length; ++i) {

```

### Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L168
```solidity
File: /src/frxETHMinter.sol
168:        currentWithheldETH -= amount;
```
The above operation cannot underflow due to the check on [Line 167](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L167) that ensures that `currentWithheldETH` is greater than `amount` before performing the subtraction

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L140
```solidity
File: /src/OperatorRegistry.sol
140:        Validator memory popped = validators[numVals - 1];
```

The operation `numVals - 1` cannot underflow due to the check on [Line 137](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L137) that ensures that `numVals` is not 0 before performing the subtraction operation

### Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

**Affected code**
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84
```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol
84:        for (uint i = 0; i < minters_array.length; i++){ 
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L129
```solidity
File: /src/frxETHMinter.sol
129:        for (uint256 i = 0; i < numDeposits; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63
```solidity
File: /src/OperatorRegistry.sol
63:        for (uint256 i = 0; i < arrayLength; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84
```solidity
File: /src/OperatorRegistry.sol
84:        for (uint256 i = 0; i < times; ++i) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114
```solidity
File: /src/OperatorRegistry.sol
114:            for (uint256 i = 0; i < original_validators.length; ++i) {
```
[see resource](https://github.com/ethereum/solidity/issues/10695)


### Cache the length of arrays in loops (saves ~6 gas per iteration)
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

The solidity compiler will always read the length of the array during each iteration. That is,

   1.if it is a storage array, this is an extra sload operation (100 additional extra gas (EIP-2929 2) for each iteration except for the first),
   2.if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first),
   3.if it is a calldata array, this is an extra calldataload operation (3 additional gas for each iteration except for the first)

This extra costs can be avoided by caching the array length (in stack):
 When reading the length of an array,  **sload** or **mload** or **calldataload** operation is only called once and subsequently replaced by a cheap **dupN** instruction. Even though mload , calldataload and dupN have the same gas cost, mload and calldataload needs an additional dupN to put the offset in the stack, i.e., an extra 3 gas. which brings this to 6 gas
 
Here, I suggest storing the array’s length in a variable before the for-loop, and use it instead:

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84
```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol
84:        for (uint i = 0; i < minters_array.length; i++){ 
```
 
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114
```solidity
File: /src/OperatorRegistry.sol
114:            for (uint256 i = 0; i < original_validators.length; ++i) {
```

### ++i costs less gas compared to i++ or i += 1  (~5 gas per iteration)

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i. Which means:

```solidity
uint i = 1;  
i++; // == 1 but i == 2  
```

But ++i returns the actual incremented value:

```solidity
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

Instances include:

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84
```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol
84:        for (uint i = 0; i < minters_array.length; i++){ 
```

### Pre-Solidity 0.8.13: > 0 is less efficient than != 0 for unsigned integers(6 gas)

Up until Solidity 0.8.13: != 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)

For uints the minimum value would be 0 and never a negative value. Since it cannot be a negative value, then the check > 0 is essentially checking that the value is not equal to 0 therefore >0 can be replaced with !=0 which saves gas.

Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you're in a require statement, this will save gas. You can see this tweet for more proofs: https://twitter.com/gzeon/status/1485428085885640706

I suggest changing > 0 with != 0 here:

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79
```solidity
File: /src/frxETHMinter.sol
79:        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

126:        require(numDeposits > 0, "Not enough ETH in contract");
```

### Boolean comparisons

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value.
I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here:

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L46
```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol
46:       require(minters[msg.sender] == true, "Only minters");
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L68
```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol
68:        require(minters[minter_address] == false, "Address already exists");
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L78
```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol
78:        require(minters[minter_address] == true, "Address nonexistant");
```

### Using bools for storage incurs overhead
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```

See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

**Instances affected include**
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L20
```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol
20:    mapping(address => bool) public minters; // Mapping is also used for faster verification
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L43
```solidity
File: /src/frxETHMinter.sol
43:    mapping(bytes => bool) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L49-L50
```solidity
File: /src/frxETHMinter.sol
49:    bool public submitPaused;
50:    bool public depositEtherPaused;
```

### Using private rather than public for constants, saves gas
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L38-L39
```solidity
File: /src/frxETHMinter.sol
38:    uint256 public constant DEPOSIT_SIZE = 32 ether; // ETH 2.0 minimum deposit size
39:    uint256 public constant RATIO_PRECISION = 1e6; // 1,000,000 
```

### use shorter revert strings(less than 32 bytes) 
Every reason string takes at least 32 bytes so make sure your string fits in 32 bytes or it will become more expensive.Each extra chunk of byetes past the original 32 incurs an MSTORE which costs 3 gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L167
```solidity
File: /src/frxETHMinter.sol
167:        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```

## Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

The entire codebase seems to be using  the following
```solidity
  pragma solidity ^0.8;
```
I've highlighted the places where we can utilize the custom errors following the above.

### Use Custom Errors instead of Revert Strings to save Gas(~50 gas)
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)
Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
see [Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)


https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L41
```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol
41:        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

46:       require(minters[msg.sender] == true, "Only minters");

66:        require(minter_address != address(0), "Zero address detected");

68:        require(minters[minter_address] == false, "Address already exists");

77:        require(minter_address != address(0), "Zero address detected");
78:        require(minters[minter_address] == true, "Address nonexistant");

95:        require(_timelock_address != address(0), "Zero address detected"); 
```


https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L46
```solidity
File: /src/OperatorRegistry.sol
46:        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

137:        require(numVals != 0, "Validator stack is empty");

182:        require(numValidators() == 0, "Clear validator array first");

203:        require(_timelock_address != address(0), "Zero address detected");
```
