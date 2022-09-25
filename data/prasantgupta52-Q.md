# 1. _safeMint() should be used rather than _mint() wherever possible
### Description:
`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both open [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.
### Links to github files
[ERC20PermitPermissionedMint.sol:L60](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L60)
### Instances
```
src/ERC20/ERC20PermitPermissionedMint.sol:60:        super._mint(m_address, m_amount);
```
### Recommendations:
Use _safeMint() instead of _mint().

----
# 2. Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom
### Description
It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.
### Links to github files
[frxETHMinter.sol:L200](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L200)
### Instances
```
src/frxETHMinter.sol:200:        require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```
### Recommended Mitigation Steps
Consider using safeTransfer/safeTransferFrom or require() consistently.

----
# 3. Avoid using Floating Pragma:
### Description:
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
### Links to github files
[sfrxETH.sol:L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L2)
[frxETHMinter.sol:L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L2)
[ERC20PermitPermissionedMint.sol:L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L2)
[frxETH.sol:L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETH.sol#L2)
[OperatorRegistry.sol:L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L2)
[xERC4626.sol:L4](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L4)
### Instances
```
src/sfrxETH.sol:2:pragma solidity ^0.8.0;
src/frxETHMinter.sol:2:pragma solidity ^0.8.0;
src/ERC20/ERC20PermitPermissionedMint.sol:2:pragma solidity ^0.8.0;
src/frxETH.sol:2:pragma solidity ^0.8.0;
src/OperatorRegistry.sol:2:pragma solidity ^0.8.0;
lib/ERC4626/src/xERC4626.sol:4:pragma solidity ^0.8.0;
```
----
# 4. Use of Block.timestamp
Impact - Non-Critical
### Description
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
### Links to github files
[sfrxETH.sol:L50](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L50)
[xERC4626.sol:L40](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L40)
[xERC4626.sol:L52](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L52)
[xERC4626.sol:L60](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L60)
[xERC4626.sol:L80](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L80)
### Instances
```
src/sfrxETH.sol:50:        if (block.timestamp >= rewardsCycleEnd) { syncRewards(); } 
lib/ERC4626/src/xERC4626.sol:40:        rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
lib/ERC4626/src/xERC4626.sol:52:        if (block.timestamp >= rewardsCycleEnd_) {
lib/ERC4626/src/xERC4626.sol:60:        uint256 unlockedRewards = (lastRewardAmount_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
lib/ERC4626/src/xERC4626.sol:80:        uint32 timestamp = block.timestamp.safeCastTo32();
```
### Recommended Mitigation Steps

Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

----
# 5. Event is missing `indexed` fields
Impact - Non-Critical
Each `event` should use three `indexed` fields if there are three or more fields
### Links to github files
[frxETHMinter.sol:L207](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L207)
[ERC20PermitPermissionedMint.sol:L102](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L102)
[ERC20PermitPermissionedMint.sol:L103](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L103)
[OperatorRegistry.sol:L212](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L212)
[OperatorRegistry.sol:L214](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L214)
### Instances
```
src/frxETHMinter.sol:207:    event ETHSubmitted(address indexed sender, address indexed recipient, uint256 sent_amount, uint256 withheld_amt);
src/ERC20/ERC20PermitPermissionedMint.sol:102:    event TokenMinterBurned(address indexed from, address indexed to, uint256 amount);
src/ERC20/ERC20PermitPermissionedMint.sol:103:    event TokenMinterMinted(address indexed from, address indexed to, uint256 amount);
src/OperatorRegistry.sol:212:    event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);
src/OperatorRegistry.sol:214:    event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);
```
----
# 6. Variable names that consist of all capital letters should be reserved for const/immutable variables
### Description
If the variable needs to be different based on which class it comes from, a `view`/`pure` function should be used instead
### Links to github files
[frxETHMinter.sol:L45](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L45)
[frxETHMinter.sol:L46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L46)
[frxETHMinter.sol:L47](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L47)
[xERC4626.sol:L24](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L24)
### Instances
```
src/frxETHMinter.sol:45:    IDepositContract public immutable depositContract; // ETH 2.0 deposit contract
src/frxETHMinter.sol:46:    frxETH public immutable frxETHToken;
src/frxETHMinter.sol:47:    IsfrxETH public immutable sfrxETHToken;
lib/ERC4626/src/xERC4626.sol:24:    uint32 public immutable rewardsCycleLength;
```
----
# 7. USE SAFEAPPROVE INSTEAD OF APPROVE
### Description
This is probably an oversight since SafeERC20 was imported and safeTransfer() was used for ERC20 token transfers. Nevertheless, note that approve() will fail for certain token implementations that do not return a boolean value (). Hence it is recommend to use safeApprove().
### Links to github files
[frxETHMinter.sol:L75](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L75)
### Instances
```
src/frxETHMinter.sol:75:        frxETHToken.approve(address(sfrxETHToken), msg.value);
```
### Recommended Mitigation Steps
Update to safeapprove in the function.

----
# 8. Incosistent return type within contracts
### Description
The functions uses an unnamed return, while the rest of the contract and functions uses named returns. Please keep it consistent within contracts and accross the project if possible to improve readibility.
### Links to github files
[sfrxETH.sol:L54](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L54)
[OperatorRegistry.sol:L175](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L175)
[OperatorRegistry.sol:L197](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L197)
[xERC4626.sol:L45](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L45)
### Instances
```
src/sfrxETH.sol:54:    function pricePerShare() public view returns (uint256) {
src/OperatorRegistry.sol:175:    ) external pure returns (Validator memory) {
src/OperatorRegistry.sol:197:    function numValidators() public view returns (uint256) {
lib/ERC4626/src/xERC4626.sol:45:    function totalAssets() public view override returns (uint256) {
```
### Recomended Mitigation Step
change the un-named return type to named return

----