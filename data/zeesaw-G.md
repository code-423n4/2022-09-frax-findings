# Gas optimisation
- `++i` saves more gas than `i++`
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
- Use != 0 instead of > 0
```
require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
```