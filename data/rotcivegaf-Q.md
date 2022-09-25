# QA report

## Low Risk
| L-N    |Issue|Instances|
|:------:|:----|:-------:|
| [L&#x2011;01] | Missing zero address checks | 9 |
| [L&#x2011;02] | Draft OpenZeppelin Dependencies | 1 |
| [L&#x2011;03] | Don't use `owner` and `timelock` | 2 |

Total: 12 instances over 3 issues

## Non-critical

| N-N    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | Unused imports | 2 |
| [N&#x2011;02] | Non-library/interface files should use fixed compiler versions, not floating ones | 6 |
| [N&#x2011;03] | Lint | 11 |
| [N&#x2011;04] | Event is missing `indexed` fields | 19 |
| [N&#x2011;05] | Functions, parameters and variables in snake case | 31 |
| [N&#x2011;06] | Wrong `event` parameter name | 2 |
| [N&#x2011;07] | Simplify `depositWithSignature` function | 1 |

Total: 72 instances over 7 issues

## Low Risk

### [L-01] Missing zero address checks

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

26        address _timelock_address,
```

```solidity
File: /src/sfrxETH.sol

42    constructor(ERC20 _underlying, uint32 _rewardsCycleLength)
```

```solidity
File: /src/frxETHMinter.sol

 53        address depositContractAddress,

 54        address frxETHAddress,

 55        address sfrxETHAddress,

 57        address _timelock_address,

 70    function submitAndDeposit(address recipient) external payable returns (uint256 shares) {

166    function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
```

```solidity
File: /src/OperatorRegistry.sol

/*_timelock_address parameter*/
40     constructor(address _owner, address _timelock_address, bytes memory _withdrawal_pubkey) Owned(_owner) {
```

### [L-02] Draft OpenZeppelin Dependencies

The `ERC20PermitPermissionedMint.sol` contract heredit from an OpenZeppelin contract who is still a draft and is not considered ready for mainnet use. OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.

#### Recommendation

Ensure the development team is aware of the risks of using a draft contract or consider waiting until the contract is finalised.

Otherwise, make sure that development team are aware of the risks of using a draft OpenZeppelin contract and accept the risk-benefit trade-off.

Also could evaluate changing to the [solmate contracts](https://github.com/transmissions11/solmate) since his [ERC20 implementation](https://github.com/transmissions11/solmate/blob/bff24e835192470ed38bf15dbed6084c2d723ace/src/tokens/ERC20.sol#L8) already has the [EIP-2612 permit](https://github.com/transmissions11/solmate/blob/bff24e835192470ed38bf15dbed6084c2d723ace/src/tokens/ERC20.sol#L116-L157)

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

6 import "openzeppelin-contracts/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
```

### [L-03] Don't use `owner` and `timelock`

Using a `timelock` contract gives confidence to the user, but when check `onlyByOwnGov` allow the `owner` and the `timelock`
The `owner` manipulate the contract without a lock time period

#### Recommendation

 - Use only `Owned` permission
 - Remove the `timelock_address`
 - The owner should be the `timelock` contract

```solidity
File: /src/frxETH.sol

38      address _timelock_address

40    ERC20PermitPermissionedMint(_creator_address, _timelock_address, "Frax Ether", "frxETH")
```

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

 16    address public timelock_address;

 26        address _timelock_address,

 34      timelock_address = _timelock_address;

 41        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

 94    function setTimelock(address _timelock_address) public onlyByOwnGov {
 95        require(_timelock_address != address(0), "Zero address detected");
 96        timelock_address = _timelock_address;
 97        emit TimelockChanged(_timelock_address);
 98    }

106    event TimelockChanged(address timelock_address);
```

```solidity
File: /src/frxETH.sol

38      address _timelock_address

40    ERC20PermitPermissionedMint(_creator_address, _timelock_address, "Frax Ether", "frxETH")
```

```solidity
File: /src/OperatorRegistry.sol

 38    address public timelock_address;

 40    constructor(address _owner, address _timelock_address, bytes memory _withdrawal_pubkey) Owned(_owner) {
 41        timelock_address = _timelock_address;

 46        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

202    function setTimelock(address _timelock_address) external onlyByOwnGov {
203        require(_timelock_address != address(0), "Zero address detected");
204        timelock_address = _timelock_address;
205        emit TimelockChanged(_timelock_address);
206    }

208    event TimelockChanged(address timelock_address);
```

```solidity
File: /src/frxETHMinter.sol

57        address _timelock_address,

59    ) OperatorRegistry(_owner, _timelock_address, _withdrawalCredential) {
```

## Non-critical

### [N-01] Unused imports

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

4 import "openzeppelin-contracts/contracts/token/ERC20/ERC20.sol";

5 import "openzeppelin-contracts/contracts/token/ERC20/IERC20.sol";
```

### [N‑02] Non-library/interface files should use fixed compiler versions, not floating ones

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

2 pragma solidity ^0.8.0;
```

```solidity
File: /src/frxETH.sol

2 pragma solidity ^0.8.0;
```

```solidity
File: /src/sfrxETH.sol

2 pragma solidity ^0.8.0;
```

```solidity
File: /src/frxETHMinter.sol

2 pragma solidity ^0.8.0;
```

```solidity
File: /src/OperatorRegistry.sol

2 pragma solidity ^0.8.0;
```

```solidity
File: /src/xERC4626.sol

4 pragma solidity ^0.8.0;
```

### [N‑03] Lint

Wrong identation:

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

From:
30    ERC20(_name, _symbol)
31    ERC20Permit(_name)
32    Owned(_creator_address)
To:
30        ERC20(_name, _symbol)
31        ERC20Permit(_name)
32        Owned(_creator_address)
```

```solidity
File: /src/frxETH.sol

From:
37      address _creator_address,
38      address _timelock_address
To:
37        address _creator_address,
38        address _timelock_address

From:
40    ERC20PermitPermissionedMint(_creator_address, _timelock_address, "Frax Ether", "frxETH")
To:
40        ERC20PermitPermissionedMint(_creator_address, _timelock_address, "Frax Ether", "frxETH")
```

Don't use extra parenthesis:

```solidity
File: /src/sfrxETH.sol

70        return (deposit(assets, receiver));

86        return (mint(shares, receiver));
```

Miss space:

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

84:56        for (uint i = 0; i < minters_array.length; i++){
```

Remove space:

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

63 \n
```

```solidity
File: /src/frxETH.sol

34 \n

42 \n
```

```solidity
File: /src/sfrxETH.sol

88 \n
```

```solidity
File: /src/OperatorRegistry.sol

29 \n
```

### [N‑04] Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

```solidity
File: /src/frxETHMinter.sol

205    event EmergencyEtherRecovered(uint256 amount);

206    event EmergencyERC20Recovered(address tokenAddress, uint256 tokenAmount);

207    event ETHSubmitted(address indexed sender, address indexed recipient, uint256 sent_amount, uint256 withheld_amt);

208    event DepositEtherPaused(bool new_status);

209    event DepositSent(bytes indexed pubKey, bytes withdrawalCredential);

210    event SubmitPaused(bool new_status);

211    event WithheldETHMoved(address indexed to, uint256 amount);

212    event WithholdRatioSet(uint256 newRatio);
```

```solidity
File: /src/OperatorRegistry.sol

208    event TimelockChanged(address timelock_address);

209    event WithdrawalCredentialSet(bytes _withdrawalCredential);

210    event ValidatorAdded(bytes pubKey, bytes withdrawalCredential);

212    event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);

213    event ValidatorsPopped(uint256 times);

214    event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);
```

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

102    event TokenMinterBurned(address indexed from, address indexed to, uint256 amount);

103    event TokenMinterMinted(address indexed from, address indexed to, uint256 amount);

104    event MinterAdded(address minter_address);

105    event MinterRemoved(address minter_address);

106    event TimelockChanged(address timelock_address);
```

### [N-05] Functions, parameters and variables in snake case

Use camel case for all functions, parameters and variables and snake case for constants

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

 16    address public timelock_address;

 19    address[] public minters_array; // Allowed to mint

 25        address _creator_address,

 26        address _timelock_address,

 53    function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {

 59    function minter_mint(address m_address, uint256 m_amount) public onlyMinters {

 65    function addMinter(address minter_address) public onlyByOwnGov {

 76    function removeMinter(address minter_address) public onlyByOwnGov {

 94    function setTimelock(address _timelock_address) public onlyByOwnGov {

104    event MinterAdded(address minter_address);

105    event MinterRemoved(address minter_address);

106    event TimelockChanged(address timelock_address);
```

```solidity
File: /src/frxETH.sol

37      address _creator_address,

38      address _timelock_address
```

```solidity
File: /src/frxETHMinter.sol

 57        address _timelock_address,

 78        uint256 sfrxeth_recieved = sfrxETHToken.deposit(msg.value, recipient);

 94        uint256 withheld_amt = 0;

208    event DepositEtherPaused(bool new_status);

210    event SubmitPaused(bool new_status);
```

```solidity
File: /src/OperatorRegistry.sol

 37    bytes curr_withdrawal_pubkey; // Pubkey for ETH 2.0 withdrawal creds. If you change it, you must empty the validators array

 38    address public timelock_address;

 40    constructor(address _owner, address _timelock_address, bytes memory _withdrawal_pubkey) Owned(_owner) {

 69    function swapValidator(uint256 from_idx, uint256 to_idx) public onlyByOwnGov {

 93    function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {

 95        bytes memory removed_pubkey = validators[remove_idx].pubKey;

108            Validator[] memory original_validators = validators;

181    function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {

202    function setTimelock(address _timelock_address) external onlyByOwnGov {

208    event TimelockChanged(address timelock_address);

212    event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);

214    event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);
```

### [N-06] Wrong `event` parameter name

Replace `to` parameter of `TokenMinterBurned` event to `minter`
Replace `from` parameter of `TokenMinterMinted` event to `minter`

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

102    event TokenMinterBurned(address indexed from, address indexed to, uint256 amount);
103    event TokenMinterMinted(address indexed from, address indexed to, uint256 amount);
```

### [N-07] Simplify `depositWithSignature` function

The parameter `approveMax` of `depositWithSignature` function could be remove, the permit `assets` should be always equal to deposit `assets`

File: /src/sfrxETH.sol
```solidity
    /// @notice Approve and deposit() in one transaction
    function depositWithSignature(
        uint256 assets,
        address receiver,
        uint256 deadline,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external nonReentrant returns (uint256 shares) {
        asset.permit(msg.sender, address(this), assets, deadline, v, r, s);
        return (deposit(assets, receiver));
    }
```
