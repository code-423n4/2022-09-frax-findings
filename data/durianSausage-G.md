### G01: custom error save more gas
#### problem
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information, as explained https://blog.soliditylang.org/2021/04/21/custom-errors/. 
Custom errors are defined using the error statement.
#### prof
src/ERC20/ERC20PermitPermissionedMint.sol, 66, b'        require(minter_address != address(0), "Zero address detected");'
src/ERC20/ERC20PermitPermissionedMint.sol, 68, b'        require(minters[minter_address] == false, "Address already exists");'
src/ERC20/ERC20PermitPermissionedMint.sol, 77, b'        require(minter_address != address(0), "Zero address detected");'
src/ERC20/ERC20PermitPermissionedMint.sol, 78, b'        require(minters[minter_address] == true, "Address nonexistant");'
src/ERC20/ERC20PermitPermissionedMint.sol, 95, b'        require(_timelock_address != address(0), "Zero address detected"); '
src/frxETHMinter.sol, 79, b"        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');"
src/frxETHMinter.sol, 87, b'        require(!submitPaused, "Submit is paused");'
src/frxETHMinter.sol, 88, b'        require(msg.value != 0, "Cannot submit 0");'
src/frxETHMinter.sol, 122, b'        require(!depositEtherPaused, "Depositing ETH is paused");'
src/frxETHMinter.sol, 126, b'        require(numDeposits > 0, "Not enough ETH in contract");'
src/frxETHMinter.sol, 140, b'            require(!activeValidators[pubKey], "Validator already has 32 ETH");'
src/frxETHMinter.sol, 160, b'        require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");'
src/frxETHMinter.sol, 167, b'        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");'
src/frxETHMinter.sol, 171, b'        require(success, "Invalid transfer");'
src/frxETHMinter.sol, 193, b'        require(success, "Invalid transfer");'
src/frxETHMinter.sol, 200, b'        require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");'
src/OperatorRegistry.sol, 137, b'        require(numVals != 0, "Validator stack is empty");'
src/OperatorRegistry.sol, 182, b'        require(numValidators() == 0, "Clear validator array first");'
src/OperatorRegistry.sol, 203, b'        require(_timelock_address != address(0), "Zero address detected");'


### G02: COMPARISONS WITH ZERO FOR UNSIGNED INTEGERS
#### problem
0 is less gas efficient than !0 if you enable the optimizer at 10k AND you’re in a require statement. Detailed explanation with the opcodes https://twitter.com/gzeon/status/1485428085885640706
#### prof
src/frxETHMinter.sol, 79, b"        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');"
src/frxETHMinter.sol, 126, b'        require(numDeposits > 0, "Not enough ETH in contract");'


### G03: PREFIX INCREMENT SAVE MORE GAS
#### problem
prefix increment ++i is more cheaper than postfix i++
#### prof
src/ERC20/ERC20PermitPermissionedMint.sol, 84, b'        for (uint i = 0; i < minters_array.length; i++){ '

### G04: X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES
#### prof
src/frxETHMinter.sol, 97, b'            currentWithheldETH += withheld_amt;'
src/frxETHMinter.sol, 168, b'        currentWithheldETH -= amount;'
lib/ERC4626/src/xERC4626.sol, 67, b'        storedTotalAssets -= amount;'
lib/ERC4626/src/xERC4626.sol, 72, b'        storedTotalAssets += amount;'

### G05: ARRAY LENTH SHOULD USE MEMoRY VARIABLE LOAD IT
#### problem
The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP (3 gas), and gets rid of the extra DUP needed to store the stack offset
#### prof
src/ERC20/ERC20PermitPermissionedMint.sol, 84, b'        for (uint i = 0; i < minters_array.length; i++){ '

### G06: USING BOOLS FOR STORAGE INCURS OVERHEAD
#### problem
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
#### prof
src/ERC20/ERC20PermitPermissionedMint.sol, 20, b'    mapping(address => bool) public minters; // Mapping is also used for faster verification'
src/frxETHMinter.sol, 43, b'    mapping(bytes => bool) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them'

### G07: resign the default value to the variables.
#### problem
 resign the default value to the variables will cost more gas.
