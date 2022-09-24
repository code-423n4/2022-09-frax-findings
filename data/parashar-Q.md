1: No zero address check for timelock address in ERC20PermitPermissionedMint: 
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L34

2: No zero address check for timelock address in OperatorRegistry:
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L41

3: No check for validator info passed like pub key length, signature length, etc
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L54