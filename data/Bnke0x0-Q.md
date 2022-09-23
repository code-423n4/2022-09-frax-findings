






### [L01] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::34 => timelock_address = _timelock_address;
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::96 => timelock_address = _timelock_address;
2022-09-frax/src/OperatorRegistry.sol::41 => timelock_address = _timelock_address;
```




### [L02] Unused `receive()` function will lock Ether in contract

#### Impact
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

#### Findings:
```
2022-09-frax/src/frxETHMinter.sol::114 => receive() external payable {
```


### [L03] approve should be replaced with safeApprove or safeIncreaseAllowance() / safeDecreaseAllowance()

#### Impact
approve is subject to a known front-running attack. Consider using safeApprove instead:

#### Findings:
```
2022-09-frax/src/frxETHMinter.sol::75 => frxETHToken.approve(address(sfrxETHToken), msg.value);
```


### [L04] Return values of `transfer()`/`transferFrom()` not checked

#### Impact
Not all IERC20 implementations revert() when there’s a failure in transfer()/transferFrom(). The function signature has a boolean
 return value and they indicate errors that way instead. By not checking
 the return value, operations that should have marked as failed, may 
potentially go through without actually making a payment

#### Findings:
```
2022-09-frax/src/frxETHMinter.sol::200 => require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```


### [L05] FeeOnTransfer Tokens not Supported

#### Impact
There exist ERC20 tokens that charge a fee for every `transfer()` or `transferFrom()`.

If this tokens are unsupported, ensure there is proper documentation about it.

#### Findings:
```
2022-09-frax/src/frxETHMinter.sol::200 => require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```




### [L06] Unspecific Compiler Version Pragma

#### Impact
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/OperatorRegistry.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETH.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETHMinter.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/sfrxETH.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/xERC4626.sol::4 => pragma solidity ^0.8.0;
```




### [L07] Use Two-Step Transfer Pattern for Access Controls

#### Impact
Contracts implementing access control's, e.g. `owner`, should consider implementing a Two-Step Transfer pattern.

Otherwise it's possible that the role mistakenly transfers ownership to the wrong address, resulting in a loss of the role.

#### Findings:
```
2022-09-frax/src/frxETHMinter.sol::56 => address _owner,
```






#### Non-Critical Issues






### [N01] constants should be defined rather than using magic numbers


#### Findings:
```
2022-09-frax/src/sfrxETH.sol::55 => return convertToAssets(1e18);
```




### [N02] Use a more recent version of solidity


#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/OperatorRegistry.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETH.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETHMinter.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/sfrxETH.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/xERC4626.sol::4 => pragma solidity ^0.8.0;
```



### [N03] File is missing NatSpec


#### Findings:
```
2022-09-frax/src/frxETH.sol::1 => // SPDX-License-Identifier: GPL-2.0-or-later
```




### [N04] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

#### Findings:
```
2022-09-frax/src/xERC4626.sol::21 => using SafeCastLib for *;
```




### [N05] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields

#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::102 => event TokenMinterBurned(address indexed from, address indexed to, uint256 amount);
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::103 => event TokenMinterMinted(address indexed from, address indexed to, uint256 amount);
2022-09-frax/src/OperatorRegistry.sol::210 => event ValidatorAdded(bytes pubKey, bytes withdrawalCredential);
2022-09-frax/src/OperatorRegistry.sol::212 => event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);
2022-09-frax/src/OperatorRegistry.sol::213 => event ValidatorsPopped(uint256 times);
2022-09-frax/src/OperatorRegistry.sol::214 => event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);
2022-09-frax/src/frxETHMinter.sol::206 => event EmergencyERC20Recovered(address tokenAddress, uint256 tokenAmount);
2022-09-frax/src/frxETHMinter.sol::207 => event ETHSubmitted(address indexed sender, address indexed recipient, uint256 sent_amount, uint256 withheld_amt);
2022-09-frax/src/frxETHMinter.sol::209 => event DepositSent(bytes indexed pubKey, bytes withdrawalCredential);
2022-09-frax/src/frxETHMinter.sol::211 => event WithheldETHMoved(address indexed to, uint256 amount);
```




### [N06] Use of sensitive/NC-inclusive terms


#### Findings:
```
2022-09-frax/src/frxETHMinter.sol::49 => bool public submitPaused;
2022-09-frax/src/frxETHMinter.sol::50 => bool public depositEtherPaused;
```



### [N07] Unused file


#### Findings:
```
2022-09-frax/src/xERC4626.sol::1 => // SPDX-License-Identifier: MIT
```




### [N08] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from public to external.
#### Findings:
```

2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::53 => function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::59 => function minter_mint(address m_address, uint256 m_amount) public onlyMinters {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::65 => function addMinter(address minter_address) public onlyByOwnGov {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::76 => function removeMinter(address minter_address) public onlyByOwnGov {
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::94 => function setTimelock(address _timelock_address) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::53 => function addValidator(Validator calldata validator) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::69 => function swapValidator(uint256 from_idx, uint256 to_idx) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::82 => function popValidators(uint256 times) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::93 => function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
2022-09-frax/src/OperatorRegistry.sol::197 => function numValidators() public view returns (uint256) {
2022-09-frax/src/sfrxETH.sol::54 => function pricePerShare() public view returns (uint256) {
2022-09-frax/src/xERC4626.sol::45 => function totalAssets() public view override returns (uint256) {
2022-09-frax/src/xERC4626.sol::78 => function syncRewards() public virtual {
```



### [N09] NC-library/interface files should use fixed compiler versions, not floating ones


#### Findings:
```
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/OperatorRegistry.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETH.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/frxETHMinter.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/sfrxETH.sol::2 => pragma solidity ^0.8.0;
2022-09-frax/src/xERC4626.sol::4 => pragma solidity ^0.8.0;
```





