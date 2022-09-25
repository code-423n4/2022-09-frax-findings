Contract: ERC20PermitPermissionedMint.sol

Changing i++ to ++i in the for loop of:

	line 84

	will save around 5 gas per iteration.
	
Recommendation:

	for (uint i = 0; i < minters_array.length; ++i){
	
This will also bring it in line with other for loops in contracts frxETHMinter.sol in line 129
and OperatorRegistry.sol in lines 63, 84, and 114.