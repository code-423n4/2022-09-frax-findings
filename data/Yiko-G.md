https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79
Because uints are always 0 or bigger than zero using "!= 0" instead of "> 0" might save gas.

