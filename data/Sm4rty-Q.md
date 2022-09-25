## 1.   Check ERC20 token approve() function return value 

### Impact

With version 4.x of the ERC20 token, the approve() function returns a boolean indicating whether it was successful or not.

https://docs.openzeppelin.com/contracts/4.x/api/token/erc20#IERC20-approve-address-uint256-

Best practice is to either check the return value or use safeApprove() / safeIncreaseAllowance() which will revert if the operation was unsuccessful

https://docs.openzeppelin.com/contracts/4.x/api/token/erc20#SafeERC20-safeApprove-contract-IERC20-address-uint256-

### Instances:

```
src/frxETHMinter.sol:75:        frxETHToken.approve(address(sfrxETHToken), msg.value);
``` 
### Recommended Mitigation Steps

use safeApprove() or safeIncreaseAllowance()


-----


## 2. Avoid using Floating Pragma:

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Instances:

```
src/frxETHMinter.sol:2:pragma solidity ^0.8.0;
src/ERC20/ERC20PermitPermissionedMint.sol:2:pragma solidity ^0.8.0;
src/OperatorRegistry.sol:2:pragma solidity ^0.8.0;
src/sfrxETH.sol:2:pragma solidity ^0.8.0;
src/frxETH.sol:2:pragma solidity ^0.8.0;
lib/ERC4626/src/xERC4626.sol:4:pragma solidity ^0.8.0;
``` 
Recommend using fixed solidity version

### References:

[https://code4rena.com/reports/2022-04-phuture#g-20-use-a-more-recent-version-of-solidity](https://code4rena.com/reports/2022-04-phuture#g-20-use-a-more-recent-version-of-solidity)


-----


##  3. Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### Instances:
```
src/sfrxETH.sol:50:        if (block.timestamp >= rewardsCycleEnd) { syncRewards(); } 
lib/ERC4626/src/xERC4626.sol:39:        rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
lib/ERC4626/src/xERC4626.sol:51:        if (block.timestamp >= rewardsCycleEnd_) {
lib/ERC4626/src/xERC4626.sol:59:        uint256 unlockedRewards = (lastRewardAmount_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
lib/ERC4626/src/xERC4626.sol:79:        uint32 timestamp = block.timestamp.safeCastTo32();
``` 
### References:

https://github.com/code-423n4/2022-04-dualityfocus-findings/issues/33


-----