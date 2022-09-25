##### Gas Optimizations

Gas savings are estimated using the gas report of existing `FORGE_GAS_REPORT=true forge test --fork-url https://eth-mainnet.g.alchemy.com/v2/<API>` tests (the sum of all deployment costs and the sum of the costs of calling methods) and may vary depending on the implementation of the fix.
**Note**: method call evaluations are volatile: `≈ ±500`

|        | Issue                                                                                                                              | Instances | Estimated gas(deployments) | Estimated gas(avg method call) | Estimated gas(min method call) | Estimated gas(max method call) |
| :----: | :--------------------------------------------------------------------------------------------------------------------------------- | :-------: | :------------------------: | :----------------------------: | :----------------------------: | :----------------------------: |
| **1**  | Deleting an array element can use a more efficient algorithm                                                                       |     1     |           23 830           |            271 820             |             5 298              |            538 343             |
| **2**  | Use function instead of modifiers                                                                                                  |     4     |          177 805           |              -990              |              -389              |             1 902              |
| **3**  | Use custom errors rather than revert()/require() strings to save gas                                                               |    21     |          150 574           |              -123              |              -25               |              -184              |
| **4**  | Using bools for storage incurs overhead                                                                                            |     3     |           20 221           |              -990              |              266               |             -5 979             |
| **5**  | Unchecking arithmetics operations that can't underflow/overflow                                                                    |     7     |           18 621           |              503               |              227               |              829               |
| **6**  | `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping` |     1     |           8 208            |              -970              |              106               |             2 487              |
| **7**  | `x = x + y` is more efficient, than `x += y`                                                                                       |     4     |           5 007            |               87               |               82               |              101               |
| **8**  | It costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied            |     2     |           4 415            |               0                |               0                |               0                |
| **9**  | Don't compare boolean expressions to boolean literals                                                                              |     3     |           3 006            |              -477              |               43               |               55               |
| **10** | State variables should be cached in stack variables rather than re-reading them from storage                                       |     1     |            400             |              511               |              -21               |             4 839              |
|        | **Overall Gas Savings**                                                                                                            |  **47**   |     **419 688**(7,43%)     |      **270 705**(12,18%)       |        **5 474**(0,42%)        |      **539 594**(18,02%)       |

**Total: 47 instances over 10 issues**

---

#### 1. **Deleting an array element can use a more efficient algorithm (1 instance)**

- Deployment. Gas Saved: **23 830**

- Minumal Method Call. Gas Saved: **5 298**

- Average Method Call. Gas Saved: **271 820**

- Maximum Method Call. Gas Saved: **538 343**

##### - src/OperatorRegistry.sol:[107-116](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L107-L116)

```diff
diff --git a/src/OperatorRegistry.sol b/src/OperatorRegistry.sol
index f81094c..6732da9 100644
--- a/src/OperatorRegistry.sol
+++ b/src/OperatorRegistry.sol
@@ -104,18 +104,13 @@ contract OperatorRegistry is Owned {
  104, 104:         }
  105, 105:         // More gassy, loop
  106, 106:         else {
- 107     :-            // Save the original validators
- 108     :-            Validator[] memory original_validators = validators;
- 109     :-
- 110     :-            // Clear the original validators list
- 111     :-            delete validators;
- 112     :-
- 113     :-            // Fill the new validators array with all except the value to remove
- 114     :-            for (uint256 i = 0; i < original_validators.length; ++i) {
- 115     :-                if (i != remove_idx) {
- 116     :-                    validators.push(original_validators[i]);
+      107:+            uint256 length = validators.length - 1;
+      108:+            unchecked {
+      109:+                for (uint256 i = remove_idx; i < length;++i) {
+      110:+                    validators[i] = validators[i + 1];
  117, 111:                 }
  118, 112:             }
+      113:+            validators.pop();
  119, 114:         }
  120, 115:
  121, 116:         emit ValidatorRemoved(removed_pubkey, remove_idx, dont_care_about_ordering);
```

---

#### 2. **Use function instead of modifiers (4 instances)**

- Deployment. Gas Saved: **177 805**

- Minumal Method Call. Gas Saved: **-389**

- Average Method Call. Gas Saved: **-990**

- Maximum Method Call. Gas Saved: **1 902**

##### - src/ERC20/ERC20PermitPermissionedMint.sol:[40](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L40), [45](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L45)

```diff
diff --git a/src/ERC20/ERC20PermitPermissionedMint.sol b/src/ERC20/ERC20PermitPermissionedMint.sol
index 3bed26d..78da7f1 100644
--- a/src/ERC20/ERC20PermitPermissionedMint.sol
+++ b/src/ERC20/ERC20PermitPermissionedMint.sol
@@ -37,32 +37,33 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   37,  37:
   38,  38:     /* ========== MODIFIERS ========== */
   39,  39:
-  40     :-    modifier onlyByOwnGov() {
+       40:+    function onlyByOwnGov() private {
   41,  41:         require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
-  42     :-        _;
   43,  42:     }
   44,  43:
-  45     :-    modifier onlyMinters() {
+       44:+    function onlyMinters() private {
   46,  45:        require(minters[msg.sender] == true, "Only minters");
-  47     :-        _;
   48,  46:     }
   49,  47:
   50,  48:     /* ========== RESTRICTED FUNCTIONS ========== */
   51,  49:
   52,  50:     // Used by minters when user redeems
-  53     :-    function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {
+       51:+    function minter_burn_from(address b_address, uint256 b_amount) public {
+       52:+        onlyMinters();
   54,  53:         super.burnFrom(b_address, b_amount);
   55,  54:         emit TokenMinterBurned(b_address, msg.sender, b_amount);
   56,  55:     }
   57,  56:
   58,  57:     // This function is what other minters will call to mint new tokens
-  59     :-    function minter_mint(address m_address, uint256 m_amount) public onlyMinters {
+       58:+    function minter_mint(address m_address, uint256 m_amount) public {
+       59:+        onlyMinters();
   60,  60:         super._mint(m_address, m_amount);
   61,  61:         emit TokenMinterMinted(msg.sender, m_address, m_amount);
   62,  62:     }
   63,  63:
   64,  64:     // Adds whitelisted minters
-  65     :-    function addMinter(address minter_address) public onlyByOwnGov {
+       65:+    function addMinter(address minter_address) public {
+       66:+        onlyByOwnGov();
   66,  67:         require(minter_address != address(0), "Zero address detected");
   67,  68:
   68,  69:         require(minters[minter_address] == false, "Address already exists");
@@ -73,7 +74,8 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   73,  74:     }
   74,  75:
   75,  76:     // Remove a minter
-  76     :-    function removeMinter(address minter_address) public onlyByOwnGov {
+       77:+    function removeMinter(address minter_address) public {
+       78:+        onlyByOwnGov();
   77,  79:         require(minter_address != address(0), "Zero address detected");
   78,  80:         require(minters[minter_address] == true, "Address nonexistant");
   79,  81:
@@ -91,7 +93,8 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   91,  93:         emit MinterRemoved(minter_address);
   92,  94:     }
   93,  95:
-  94     :-    function setTimelock(address _timelock_address) public onlyByOwnGov {
+       96:+    function setTimelock(address _timelock_address) public {
+       97:+        onlyByOwnGov();
   95,  98:         require(_timelock_address != address(0), "Zero address detected");
   96,  99:         timelock_address = _timelock_address;
   97, 100:         emit TimelockChanged(_timelock_address);
```

##### - src/OperatorRegistry.sol:[45](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L45)

