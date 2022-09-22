## Info

- The function `beforeWithdraw()` on `sfrxETH` contract (line 48) is internal but is never called inside the contract. Besides, it's not seen that `sfrxETH.sol` is inherited in any other contract on the protocol, which makes the function a piece of "dead code".

  RECOMMENDATION: Consider changing its visibilty or make sure that is called/implemented correctly inside the protocol.