If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { 
should be replaced with 
for (uint256 i; i < numIterations; ++i) {

Affected code :
 # 1.  ( frxETHMinter.sol)

        // Give each deposit chunk to an empty validator
        for (uint256 i = 0; i < numDeposits; ++i) {
            // Get validator information
            (

# 2. OperatorRegistery.sol

// Fill the new validators array with all except the value to remove

            for (uint256 i = 0; i < original_validators.length; ++i) {
                if (i != remove_idx) {
                    validators.push(original_validators[i]);
                }
            

# 3. OperatorRegistery.sol

 /// @notice Remove validators from the end of the validators array, in case they were added in error

    function popValidators(uint256 times) public onlyByOwnGov {
        // Loop through and remove validator entries at the end
        for (uint256 i = 0; i < times; ++i) {
            validators.pop();
        }

#  4. OperatorRegistery.sol

/// @notice Add multiple new validators in one function call

    /** @dev You should verify offchain that the validators are indeed valid before adding them
        Reason we don't do that here is for gas */
    function addValidators(Validator[] calldata validatorArray) external onlyByOwnGov {
        uint arrayLength = validatorArray.length;
        for (uint256 i = 0; i < arrayLength; ++i) {
            addValidator(validatorArray[i]);
        }
    }    

# 5. ERC20PermitPermissionedMint.sol

 // 'Delete' from the array by setting the address to 0x0

        for (uint i = 0; i < minters_array.length; i++){ 
            if (minters_array[i] == minter_address) {
                minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
                break;
            }
        }

