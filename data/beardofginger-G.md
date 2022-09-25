
# i++ is less efficient than ++i
The gas cost can be reduced by using ++i or i += 1 instead of i++ in the following locations:

DepositContract.sol, Line 76:        

		for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH - 1; height++)


DepositContract.sol, Line 83:        

		for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {


DepositContract.sol, Line 148:        

		for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {


ERC20/ERC20PermitPermissionedMint.sol, Line 84:        

		for (uint i = 0; i < minters_array.length; i++){ 


# > 0 is less efficient than != 0 for unsigned integers
The gas cost can be reduced by using != 0 instead of > 0 in the following locations:

frxETHMinter.sol, Line 79:        

		require(sfrxeth_recieved > 0, 'No sfrxETH was returned');


frxETHMinter.sol, Line 126:        

		require(numDeposits > 0, "Not enough ETH in contract");


# Initialising variables to 0 uses unnecessary gas
Uninitialsed variables default to 0x0. Hence, assigning them 0 uses gas unnecessarily.

DepositContract.sol, Line 76:        

		for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH - 1; height++)


DepositContract.sol, Line 83:        

		for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {


DepositContract.sol, Line 148:        

		for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {


ERC20/ERC20PermitPermissionedMint.sol, Line 84:        

		for (uint i = 0; i < minters_array.length; i++){ 


OperatorRegistry.sol, Line 63:        

		for (uint256 i = 0; i < arrayLength; ++i) {


OperatorRegistry.sol, Line 84:        

		for (uint256 i = 0; i < times; ++i) {


OperatorRegistry.sol, Line 114:            

		for (uint256 i = 0; i < original_validators.length; ++i) {


frxETHMinter.sol, Line 94:        

		uint256 withheld_amt = 0;


frxETHMinter.sol, Line 129:        
	
		for (uint256 i = 0; i < numDeposits; ++i) {


# Unnecessary checks use gas
Solidity 0.8+ implements over- and underflow checks on arithmetic operations by default. In DepositContract.sol there are three for-loops where the uint height is incremented. We can be confident that height will not overflow because DEPOSIT_CONTRACT_TREE_DEPTH is a constant. The default check can be removed by modifying the code as such:

Gassy:

		for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH - 1; height++) {

		}

Optimised:

		for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH - 1; ) {
			unchecked { ++height; }
		}


Occurrences:

DepositContract.sol, Line 76:        

		for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH - 1; height++)


DepositContract.sol, Line 83:        

		for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {


DepositContract.sol, Line 148:        
		
		for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++) {