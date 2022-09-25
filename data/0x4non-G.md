
## `withholdRatio` and `currentWithheldETH` are already 0, avoid to set variables with `0`

[frxETHMinter.sol#L63-L64](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L63-L64):
```
withholdRatio = 0; // No ETH is withheld initially
currentWithheldETH = 0;
```
`withholdRatio` and `currentWithheldETH` are already 0

## Instead of `numValidators()` use `validators.length`
Save gas avoiding calling a view function on:
[OperatorRegistry.sol#L136](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L136)
[OperatorRegistry.sol#L182](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L182)


## Use `!= 0` on requires instead of `>0` to save gas
On:
[frxETHMinter.sol#L79](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79)
[frxETHMinter.sol#L126](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L126)



## Use `++i`instead of `i++`, `++i` costs less gas than `i++`
On:
- [ERC20PermitPermissionedMint.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

## Instead of `++i`/`i++` you should use `unchecked{++i}`/`unchecked{i++}` (when its safe)

On;
- [frxETHMinter.sol#L129](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L129)
- [OperatorRegistry.sol#L63](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63)
- [OperatorRegistry.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84)
- [OperatorRegistry.sol#L114](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114)
- [ERC20/ERC20PermitPermissionedMint.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

Use this pattern;
```solidity
for (uint256 i = 0; i < numDeposits;) {
  ... code
  unchecked{ ++i; }
}
```
