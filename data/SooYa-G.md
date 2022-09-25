## Loops can be optimized in several ways
To optimize this loop and make it consume less gas, we can do the folowing things:

1. Use ++i instead of i++, which is a cheaper operation (in this case there is no difference between i++ and ++i because we dont use the return value of this expression, which is the only difference between these two expression).
2. There’s no need to initialize i to its default value, it will be done automatically and it will consume more gas if it will be done (I know, sounds stupid, but trust me - it works).
So after applying all these changes, the loop will look something like this:

```
for (uint256 i; i < Variable; ++i) {
```

I suggest to change the code here:
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

## > 0 is less efficient than != 0 for unsigned integers

!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)

Proof: While it may seem that > 0 is cheaper than !=,
this is only true without the optimizer enabled and outside a require statement.
If you enable the optimizer at 10k AND you’re in a require statement,
this will save gas. You can see this tweet for more proofs:
https://twitter.com/gzeon/status/1485428085885640706

I suggest changing > 0 with != 0 here:
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126

## Cache The Length Of Arrays In Loops
The solidity compiler will always read the length of the array during each iteration. That is,

1.if it is a storage array, this is an extra sload operation (100 additional extra gas (EIP-2929 2) for each iteration except for the first),
2.if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first),
3.if it is a calldata array, this is an extra calldataload operation (3 additional gas for each iteration except for the first)

This extra costs can be avoided by caching the array length (in stack):

Here, I suggest storing the array’s length in a variable before the for-loop, and use it instead:
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

## No Need Initialize Variable Its Default Value
There’s no need to initialize variable to its default value, it will be done automatically and it will consume more gas if it will be done.

I suggest to change the code here:
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129