```diff
diff --git a/src/OperatorRegistry.sol b/src/OperatorRegistry.sol
index f81094c..fc5d16d 100644
--- a/src/OperatorRegistry.sol
+++ b/src/OperatorRegistry.sol
@@ -42,15 +42,15 @@ contract OperatorRegistry is Owned {
   42,  42:         curr_withdrawal_pubkey = _withdrawal_pubkey;
   43,  43:     }
   44,  44:
-  45     :-    modifier onlyByOwnGov() {
+       45:+    function onlyByOwnGov() internal {
   46,  46:         require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
-  47     :-        _;
   48,  47:     }
   49,  48:
   50,  49:     /// @notice Add a new validator
   51,  50:     /** @dev You should verify offchain that the validator is indeed valid before adding it
   52,  51:         Reason we don't do that here is for gas */
-  53     :-    function addValidator(Validator calldata validator) public onlyByOwnGov {
+       52:+    function addValidator(Validator calldata validator) public {
+       53:+        onlyByOwnGov();
   54,  54:         validators.push(validator);
   55,  55:         emit ValidatorAdded(validator.pubKey, curr_withdrawal_pubkey);
   56,  56:     }
@@ -58,7 +58,8 @@ contract OperatorRegistry is Owned {
   58,  58:     /// @notice Add multiple new validators in one function call
   59,  59:     /** @dev You should verify offchain that the validators are indeed valid before adding them
   60,  60:         Reason we don't do that here is for gas */
-  61     :-    function addValidators(Validator[] calldata validatorArray) external onlyByOwnGov {
+       61:+    function addValidators(Validator[] calldata validatorArray) external {
+       62:+        onlyByOwnGov();
   62,  63:         uint arrayLength = validatorArray.length;
   63,  64:         for (uint256 i = 0; i < arrayLength; ++i) {
   64,  65:             addValidator(validatorArray[i]);
@@ -66,7 +67,8 @@ contract OperatorRegistry is Owned {
   66,  67:     }
   67,  68:
   68,  69:     /// @notice Swap the location of one validator with another
-  69     :-    function swapValidator(uint256 from_idx, uint256 to_idx) public onlyByOwnGov {
+       70:+    function swapValidator(uint256 from_idx, uint256 to_idx) public {
+       71:+        onlyByOwnGov();
   70,  72:         // Get the original values
   71,  73:         Validator memory fromVal = validators[from_idx];
   72,  74:         Validator memory toVal = validators[to_idx];
@@ -79,7 +81,8 @@ contract OperatorRegistry is Owned {
   79,  81:     }
   80,  82:
   81,  83:     /// @notice Remove validators from the end of the validators array, in case they were added in error
-  82     :-    function popValidators(uint256 times) public onlyByOwnGov {
+       84:+    function popValidators(uint256 times) public {
+       85:+        onlyByOwnGov();
   83,  86:         // Loop through and remove validator entries at the end
   84,  87:         for (uint256 i = 0; i < times; ++i) {
   85,  88:             validators.pop();
@@ -90,7 +93,8 @@ contract OperatorRegistry is Owned {
   90,  93:
   91,  94:     /** @notice Remove a validator from the array. If dont_care_about_ordering is true,
   92,  95:         a swap and pop will occur instead of a more gassy loop */
-  93     :-    function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
+       96:+    function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public {
+       97:+        onlyByOwnGov();
   94,  98:         // Get the pubkey for the validator to remove (for informational purposes)
   95,  99:         bytes memory removed_pubkey = validators[remove_idx].pubKey;
   96, 100:
@@ -178,7 +182,8 @@ contract OperatorRegistry is Owned {
  178, 182:
  179, 183:     /// @notice Requires empty validator stack as changing withdrawal creds invalidates signature
  180, 184:     /// @dev May need to call clearValidatorArray() first
- 181     :-    function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
+      185:+    function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external {
+      186:+        onlyByOwnGov();
  182, 187:         require(numValidators() == 0, "Clear validator array first");
  183, 188:         curr_withdrawal_pubkey = _new_withdrawal_pubkey;
  184, 189:
@@ -187,7 +192,8 @@ contract OperatorRegistry is Owned {
  187, 192:
  188, 193:     /// @notice Empties the validator array
  189, 194:     /// @dev Need to do this before setWithdrawalCredential()
- 190     :-    function clearValidatorArray() external onlyByOwnGov {
+      195:+    function clearValidatorArray() external {
+      196:+        onlyByOwnGov();
  191, 197:         delete validators;
  192, 198:
  193, 199:         emit ValidatorArrayCleared();
@@ -199,7 +205,8 @@ contract OperatorRegistry is Owned {
  199, 205:     }
  200, 206:
  201, 207:     /// @notice Set the timelock contract
- 202     :-    function setTimelock(address _timelock_address) external onlyByOwnGov {
+      208:+    function setTimelock(address _timelock_address) external {
+      209:+        onlyByOwnGov();
  203, 210:         require(_timelock_address != address(0), "Zero address detected");
  204, 211:         timelock_address = _timelock_address;
  205, 212:         emit TimelockChanged(_timelock_address);
```

##### - src/frxETHMinter.sol:[link](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol)

```diff
diff --git a/src/frxETHMinter.sol b/src/frxETHMinter.sol
index 4565883..2690157 100644
--- a/src/frxETHMinter.sol
+++ b/src/frxETHMinter.sol
@@ -156,14 +156,16 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  156, 156:
  157, 157:     /// @param newRatio of ETH that is sent to deposit contract vs withheld, 1e6 precision
  158, 158:     /// @notice An input of 1e6 results in 100% of Eth deposited, 0% withheld
- 159     :-    function setWithholdRatio(uint256 newRatio) external onlyByOwnGov {
+      159:+    function setWithholdRatio(uint256 newRatio) external {
+      160:+        onlyByOwnGov();
  160, 161:         require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");
  161, 162:         withholdRatio = newRatio;
  162, 163:         emit WithholdRatioSet(newRatio);
  163, 164:     }
  164, 165:
  165, 166:     /// @notice Give the withheld ETH to the "to" address
- 166     :-    function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
+      167:+    function moveWithheldETH(address payable to, uint256 amount) external  {
+      168:+        onlyByOwnGov();
  167, 169:         require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
  168, 170:         currentWithheldETH -= amount;
  169, 171:
@@ -174,21 +176,24 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  174, 176:     }
  175, 177:
  176, 178:     /// @notice Toggle allowing submites
- 177     :-    function togglePauseSubmits() external onlyByOwnGov {
+      179:+    function togglePauseSubmits() external  {
+      180:+        onlyByOwnGov();
  178, 181:         submitPaused = !submitPaused;
  179, 182:
  180, 183:         emit SubmitPaused(submitPaused);
  181, 184:     }
  182, 185:
  183, 186:     /// @notice Toggle allowing depositing ETH to validators
- 184     :-    function togglePauseDepositEther() external onlyByOwnGov {
+      187:+    function togglePauseDepositEther() external {
+      188:+        onlyByOwnGov();
  185, 189:         depositEtherPaused = !depositEtherPaused;
  186, 190:
  187, 191:         emit DepositEtherPaused(depositEtherPaused);
  188, 192:     }
  189, 193:
  190, 194:     /// @notice For emergencies if something gets stuck
- 191     :-    function recoverEther(uint256 amount) external onlyByOwnGov {
+      195:+    function recoverEther(uint256 amount) external {
+      196:+        onlyByOwnGov();
  192, 197:         (bool success,) = address(owner).call{ value: amount }("");
  193, 198:         require(success, "Invalid transfer");
  194, 199:
@@ -196,7 +201,8 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  196, 201:     }
  197, 202:
  198, 203:     /// @notice For emergencies if someone accidentally sent some ERC20 tokens here
- 199     :-    function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyByOwnGov {
+      204:+    function recoverERC20(address tokenAddress, uint256 tokenAmount) external {
+      205:+        onlyByOwnGov();
  200, 206:         require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
  201, 207:
  202, 208:         emit EmergencyERC20Recovered(tokenAddress, tokenAmount);
```

