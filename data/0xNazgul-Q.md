## [NAZ-L1] Missing Zero-address Validation
**Severity**: Low
**Context**: [`ERC20PermitPermissionedMint.sol#L26`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L26), [`frxETHMinter.sol#L53-L55`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L53-L55),  [`OperatorRegistry.sol#L40`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L40)

**Description**:
Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

**Recommendation**:
Consider adding explicit zero-address validation on input parameters of address type.

## [NAZ-L2] Missing Equivalence Checks in Setters
**Severity**: Low
**Context**: [`ERC20PermitPermissionedMint.sol#L94`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L94)

**Description**:
Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.

**Recommendation**:
This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.


## [NAZ-L3] Missing Time locks
**Severity**: Low 
**Context**: [`ERC20PermitPermissionedMint.sol#L65-L98`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L65-L98)

**Description**:
When critical parameters of systems need to be changed, it is required to broadcast the change via event emission and recommended to enforce the changes after a time-delay. This is to allow system users to be aware of such critical changes and give them an opportunity to exit or adjust their engagement with the system accordingly. None of the onlyOwner functions that change critical protocol addresses/parameters have a timelock for a time-delayed change to alert: (1) users and give them a chance to engage/exit protocol if they are not agreeable to the changes (2) team in case of compromised owner(s) and give them a chance to perform incident response.

**Recommendation**:
Users may be surprised when critical parameters are changed. Furthermore, it can erode users' trust since they can’t be sure the protocol rules won’t be changed later on. Compromised owner keys may be used to change protocol addresses/parameters to benefit attackers. Without a time-delay, authorized owners have no time for any planned incident response.


## [NAZ-L4] `receive()` Function Should Emit An Event
**Severity**: Low
**Context**: [`frxETHMinter.sol#L114`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L114)

**Description**:
Consider emitting an event inside this function with `msg.sender` and `msg.value` as the parameters. This would make it easier to track incoming ether transfers.

**Recommendation**:
Consider adding events to the `receive()` functions. 


## [NAZ-N1] Function States Named Return But Doesn't Use It
**Severity** Informational
**Context**: [`sfrxETH.sol#L67`](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L67), [`sfrxETH.sol#L83`](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L83), [`frxETHMinter.sol#L70`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L70)

**Description**:
The linked function(s) have a named return that is not used. Using both named returns and a return statement isn't necessary. 

**Recommendation**:
Removing one of those can improve code clarity.


## [NAZ-N2] Visibility Should Be Before Modifiers
**Severity**: Informational
**Context**: [`OperatorRegistry.sol#L153`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L153)

**Description**:
Visibility keyword should be before the list of modifiers as per best practice.

**Recommendation**:
Consider moving the visibility keyword before the list of modifiers.


## [NAZ-N3] Line Length
**Severity**: Informational
**Context**: [`sfrxETH.sol#L32-L34`](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L32-L34), [`frxETHMinter.sol#L33`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L33), [`frxETHMinter.sol#L36`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L36), [`frxETHMinter.sol#L43`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L43), [`OperatorRegistry.sol#L37`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L37), [`xERC4626.sol#L16-L18`](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L16-L18)

**Description**:
Max line length must be no more than 120 but many lines are extended past this length.

**Recommendation**:
Consider cutting down the line length below 120.


