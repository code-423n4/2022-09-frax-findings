[G-01] USE CUSTOM ERRORS INSTEAD OF REVERT STRINGS TO SAVE GAS

Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. You could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.
Source: https://blog.soliditylang.org/2021/04/21/custom-errors/

Additional recommendation:
We can add natspec documentation to explain the custom errors in contract.

Instances:
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L87-L88
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L122
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L171
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L193
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L200
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L41
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L46
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L66
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L68
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L77-L78
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L95
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L46
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L137
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L182
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L203

[G-02]  USING UNCHECKED FOR INCREMENTS TO SAVE GAS

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration but at the cost of some code readability, as this uncheck cannot be made inline.
Source: https://github.com/ethereum/solidity/issues/10695

Instances
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

Sample affected code at file OperatorRegistry.sol#L63:

for (uint256 i = 0; i < arrayLength; ++i) {
            addValidator(validatorArray[i]);
        }
    }

can be changed to the following:

for (uint256 i; i < arrayLength;) {
        addValidator(validatorArray[i]);
            unchecked {
                  ++i;
          }
 }

Note: the default value of uint256 i is zero, so no need to assign again to save gas.

[G-03] IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

In Solidity, all variables are set to zeros by default. So, do not explicitly initialize a variable with its default value if it is zero.
As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {

Instances:
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L63-L64
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

[G-04] ++I COSTS LESS GAS COMPARED TO I++ OR I += 1 IN FOR LOOPS (~5 GAS PER ITERATION)

Pre-increments are cheaper than post-increments.

For a uint256 i variable, pre-increment ++i costs 5 gas  less than i++ with the optimizer enabled.

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name post-increment:
However, pre-increments (or pre-decrements) return the new value.

Instance:
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84