---

#### 3. **Use custom errors rather than revert()/require() strings to save gas (21 instances)**

- Deployment. Gas Saved: **150 574**

- Minumal Method Call. Gas Saved: **-25**

- Average Method Call. Gas Saved: **-123**

- Maximum Method Call. Gas Saved: **-184**

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

##### - src/ERC20/ERC20PermitPermissionedMint.sol:[41](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L41), [46](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L46), [66](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L66), [68](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L68), [77-78](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L77-L78), [95](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L95)

```diff
diff --git a/src/ERC20/ERC20PermitPermissionedMint.sol b/src/ERC20/ERC20PermitPermissionedMint.sol
index 3bed26d..758ca2a 100644
--- a/src/ERC20/ERC20PermitPermissionedMint.sol
+++ b/src/ERC20/ERC20PermitPermissionedMint.sol
@@ -7,6 +7,13 @@ import "openzeppelin-contracts/contracts/token/ERC20/extensions/draft-ERC20Permi
    7,   7: import "openzeppelin-contracts/contracts/token/ERC20/extensions/ERC20Burnable.sol";
    8,   8: import "../Utils/Owned.sol";
    9,   9:
+       10:+
+       11:+error ZeroAddressDectected();
+       12:+error AddresssNonExists();
+       13:+error AddressAlreadyExists();
+       14:+error OnlyMinters();
+       15:+error NotOwnerOrTimelock();
+       16:+
   10,  17: /// @title Parent contract for frxETH.sol
   11,  18: /** @notice Combines Openzeppelin's ERC20Permit and ERC20Burnable with Synthetix's Owned.
   12,  19:     Also includes a list of authorized minters */
@@ -38,12 +45,12 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   38,  45:     /* ========== MODIFIERS ========== */
   39,  46:
   40,  47:     modifier onlyByOwnGov() {
-  41     :-        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
+       48:+        if(msg.sender != timelock_address && msg.sender != owner) revert NotOwnerOrTimelock();
   42,  49:         _;
   43,  50:     }
   44,  51:
   45,  52:     modifier onlyMinters() {
-  46     :-       require(minters[msg.sender] == true, "Only minters");
+       53:+       if(minters[msg.sender] != true) revert OnlyMinters();
   47,  54:         _;
   48,  55:     }
   49,  56:
@@ -63,9 +70,10 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   63,  70:
   64,  71:     // Adds whitelisted minters
   65,  72:     function addMinter(address minter_address) public onlyByOwnGov {
-  66     :-        require(minter_address != address(0), "Zero address detected");
+       73:+        if(minter_address == address(0)) revert ZeroAddressDectected();
   67,  74:
-  68     :-        require(minters[minter_address] == false, "Address already exists");
+       75:+
+       76:+        if(minters[minter_address] != false) revert AddressAlreadyExists();
   69,  77:         minters[minter_address] = true;
   70,  78:         minters_array.push(minter_address);
   71,  79:
@@ -74,8 +82,8 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   74,  82:
   75,  83:     // Remove a minter
   76,  84:     function removeMinter(address minter_address) public onlyByOwnGov {
-  77     :-        require(minter_address != address(0), "Zero address detected");
-  78     :-        require(minters[minter_address] == true, "Address nonexistant");
+       85:+        if(minter_address == address(0)) revert ZeroAddressDectected();
+       86:+        if(minters[minter_address] != true) revert AddresssNonExists();
   79,  87:
   80,  88:         // Delete from the mapping
   81,  89:         delete minters[minter_address];
@@ -92,7 +100,7 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   92, 100:     }
   93, 101:
   94, 102:     function setTimelock(address _timelock_address) public onlyByOwnGov {
-  95     :-        require(_timelock_address != address(0), "Zero address detected");
+      103:+        if(_timelock_address == address(0)) revert ZeroAddressDectected();
   96, 104:         timelock_address = _timelock_address;
   97, 105:         emit TimelockChanged(_timelock_address);
   98, 106:     }
```

##### - src/OperatorRegistry.sol:[46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L46), [137](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L137), [182](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L182), [203](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L203)

```diff
diff --git a/src/OperatorRegistry.sol b/src/OperatorRegistry.sol
index f81094c..ac3b7a1 100644
--- a/src/OperatorRegistry.sol
+++ b/src/OperatorRegistry.sol
@@ -23,6 +23,12 @@ pragma solidity ^0.8.0;
   23,  23:
   24,  24: import "./Utils/Owned.sol";
   25,  25:
+       26:+error NotOwnerOrTimelock();
+       27:+error ClearValidatorArrayFirst();
+       28:+error ZeroAddressDectected();
+       29:+error ValidatorStackEmpty();
+       30:+
+       31:+
   26,  32: /// @title Keeps track of validators used for ETH 2.0 staking
   27,  33: /// @notice A permissioned owner can add and removed them at will
   28,  34: contract OperatorRegistry is Owned {
@@ -43,7 +49,7 @@ contract OperatorRegistry is Owned {
   43,  49:     }
   44,  50:
   45,  51:     modifier onlyByOwnGov() {
-  46     :-        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
+       52:+        if(msg.sender != timelock_address && msg.sender != owner) revert NotOwnerOrTimelock();
   47,  53:         _;
   48,  54:     }
   49,  55:
@@ -134,7 +140,7 @@ contract OperatorRegistry is Owned {
  134, 140:     {
  135, 141:         // Make sure there are free validators available
  136, 142:         uint numVals = numValidators();
- 137     :-        require(numVals != 0, "Validator stack is empty");
+      143:+        if(numVals == 0) revert ValidatorStackEmpty();
  138, 144:
  139, 145:         // Pop the last validator off the array
  140, 146:         Validator memory popped = validators[numVals - 1];
@@ -179,7 +185,7 @@ contract OperatorRegistry is Owned {
  179, 185:     /// @notice Requires empty validator stack as changing withdrawal creds invalidates signature
  180, 186:     /// @dev May need to call clearValidatorArray() first
  181, 187:     function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
- 182     :-        require(numValidators() == 0, "Clear validator array first");
+      188:+        if(numValidators() != 0) revert ClearValidatorArrayFirst();
  183, 189:         curr_withdrawal_pubkey = _new_withdrawal_pubkey;
  184, 190:
  185, 191:         emit WithdrawalCredentialSet(_new_withdrawal_pubkey);
@@ -200,7 +206,7 @@ contract OperatorRegistry is Owned {
  200, 206:
  201, 207:     /// @notice Set the timelock contract
  202, 208:     function setTimelock(address _timelock_address) external onlyByOwnGov {
- 203     :-        require(_timelock_address != address(0), "Zero address detected");
+      209:+        if(_timelock_address == address(0)) revert ZeroAddressDectected();
  204, 210:         timelock_address = _timelock_address;
  205, 211:         emit TimelockChanged(_timelock_address);
  206, 212:     }
```

