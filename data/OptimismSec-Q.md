## L-01: Round-off errors in `frxETHMinter._submit` can lead to loss of funds

Although the amount we are speaking about are tiny, perhaps consider rounding up when the `withholdRatio` is set to a non-zero value. Otherwise the possiblity exists that when extremely small `msg.value`s are sent nothing is withheld. 

The only check done for `msg.value` is to see if it is greater than zero on [line 88](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L88). No other checks are done 

```solidity
require(msg.value != 0, "Cannot submit 0");
```

However, `msg.value` on [line 96](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L96), is being divided by a constant `RATIO_PRECISION` which can lead to a result of zero for `withheld_amt`