## [NAZ-N4] Code Contains Empty Blocks
**Severity**: Informational
**Context**: [`frxETH.sol#L41`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#L41), [`sfrxETH.sol#L45`](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L45)

**Description**:
It's best practice that when there is an empty block, to add a comment in the block explaining why it's empty.

**Recommendation**:
Consider adding `/* Comment on why */` to the empty blocks.

## [NAZ-N5] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`frxETH.sol#L33`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#L33), [`frxETH.sol#L37-L38`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#L37-L38), [`sfrxETH.sol#L40`](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L40), [`sfrxETH.sol#L48`](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L48), [`ERC20PermitPermissionedMint.sol#L16`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L16), [`ERC20PermitPermissionedMint.sol#L19`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L19), [`ERC20PermitPermissionedMint.sol#L25-L26`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L25-L26), [`ERC20PermitPermissionedMint.sol#L53`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L53), [`ERC20PermitPermissionedMint.sol#L59`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L59), [`ERC20PermitPermissionedMint.sol#L65`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L65), [`ERC20PermitPermissionedMint.sol#L76`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L76), [`ERC20PermitPermissionedMint.sol#L94`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L94), [`ERC20PermitPermissionedMint.sol#L104-L106`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L104-L106), [`frxETHMinter.sol#L37`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L37), [`frxETHMinter.sol#L57`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L57), [`frxETHMinter.sol#L78`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L78), [`frxETHMinter.sol#L94`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94), [`frxETHMinter.sol#L207-L208`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L207-L208), [`frxETHMinter.sol#L210`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L210), [`OperatorRegistry.sol#L38-L40`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L38-L40), [`OperatorRegistry.sol#L69`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L69), [`OperatorRegistry.sol#L93`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L93), [`OperatorRegistry.sol#L95`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L95), [`OperatorRegistry.sol#L108`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L108), [`OperatorRegistry.sol#L126`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L126), [`OperatorRegistry.sol#L181`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L181), [`OperatorRegistry.sol#L208`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#l208), [`OperatorRegistry.sol#L212`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L212), [`OperatorRegistry.sol#L214`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L214), [`xERC4626.sol#L20`](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L20), [`xERC4626.sol#L35`](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L35), [`xERC4626.sol#L65`](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L65), [`xERC4626.sol#L71`](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L71)

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the `mixedCase` format unless variables are declared as `constant` in which case they utilize the `UPPER_CASE_WITH_UNDERSCORES` format. Private variables and functions should lead with an `_underscore`.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the [Solidity style guide](https://docs.soliditylang.org/en/latest/style-guide.html). 


## [NAZ-N6] Code Structure Deviates From Best-Practice
**Severity**: Informational
**Context**: [`sfrxETH.sol#L54`](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L54), [`ERC20PermitPermissionedMint.sol#L40`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L40), [`ERC20PermitPermissionedMint.sol#L104-L106`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L104-L106), [`frxETHMinter.sol#L104`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L104), [`frxETHMinter.sol#L205-L212`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L205-L212), [`OperatorRegistry.sol#L45`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L45), [`OperatorRegistry.sol#L208-L215`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L208-L215), [`xERC4626.sol#L78`](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L78)

**Description**:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.  Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Functions should then further be ordered with view functions coming after the non-view labeled ones.

**Recommendation**:
Consider adopting recommended best-practice for code structure and layout.


## [NAZ-N7] Unindexed Event Parameters
**Severity** Informational
**Context**: [`frxETHMinter.sol#L206`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L206), [`OperatorRegistry.sol#L212`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L212), [`OperatorRegistry.sol#L214`](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol)

**Description**:
Parameters of certain events are expected to be indexed so that they’re included in the block’s bloom filter for faster access. Failure to do so might confuse off-chain tooling looking for such indexed events.

**Recommendation**:
Consider adding the indexed keyword to event parameters that should include it.


## [NAZ-N8] Spelling Errors
**Severity**: Informational
**Context**: [`ERC20PermitPermissionedMint.sol#L78 (nonexistant => nonexistent)`](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78), [`frxETHMinter.sol#L176 (submites => submits)`](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L176)

**Description**:
Spelling errors in comments can cause confusion to both users and developers.

**Recommendation**:
Consider checking all misspellings to ensure they are corrected.

## [NAZ-N9] Missing or Incomplete NatSpec
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-frax/tree/main/src)

**Description**:
Some functions are missing @notice/@dev NatSpec comments for the function, @param for all/some of their parameters and @return for return values. Given that NatSpec is an important part of code documentation, this affects code comprehension, auditability and usability.

**Recommendation**:
Consider adding in full NatSpec comments for all functions to have complete code documentation for future use.


## [NAZ-N10] Floating Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-frax/tree/main/src)

**Description**:
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

**Recommendation**: 
Consider locking the pragma version.


## [NAZ-N11] Older Version Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-09-frax/tree/main/src)

**Description**:
Using very old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs. 

**Recommendation**:
Consider using the most recent version.