**ERC20PermitPermissionedMint**
- L5 - The IERC20 interface is imported, but it is never used, therefore it should be deleted.

- L40/45/53/59/65 - Within the definition of functions, some use cammel case and others use underscore, this implies that the same style is not respected when defining function names.



**Operator Registry**
- L215 - The KeysCleared() event is created, but it is never used, therefore it should be deleted.