##### - src/frxETHMinter.sol:[79](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79), [87-88](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L87-L88), [122](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L122), [126](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L126), [140](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L140), [167](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L167), [171](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L171), [193](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L193), [200](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L200)

```diff
diff --git a/src/frxETHMinter.sol b/src/frxETHMinter.sol
index 4565883..f3b5abe 100644
--- a/src/frxETHMinter.sol
+++ b/src/frxETHMinter.sol
@@ -29,6 +29,17 @@ import "openzeppelin-contracts/contracts/token/ERC20/IERC20.sol";
   29,  29: import { IDepositContract } from "./DepositContract.sol";
   30,  30: import "./OperatorRegistry.sol";
   31,  31:
+       32:+error InvalidTransferERC20();
+       33:+error InvalidTransfer();
+       34:+error NotEnoughWithgeld();
+       35:+error AlreadyDeposited();
+       36:+error NotEnoughETH();
+       37:+error DepositPaused();
+       38:+error CannotSubmitZero();
+       39:+error NoSfrxETHReturned();
+       40:+error SubmitIsPaused();
+       41:+
+       42:+
   32,  43: /// @title Authorized minter contract for frxETH
   33,  44: /// @notice Accepts user-supplied ETH and converts it to frxETH (submit()), and also optionally inline stakes it for sfrxETH (submitAndDeposit())
   34,  45: /** @dev Has permission to mint frxETH.
@@ -76,7 +87,7 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
   76,  87:
   77,  88:         // Deposit the frxETH and give the generated sfrxETH to the final recipient
   78,  89:         uint256 sfrxeth_recieved = sfrxETHToken.deposit(msg.value, recipient);
-  79     :-        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
+       90:+        if(sfrxeth_recieved == 0) revert NoSfrxETHReturned();
   80,  91:
   81,  92:         return sfrxeth_recieved;
   82,  93:     }
@@ -84,8 +95,8 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
   84,  95:     /// @notice Mint frxETH to the recipient using sender's funds. Internal portion
   85,  96:     function _submit(address recipient) internal nonReentrant {
   86,  97:         // Initial pause and value checks
-  87     :-        require(!submitPaused, "Submit is paused");
-  88     :-        require(msg.value != 0, "Cannot submit 0");
+       98:+        if(submitPaused) revert SubmitIsPaused();
+       99:+        if(msg.value == 0) revert CannotSubmitZero();
   89, 100:
   90, 101:         // Give the sender frxETH
   91, 102:         frxETHToken.minter_mint(recipient, msg.value);
@@ -119,11 +130,11 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  119, 130:     /// @dev Usually a bot will call this periodically
  120, 131:     function depositEther() external nonReentrant {
  121, 132:         // Initial pause check
- 122     :-        require(!depositEtherPaused, "Depositing ETH is paused");
+      133:+        if(depositEtherPaused) revert DepositPaused();
  123, 134:
  124, 135:         // See how many deposits can be made. Truncation desired.
  125, 136:         uint256 numDeposits = (address(this).balance - currentWithheldETH) / DEPOSIT_SIZE;
- 126     :-        require(numDeposits > 0, "Not enough ETH in contract");
+      137:+        if(numDeposits == 0) revert NotEnoughETH();
  127, 138:
  128, 139:         // Give each deposit chunk to an empty validator
  129, 140:         for (uint256 i = 0; i < numDeposits; ++i) {
@@ -137,7 +148,7 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  137, 148:
  138, 149:             // Make sure the validator hasn't been deposited into already, to prevent stranding an extra 32 eth
  139, 150:             // until withdrawals are allowed
- 140     :-            require(!activeValidators[pubKey], "Validator already has 32 ETH");
+      151:+            if(activeValidators[pubKey]) revert AlreadyDeposited();
  141, 152:
  142, 153:             // Deposit the ether in the ETH 2.0 deposit contract
  143, 154:             depositContract.deposit{value: DEPOSIT_SIZE}(
@@ -164,11 +175,11 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  164, 175:
  165, 176:     /// @notice Give the withheld ETH to the "to" address
  166, 177:     function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
- 167     :-        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
+      178:+        if(amount > currentWithheldETH) revert NotEnoughWithgeld();
  168, 179:         currentWithheldETH -= amount;
  169, 180:
  170, 181:         (bool success,) = payable(to).call{ value: amount }("");
- 171     :-        require(success, "Invalid transfer");
+      182:+        if(!success) revert InvalidTransfer();
  172, 183:
  173, 184:         emit WithheldETHMoved(to, amount);
  174, 185:     }
@@ -190,14 +201,14 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  190, 201:     /// @notice For emergencies if something gets stuck
  191, 202:     function recoverEther(uint256 amount) external onlyByOwnGov {
  192, 203:         (bool success,) = address(owner).call{ value: amount }("");
- 193     :-        require(success, "Invalid transfer");
+      204:+        if(!success) revert InvalidTransfer();
  194, 205:
  195, 206:         emit EmergencyEtherRecovered(amount);
  196, 207:     }
  197, 208:
  198, 209:     /// @notice For emergencies if someone accidentally sent some ERC20 tokens here
  199, 210:     function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyByOwnGov {
- 200     :-        require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
+      211:+        if(!IERC20(tokenAddress).transfer(owner, tokenAmount)) revert InvalidTransferERC20();
  201, 212:
  202, 213:         emit EmergencyERC20Recovered(tokenAddress, tokenAmount);
  203, 214:     }
```

##### - test/frxETHMinter.t.sol

```diff
diff --git a/test/frxETHMinter.t.sol b/test/frxETHMinter.t.sol
index f4d6265..9529428 100644
--- a/test/frxETHMinter.t.sol
+++ b/test/frxETHMinter.t.sol
@@ -3,7 +3,7 @@ pragma solidity ^0.8.0;
    3,   3:
    4,   4: import { Test } from "forge-std/Test.sol";
    5,   5: import { DepositContract } from "../src/DepositContract.sol";
-   6     :-import { frxETHMinter, OperatorRegistry } from "../src/frxETHMinter.sol";
+        6:+import { frxETHMinter, OperatorRegistry, NotEnoughETH, SubmitIsPaused, DepositPaused, ValidatorStackEmpty } from "../src/frxETHMinter.sol";
    7,   7: import { frxETH } from "../src/frxETH.sol";
    8,   8: import { sfrxETH, ERC20 } from "../src/sfrxETH.sol";
    9,   9:
@@ -223,7 +223,7 @@ contract frxETHMinterTest is Test {
  223, 223:
  224, 224:         // Try having the validator deposit.
  225, 225:         // Should fail due to lack of ETH
- 226     :-        vm.expectRevert("Not enough ETH in contract");
+      226:+        vm.expectRevert(NotEnoughETH.selector);
  227, 227:         minter.depositEther();
  228, 228:
  229, 229:         // Deposit last 1 ETH for frxETH, making the total 32.
@@ -239,7 +239,7 @@ contract frxETHMinterTest is Test {
  239, 239:
  240, 240:         // Try having the validator deposit another 32 ETH.
  241, 241:         // Should fail due to lack of ETH
- 242     :-        vm.expectRevert("Not enough ETH in contract");
+      242:+        vm.expectRevert(NotEnoughETH.selector);
  243, 243:         minter.depositEther();
  244, 244:
  245, 245:         // Deposit 32 ETH for frxETH
@@ -247,14 +247,14 @@ contract frxETHMinterTest is Test {
  247, 247:
  248, 248:         // Try having the validator deposit another 32 ETH.
  249, 249:         // Should fail due to lack of a free validator
- 250     :-        vm.expectRevert("Validator stack is empty");
+      250:+        vm.expectRevert(ValidatorStackEmpty.selector);
  251, 251:         minter.depositEther();
  252, 252:
  253, 253:         // Pause submits
  254, 254:         minter.togglePauseSubmits();
  255, 255:
  256, 256:         // Try submitting while paused (should fail)
- 257     :-        vm.expectRevert("Submit is paused");
+      257:+        vm.expectRevert(SubmitIsPaused.selector);
  258, 258:         minter.submit{ value: 1 ether }();
  259, 259:
  260, 260:         // Unpause submits
@@ -264,7 +264,7 @@ contract frxETHMinterTest is Test {
  264, 264:         minter.togglePauseDepositEther();
  265, 265:
  266, 266:         // Try submitting while paused (should fail)
- 267     :-        vm.expectRevert("Depositing ETH is paused");
+      267:+        vm.expectRevert(DepositPaused.selector);
  268, 268:         minter.depositEther();
  269, 269:
  270, 270:         // Unpause validator ETH deposits
@@ -303,7 +303,7 @@ contract frxETHMinterTest is Test {
  303, 303:
  304, 304:         // Try having the validator deposit.
  305, 305:         // Should fail due to lack of ETH because half of it was withheld
- 306     :-        vm.expectRevert("Not enough ETH in contract");
+      306:+        vm.expectRevert(NotEnoughETH.selector);
  307, 307:         minter.depositEther();
  308, 308:
  309, 309:         // Deposit another 32 ETH for frxETH.
```

