# Gas Optimization

## Declare errors for revert, instead of using string, to reduce gas.

        File: src/ERC20/ERC20PermitPermissionedMint.sol

       require(minters[msg.sender] == true, "Only minters");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L46

        File: src/ERC20/ERC20PermitPermissionedMint.sol

        require(minter_address != address(0), "Zero address detected");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L66

        File: src/ERC20/ERC20PermitPermissionedMint.sol

        require(minter_address != address(0), "Zero address detected");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L77

        File: src/ERC20/ERC20PermitPermissionedMint.sol

        require(minters[minter_address] == true, "Address nonexistant");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L78

        File: src/ERC20/ERC20PermitPermissionedMint.sol

        require(_timelock_address != address(0), "Zero address detected"); 

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L95

        File: src/frxETHMinter.sol

        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79

        File: src/frxETHMinter.sol

        require(!submitPaused, "Submit is paused");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L87

        File: src/frxETHMinter.sol

        require(msg.value != 0, "Cannot submit 0");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L88

        File: src/frxETHMinter.sol

        require(!depositEtherPaused, "Depositing ETH is paused");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L122

        File: src/frxETHMinter.sol

        require(numDeposits > 0, "Not enough ETH in contract");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L126

        File: src/frxETHMinter.sol

            require(!activeValidators[pubKey], "Validator already has 32 ETH");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L140

        File: src/frxETHMinter.sol

        require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L160

        File: src/frxETHMinter.sol

        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L167

        File: src/frxETHMinter.sol

        require(success, "Invalid transfer");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L171

        File: src/frxETHMinter.sol

        require(success, "Invalid transfer");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L193

        File: src/frxETHMinter.sol

        require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L200

        File: src/OperatorRegistry.sol

        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L46

        File: src/OperatorRegistry.sol

        require(numVals != 0, "Validator stack is empty");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L137

        File: src/OperatorRegistry.sol

        require(numValidators() == 0, "Clear validator array first");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L182

        File: src/OperatorRegistry.sol

        require(_timelock_address != address(0), "Zero address detected");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L203


## ++i costs less gas than i++ and i+=1 (--i/i--/i-=1 too)

        File: src/ERC20/ERC20PermitPermissionedMint.sol

        for (uint i = 0; i < minters_array.length; i++){ 

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84


## Initializing a variable to its default value costs unnecessary gas.

        File: src/ERC20/ERC20PermitPermissionedMint.sol

        for (uint i = 0; i < minters_array.length; i++){ 

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84

        File: src/OperatorRegistry.sol

        for (uint256 i = 0; i < arrayLength; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63

        File: src/OperatorRegistry.sol

        for (uint256 i = 0; i < times; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84

        File: src/OperatorRegistry.sol

        for (uint256 i = 0; i < arrayLength; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63

        File: src/OperatorRegistry.sol

        for (uint256 i = 0; i < times; ++i) {
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84

        File: src/OperatorRegistry.sol

            for (uint256 i = 0; i < original_validators.length; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114


## Variable increment(e.g.++i/i++) for looping should be unchecked{++i} when they are not possible to overflow, to remove overflow checking to save gas.

        File: src/ERC20/ERC20PermitPermissionedMint.sol

        for (uint i = 0; i < minters_array.length; i++){ 

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84

        File: src/OperatorRegistry.sol

        for (uint256 i = 0; i < arrayLength; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63

        File: src/OperatorRegistry.sol

        for (uint256 i = 0; i < times; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84

        File: src/OperatorRegistry.sol

        for (uint256 i = 0; i < arrayLength; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63

        File: src/OperatorRegistry.sol

        for (uint256 i = 0; i < times; ++i) {
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84

        File: src/OperatorRegistry.sol

            for (uint256 i = 0; i < original_validators.length; ++i) {

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114


## use access state variable directly instead of calling accessor function

        File: src/OperatorRegistry.sol

        uint numVals = numValidators();

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L136

        File: src/OperatorRegistry.sol

        require(numValidators() == 0, "Clear validator array first");

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L182

