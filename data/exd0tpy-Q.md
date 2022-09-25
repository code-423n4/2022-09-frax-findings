Nice document and solid code base.

# Low-risk

## `recoverERC20` could be failed with some non ERC20 standard tokens.

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L200

There is function for emergencies if someone accidentally sent some ERC20 tokens.

But using IERC20 and transfer function will failed when non ERC20 standard tokens like usdt.

This should be done using safeTransfer.

# non-risk

## not using indexed with address when emit event.

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L206

event EmergencyERC20Recovered's tokenAddress need to be set indexed.

## Tip for Interface name 

https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L50

Using interface name start with `I`.



