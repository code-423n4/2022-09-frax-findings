## Table of Contents
### Low Risk Issues
- Missing Zero Address Check 

### Non-critical Issues
- Use fixed compiler versions instead of floating version
- Events Not Emitted for Important State Changes
- Event is Missing Indexed Fields

&ensp;
## Low Risk Issues
### Missing Zero Address Check 

#### Issue
I recommend adding check of 0-address for input validation of critical address parameters.
Not doing so might lead to non-functional contract and have to redeploy the contract, when it is updated to 0-address accidentally. 
For functions other than constructor(), lack of 0-address check might cause loss of funds for the user.

#### PoC
Total of 9 instances found.

1. ERC20PermitPermissionedMint.sol:constructor(): "timelock_address" address
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L34

2. OperatorRegistry.sol:constructor(): "timelock_address" address
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L41

3. frxETHMinter.sol:constructor(): "depositContract" address
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L60

4. frxETHMinter.sol:constructor(): "frxETHAddress" address
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L61

5. frxETHMinter.sol:constructor(): "sfrxETHToken" address
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L62

6. frxETHMinter.sol:submitAndDeposit(): "recipient" address
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L70

7. frxETHMinter.sol:moveWithheldETH(): "to" address
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L170

8. sfrxETH.sol:depositWithSignature(): "receiver" address
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L70

9. sfrxETH.sol:mintWithSignature(): "receiver" address
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L86

#### Mitigation
Add 0-address check for above addresses.

&ensp;
## Non-critical Issues
### Use fixed compiler versions instead of floating version

#### Issue
it is best practice to lock your pragma instead of using floating pragma.
the use of floating pragma has a risk of accidentally get deployed using latest complier
which may have higher risk of undiscovered bugs.
Reference: https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/

#### PoC
Total of 6 instances found.
```
./sfrxETH.sol:2:pragma solidity ^0.8.0;
./ERC20PermitPermissionedMint.sol:2:pragma solidity ^0.8.0;
./frxETHMinter.sol:2:pragma solidity ^0.8.0;
./OperatorRegistry.sol:2:pragma solidity ^0.8.0;
./frxETH.sol:2:pragma solidity ^0.8.0;
./xERC4626.sol:4:pragma solidity ^0.8.0;
```

#### Mitigation
I suggest to lock your pragma and aviod using floating pragma.
```
// bad
pragma solidity ^0.8.10;

// good
pragma solidity 0.8.10;
```

&ensp;
### Events Not Emitted for Important State Changes

#### Issue
It is best practice to emit an event when we there is important state changes like update a 
dynamic array or mapping because it allows monitoring activities with off-chain monitoring tools.

#### PoC
Total of 1 instance found.

1. OperatorRegistry.sol:getNextValidator(): No event is emitted even though "validators" array is modified.
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L141

#### Mitigation
Emit an event when there is important state changes.

&ensp;
### Event is Missing Indexed Fields

#### Issue
Each event should have 3 indexed fields if there are 3 or more fields.

#### PoC
Total of 19 instances found.
```
./ERC20PermitPermissionedMint.sol:102:    event TokenMinterBurned(address indexed from, address indexed to, uint256 amount);
./ERC20PermitPermissionedMint.sol:103:    event TokenMinterMinted(address indexed from, address indexed to, uint256 amount);
./ERC20PermitPermissionedMint.sol:104:    event MinterAdded(address minter_address);
./ERC20PermitPermissionedMint.sol:105:    event MinterRemoved(address minter_address);
./ERC20PermitPermissionedMint.sol:106:    event TimelockChanged(address timelock_address);
./frxETHMinter.sol:205:    event EmergencyEtherRecovered(uint256 amount);
./frxETHMinter.sol:206:    event EmergencyERC20Recovered(address tokenAddress, uint256 tokenAmount);
./frxETHMinter.sol:207:    event ETHSubmitted(address indexed sender, address indexed recipient, uint256 sent_amount, uint256 withheld_amt);
./frxETHMinter.sol:208:    event DepositEtherPaused(bool new_status);
./frxETHMinter.sol:209:    event DepositSent(bytes indexed pubKey, bytes withdrawalCredential);
./frxETHMinter.sol:210:    event SubmitPaused(bool new_status);
./frxETHMinter.sol:211:    event WithheldETHMoved(address indexed to, uint256 amount);
./frxETHMinter.sol:212:    event WithholdRatioSet(uint256 newRatio);
./OperatorRegistry.sol:208:    event TimelockChanged(address timelock_address);
./OperatorRegistry.sol:209:    event WithdrawalCredentialSet(bytes _withdrawalCredential);
./OperatorRegistry.sol:210:    event ValidatorAdded(bytes pubKey, bytes withdrawalCredential);
./OperatorRegistry.sol:212:    event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);
./OperatorRegistry.sol:213:    event ValidatorsPopped(uint256 times);
./OperatorRegistry.sol:214:    event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);
```

#### Mitigation
Add up to 3 indexed fields when possible.

&ensp;
