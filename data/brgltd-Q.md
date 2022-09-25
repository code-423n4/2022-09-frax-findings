# [NC-01] Public functions not called by the contract can be refactored to be external functions

```
function popValidators(uint256 times) public onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L82

```
function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L93

# [NC-02] Lack of checks-effects-interactions

```
function depositEther() external nonReentrant {
    // Initial pause check
    require(!depositEtherPaused, "Depositing ETH is paused");

    // See how many deposits can be made. Truncation desired.
    uint256 numDeposits = (address(this).balance - currentWithheldETH) / DEPOSIT_SIZE;
    require(numDeposits > 0, "Not enough ETH in contract");

    // Give each deposit chunk to an empty validator
    for (uint256 i = 0; i < numDeposits; ++i) {
        // Get validator information
        (
            bytes memory pubKey,
            bytes memory withdrawalCredential,
            bytes memory signature,
            bytes32 depositDataRoot
        ) = getNextValidator(); // Will revert if there are not enough free validators

        // Make sure the validator hasn't been deposited into already, to prevent stranding an extra 32 eth
        // until withdrawals are allowed
        require(!activeValidators[pubKey], "Validator already has 32 ETH");

        // Deposit the ether in the ETH 2.0 deposit contract
        depositContract.deposit{value: DEPOSIT_SIZE}(
            pubKey,
            withdrawalCredential,
            signature,
            depositDataRoot
        );

        // Set the validator as used so it won't get an extra 32 ETH
        activeValidators[pubKey] = true;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L120-L151

The `depositEther()` function is updating the state variable `activeValidators` before the `depositContract.deposit()` call. The function is not at risk of reentrancy due to the `nonReentrant` modifier. However, to follow the [checks-effects-interactions](https://docs.soliditylang.org/en/v0.8.17/security-considerations.html#re-entrancy), state modifications should be computed before the external call to ensure the state is only updated on successul calls.

In this case, the following modification could be made.

```
$ git diff --no-index frxETHMinter.sol.orig frxETHMinter.sol
diff --git a/frxETHMinter.sol.orig b/frxETHMinter.sol
index a33bdbe..6f5fd2e 100644
--- a/frxETHMinter.sol.orig
+++ b/frxETHMinter.sol
@@ -139,6 +139,9 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
             // until withdrawals are allowed
             require(!activeValidators[pubKey], "Validator already has 32 ETH");

+            // Set the validator as used so it won't get an extra 32 ETH
+            activeValidators[pubKey] = true;
+
             // Deposit the ether in the ETH 2.0 deposit contract
             depositContract.deposit{value: DEPOSIT_SIZE}(
                 pubKey,
@@ -147,9 +150,6 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
                 depositDataRoot
             );

-            // Set the validator as used so it won't get an extra 32 ETH
-            activeValidators[pubKey] = true;
-
             emit DepositSent(pubKey, withdrawalCredential);
         }
     }
```

# [NC-03] Normalize between snake_case and mixedCase

Some arguments and variables are written in snake_case

```
function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L93

```
bytes memory removed_pubkey = validators[remove_idx].pubKey;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L95

While others are mixedCase.

```
function getValidatorStruct(
    bytes memory pubKey,
    bytes memory signature,
    bytes32 depositDataRoot
) external pure returns (Validator memory) {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L171-L175

```
uint arrayLength = validatorArray.length;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L62

Consider using only one convention. Note that the solidity docs recommends mixedCase for both [function arguments](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#function-argument-names) and [local variables](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#local-and-state-variable-names).

# [NC-04] The algorithm to remove a validator while preserving the order can be improved

The current algorithm will create a new array and push every element except the one to be excluded.

```
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

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L107-L118

A better approach would be to switch elements (i with i + 1) starting from the `remove_idx` until the last index, and then pop the last element. This would also improve performance and gas consumption if the target element is located at the end of the array (since it won't traverse the entire array anymore).

```
for (uint256 i = remove_idx; i < validators.length - 1; ++i) {
    validators[i] = validators[i + 1]
}
validators.pop();
```

# [NC-05] Missing zero-address checks in constructors

Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are initialized incorrectly.

```
constructor(
    address depositContractAddress,
    address frxETHAddress,
    address sfrxETHAddress,
    address _owner,
    address _timelock_address,
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L52-L57

```
constructor(
    address _creator_address,
    address _timelock_address,
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L24-L26

# [NC-06] Non library/interface files should use fixed compiler verion

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package.

The following 5 contracts are not libraries and are floating the pragma.

```
src/frxETH.sol,
src/sfrxETH.sol,
src/ERC20/ERC20PermitPermissionedMint.sol,
src/frxETHMinter.sol,
src/OperatorRegistry.sol,
```

# [NC-07] block.timestamp should not be downcasted to 32 bits

32-bits will [stop working](https://en.wikipedia.org/wiki/Year_2038_problem) after January 19th 2038 for storing unix timestamp. This is likely not a real issue for the protocol, since 16 years is still a lot of time. However, from a purely software resilience and quality assurance point of view, I would recommend to use 40 bits when downcasting block.timestamp.

# [NC-08] Modify the order of functions and events

The official solidity style guide [recommends](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout) to declare an event before functions. Also, another [recommendation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) is to declare public functions bellow external functions.

```
/// @notice Empties the validator array
/// @dev Need to do this before setWithdrawalCredential()
function clearValidatorArray() external onlyByOwnGov {
    delete validators;

    emit ValidatorArrayCleared();
}

/// @notice Returns the number of validators
function numValidators() public view returns (uint256) {
    return validators.length;
}

/// @notice Set the timelock contract
function setTimelock(address _timelock_address) external onlyByOwnGov {
    require(_timelock_address != address(0), "Zero address detected");
    timelock_address = _timelock_address;
    emit TimelockChanged(_timelock_address);
}

event TimelockChanged(address timelock_address);
event WithdrawalCredentialSet(bytes _withdrawalCredential);
event ValidatorAdded(bytes pubKey, bytes withdrawalCredential);
event ValidatorArrayCleared();
event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);
event ValidatorsPopped(uint256 times);
event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);
event KeysCleared();
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L188-L215

# [NC-09] Critical changes should use two-step procedure

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

```
function setTimelock(address _timelock_address) public onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L94

```
function setWithholdRatio(uint256 newRatio) external onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L181

```
function setTimelock(address _timelock_address) external onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L202

# [NC-10] Inconsistent use of return named variable

Consider removing the `shares` state variable or refactoring `submitAndDeposit()` to use the return named variable.

```
function submitAndDeposit(address recipient) external payable returns (uint256 shares) {
    // Give the frxETH to this contract after it is generated
    _submit(address(this));

    // Approve frxETH to sfrxETH for staking
    frxETHToken.approve(address(sfrxETHToken), msg.value);

    // Deposit the frxETH and give the generated sfrxETH to the final recipient
    uint256 sfrxeth_recieved = sfrxETHToken.deposit(msg.value, recipient);
    require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

    return sfrxeth_recieved;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L70-L82
