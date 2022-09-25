### Use `!= 0` instead of `> 0` in a `require` statement if variable is an unsigned integer (`uint`) 
`!= 0` should be used where possible since `> 0` costs more gas

[frxETHMinter.sol: L79](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79)
```solidity
        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
```
Recommendation: Change `sfrxeth_recieved > 0` to `sfrxeth_recieved != 0` 

Note: The spelling error (recieved) was addressed under Typos in the QA Report (low / non-critical)
___
___

### State variables should not be initialized to their default values
In particular, initializing `uint` variables to their default value of `0` is unnecessary and costs gas
___
[frxETHMinter.sol: L50](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L94)
```solidity
        uint256 withheld_amt = 0;
```
Change to: 
```solidity
        uint256 withheld_amt;
```
___
___


### `Require` revert string is too long
The revert string below can be shortened to 32 characters (as shown) to save gas
___
[frxETHMinter.sol: L167](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L167)
```solidity
        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
```
Change message to `Not enough contract withheld ETH`
___
___

### Array length should not be looked up during every iteration of a `for` loop
Since calculating the array length costs gas, it's best to read the length of the array from memory before executing the loop, as demonstrated below
___
[ERC20PermitPermissionedMint.sol: L84-89](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84-L89)
```solidity
        for (uint i = 0; i < minters_array.length; i++){ 
            if (minters_array[i] == minter_address) {
                minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
                break;
            }
        }
```
Suggestion:
```solidity
        uint256 totalMintersLength = minters_array.length; 
        for (uint i = 0; i < totalMintersLength; i++){ 
            if (minters_array[i] == minter_address) {
                minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
                break;
            }
        }
```
___
Similarly for the following `for` loop:

[OperatorRegistry.sol: L114-118](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114-L118)
___
___

### Use `++i` instead of `i++` to increase count in a `for` loop
Since use of  `i++` (or equivalent counter) costs more gas, it is better to use `++i`

[ERC20PermitPermissionedMint.sol: L84-89](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84-L89)
```solidity
        for (uint i = 0; i < minters_array.length; i++){ 
            if (minters_array[i] == minter_address) {
                minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
                break;
            }
        }
```
Suggestion:
```solidity
        uint256 totalMintersLength = minters_array.length; 
        for (uint i = 0; i < totalMintersLength; ++i){ 
            if (minters_array[i] == minter_address) {
                minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
                break;
            }
        }
```
Note that I have included the previous correction (avoiding the array length look up during every `for` loop iteration) in the suggestion
___
___

### Avoid use of default "checked" behavior in a `for` loop
Underflow/overflow checks are made every time `++i` (or equivalent counter) is called. Such checks are unnecessary since `i` is already limited. Therefore, use `unchecked {++i} instead, as shown below:
 
[ERC20PermitPermissionedMint.sol: L84-89](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84-L89)
```solidity
        for (uint i = 0; i < minters_array.length; i++){ 
            if (minters_array[i] == minter_address) {
                minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
                break;
            }
        }
```
Suggestion:
```solidity
        uint256 totalMintersLength = minters_array.length; 
        for (uint i = 0; i < totalMintersLength;){ 
            if (minters_array[i] == minter_address) {
                minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
                break;

                unchecked {
                  ++i;
              }
            }
        }
```
Similarly for the following four `for` loops:

[frxETHMinter.sol: L129-154](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L129-L154)

[OperatorRegistry.sol: L63-65](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63-L65)

[OperatorRegistry.sol: L84-86](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84-L86)

[OperatorRegistry.sol: L114-117](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114-L117)

___
___
