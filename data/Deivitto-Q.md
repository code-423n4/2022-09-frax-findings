
# QA
# Low
## Not checking 0 value would revert on `xERC4626` constructor 
### Impact
Revert on constructor that can be not expected neither checked
### Proof of Concept
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L38

### Recommended Mitigation Steps
Add a check for != 0 to avoid reverting when calling `xERC4626` constructor 

## Order of operations can affect time
### Impact
Solidity integer division might truncate. As a result, performing multiplication before division can sometimes avoid loss of precision.
### Details
In general, this is a problem due to precision.

Precision of time can be affected
`end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength`
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L78-L97


Precision of time can be affected
`rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength`

https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L40


### Tools
Slither + manual analysis

### Proof of Concept
Lack of precision due to the order of operations


### Recommended Mitigation Steps
Reorder the operations. For more info: https://github.com/crytic/slither/wiki/Detector-Documentation#divide-before-multiply


## Event emmited by ERC20 Tokens with fee on transfer won't display actual value
### Vulnerability details
There are ERC20 tokens that charge fee for every `transfer()`.

`frxETHMinter.sol#recoverERC20()` assumes that the received amount is the same as the transfer amount, then emit that value. However, the actual transferred amount can be lower for those tokens.

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L200

```
    function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyByOwnGov {
        require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");

        emit EmergencyERC20Recovered(tokenAddress, tokenAmount);
    }
```

### Recommendation
Consider comparing before and after balance to get the actual transferred amount.

## Prevent div by 0
### Impact
On several locations in the code precautions are not being taken to not divide by 0, this would revert the code if happens.

### Proof of Concept
Navigate to the following contracts,

If 0 this would revert creating an instance of the contract as this happens on the constructor.
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L40
        rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;

Safety checks that rewardsCycleEnd != lastSync 
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L60
        uint256 unlockedRewards = (lastRewardAmount_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);

INFORMATIONAL: Not checked but can't reach this state as it would revert on constructor
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L89
        uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;

### Recommended Mitigation Steps
Recommend making sure division by 0 won’t occur by checking the variables beforehand and handling this edge case.

## Variable shadows another variable
###  Summary 
Name shadowing where two or more variables/functions share the same name could be confusing to developers and/or reviewers

###  Github Permalinks 
- `ERC20PermitPermissionedMint._name` shadows `ERC20._name`
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L27

- `ERC20PermitPermissionedMint._symbol` shadows `ERC20._symbol`
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L28

###  Mitigation
Replace for example `_name` variable to `_permissionedMint_name` or a similar substitution


## Missing checks for address(0x0) when assigning values to `address` state or `immutable` variables 
### Summary
Zero address should be checked for state variables, immutable variables. A zero address can lead into problems.
### Github Permalinks
- state
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L41

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L34

- immutable address not being checked
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L53-L57
### Mitigation
Check zero address before assigning or using it


## block.timestamp used as time proxy 
### Summary
Risk of using block.timestamp for time should be considered. 
### Details
block.timestamp is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

### References
SWC ID: 116

### Github Permalinks 

https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L78-L97

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L48-L51

https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L45-L62


### Mitigation
- Consider the risk of using block.timestamp as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 
- Consider using an oracle

## Return value not being checked 
### Details
Return values not being checked may lead into unexpected behaviors with functions. Not events/Error are being emitted if that fails, so functions would be called even of not being working as expect as for example `submitAndDeposit` doesn't check for `frxETHToken.approve()` value.

### Github permalinks

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L75

### Mitigation
Check values and revert/emit events if needed








# Informational
## Comparison with a a boolean
### Summary
There are a number of instances where a boolean variable/function is checked.
### Details
- This check can be further simplified from `variable == false` to `!variable`.
- This check can be further simplified from `variable == true` to `variable`.

### Github Permalink
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L46
       require(minters[msg.sender] == true, "Only minters");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L78
        require(minters[minter_address] == true, "Address nonexistant");

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L68
        require(minters[minter_address] == false, "Address already exists");



### Mitigation
Simplify boolean comparisons in order to improve readability and save gas


##  Use of magic numbers is confusing and risky
### Summary
Magic numbers are hardcoded numbers used in the code which are ambiguous to their intended purpose. These should be replaced with constants to make code more readable and maintainable.
### Details
Values are hardcoded and would be more readable and maintainable if declared as a constant

### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L55
        return convertToAssets(1e18);

### Mitigation
Replace magic hardcoded numbers with declared constants.

## Missing indexed event parameters
### Summary
Events without indexed event parameters make it harder and
inefficient for off-chain tools to analyze them.
### Details
Indexed parameters (“topics”) are searchable event parameters.
They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
 
### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L104
    event MinterAdded(address minter_address);

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L105
    event MinterRemoved(address minter_address);

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L106
    event TimelockChanged(address timelock_address);

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L205
    event EmergencyEtherRecovered(uint256 amount);

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L206
    event EmergencyERC20Recovered(address tokenAddress, uint256 tokenAmount);

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L208
    event DepositEtherPaused(bool new_status);

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L210
    event SubmitPaused(bool new_status);

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L212
    event WithholdRatioSet(uint256 newRatio);

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L208
    event TimelockChanged(address timelock_address);

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L209
    event WithdrawalCredentialSet(bytes _withdrawalCredential);

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L210
    event ValidatorAdded(bytes pubKey, bytes withdrawalCredential);

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L212
    event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L213
    event ValidatorsPopped(uint256 times);

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L214
    event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);

- Suggested to add some parameter as a good practice
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L215
    event KeysCleared();

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L211
    event ValidatorArrayCleared();
### Mitigation
Consider which event parameters could be particularly useful to off-chain tools and should be indexed.

## Different versions of pragma
### Summary
Some of the contracts include an unlocked pragma, e.g., pragma solidity >=0.8.0. 

Locking the pragma helps ensure that contracts are not accidentally deployed using an old compiler version with unfixed bugs.

### Github Permalinks
All 6 files

https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L4
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETH.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L2
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L2
### Mitigation
Lock pragmas to a specific Solidity version. 
Consider converting ^ 0.8.0 into 0.8.10]


## Bad order of code
### Summary
Clearness of the code is important for the readability and maintainability.
As Solidity guidelines says about declaration order:
1.Type declarations
2.State variables
3.Events
4.Modifiers
5.Functions

### github permalink
- events
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L100-L106
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L205-L212

- modifiers expected before constructor
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L24-L48

- modifiers expected before constructor
- events
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L40-L215

- receive expected at the very end
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L114-L116

### Mitigation
Follow solidity style guidelines https://docs.soliditylang.org/en/v0.8.15/style-guide.html

## Missing Natspec 
### Summary 
Missing Natspec and regular comments affect readability and maintainability of a codebase. 
### Details 
Contracts has partial or full lack of comments
### Github Permalinks 
#### Natspec @param
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETH.sol#L35-L41
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L42-L51
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L58-L87
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L24-L35
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L53-L98
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L37-L41
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L65-L74
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L190-L203
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L166-L174
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L108-L111
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L52-L101
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L202-L206
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L150-L186
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L53-L122
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L40-L43

#### Natspec @return value
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L54
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L67
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L83
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L70
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L128
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L154
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L175
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L197
#### More documentation needed
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L38-L48 
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L45-L48
 
 ### mitigation
 - Add @param descriptors
 - Add @return descriptors
 - Add Natspec comments. 
 - Add inline comments. 
 - Add comments for what the contract does

## Unused code
### Summary
Code that is not used should be removed
### Details
- import of IERC20 not used
### Github Permalinks
https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L5
### Mitigation
Remove the code that is not used.


## Maximum line length exceeded
### Summary
Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details 
Lines that exceed the 79 (or 99) character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
### Github Permalinks 

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETH.sol#L29

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L31

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L32

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L33

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L34

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/sfrxETH.sol#L35

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/ERC20/ERC20PermitPermissionedMint.sol#L86

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L33

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L35

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L36

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L43

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L138

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L200

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/frxETHMinter.sol#L207

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L36

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L37

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L40

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L81

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L93

https://github.com/code-423n4/2022-09-frax/blob/dc6684f77b4e9bd965e8862be7f5fb71473a4c4c/src/OperatorRegistry.sol#L170
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L13
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L14
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L15
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L16
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L18
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L29
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L40
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L44
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L60
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L77
https://github.com/corddry/ERC4626/blob/2b2baba0fc480326a89251716f52d2cfa8b09230/src/xERC4626.sol#L85


### Mitigation
Reduce line length to less than 99 at least to improve maintainability and readability of the code 
