## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

```
2022-09-frax/lib/xERC4626.sol::4 => pragma solidity ^0.8.0;
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/OperatorRegistry.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETH.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETHMinter.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/sfrxETH.sol::2 => pragma solidity ^0.8.0;
```

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2022-09-frax/lib/xERC4626.sol::40 => rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;
2022-09-frax/lib/xERC4626.sol::52 => if (block.timestamp >= rewardsCycleEnd_) {
2022-09-frax/lib/xERC4626.sol::60 => uint256 unlockedRewards = (lastRewardAmount_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);
2022-09-frax/lib/xERC4626.sol::80 => uint32 timestamp = block.timestamp.safeCastTo32();
2022-09-frax/src/sfrxETH.sol::50 => if (block.timestamp >= rewardsCycleEnd) { syncRewards(); }
```

## [L-03] approve should be replaced with safeApprove or safeIncreaseAllowance() / safeDecreaseAllowance()

approve is subject to a known front-running attack. Consider using safeApprove() or safeIncreaseAllowance() or safeDecreaseAllowance() instead

```
2022-09-frax/src/frxETHMinter.sol::75 => frxETHToken.approve(address(sfrxETHToken), msg.value);
```

## [L-04] Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-09-frax/src/frxETHMinter.sol::200 => require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```

## [L-05] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::60 => super._mint(m_address, m_amount);
2022-09-frax/src/frxETHMinter.sol::91 => frxETHToken.minter_mint(recipient, msg.value);
```

## [N-01] Use a more recent version of solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

```
2022-09-frax/lib/xERC4626.sol::4 => pragma solidity ^0.8.0;
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/OperatorRegistry.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETH.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETHMinter.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/sfrxETH.sol::2 => pragma solidity ^0.8.0;
```

## [N-02] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::104 => event MinterAdded(address minter_address);
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::105 => event MinterRemoved(address minter_address);
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::106 => event TimelockChanged(address timelock_address);
2022-09-frax/src/OperatorRegistry.sol::208 => event TimelockChanged(address timelock_address);
2022-09-frax/src/OperatorRegistry.sol::209 => event WithdrawalCredentialSet(bytes _withdrawalCredential);
2022-09-frax/src/OperatorRegistry.sol::210 => event ValidatorAdded(bytes pubKey, bytes withdrawalCredential);
2022-09-frax/src/OperatorRegistry.sol::211 => event ValidatorArrayCleared();
2022-09-frax/src/OperatorRegistry.sol::212 => event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);
2022-09-frax/src/OperatorRegistry.sol::213 => event ValidatorsPopped(uint256 times);
2022-09-frax/src/OperatorRegistry.sol::214 => event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);
2022-09-frax/src/OperatorRegistry.sol::215 => event KeysCleared();
2022-09-frax/src/frxETHMinter.sol::205 => event EmergencyEtherRecovered(uint256 amount);
2022-09-frax/src/frxETHMinter.sol::206 => event EmergencyERC20Recovered(address tokenAddress, uint256 tokenAmount);
2022-09-frax/src/frxETHMinter.sol::208 => event DepositEtherPaused(bool new_status);
2022-09-frax/src/frxETHMinter.sol::210 => event SubmitPaused(bool new_status);
2022-09-frax/src/frxETHMinter.sol::212 => event WithholdRatioSet(uint256 newRatio);
```

## [N-03] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public.

```
2022-09-frax/lib/xERC4626.sol::45 => function totalAssets() public view override returns (uint256) {
2022-09-frax/lib/xERC4626.sol::78 => function syncRewards() public virtual {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::53 => function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::59 => function minter_mint(address m_address, uint256 m_amount) public onlyMinters {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::65 => function addMinter(address minter_address) public onlyByOwnGov {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::76 => function removeMinter(address minter_address) public onlyByOwnGov {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::94 => function setTimelock(address _timelock_address) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::82 => function popValidators(uint256 times) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::93 => function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
2022-09-frax/src/sfrxETH.sol::54 => function pricePerShare() public view returns (uint256) {
```

## [N-04] Adding a return statement when the function defines a named return variable, is redundant

It is not necessary to have both a named return and a return statement.

```
2022-09-frax/src/frxETHMinter.sol::70 => function submitAndDeposit(address recipient) external payable returns (uint256 shares) {
2022-09-frax/src/sfrxETH.sol::67 => ) external nonReentrant returns (uint256 shares) {
2022-09-frax/src/sfrxETH.sol::83 => ) external nonReentrant returns (uint256 assets) {
```
