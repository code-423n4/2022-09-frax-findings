https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

[G-01] No need to initialize variables (either storage, memory, local) to 0
[G-02] Use pre increment in "for loop", ++i instead of i++
[G-03] Use Unchecked for pre increment in "for loop"
[G-04]  Use unchecked wherever arithmetic operation does not under/over flow
[G-05] Move storage operation to event itself, saves one SLOAD.
Move operation https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L178 to event.
Move operation https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L185 to event.
```git diff
diff --git a/src/frxETHMinter.sol b/src/frxETHMinter.sol
index 4565883..3ab2063 100644
--- a/src/frxETHMinter.sol
+++ b/src/frxETHMinter.sol
@@ -60,8 +60,9 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
         depositContract = IDepositContract(depositContractAddress);
         frxETHToken = frxETH(frxETHAddress);
         sfrxETHToken = IsfrxETH(sfrxETHAddress);
-        withholdRatio = 0; // No ETH is withheld initially
-        currentWithheldETH = 0;
+        // no need to assign value to 0
+        //withholdRatio = 0; // No ETH is withheld initially
+        //currentWithheldETH = 0;
     }
 
     /// @notice Mint frxETH and deposit it to receive sfrxETH in one transaction
@@ -91,10 +92,13 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
         frxETHToken.minter_mint(recipient, msg.value);
 
         // Track the amount of ETH that we are keeping
-        uint256 withheld_amt = 0;
+        // No need to initialize variable to 0
+        uint256 withheld_amt;
         if (withholdRatio != 0) {
             withheld_amt = (msg.value * withholdRatio) / RATIO_PRECISION;
-            currentWithheldETH += withheld_amt;
+            unchecked {
+                currentWithheldETH += withheld_amt;
+            }
         }
 
         emit ETHSubmitted(msg.sender, recipient, msg.value, withheld_amt);
@@ -126,7 +130,7 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
         require(numDeposits > 0, "Not enough ETH in contract");
 
         // Give each deposit chunk to an empty validator
-        for (uint256 i = 0; i < numDeposits; ++i) {
+        for (uint256 i; i < numDeposits;) {
             // Get validator information
             (
                 bytes memory pubKey,
@@ -151,6 +155,9 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
             activeValidators[pubKey] = true;
 
             emit DepositSent(pubKey, withdrawalCredential);
+            unchecked {
+                ++i;
+            }
         }
     }
 
@@ -175,16 +182,18 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
 
     /// @notice Toggle allowing submites
     function togglePauseSubmits() external onlyByOwnGov {
-        submitPaused = !submitPaused;
+        //save one sload
+        //submitPaused = !submitPaused;
 
-        emit SubmitPaused(submitPaused);
+        emit SubmitPaused(submitPaused = !submitPaused);
     }
 
     /// @notice Toggle allowing depositing ETH to validators
     function togglePauseDepositEther() external onlyByOwnGov {
-        depositEtherPaused = !depositEtherPaused;
+        //save one sload
+        //depositEtherPaused = !depositEtherPaused;
 
-        emit DepositEtherPaused(depositEtherPaused);
+        emit DepositEtherPaused(depositEtherPaused = !depositEtherPaused);
     }
 
     /// @notice For emergencies if something gets stuck
```
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol
[G-01] No need to initialize variables (either storage, memory, local) to 0
[G-02] Use pre increment in "for loop", ++i instead of i++
[G-03] Use Unchecked for pre increment in "for loop"
[G-06] Cache the length of arrays in "for loop"
```git diff
diff --git a/src/ERC20/ERC20PermitPermissionedMint.sol b/src/ERC20/ERC20PermitPermissionedMint.sol
index 3bed26d..6ce6882 100644
--- a/src/ERC20/ERC20PermitPermissionedMint.sol
+++ b/src/ERC20/ERC20PermitPermissionedMint.sol
@@ -81,11 +81,15 @@ contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned {
         delete minters[minter_address];
 
         // 'Delete' from the array by setting the address to 0x0
-        for (uint i = 0; i < minters_array.length; i++){ 
+        uint _len = minters_array.length;
+        for (uint i; i < _len;){ 
             if (minters_array[i] == minter_address) {
                 minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
                 break;
             }
+            unchecked {
+                ++i;
+            }
         }
 
         emit MinterRemoved(minter_address);
```
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol
[G-01] No need to initialize variables (either storage, memory, local) to 0
[G-02] Use pre increment in "for loop", ++i instead of i++
[G-03] Use Unchecked for pre increment in "for loop"
[G-04] Use unchecked wherever arithmetic operation does not under/over flow

```git diff
diff --git a/src/OperatorRegistry.sol b/src/OperatorRegistry.sol
index f81094c..5784a40 100644
--- a/src/OperatorRegistry.sol
+++ b/src/OperatorRegistry.sol
@@ -60,8 +60,11 @@ contract OperatorRegistry is Owned {
         Reason we don't do that here is for gas */
     function addValidators(Validator[] calldata validatorArray) external onlyByOwnGov {
         uint arrayLength = validatorArray.length;
-        for (uint256 i = 0; i < arrayLength; ++i) {
+        for (uint256 i; i < arrayLength;) {
             addValidator(validatorArray[i]);
+            unchecked {
+                ++i;
+            }
         }
     }
 
@@ -81,8 +84,11 @@ contract OperatorRegistry is Owned {
     /// @notice Remove validators from the end of the validators array, in case they were added in error
     function popValidators(uint256 times) public onlyByOwnGov {
         // Loop through and remove validator entries at the end
-        for (uint256 i = 0; i < times; ++i) {
+        for (uint256 i; i < times;) {
             validators.pop();
+            unchecked {
+                ++i;
+            }
         }
 
         emit ValidatorsPopped(times);
@@ -111,10 +117,14 @@ contract OperatorRegistry is Owned {
             delete validators;
 
             // Fill the new validators array with all except the value to remove
-            for (uint256 i = 0; i < original_validators.length; ++i) {
+            uint256 _len = original_validators.length;
+            for (uint256 i; i < _len;) {
                 if (i != remove_idx) {
                     validators.push(original_validators[i]);
                 }
+                unchecked {
+                    ++i;
+                }
             }
         }
 
@@ -137,14 +147,16 @@ contract OperatorRegistry is Owned {
         require(numVals != 0, "Validator stack is empty");
 
         // Pop the last validator off the array
-        Validator memory popped = validators[numVals - 1];
-        validators.pop();
+        unchecked {
+            Validator memory popped = validators[numVals - 1];
+            validators.pop();
 
-        // Return the validator's information
-        pubKey = popped.pubKey;
-        withdrawalCredentials = curr_withdrawal_pubkey;
-        signature = popped.signature;
-        depositDataRoot = popped.depositDataRoot;
+            // Return the validator's information
+            pubKey = popped.pubKey;
+            withdrawalCredentials = curr_withdrawal_pubkey;
+            signature = popped.signature;
+            depositDataRoot = popped.depositDataRoot;
+        }
     }
 
     /// @notice Return the information of the i'th validator in the registry
```