##### - test/frxETH_sfrxETH_combo.t.sol

```diff
diff --git a/test/frxETH_sfrxETH_combo.t.sol b/test/frxETH_sfrxETH_combo.t.sol
index 5fd1612..be1236c 100644
--- a/test/frxETH_sfrxETH_combo.t.sol
+++ b/test/frxETH_sfrxETH_combo.t.sol
@@ -5,7 +5,7 @@ pragma solidity ^0.8.0;
    5,   5: import { Test } from "forge-std/Test.sol";
    6,   6: import { frxETH } from "../src/frxETH.sol";
    7,   7: import { sfrxETH, ERC20 } from "../src/sfrxETH.sol";
-   8     :-import { frxETHMinter } from "../src/frxETHMinter.sol";
+        8:+import { frxETHMinter, NotEnoughETH, CannotSubmitZero } from "../src/frxETHMinter.sol";
    9,   9: import { SigUtils } from "../src/Utils/SigUtils.sol";
   10,  10:
   11,  11: contract xERC4626Test is Test {
@@ -822,7 +822,7 @@ contract xERC4626Test is Test {
  822, 822:         if (transfer_amount > 0) require(owner.balance > 0, "No ether. Fork mainnet or get some.");
  823, 823:
  824, 824:         vm.prank(owner);
- 825     :-        if (transfer_amount == 0) vm.expectRevert("Cannot submit 0");
+      825:+        if (transfer_amount == 0) vm.expectRevert(CannotSubmitZero.selector);
  826, 826:         frxETHMinterContract.submitAndDeposit{ value: transfer_amount }(owner);
  827, 827:
  828, 828:         assertEq(frxETHtoken.balanceOf(owner), 0); // From original mint
```

---

#### 4. **Using bools for storage incurs overhead (3 instances)**

- Deployment. Gas Saved: **20 221**

- Minumal Method Call. Gas Saved: **266**

- Average Method Call. Gas Saved: **-990**

- Maximum Method Call. Gas Saved: **-5 979**

```
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
```

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

##### - src/ERC20/ERC20PermitPermissionedMint.sol:[20](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L20)

```diff
diff --git a/src/ERC20/ERC20PermitPermissionedMint.sol b/src/ERC20/ERC20PermitPermissionedMint.sol
index 3bed26d..a5d0aab 100644
--- a/src/ERC20/ERC20PermitPermissionedMint.sol
+++ b/src/ERC20/ERC20PermitPermissionedMint.sol
@@ -17,7 +17,7 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   17,  17:
   18,  18:     // Minters
   19,  19:     address[] public minters_array; // Allowed to mint
-  20     :-    mapping(address => bool) public minters; // Mapping is also used for faster verification
+       20:+    mapping(address => uint256) public minters; // Mapping is also used for faster verification
   21,  21:
   22,  22:     /* ========== CONSTRUCTOR ========== */
   23,  23:
@@ -43,7 +43,7 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   43,  43:     }
   44,  44:
   45,  45:     modifier onlyMinters() {
-  46     :-       require(minters[msg.sender] == true, "Only minters");
+       46:+       require(minters[msg.sender] == 1, "Only minters");
   47,  47:         _;
   48,  48:     }
   49,  49:
@@ -65,8 +65,8 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   65,  65:     function addMinter(address minter_address) public onlyByOwnGov {
   66,  66:         require(minter_address != address(0), "Zero address detected");
   67,  67:
-  68     :-        require(minters[minter_address] == false, "Address already exists");
-  69     :-        minters[minter_address] = true;
+       68:+        require(minters[minter_address] == 0, "Address already exists");
+       69:+        minters[minter_address] = 1;
   70,  70:         minters_array.push(minter_address);
   71,  71:
   72,  72:         emit MinterAdded(minter_address);
@@ -75,7 +75,7 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   75,  75:     // Remove a minter
   76,  76:     function removeMinter(address minter_address) public onlyByOwnGov {
   77,  77:         require(minter_address != address(0), "Zero address detected");
-  78     :-        require(minters[minter_address] == true, "Address nonexistant");
+       78:+        require(minters[minter_address] == 1, "Address nonexistant");
   79,  79:
   80,  80:         // Delete from the mapping
   81,  81:         delete minters[minter_address];
```

##### - src/frxETHMinter.sol:[43](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L43), [49-50](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L49-L50)

