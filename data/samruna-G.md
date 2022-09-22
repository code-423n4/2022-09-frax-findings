## 1. Use of custom errors
Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

# Code references:
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L87-88
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L122-140
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L160
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167-171
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L193
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L200
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L137
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L182
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L203
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L66-68
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L77-78
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L95
https://github.com/code-423n4/2022-09-frax/blob/main/src/Utils/Owner.sol#L22

# Mitigation
Replace require() with if (a != b) revert ERROR()

## 2. Long error messages
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional store, along with additional overhead for computing memory offset, etc.

# Code references:
https://github.com/code-423n4/2022-09-frax/blob/main/src/Utils/Owner.sol#L22
https://github.com/code-423n4/2022-09-frax/blob/main/src/Utils/Owner.sol#L29
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167

# Mitigation
Use error codes or shorten the error messages

## 3. Use of unchecked block in loops for arithmetic ops - to avoid overflow checks
Solidity version 0.8+ comes with an implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop. eg.

# Code references
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84


# Mitigation

e.g Let's work with a sample loop below.

for(uint256 i; i < 10; i++){
//doSomething
}

can be written as shown below.

for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}

## 4. uint comparison
Using != for uint Comparision saves gas compared to >

# Code references
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126

# Mitigation
Replace > 0 with != 0

## 5. No need to initialize uint variables
uint variables are by default assigned 0 value. Explicitly assigning it 0, costs more gas

# Code references
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94

# Mitigation
Remove explicit assignment of uint variables

## 5. Use external vs public if not called within
For all the public functions, the input parameters are copied to memory automatically, and it costs gas. If your function is only called externally, then you should explicitly mark it as external. External function’s parameters are not copied into memory but are read from calldata directly. This small optimization in your solidity code can save you a lot of gas when the function input parameters are huge.

# Code references
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L82
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L92
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L65
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L76
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L94

# Mitigation
Make public functions external if not called within the contract.