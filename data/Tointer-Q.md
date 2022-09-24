## `frxETHMinter.sol`: `depositEther` function could become unusable
Link: https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L129

If large enough sum would be deposited to this contract, `depositEther` could become unusable, as it will run out of gas every time. Luckily, there is a governance function that could fix it by withdrawing ether, so I think this is low risk issue. But fixing it could become cumbersome and cause people to panic.
Additionaly, some bots have certain gas limits (for example, Chainlink Keepers can't run transactions with more then 1.5m gas). This could cause additional problems even sooner then `depositEther` will became unusable

Solution: limit `numDeposit` variable