#### prof
src/ERC20/ERC20PermitPermissionedMint.sol, 84, b'        for (uint i = 0; i < minters_array.length; i++){ '
src/frxETHMinter.sol, 94, b'        uint256 withheld_amt = 0;'
src/frxETHMinter.sol, 129, b'        for (uint256 i = 0; i < numDeposits; ++i) {'
src/OperatorRegistry.sol, 63, b'        for (uint256 i = 0; i < arrayLength; ++i) {'
src/OperatorRegistry.sol, 84, b'        for (uint256 i = 0; i < times; ++i) {'
src/OperatorRegistry.sol, 114, b'            for (uint256 i = 0; i < original_validators.length; ++i) {'

### G08: ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
#### problem
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
#### prof
src/ERC20/ERC20PermitPermissionedMint.sol, 84, b'        for (uint i = 0; i < minters_array.length; i++){ '
src/frxETHMinter.sol, 129, b'        for (uint256 i = 0; i < numDeposits; ++i) {'
src/OperatorRegistry.sol, 63, b'        for (uint256 i = 0; i < arrayLength; ++i) {'
src/OperatorRegistry.sol, 84, b'        for (uint256 i = 0; i < times; ++i) {'
src/OperatorRegistry.sol, 114, b'            for (uint256 i = 0; i < original_validators.length; ++i) {'

### G09: FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
#### problem
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
#### prof
src/frxETHMinter.sol, 196, b'    function recoverEther(uint256 amount) external onlyByOwnGov '
src/frxETHMinter.sol, 203, b'    function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyByOwnGov '

### G10: USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS
#### problem:
We can save getter function of public constants.
#### prof:
src/frxETHMinter.sol, 38, b'    uint256 public constant DEPOSIT_SIZE = 32 ether; // ETH 2.0 minimum deposit size'
src/frxETHMinter.sol, 39, b'    uint256 public constant RATIO_PRECISION = 1e6; // 1,000,000 '
src/frxETHMinter.sol, 45, b'    IDepositContract public immutable depositContract; // ETH 2.0 deposit contract'
src/frxETHMinter.sol, 46, b'    frxETH public immutable frxETHToken;'
src/frxETHMinter.sol, 47, b'    IsfrxETH public immutable sfrxETHToken;'
lib/ERC4626/src/xERC4626.sol, 24, b'    uint32 public immutable rewardsCycleLength;'