```diff
diff --git a/src/frxETHMinter.sol b/src/frxETHMinter.sol
index 4565883..3036cea 100644
--- a/src/frxETHMinter.sol
+++ b/src/frxETHMinter.sol
@@ -40,14 +40,14 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
   40,  40:
   41,  41:     uint256 public withholdRatio; // What we keep and don't deposit whenever someone submit()'s ETH
   42,  42:     uint256 public currentWithheldETH; // Needed for internal tracking
-  43     :-    mapping(bytes => bool) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them
+       43:+    mapping(bytes => uint256) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them
   44,  44:
   45,  45:     IDepositContract public immutable depositContract; // ETH 2.0 deposit contract
   46,  46:     frxETH public immutable frxETHToken;
   47,  47:     IsfrxETH public immutable sfrxETHToken;
   48,  48:
-  49     :-    bool public submitPaused;
-  50     :-    bool public depositEtherPaused;
+       49:+    uint256 public submitPaused;
+       50:+    uint256 public depositEtherPaused;
   51,  51:
   52,  52:     constructor(
   53,  53:         address depositContractAddress,
@@ -84,7 +84,7 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
   84,  84:     /// @notice Mint frxETH to the recipient using sender's funds. Internal portion
   85,  85:     function _submit(address recipient) internal nonReentrant {
   86,  86:         // Initial pause and value checks
-  87     :-        require(!submitPaused, "Submit is paused");
+       87:+        require(0==submitPaused, "Submit is paused");
   88,  88:         require(msg.value != 0, "Cannot submit 0");
   89,  89:
   90,  90:         // Give the sender frxETH
@@ -119,7 +119,7 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  119, 119:     /// @dev Usually a bot will call this periodically
  120, 120:     function depositEther() external nonReentrant {
  121, 121:         // Initial pause check
- 122     :-        require(!depositEtherPaused, "Depositing ETH is paused");
+      122:+        require(0==depositEtherPaused, "Depositing ETH is paused");
  123, 123:
  124, 124:         // See how many deposits can be made. Truncation desired.
  125, 125:         uint256 numDeposits = (address(this).balance - currentWithheldETH) / DEPOSIT_SIZE;
@@ -137,7 +137,7 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  137, 137:
  138, 138:             // Make sure the validator hasn't been deposited into already, to prevent stranding an extra 32 eth
  139, 139:             // until withdrawals are allowed
- 140     :-            require(!activeValidators[pubKey], "Validator already has 32 ETH");
+      140:+            require(0==activeValidators[pubKey], "Validator already has 32 ETH");
  141, 141:
  142, 142:             // Deposit the ether in the ETH 2.0 deposit contract
  143, 143:             depositContract.deposit{value: DEPOSIT_SIZE}(
@@ -148,7 +148,7 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  148, 148:             );
  149, 149:
  150, 150:             // Set the validator as used so it won't get an extra 32 ETH
- 151     :-            activeValidators[pubKey] = true;
+      151:+            activeValidators[pubKey] = 1;
  152, 152:
  153, 153:             emit DepositSent(pubKey, withdrawalCredential);
  154, 154:         }
@@ -175,14 +175,14 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  175, 175:
  176, 176:     /// @notice Toggle allowing submites
  177, 177:     function togglePauseSubmits() external onlyByOwnGov {
- 178     :-        submitPaused = !submitPaused;
+      178:+        submitPaused = submitPaused==1?0:1;
  179, 179:
  180, 180:         emit SubmitPaused(submitPaused);
  181, 181:     }
  182, 182:
  183, 183:     /// @notice Toggle allowing depositing ETH to validators
  184, 184:     function togglePauseDepositEther() external onlyByOwnGov {
- 185     :-        depositEtherPaused = !depositEtherPaused;
+      185:+        depositEtherPaused = depositEtherPaused==1?0:1;
  186, 186:
  187, 187:         emit DepositEtherPaused(depositEtherPaused);
  188, 188:     }
@@ -205,9 +205,9 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  205, 205:     event EmergencyEtherRecovered(uint256 amount);
  206, 206:     event EmergencyERC20Recovered(address tokenAddress, uint256 tokenAmount);
  207, 207:     event ETHSubmitted(address indexed sender, address indexed recipient, uint256 sent_amount, uint256 withheld_amt);
- 208     :-    event DepositEtherPaused(bool new_status);
+      208:+    event DepositEtherPaused(uint256 new_status);
  209, 209:     event DepositSent(bytes indexed pubKey, bytes withdrawalCredential);
- 210     :-    event SubmitPaused(bool new_status);
+      210:+    event SubmitPaused(uint256 new_status);
  211, 211:     event WithheldETHMoved(address indexed to, uint256 amount);
  212, 212:     event WithholdRatioSet(uint256 newRatio);
  213, 213: }
```

---

#### 5. **Unchecking arithmetics operations that can't underflow/overflow (7 instances)**

- Deployment. Gas Saved: **18 621**

- Minumal Method Call. Gas Saved: **227**

- Average Method Call. Gas Saved: **503**

- Maximum Method Call. Gas Saved: **829**

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn't possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block: https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic

##### - src/ERC20/ERC20PermitPermissionedMint.sol:[84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

```diff
diff --git a/src/ERC20/ERC20PermitPermissionedMint.sol b/src/ERC20/ERC20PermitPermissionedMint.sol
index 3bed26d..25010cb 100644
--- a/src/ERC20/ERC20PermitPermissionedMint.sol
+++ b/src/ERC20/ERC20PermitPermissionedMint.sol
@@ -81,11 +81,14 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   81,  81:         delete minters[minter_address];
   82,  82:
   83,  83:         // 'Delete' from the array by setting the address to 0x0
-  84     :-        for (uint i = 0; i < minters_array.length; i++){
+       84:+        for (uint i = 0; i < minters_array.length;){
   85,  85:             if (minters_array[i] == minter_address) {
   86,  86:                 minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
   87,  87:                 break;
   88,  88:             }
+       89:+            unchecked {
+       90:+                ++i;
+       91:+            }
   89,  92:         }
   90,  93:
   91,  94:         emit MinterRemoved(minter_address);
```

##### - src/OperatorRegistry.sol:[63](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63), [84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84), [114](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114)

```diff
diff --git a/src/OperatorRegistry.sol b/src/OperatorRegistry.sol
index f81094c..aef4e17 100644
--- a/src/OperatorRegistry.sol
+++ b/src/OperatorRegistry.sol
@@ -60,8 +60,11 @@ contract OperatorRegistry is Owned {
   60,  60:         Reason we don't do that here is for gas */
   61,  61:     function addValidators(Validator[] calldata validatorArray) external onlyByOwnGov {
   62,  62:         uint arrayLength = validatorArray.length;
-  63     :-        for (uint256 i = 0; i < arrayLength; ++i) {
+       63:+        for (uint256 i = 0; i < arrayLength;) {
   64,  64:             addValidator(validatorArray[i]);
+       65:+            unchecked {
+       66:+                 ++i;
+       67:+            }
   65,  68:         }
   66,  69:     }
   67,  70:
@@ -81,8 +84,11 @@ contract OperatorRegistry is Owned {
   81,  84:     /// @notice Remove validators from the end of the validators array, in case they were added in error
   82,  85:     function popValidators(uint256 times) public onlyByOwnGov {
   83,  86:         // Loop through and remove validator entries at the end
-  84     :-        for (uint256 i = 0; i < times; ++i) {
+       87:+        for (uint256 i = 0; i < times;) {
   85,  88:             validators.pop();
+       89:+            unchecked {
+       90:+                 ++i;
+       91:+            }
   86,  92:         }
   87,  93:
   88,  94:         emit ValidatorsPopped(times);
@@ -111,10 +117,13 @@ contract OperatorRegistry is Owned {
  111, 117:             delete validators;
  112, 118:
  113, 119:             // Fill the new validators array with all except the value to remove
- 114     :-            for (uint256 i = 0; i < original_validators.length; ++i) {
+      120:+            for (uint256 i = 0; i < original_validators.length;) {
  115, 121:                 if (i != remove_idx) {
  116, 122:                     validators.push(original_validators[i]);
  117, 123:                 }
+      124:+                unchecked {
+      125:+                     ++i;
+      126:+                }
  118, 127:             }
  119, 128:         }
  120, 129:
```

##### - src/frxETHMinter.sol:[96](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L96), [129](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L129)

