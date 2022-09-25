[1] Missing Zero Address check for timelock_address in constructor

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L34
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L41

[2] Consider adding modifier to verify if array (validators) index out of bounds.
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L36

The modifier can be checked in below functions and revert with proper error string if array index out of bounds.
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L93
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L151