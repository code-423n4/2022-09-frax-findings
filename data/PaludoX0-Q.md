https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L166
It has to be checked by means of "require" that "address payable to" != 0x000 address otherwise funds could be lost forever

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L60
There'a a very unlikely case that rewardsCycleEnd_ = lastSync_ and function will revert. 
This makes call revert without giving a clear error.