### G11: USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
#### problem
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
#### prof
src/sfrxETH.sol, 50, b'        if (block.timestamp >= rewardsCycleEnd) { syncRewards(); } '
src/sfrxETH.sol, 55, b'        return convertToAssets(1e18);'
src/sfrxETH.sol, 69, b'        asset.permit(msg.sender, address(this), amount, deadline, v, r, s);'
src/sfrxETH.sol, 85, b'        asset.permit(msg.sender, address(this), amount, deadline, v, r, s);'
src/ERC20/ERC20PermitPermissionedMint.sol, 54, b'        super.burnFrom(b_address, b_amount);'
src/ERC20/ERC20PermitPermissionedMint.sol, 60, b'        super._mint(m_address, m_amount);'
src/ERC20/ERC20PermitPermissionedMint.sol, 66, b'        require(minter_address != address(0), "Zero address detected");'
src/ERC20/ERC20PermitPermissionedMint.sol, 70, b'        minters_array.push(minter_address);'
src/ERC20/ERC20PermitPermissionedMint.sol, 77, b'        require(minter_address != address(0), "Zero address detected");'
src/ERC20/ERC20PermitPermissionedMint.sol, 84, b'        for (uint i = 0; i < minters_array.length; i++){ '
src/ERC20/ERC20PermitPermissionedMint.sol, 95, b'        require(_timelock_address != address(0), "Zero address detected"); '
src/frxETHMinter.sol, 72, b'        _submit(address(this));'
src/frxETHMinter.sol, 79, b"        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');"
src/frxETHMinter.sol, 88, b'        require(msg.value != 0, "Cannot submit 0");'
src/frxETHMinter.sol, 94, b'        uint256 withheld_amt = 0;'
src/frxETHMinter.sol, 95, b'        if (withholdRatio != 0) {'
src/frxETHMinter.sol, 126, b'        require(numDeposits > 0, "Not enough ETH in contract");'
src/frxETHMinter.sol, 129, b'        for (uint256 i = 0; i < numDeposits; ++i) {'
src/OperatorRegistry.sol, 54, b'        validators.push(validator);'
src/OperatorRegistry.sol, 63, b'        for (uint256 i = 0; i < arrayLength; ++i) {'
src/OperatorRegistry.sol, 85, b'            validators.pop();'
src/OperatorRegistry.sol, 84, b'        for (uint256 i = 0; i < times; ++i) {'
src/OperatorRegistry.sol, 116, b'                    validators.push(original_validators[i]);'
src/OperatorRegistry.sol, 114, b'            for (uint256 i = 0; i < original_validators.length; ++i) {'
src/OperatorRegistry.sol, 100, b'            swapValidator(remove_idx, validators.length - 1);'
src/OperatorRegistry.sol, 103, b'            validators.pop();'
src/OperatorRegistry.sol, 137, b'        require(numVals != 0, "Validator stack is empty");'
src/OperatorRegistry.sol, 141, b'        validators.pop();'
src/OperatorRegistry.sol, 176, b'        return Validator(pubKey, signature, depositDataRoot);'
src/OperatorRegistry.sol, 182, b'        require(numValidators() == 0, "Clear validator array first");'
src/OperatorRegistry.sol, 203, b'        require(_timelock_address != address(0), "Zero address detected");'
lib/ERC4626/src/xERC4626.sol, 24, b'    uint32 public immutable rewardsCycleLength;'
lib/ERC4626/src/xERC4626.sol, 27, b'    uint32 public lastSync;'
lib/ERC4626/src/xERC4626.sol, 30, b'    uint32 public rewardsCycleEnd;'
lib/ERC4626/src/xERC4626.sol, 33, b'    uint192 public lastRewardAmount;'
lib/ERC4626/src/xERC4626.sol, 38, b'        rewardsCycleLength = _rewardsCycleLength;'
lib/ERC4626/src/xERC4626.sol, 40, b'        rewardsCycleEnd = (block.timestamp.safeCastTo32() / rewardsCycleLength) * rewardsCycleLength;'
lib/ERC4626/src/xERC4626.sol, 48, b'        uint192 lastRewardAmount_ = lastRewardAmount;'
lib/ERC4626/src/xERC4626.sol, 48, b'        uint192 lastRewardAmount_ = lastRewardAmount;'
lib/ERC4626/src/xERC4626.sol, 49, b'        uint32 rewardsCycleEnd_ = rewardsCycleEnd;'
lib/ERC4626/src/xERC4626.sol, 49, b'        uint32 rewardsCycleEnd_ = rewardsCycleEnd;'
lib/ERC4626/src/xERC4626.sol, 50, b'        uint32 lastSync_ = lastSync;'
lib/ERC4626/src/xERC4626.sol, 50, b'        uint32 lastSync_ = lastSync;'
lib/ERC4626/src/xERC4626.sol, 52, b'        if (block.timestamp >= rewardsCycleEnd_) {'
lib/ERC4626/src/xERC4626.sol, 55, b'            return storedTotalAssets_ + lastRewardAmount_;'
lib/ERC4626/src/xERC4626.sol, 60, b'        uint256 unlockedRewards = (lastRewardAmount_ * (block.timestamp - lastSync_)) / (rewardsCycleEnd_ - lastSync_);'
lib/ERC4626/src/xERC4626.sol, 79, b'        uint192 lastRewardAmount_ = lastRewardAmount;'
lib/ERC4626/src/xERC4626.sol, 79, b'        uint192 lastRewardAmount_ = lastRewardAmount;'
lib/ERC4626/src/xERC4626.sol, 80, b'        uint32 timestamp = block.timestamp.safeCastTo32();'
lib/ERC4626/src/xERC4626.sol, 80, b'        uint32 timestamp = block.timestamp.safeCastTo32();'
lib/ERC4626/src/xERC4626.sol, 82, b'        if (timestamp < rewardsCycleEnd) revert SyncError();'
lib/ERC4626/src/xERC4626.sol, 82, b'        if (timestamp < rewardsCycleEnd) revert SyncError();'
lib/ERC4626/src/xERC4626.sol, 85, b'        uint256 nextRewards = asset.balanceOf(address(this)) - storedTotalAssets_ - lastRewardAmount_;'
lib/ERC4626/src/xERC4626.sol, 89, b'        uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;'
lib/ERC4626/src/xERC4626.sol, 89, b'        uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;'
lib/ERC4626/src/xERC4626.sol, 89, b'        uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;'
lib/ERC4626/src/xERC4626.sol, 89, b'        uint32 end = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;'
lib/ERC4626/src/xERC4626.sol, 92, b'        lastRewardAmount = nextRewards.safeCastTo192();'
lib/ERC4626/src/xERC4626.sol, 93, b'        lastSync = timestamp;'
lib/ERC4626/src/xERC4626.sol, 94, b'        rewardsCycleEnd = end;'