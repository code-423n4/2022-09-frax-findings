# Low Risk Issues

## 1. The validator status in the activeValidators cant be modified

Lines of code:

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L140-L151

There is no function to modify the elements status of activeValidators. If any validator exits the verification for various reasons (penalty or exit), it cannot join the verification again through frxETHMinter.

## 2. Lack of liquidity in the design of economic models

The swap from Eth to frxEth is unidirectional. 

I can understand that this is the demand of the ETH2.0 stack at this stage. But it needs to design more schemes to ensure the liquidity of the frxETH token and add a logic about redemption commitment with a non-fixed date.

Otherwise, the existing measures are difficult to ensure non-decoupling in the future.