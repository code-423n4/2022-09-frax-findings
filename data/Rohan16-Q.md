## 1. safeApprove() is deprecated
### Description
Deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance().Whenever possible, use {safeIncreaseAllowance} and {safeDecreaseAllowance} instead.
This is probably an oversight since SafeERC20 was imported and safeTransfer() was used for ERC20 token transfers. Nevertheless, note that approve() will fail for certain token implementations that do not return a boolean value (). Hence it is recommend to use safeApprove().
## Proof of concept
 ### Bug

```
src/frxETHMinter.sol:75:        frxETHToken.approve(address(sfrxETHToken), msg.value);
```

### Recommendation
Update to  frxETHToken.safeApprove(spender, msg.value) in the function.

----
## 2. Avoid using Floating Pragma:
### Description
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
The pragma declared across the solution is ^0.8.0
As the compiler introduces a several interesting upgrades in newer  versions of Solidity
consider locking at this version or a more recent one.

### Bug
```
lib/ERC4626/src/xERC4626.sol:4:pragma solidity ^0.8.0;
src/frxETHMinter.sol:2:pragma solidity ^0.8.0;
src/ERC20/ERC20PermitPermissionedMint.sol:2:pragma solidity ^0.8.0;
src/sfrxETH.sol:2:pragma solidity ^0.8.0;
src/frxETH.sol:2:pragma solidity ^0.8.0;
src/OperatorRegistry.sol:2:pragma solidity ^0.8.0;
```
---
## 3. _safeMint() should be used rather than _mint() wherever possible
### Description
`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`.

### Bugs
```
src/ERC20/ERC20PermitPermissionedMint.sol:60:        super._mint(m_address, m_amount);

```

### Recommendations:

Use _safeMint() instead of _mint().

----
## 4. Event is missing indexed fields

### Description
Each event should use three indexed fields if there are three or more fields

### Bugs

```
src/OperatorRegistry.sol:212:    event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);
src/OperatorRegistry.sol:214:    event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);
```