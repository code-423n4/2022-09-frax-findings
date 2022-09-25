### 1. calldata for parameters instead of memory in external functions

It is reasonable to use `calldata` instead of `memory` for input arrays in `setWithdrawalCredential` and `getValidatorStruct`  functions to reduce gas consumption and make the code more clear.

### 2. removeValidator function gas optimization

It will be more efficient to use a little bit different logic for `removeValidator` function from `OperatorRegistry.sol` contract.

There is the following logic for the case when `dont_care_about_ordering` input parameter is `false`:

```solidity
// Save the original validators
Validator[] memory original_validators = validators;

// Clear the original validators list
delete validators;

// Fill the new validators array with all except the value to remove
for (uint256 i = 0; i < original_validators.length; ++i) {
    if (i != remove_idx) {
        validators.push(original_validators[i]);
    }
}
```

It is reasonable to not clear all the array and do redundant reads and writes from the storage, but instead this implement the following logic:


```solidity
for (uint256 i = remove_idx; i + 1 < original_validators.length; ++i) {
    validators[i] = validators[i + 1];
}
validators.pop();
```