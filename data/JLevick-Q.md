1. Malicious admin could grief users by changing withholdRatio to 100%, recommend changing RATIO_PRECISION to a lower value. [frxETHMinter.sol#L159-L163](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L159-L163) 

2. Division before multiplication will lead to an earlier rewardsCycleEnd due to precision loss. I am assuming that the rewardsCycleEnd should be roughly the distance of the rewardsCycleLength from the starting timestamp, however due to precision loss when doing the calculation it can result in being quite a bit earlier.
**PoC:** (using 1 day, 86,400 for rewardsCycleLength & 1,664,093,282 for timestamp)
```
uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
uint32 end = ((1,664,093,282 + 86,400) / 86,400) * 86,400;
uint32 end = ((1,664,179,682) / 86,400) * 86,400;
uint32 end = 19,261 * 86,400;
uint32 end = 1,664,150,400;
timestamp + rewardsCycleLength - end = 29,282 (8.13 hours earlier then expected)
```
		

3. When initialising rewardsCycleEnd it is missing adding rewardsCycleLength to block.timestamp.safeCastTo32().  [xERC4626.sol#L40](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L40) 

4. Recommend locking the pragma in all contracts to the version that was used in testing. 