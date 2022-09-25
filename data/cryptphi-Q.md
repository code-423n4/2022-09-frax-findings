1. Missing zero value check would cause a divide by zero error revertWhen `sfrxETH` contract is deployed, it also deploys the `xERC4626` contract. However, there is a missing zero value check in both contracts, which may cause a `divide by zero error` revert.


2. Missing zero address check
**Occurrences**
- ERC20PermitPermissionedMint.constructor() is missing a zero address check for the _timelock_address argument. 
- OperatorRegistry.constructor() is missing a zero address check for the _timelock_address argument.
- frxETHMinter.constructor() is missing a zero address check for the _timelock_address, depositContractAddress, frxETHAddress, sfrxETHAddress  arguments.


3. Setting minters_array index to 0x0 address may cause revert in a future feature

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84-L88

This block of code attempts to set the minters_array index to null after deleting the minter address in that index. Peradventure the state variable is used in a different feature in the future, it may cause reverts or have an input for the feature's function to be address(0). It is better to use a different method that will use a pop() .


4. Missing zero value check
OperatorRegistry.constructor() is missing a zero value check for _withdrawal_pubkey argument


5. No check for same validators index input in OperatorRegistry.swapValidator()
There is no check in OperatorRegistry.swapValidator() to ensure the `from_idx` and `to_idx` are not the same.