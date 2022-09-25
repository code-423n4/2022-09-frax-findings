# Non-Critical Issues
- unused event
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L215
```
 event KeysCleared();
```
- typo
```
require(minters[minter_address] == true, "Address nonexistant"); 
```
it should be `Address nonexistent`
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78
