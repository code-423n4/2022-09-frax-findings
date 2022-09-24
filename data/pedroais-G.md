Unchecked arithmetic could be used safely in many cases to save gas.

Instances :
1- https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L125

currentWithheldETH can never be bigger than contract balance since it's computed as a fraction of deposits and the fraction can never be more than 100%. Underflow is impossible.

2-https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L129
++i can be unchecked

3-https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L168

The require statement prior to the arithmetic ensures underflow is impossible. The require statement can be deleted OR the arithmetic can be made unchecked to save gas

