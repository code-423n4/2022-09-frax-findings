### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | `public` functions not called by the contract should be declared `external` instead | 8 |
| [N&#x2011;02] | `constant`s should be defined rather than using magic numbers | 1 |
| [N&#x2011;03] | Use a more recent version of solidity | 1 |
| [N&#x2011;04] | Non-library/interface files should use fixed compiler versions, not floating ones | 5 |
| [N&#x2011;05] | Event is missing `indexed` fields | 19 |
| [N&#x2011;06] | Not using the named return variables anywhere in the function is confusing | 3 |
| [N&#x2011;07] | Duplicated `require()`/`revert()` checks should be refactored to a modifier or function | 2 |

Total: 39 instances over 7 issues

## Non-critical Issues

### [N&#x2011;01]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 8 instances of this issue:*
```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

53:       function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {

59:       function minter_mint(address m_address, uint256 m_amount) public onlyMinters {

65:       function addMinter(address minter_address) public onlyByOwnGov {

76:       function removeMinter(address minter_address) public onlyByOwnGov {

94:       function setTimelock(address _timelock_address) public onlyByOwnGov {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L53

```solidity
File: src/OperatorRegistry.sol

82:       function popValidators(uint256 times) public onlyByOwnGov {

93:       function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/OperatorRegistry.sol#L82

```solidity
File: src/sfrxETH.sol

54:       function pricePerShare() public view returns (uint256) {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/sfrxETH.sol#L54

### [N&#x2011;02]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There is 1 instance of this issue:*
```solidity
File: src/sfrxETH.sol

/// @audit 1e18
55:           return convertToAssets(1e18);

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/sfrxETH.sol#L55

### [N&#x2011;03]  Use a more recent version of solidity
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

*There is 1 instance of this issue:*
```solidity
File: lib/ERC4626/src/xERC4626.sol

4:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/lib/ERC4626/src/xERC4626.sol#L4

### [N&#x2011;04]  Non-library/interface files should use fixed compiler versions, not floating ones

*There are 5 instances of this issue:*
```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L2

```solidity
File: src/frxETHMinter.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L2

```solidity
File: src/frxETH.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETH.sol#L2

```solidity
File: src/OperatorRegistry.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/OperatorRegistry.sol#L2

```solidity
File: src/sfrxETH.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/sfrxETH.sol#L2

### [N&#x2011;05]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 19 instances of this issue:*
```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

102:      event TokenMinterBurned(address indexed from, address indexed to, uint256 amount);

103:      event TokenMinterMinted(address indexed from, address indexed to, uint256 amount);

104:      event MinterAdded(address minter_address);

105:      event MinterRemoved(address minter_address);

106:      event TimelockChanged(address timelock_address);

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L102

```solidity
File: src/frxETHMinter.sol

205:      event EmergencyEtherRecovered(uint256 amount);

206:      event EmergencyERC20Recovered(address tokenAddress, uint256 tokenAmount);

207:      event ETHSubmitted(address indexed sender, address indexed recipient, uint256 sent_amount, uint256 withheld_amt);

208:      event DepositEtherPaused(bool new_status);

209:      event DepositSent(bytes indexed pubKey, bytes withdrawalCredential);

210:      event SubmitPaused(bool new_status);

211:      event WithheldETHMoved(address indexed to, uint256 amount);

212:      event WithholdRatioSet(uint256 newRatio);

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L205

```solidity
File: src/OperatorRegistry.sol

208:      event TimelockChanged(address timelock_address);

209:      event WithdrawalCredentialSet(bytes _withdrawalCredential);

210:      event ValidatorAdded(bytes pubKey, bytes withdrawalCredential);

212:      event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);

213:      event ValidatorsPopped(uint256 times);

214:      event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/OperatorRegistry.sol#L208

### [N&#x2011;06]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 3 instances of this issue:*
```solidity
File: src/frxETHMinter.sol

/// @audit shares
70:       function submitAndDeposit(address recipient) external payable returns (uint256 shares) {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L70

```solidity
File: src/sfrxETH.sol

/// @audit shares
59        function depositWithSignature(
60            uint256 assets,
61            address receiver,
62            uint256 deadline,
63            bool approveMax,
64            uint8 v,
65            bytes32 r,
66            bytes32 s
67:       ) external nonReentrant returns (uint256 shares) {

/// @audit assets
75        function mintWithSignature(
76            uint256 shares,
77            address receiver,
78            uint256 deadline,
79            bool approveMax,
80            uint8 v,
81            bytes32 r,
82            bytes32 s
83:       ) external nonReentrant returns (uint256 assets) {

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/sfrxETH.sol#L59-L67

### [N&#x2011;07]  Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
The compiler will inline the function, which will avoid `JUMP` instructions usually associated with functions

*There are 2 instances of this issue:*
```solidity
File: src/ERC20/ERC20PermitPermissionedMint.sol

77:           require(minter_address != address(0), "Zero address detected");

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/ERC20/ERC20PermitPermissionedMint.sol#L77

```solidity
File: src/frxETHMinter.sol

193:          require(success, "Invalid transfer");

```
https://github.com/code-423n4/2022-09-frax/blob/ea44bc25cfcda10f9d00df508d3f1ad3394b5f88/src/frxETHMinter.sol#L193