```diff
diff --git a/src/frxETHMinter.sol b/src/frxETHMinter.sol
index 4565883..4cee757 100644
--- a/src/frxETHMinter.sol
+++ b/src/frxETHMinter.sol
@@ -93,7 +93,9 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
   93,  93:         // Track the amount of ETH that we are keeping
   94,  94:         uint256 withheld_amt = 0;
   95,  95:         if (withholdRatio != 0) {
-  96     :-            withheld_amt = (msg.value * withholdRatio) / RATIO_PRECISION;
+       96:+            unchecked {
+       97:+                withheld_amt = (msg.value * withholdRatio) / RATIO_PRECISION;
+       98:+            }
   97,  99:             currentWithheldETH += withheld_amt;
   98, 100:         }
   99, 101:
@@ -126,7 +128,7 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  126, 128:         require(numDeposits > 0, "Not enough ETH in contract");
  127, 129:
  128, 130:         // Give each deposit chunk to an empty validator
- 129     :-        for (uint256 i = 0; i < numDeposits; ++i) {
+      131:+        for (uint256 i = 0; i < numDeposits;) {
  130, 132:             // Get validator information
  131, 133:             (
  132, 134:                 bytes memory pubKey,
@@ -151,6 +153,9 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  151, 153:             activeValidators[pubKey] = true;
  152, 154:
  153, 155:             emit DepositSent(pubKey, withdrawalCredential);
+      156:+            unchecked {
+      157:+                 ++i;
+      158:+            }
  154, 159:         }
  155, 160:     }
  156, 161:
```

---

#### 6. **`storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping` (1 instance)**

- Deployment. Gas Saved: **8 208**

- Minumal Method Call. Gas Saved: **106**

- Average Method Call. Gas Saved: **-970**

- Maximum Method Call. Gas Saved: **2 487**

##### - src/OperatorRegistry.sol:[161](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L161)

```diff
diff --git a/src/OperatorRegistry.sol b/src/OperatorRegistry.sol
index f81094c..b7b094d 100644
--- a/src/OperatorRegistry.sol
+++ b/src/OperatorRegistry.sol
@@ -158,7 +158,7 @@ contract OperatorRegistry is Owned {
  158, 158:             bytes32 depositDataRoot
  159, 159:         )
  160, 160:     {
- 161     :-        Validator memory v = validators[i];
+      161:+        Validator storage v = validators[i];
  162, 162:
  163, 163:         // Return the validator's information
  164, 164:         pubKey = v.pubKey;
```

---

#### 7. **`x = x + y` is more efficient, than `x += y` (4 instances)**

- Deployment. Gas Saved: **5 007**

- Minumal Method Call. Gas Saved: **82**

- Average Method Call. Gas Saved: **87**

- Maximum Method Call. Gas Saved: **101**

##### - src/frxETHMinter.sol:[97](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L97), [168](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L168)

```diff
diff --git a/src/frxETHMinter.sol b/src/frxETHMinter.sol
index 4565883..a591be9 100644
--- a/src/frxETHMinter.sol
+++ b/src/frxETHMinter.sol
@@ -94,7 +94,7 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
   94,  94:         uint256 withheld_amt = 0;
   95,  95:         if (withholdRatio != 0) {
   96,  96:             withheld_amt = (msg.value * withholdRatio) / RATIO_PRECISION;
-  97     :-            currentWithheldETH += withheld_amt;
+       97:+            currentWithheldETH = currentWithheldETH + withheld_amt;
   98,  98:         }
   99,  99:
  100, 100:         emit ETHSubmitted(msg.sender, recipient, msg.value, withheld_amt);
@@ -165,7 +165,7 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  165, 165:     /// @notice Give the withheld ETH to the "to" address
  166, 166:     function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
  167, 167:         require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
- 168     :-        currentWithheldETH -= amount;
+      168:+        currentWithheldETH = currentWithheldETH - amount;
  169, 169:
  170, 170:         (bool success,) = payable(to).call{ value: amount }("");
  171, 171:         require(success, "Invalid transfer");
```

##### - src/xERC4626.sol:[67](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L67), [72](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L72)

```diff
diff --git a/src/xERC4626.sol b/src/xERC4626.sol
index a8a4726..dea5982 100644
--- a/src/xERC4626.sol
+++ b/src/xERC4626.sol
@@ -64,12 +64,12 @@ abstract contract xERC4626 is IxERC4626, ERC4626 {
   64,  64:     // Update storedTotalAssets on withdraw/redeem
   65,  65:     function beforeWithdraw(uint256 amount, uint256 shares) internal virtual override {
   66,  66:         super.beforeWithdraw(amount, shares);
-  67     :-        storedTotalAssets -= amount;
+       67:+        storedTotalAssets = storedTotalAssets - amount;
   68,  68:     }
   69,  69:
   70,  70:     // Update storedTotalAssets on deposit/mint
   71,  71:     function afterDeposit(uint256 amount, uint256 shares) internal virtual override {
-  72     :-        storedTotalAssets += amount;
+       72:+        storedTotalAssets = storedTotalAssets + amount;
   73,  73:         super.afterDeposit(amount, shares);
   74,  74:     }
   75,  75:
```

---

##### 8. **It costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied (2 instances)**

- Deployment. Gas Saved: **4 415**

- Minumal Method Call. Gas Saved: **0**

- Average Method Call. Gas Saved: **0**

- Maximum Method Call. Gas Saved: **0**

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address...). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

##### - src/frxETHMinter.sol:[63-64](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L63-L64)

```diff
diff --git a/src/frxETHMinter.sol b/src/frxETHMinter.sol
index 4565883..b0f66a8 100644
--- a/src/frxETHMinter.sol
+++ b/src/frxETHMinter.sol
@@ -60,8 +60,6 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
   60,  60:         depositContract = IDepositContract(depositContractAddress);
   61,  61:         frxETHToken = frxETH(frxETHAddress);
   62,  62:         sfrxETHToken = IsfrxETH(sfrxETHAddress);
-  63     :-        withholdRatio = 0; // No ETH is withheld initially
-  64     :-        currentWithheldETH = 0;
   65,  63:     }
   66,  64:
   67,  65:     /// @notice Mint frxETH and deposit it to receive sfrxETH in one transaction
```

---

#### 9. **Don't compare boolean expressions to boolean literals (3 instances)**

- Deployment. Gas Saved: **3 006**

- Minumal Method Call. Gas Saved: **43**

- Average Method Call. Gas Saved: **-477**

- Maximum Method Call. Gas Saved: **55**

##### - src/ERC20/ERC20PermitPermissionedMint.sol:[46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L46), [68](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L68), [78](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L78)

```diff
diff --git a/src/ERC20/ERC20PermitPermissionedMint.sol b/src/ERC20/ERC20PermitPermissionedMint.sol
index 3bed26d..860d2c4 100644
--- a/src/ERC20/ERC20PermitPermissionedMint.sol
+++ b/src/ERC20/ERC20PermitPermissionedMint.sol
@@ -43,7 +43,7 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   43,  43:     }
   44,  44:
   45,  45:     modifier onlyMinters() {
-  46     :-       require(minters[msg.sender] == true, "Only minters");
+       46:+       require(minters[msg.sender], "Only minters");
   47,  47:         _;
   48,  48:     }
   49,  49:
@@ -65,7 +65,7 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   65,  65:     function addMinter(address minter_address) public onlyByOwnGov {
   66,  66:         require(minter_address != address(0), "Zero address detected");
   67,  67:
-  68     :-        require(minters[minter_address] == false, "Address already exists");
+       68:+        require(!minters[minter_address], "Address already exists");
   69,  69:         minters[minter_address] = true;
   70,  70:         minters_array.push(minter_address);
   71,  71:
@@ -75,7 +75,7 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
   75,  75:     // Remove a minter
   76,  76:     function removeMinter(address minter_address) public onlyByOwnGov {
   77,  77:         require(minter_address != address(0), "Zero address detected");
-  78     :-        require(minters[minter_address] == true, "Address nonexistant");
+       78:+        require(minters[minter_address], "Address nonexistant");
   79,  79:
   80,  80:         // Delete from the mapping
   81,  81:         delete minters[minter_address];
```

