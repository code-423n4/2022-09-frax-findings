Floating pragma used in contracts: (ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.)

e.g.:
1 - DepositContract.sol (https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/DepositContract.sol#L12)
2 - OperatorRegistry.sol (https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L2)
