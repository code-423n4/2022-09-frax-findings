L-1 Missing zero approval before approval
USDT requires the approved amount to be zero before approving a non-zero amount. Even though the approved amount is expected to always be zero at the beginning of tx (since the swapper always uses all of the approved amount), it's better to approve zero to avoid any kind of errors (even if there's 1 wei of USDT left approved due to some error or rounding with the swapper, it'll make the functions that use approval unusable).

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L75

L-2  Missing checks for address(0x0) when assigning values to address state variables
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L41

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L52-L65

When these variables set incorrectly the lending pair functionality become mostly unusable

Informative

1.  Missing indexed event parameters
Each `event` should use three `indexed`  fields if there are three or more fields.

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L212
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L214
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L207
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L102-L103

2.  Non-library/interface files should use fixed compiler versions, not floating ones
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#L2

3. Missing @return NATSPEC
https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L75
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L70