---

#### 10. **State variables should be cached in stack variables rather than re-reading them from storage (1 instances)**

- Deployment. Gas Saved: **400**

- Minumal Method Call. Gas Saved: **-21**

- Average Method Call. Gas Saved: **511**

- Maximum Method Call. Gas Saved: **4 839**

##### - src/frxETHMinter.sol:[95-96](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L95-L96)

```diff
diff --git a/src/frxETHMinter.sol b/src/frxETHMinter.sol
index 4565883..802e94b 100644
--- a/src/frxETHMinter.sol
+++ b/src/frxETHMinter.sol
@@ -92,8 +92,9 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
   92,  92:
   93,  93:         // Track the amount of ETH that we are keeping
   94,  94:         uint256 withheld_amt = 0;
-  95     :-        if (withholdRatio != 0) {
-  96     :-            withheld_amt = (msg.value * withholdRatio) / RATIO_PRECISION;
+       95:+        uint256 _withholdRatio;
+       96:+        if ((_withholdRatio = withholdRatio) != 0) {
+       97:+            withheld_amt = (msg.value * _withholdRatio) / RATIO_PRECISION;
   97,  98:             currentWithheldETH += withheld_amt;
   98,  99:         }
   99, 100:
```

---

##### 99. **Overall gas savings**

- Deployment. Gas Saved: **419 688**

- Minumal Method Call. Gas Saved: **5 474**

- Average Method Call. Gas Saved: **270 705**

- Maximum Method Call. Gas Saved: **539 594**

The result of merging all optimizations

```diff
diff --git a/original.txt b/foundry.txt
index 83cd313..4a4aaa0 100644
--- a/original.txt
+++ b/foundry.txt
@@ -3,13 +3,13 @@
 ╞════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
 │ Deployment Cost                ┆ Deployment Size ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 1439975                        ┆ 7889            ┆       ┆        ┆       ┆         │
+│ 1353480                        ┆ 7457            ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                  ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ DOMAIN_SEPARATOR               ┆ 365             ┆ 365   ┆ 365    ┆ 365   ┆ 30      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ addMinter                      ┆ 46593           ┆ 59107 ┆ 68493  ┆ 68493 ┆ 70      │
+│ addMinter                      ┆ 46508           ┆ 59022 ┆ 68408  ┆ 68408 ┆ 70      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ allowance                      ┆ 826             ┆ 1048  ┆ 826    ┆ 2826  ┆ 9       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -19,7 +19,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ decimals                       ┆ 289             ┆ 289   ┆ 289    ┆ 289   ┆ 40      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ minter_mint                    ┆ 4906            ┆ 37627 ┆ 50706  ┆ 50706 ┆ 37      │
+│ minter_mint                    ┆ 4918            ┆ 37639 ┆ 50718  ┆ 50718 ┆ 37      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ nonces                         ┆ 661             ┆ 1751  ┆ 2661   ┆ 2661  ┆ 11      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -45,45 +45,45 @@
 ╞════════════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
 │ Deployment Cost                            ┆ Deployment Size ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 2575261                                    ┆ 13642           ┆        ┆        ┆        ┆         │
+│ 2242068                                    ┆ 11990           ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                              ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ addValidator                               ┆ 186231          ┆ 202191 ┆ 212831 ┆ 212831 ┆ 5       │
+│ addValidator                               ┆ 186267          ┆ 202227 ┆ 212867 ┆ 212867 ┆ 5       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ addValidators                              ┆ 396361          ┆ 688820 ┆ 579148 ┆ 944722 ┆ 5       │
+│ addValidators                              ┆ 396343          ┆ 688759 ┆ 579103 ┆ 944623 ┆ 5       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ currentWithheldETH                         ┆ 406             ┆ 406    ┆ 406    ┆ 406    ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ depositEther                               ┆ 3574            ┆ 27335  ┆ 5851   ┆ 63547  ┆ 9       │
+│ depositEther                               ┆ 3494            ┆ 28015  ┆ 7771   ┆ 64976  ┆ 9       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ fallback                                   ┆ 8745            ┆ 8745   ┆ 8745   ┆ 8745   ┆ 1       │
+│ fallback                                   ┆ 8759            ┆ 8759   ┆ 8759   ┆ 8759   ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ getValidator                               ┆ 4756            ┆ 4756   ┆ 4756   ┆ 4756   ┆ 8       │
+│ getValidator                               ┆ 4650            ┆ 4650   ┆ 4650   ┆ 4650   ┆ 8       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ numValidators                              ┆ 404             ┆ 404    ┆ 404    ┆ 404    ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ popValidators                              ┆ 5065            ┆ 5065   ┆ 5065   ┆ 5065   ┆ 1       │
+│ popValidators                              ┆ 4993            ┆ 4993   ┆ 4993   ┆ 4993   ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ recoverERC20                               ┆ 23675           ┆ 23675  ┆ 23675  ┆ 23675  ┆ 1       │
+│ recoverERC20                               ┆ 23704           ┆ 23704  ┆ 23704  ┆ 23704  ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ recoverEther                               ┆ 19034           ┆ 19034  ┆ 19034  ┆ 19034  ┆ 1       │
+│ recoverEther                               ┆ 19070           ┆ 19070  ┆ 19070  ┆ 19070  ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ removeValidator                            ┆ 17629           ┆ 286800 ┆ 286800 ┆ 555972 ┆ 2       │
+│ removeValidator                            ┆ 12360           ┆ 15023  ┆ 15023  ┆ 17687  ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setWithholdRatio                           ┆ 1424            ┆ 12602  ┆ 12602  ┆ 23780  ┆ 2       │
+│ setWithholdRatio                           ┆ 1453            ┆ 12634  ┆ 12634  ┆ 23816  ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ submit                                     ┆ 3508            ┆ 33470  ┆ 9321   ┆ 83051  ┆ 7       │
+│ submit                                     ┆ 3444            ┆ 33407  ┆ 9128   ┆ 82810  ┆ 7       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ submitAndDeposit                           ┆ 154463          ┆ 154463 ┆ 154463 ┆ 154463 ┆ 1       │
+│ submitAndDeposit                           ┆ 154476          ┆ 154476 ┆ 154476 ┆ 154476 ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ submitAndGive                              ┆ 30318           ┆ 30318  ┆ 30318  ┆ 30318  ┆ 1       │
+│ submitAndGive                              ┆ 30335           ┆ 30335  ┆ 30335  ┆ 30335  ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapValidator                              ┆ 15342           ┆ 15342  ┆ 15342  ┆ 15342  ┆ 1       │
+│ swapValidator                              ┆ 15378           ┆ 15378  ┆ 15378  ┆ 15378  ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ togglePauseDepositEther                    ┆ 1494            ┆ 11630  ┆ 11630  ┆ 21767  ┆ 2       │
+│ togglePauseDepositEther                    ┆ 1475            ┆ 11614  ┆ 11614  ┆ 21753  ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ togglePauseSubmits                         ┆ 1485            ┆ 11620  ┆ 11620  ┆ 21756  ┆ 2       │
+│ togglePauseSubmits                         ┆ 1502            ┆ 11644  ┆ 11644  ┆ 21787  ┆ 2       │
 ╰────────────────────────────────────────────┴─────────────────┴────────┴────────┴────────┴─────────╯
 ╭──────────────────────────────────┬─────────────────┬────────┬────────┬────────┬─────────╮
 │ src/sfrxETH.sol:sfrxETH contract ┆                 ┆        ┆        ┆        ┆         │
```
