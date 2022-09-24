Total Low Severity: 3
Total Non-Critical Severity: 9

## (1) IERC20 approve() Is Deprecated

Severity: Low

It is deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance(). If only setting the initial allowance to the value that means infinite, safeIncreaseAllowance() can be used instead.

https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#IERC20-approve-address-uint256-

## Proof Of Concept

	frxETHToken.approve(address(sfrxETHToken), msg.value);
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L75



## Recommended Mitigation Steps

Consider using safeIncreaseAllowance() / safeDecreaseAllowance() instead.



## (2) Missing Checks for Address(0x0) 

Severity: Low

## Proof Of Concept


	function submitAndDeposit(address recipient) external payable returns (uint256 shares) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L70

	function _submit(address recipient) internal nonReentrant {
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L85

	function submitAndGive(address recipient) external payable {
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L109



## Recommended Mitigation Steps

Consider adding zero-address checks in the mentioned codebase.



## (3) Contracts are not using their OZ Upgradeable counterparts

Severity: Low

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

## Proof of Concept

	import "openzeppelin-contracts/contracts/security/ReentrancyGuard.sol";
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L27

	import "openzeppelin-contracts/contracts/token/ERC20/IERC20.sol";
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L28

	import "openzeppelin-contracts/contracts/security/ReentrancyGuard.sol";
https://github.com/code-423n4/2022-09-frax/tree/main/src/sfrxETH.sol#L27

	import "openzeppelin-contracts/contracts/token/ERC20/ERC20.sol";
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L4

	import "openzeppelin-contracts/contracts/token/ERC20/IERC20.sol";
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L5

	import "openzeppelin-contracts/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L6

	import "openzeppelin-contracts/contracts/token/ERC20/extensions/ERC20Burnable.sol";
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L7



## Recommended Mitigation Steps

Where applicable, use the contracts from @openzeppelin/contracts-upgradeable instead of @openzeppelin/contracts.


## (4) Avoid Floating Pragmas: The Version Should Be Locked

Severity: Non-Critical

## Proof Of Concept

	Found usage of floating pragmas ^0.8.0 of Solidity
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETH.sol#L2

	Found usage of floating pragmas ^0.8.0 of Solidity
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L2

	Found usage of floating pragmas ^0.8.0 of Solidity
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L2

	Found usage of floating pragmas ^0.8.0 of Solidity
https://github.com/code-423n4/2022-09-frax/tree/main/src/sfrxETH.sol#L2

	Found usage of floating pragmas ^0.8.0 of Solidity 
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L2




## (5) Constants Should Be Defined Rather Than Using Magic Numbers

Severity: Non-Critical

## Proof Of Concept

        return convertToAssets(1e18);
https://github.com/code-423n4/2022-09-frax/tree/main/src/sfrxETH.sol#L55



## (6) Missing parameter validation

Severity: Non-Critical

Some parameters of constructors are not checked for invalid values.

## Proof Of Concept

    constructor(
        address depositContractAddress, 
        address frxETHAddress, 
        address sfrxETHAddress, 
        address _owner, 
        address _timelock_address,
        bytes memory _withdrawalCredential
    ) OperatorRegistry(_owner, _timelock_address, _withdrawalCredential) {
        depositContract = IDepositContract(depositContractAddress);
        frxETHToken = frxETH(frxETHAddress);
        sfrxETHToken = IsfrxETH(sfrxETHAddress);
        withholdRatio = 0; // No ETH is withheld initially
        currentWithheldETH = 0;
    }
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L52-L65

    constructor(address _owner, address _timelock_address, bytes memory _withdrawal_pubkey) Owned(_owner) {
        timelock_address = _timelock_address;
        curr_withdrawal_pubkey = _withdrawal_pubkey;
    }
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L40-L43

    constructor(
        address _creator_address,
        address _timelock_address,
        string memory _name,
        string memory _symbol
    ) 
    ERC20(_name, _symbol)
    ERC20Permit(_name) 
    Owned(_creator_address)
    {
      timelock_address = _timelock_address;
    } 
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L24-L35



## Recommended Mitigation Steps

Validate the parameters.



## (7) Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

Severity: Non-Critical

## Proof Of Concept


	contract frxETH is ERC20PermitPermissionedMint 
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETH.sol#L33

	contract frxETHMinter is OperatorRegistry, ReentrancyGuard 
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L37

	contract OperatorRegistry is Owned 
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L28

	contract sfrxETH is xERC4626, ReentrancyGuard 
https://github.com/code-423n4/2022-09-frax/tree/main/src/sfrxETH.sol#L40

	contract ERC20PermitPermissionedMint is ERC20Permit, ERC20Burnable, Owned 
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L14





## (8) Non-usage of specific imports

Severity: Non-Critical

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

## Proof Of Concept


	import "./OperatorRegistry.sol";
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L30

	import "./Utils/Owned.sol";
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L24

	import "../Utils/Owned.sol";
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L8




## Recommended Mitigation Steps

Use specific imports syntax per solidity docs recommendation.



## (9) Use a more recent version of Solidity

Severity: Non-Critical

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

## Proof Of Concept


	Found old version 0.8.0 of Solidity in [.\Projects\2022-09-frax\src\frxETH.sol]
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETH.sol#L2

	Found old version 0.8.0 of Solidity in [.\Projects\2022-09-frax\src\frxETHMinter.sol]
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L2

	Found old version 0.8.0 of Solidity in [.\Projects\2022-09-frax\src\OperatorRegistry.sol]
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L2

	Found old version 0.8.0 of Solidity in [.\Projects\2022-09-frax\src\sfrxETH.sol]
https://github.com/code-423n4/2022-09-frax/tree/main/src/sfrxETH.sol#L2

	Found old version 0.8.0 of Solidity in [.\Projects\2022-09-frax\src\ERC20\ERC20PermitPermissionedMint.sol]
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L2



## Recommended Mitigation Steps

Consider updating to a more recent solidity version.



## (10) Public Functions Not Called By The Contract Should Be Declared External Instead

Severity: Non-Critical

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

## Proof Of Concept


	function addValidator(
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L53

	function popValidators(
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L82

	function removeValidator(
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L93

	function minter_burn_from(
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L53

	function addMinter(
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L65

	function removeMinter(
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L76





## (11) Unused event may be unused code or indicative of missed emit/logic

Severity: Non-Critical

Events that are declared but not used may be indicative of unused declarations where it makes sense to remove them for better readability/maintainability/auditability, or worse indicative of a missing emit which is bad for monitoring or missing logic that would have emitted that event.

## Proof Of Concept


	event KeysCleared
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L215


## Recommended Mitigation Steps
Add emit or remove event declaration.



## (12) Use of Block.Timestamp

Severity: Non-Critical

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

## Proof Of Concept


        if (block.timestamp >= rewardsCycleEnd) { syncRewards(); } 
https://github.com/code-423n4/2022-09-frax/tree/main/src/sfrxETH.sol#L50



## Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.



