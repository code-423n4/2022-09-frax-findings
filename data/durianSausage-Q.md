## none-critical issue foundings
### N01: EVENT IS MISSING INDEXED FIELDS
#### prof
src/ERC20/ERC20PermitPermissionedMint.sol, 104, b'    event MinterAdded(address minter_address);'
src/ERC20/ERC20PermitPermissionedMint.sol, 105, b'    event MinterRemoved(address minter_address);'
src/ERC20/ERC20PermitPermissionedMint.sol, 106, b'    event TimelockChanged(address timelock_address);'
src/frxETHMinter.sol, 205, b'    event EmergencyEtherRecovered(uint256 amount);'
src/frxETHMinter.sol, 206, b'    event EmergencyERC20Recovered(address tokenAddress, uint256 tokenAmount);'
src/frxETHMinter.sol, 208, b'    event DepositEtherPaused(bool new_status);'
src/frxETHMinter.sol, 210, b'    event SubmitPaused(bool new_status);'
src/frxETHMinter.sol, 212, b'    event WithholdRatioSet(uint256 newRatio);'
src/OperatorRegistry.sol, 208, b'    event TimelockChanged(address timelock_address);'
src/OperatorRegistry.sol, 209, b'    event WithdrawalCredentialSet(bytes _withdrawalCredential);'
src/OperatorRegistry.sol, 210, b'    event ValidatorAdded(bytes pubKey, bytes withdrawalCredential);'
src/OperatorRegistry.sol, 211, b'    event ValidatorArrayCleared();'
src/OperatorRegistry.sol, 212, b'    event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);'
src/OperatorRegistry.sol, 213, b'    event ValidatorsPopped(uint256 times);'
src/OperatorRegistry.sol, 214, b'    event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);'
src/OperatorRegistry.sol, 215, b'    event KeysCleared();'

### N02: NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES
src/sfrxETH.sol, 2, b'pragma solidity ^0.8.0;'
src/ERC20/ERC20PermitPermissionedMint.sol, 2, b'pragma solidity ^0.8.0;'
src/frxETHMinter.sol, 2, b'pragma solidity ^0.8.0;'
src/OperatorRegistry.sol, 2, b'pragma solidity ^0.8.0;'
src/frxETH.sol, 2, b'pragma solidity ^0.8.0;'
lib/ERC4626/src/xERC4626.sol, 4, b'pragma solidity ^0.8.0;'



### L01: RETURN VALUES OF approve() NOT CHECKED
#### prof
approve() NOT CHECKED
Not all IERC20 implementations revert() when there’s a failure in approve(). The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment
#### problem
src/frxETHMinter.sol, 75, b'        frxETHToken.approve(address(sfrxETHToken), msg.value);'

### L02: address variable should check if it is zero MISSING CHECKS FOR ADDRESS(0X0) WHEN ASSIGNING VALUES TO ADDRESS STATE VARIABLES
#### prof
src/ERC20/ERC20PermitPermissionedMint.sol, 34, b'      timelock_address = _timelock_address;'
src/ERC20/ERC20PermitPermissionedMint.sol, 96, b'        timelock_address = _timelock_address;'
src/frxETHMinter.sol, 60, b'        depositContract = IDepositContract(depositContractAddress);'
src/frxETHMinter.sol, 61, b'        frxETHToken = frxETH(frxETHAddress);'
src/frxETHMinter.sol, 62, b'        sfrxETHToken = IsfrxETH(sfrxETHAddress);'
src/OperatorRegistry.sol, 41, b'        timelock_address = _timelock_address;'
src/OperatorRegistry.sol, 204, b'        timelock_address = _timelock_address;'