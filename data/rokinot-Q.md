## Low

### Allowance is overridden for those who already deposited once

For example, if someone deposits 32 ETH, then 32 ETH again the allowance will be 32 instead of 64.

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L75

## Non-critical

### `sfrxETHToken` is not an implementation of `IsfrxETHToken`

This can be easily verified in [sfrxETHtoken.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L40), as there's no interface inheritance.

### All functions in the contract are missing dev parameters

[OperatorRegistry.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol)

### Functions don't follow camelback style like others

`minter_burn_from()` and `minter_mint()`

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L53
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L59