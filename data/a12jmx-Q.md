1.

Missing space in the comment in:

Contract: ERC20PermitPermissionedMint.sol

	line 01
	
Recommendation:
	
	// SPDX-License-Identifier: Unlicense
	
2.

It is best practice and also unnecessary to initialize variables in for loops as they get set to 0 by default in:

Contract: ERC20PermitPermissionedMint.sol

	line 84
	
Recommendation:

	for (uint i; i < minters_array.length; i++){
	
Contract: frxETHMinter.sol

	line 129
	
Recommendation:

	for (uint256 i; i < numDeposits; ++i) {
	
Contract: OperatorRegistry.sol

	line 63
	line 84
	line 114
	
Recommendation:

	for (uint256 i; i < arrayLength; ++i) {
	for (uint256 i; i < times; ++i) {
	for (uint256 i; i < original_validators.length; ++i) {
	
3.

It is best practice to always start a new statement/sentence in capital letters, in comments. 
This will also bring more consistency throughout all code under review.

Contract: xERC4626.sol

	line 23
	line 26
	line 29
	line 32
	line 39
	line 46
	line 53
	line 54
	line 58
	line 59
	
Recommendation:

	/// @notice The maximum length of a rewards cycle
	/// @notice The effective start of the current cycle
	/// @notice The end of the current cycle. Will always be evenly divisible by `rewardsCycleLength`.
	/// @notice The amount of rewards distributed in a the most recent cycle.
	// Seed initial rewardsCycleEnd
	// Cache global vars
	// No rewards or rewards fully unlocked
	// Entire reward amount is available
	// Rewards not fully unlocked
	// Add unlocked rewards to stored total

4.

It is best practice and also unnecessary to initialize variables as they get set to 0 by default in:
	
Contract: frxETHMinter.sol

	line 94
	
Recommendation:

	uint256 withheld_amt;	
	
5.

It is best practice to use the safe library from OpenZeppelin to, in this case, make external approvals and transactions in: 

Contract: frxETHMinter.sol

	line 75
	line 200
	
Recommendation:

	frxETHToken.safeApprove(address(sfrxETHToken), msg.value);
	require(IERC20(tokenAddress).safeTransfer(owner, tokenAmount), "recoverERC20: Transfer failed");	
	
6.

Grammer issues in comments in:

Contract: sfrxETH.sol

	line 32
	
	"drops" should be "drop"

	line 33
	
	"is" should be "are"
	
	line 47
	
	"Noop" should be "No"
	
	line 53
	
	the "." after worth should be "?"

Contract: frxETHMinter.sol

	line 35

	"adds" should be "add"

	line 84

	and "sender's" should ultimately be changed to "senders"

	line 108

	and "sender's" should ultimately be changed to "senders"

	line 138

	the "into" should be removed completely

	line 176

	"submites" should be "submits"

Contract: xERC4626.sol

	line 13

	"autocompound" should be two words "auto compound"

	line 16

	there is no need for the "," after "creation"

	line 18

	"which" should be "that"

	line 32

	"amount" should be "number

	there is no need for the "a" in front of the "the"

	line 43

	"amount" should be "number"

	"share holders" should be one-word "shareholders"