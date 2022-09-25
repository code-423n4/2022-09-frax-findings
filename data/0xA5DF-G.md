## `removeValidator()` optimization
Code: [OperatorRegistry.sol#L92-L117](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L92-L117)

Gas saved: 500,000 gas units when keeping order (in the test scenario, can vary by array size and remove index)
            8.7K gas units when don't care about order

There are 2 optimization that can be done with the `removeValidator()` function:
1. When keeping order - instead of deleting and writing the array from scratch, just move the elements that are supposed to be changed (from the remove-index and on), and then pop the last element. 
    * That would save a significant amount of gas, since the current method uses `n` `sload` + `2n` `sstore` (delete + write). While the optimization uses only `(n-i)` `sload` and `sstore` (where n is the length of the array, and i is the remove index)
2. When order doesn't matter - the `swapValidator()` uses an extra `sstore` and `sload` by writing the element to be removed to the end of the array.

Code diff:
```diff
diff --git a/src/OperatorRegistry.sol b/src/OperatorRegistry.sol
index f81094c..512e681 100644
--- a/src/OperatorRegistry.sol
+++ b/src/OperatorRegistry.sol
@@ -97,25 +97,20 @@ contract OperatorRegistry is Owned {
         // Less gassy to swap and pop
         if (dont_care_about_ordering){
             // Swap the (validator to remove) with the (last validator in the array)
-            swapValidator(remove_idx, validators.length - 1);
+            validators[remove_idx] = validators[validators.length - 1];
 
             // Pop off the validator to remove, which is now at the end of the array
             validators.pop();
         }
         // More gassy, loop
         else {
-            // Save the original validators
-            Validator[] memory original_validators = validators;
 
-            // Clear the original validators list
-            delete validators;
 
             // Fill the new validators array with all except the value to remove
-            for (uint256 i = 0; i < original_validators.length; ++i) {
-                if (i != remove_idx) {
-                    validators.push(original_validators[i]);
-                }
+            for (uint256 i = remove_idx; i < validators.length - 1; ++i) {
+                validators[i] = validators[i+1];
             }
+            validators.pop();
         }
 
         emit ValidatorRemoved(removed_pubkey, remove_idx, dont_care_about_ordering);
```

Gas diff (the `min` is when we don't care about order, `max` is when we keep order):
```diff
 │ Deployment Cost                            ┆ Deployment Size ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 2578673                                    ┆ 13669           ┆        ┆        ┆        ┆         │
+│ 2576266                                    ┆ 13657           ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                              ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -142,7 +142,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ recoverEther                               ┆ 19034           ┆ 19034  ┆ 19034  ┆ 19034  ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ removeValidator                            ┆ 17683           ┆ 286837 ┆ 286837 ┆ 555992 ┆ 2       │
+│ removeValidator                            ┆ 8940            ┆ 10871  ┆ 10871  ┆ 12802  ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ setWithholdRatio                           ┆ 1424            ┆ 12602  ┆ 12602  ┆ 23780  ┆ 2       │
```