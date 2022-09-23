# 2022-09-FRAX

## Low Risk and Non-Critical Issues

### `public` functions not called by the contract should be declared `external` instead

_There are **8** instances of this issue:_

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

53:   function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {

59:   function minter_mint(address m_address, uint256 m_amount) public onlyMinters {

65:   function addMinter(address minter_address) public onlyByOwnGov {

76:   function removeMinter(address minter_address) public onlyByOwnGov {

94:   function setTimelock(address _timelock_address) public onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```solidity
File: src/OperatorRegistry.sol

82:   function popValidators(uint256 times) public onlyByOwnGov {

93:   function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```solidity
File: src/sfrxETH.sol

54:   function pricePerShare() public view returns (uint256) {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol

### Empty `receive()`/`fallback()` functions

_There are **1** instances of this issue:_

```solidity
File: src/frxETHMinter.sol

114:  receive() external payable {
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

### Non-library/interface files should use fixed compiler versions, not floating ones

_There are **5** instances of this issue:_

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

2:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```solidity
File: src/frxETH.sol

2:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol

```solidity
File: src/frxETHMinter.sol

2:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```solidity
File: src/OperatorRegistry.sol

2:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```solidity
File: src/sfrxETH.sol

2:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol

### File does not contain an SPDX identifier

_There are **1** instances of this issue:_

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

      /// @audit
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

### Event is missing `indexed` fields

_There are **5** instances of this issue:_

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

event      TokenMinterBurned(address indexed from, address indexed to, uint256 amount)

event      TokenMinterMinted(address indexed from, address indexed to, uint256 amount)
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```solidity
File: src/frxETHMinter.sol

event      ETHSubmitted(address indexed sender, address indexed recipient, uint256 sent_amount, uint256 withheld_amt)
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```solidity
File: src/OperatorRegistry.sol

event      ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering)

event      ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx)
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

### Not used import

_There are **2** instances of this issue:_

```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

5:    import "openzeppelin-contracts/contracts/token/ERC20/IERC20.sol";

6:    import "openzeppelin-contracts/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol
