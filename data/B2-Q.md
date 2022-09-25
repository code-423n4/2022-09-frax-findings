## public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents’ functions and change the visibility from external to public

    File: main/src/OperatorRegistry.sol   #1

    82    function popValidators(uint256 times) public onlyByOwnGov {

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L82

      File: main/src/ERC20/ERC20PermitPermissionedMint.sol  #2

      53    function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L53

      File: main/src/ERC20/ERC20PermitPermissionedMint.sol  #3

      59    function minter_mint(address m_address, uint256 m_amount) public onlyMinters {

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L59

      File: main/src/ERC20/ERC20PermitPermissionedMint.sol   #4

      65    function addMinter(address minter_address) public onlyByOwnGov {

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L65

      File: main/src/ERC20/ERC20PermitPermissionedMint.sol   #5

      76    function removeMinter(address minter_address) public onlyByOwnGov {

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L76

      File: main/src/sfrxETH.sol   #6

      54     function pricePerShare() public view returns (uint256) {

* https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L54

## Missing 0 address check
If contract miss this zero check address validation there is chance that contract will loose some functionality

    File: main/src/OperatorRegistry.sol   #1

    41        timelock_address = _timelock_address;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L41

      File: main/src/ERC20/ERC20PermitPermissionedMint.sol   #2

      34      timelock_address = _timelock_address;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L34

## Floating Pragma

    File: main/src/OperatorRegistry.sol   #1

    2         pragma solidity ^0.8.0;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L2

      File: main/src/frxETHMinter.sol   #2

      2         pragma solidity ^0.8.0;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L2

      File: main/src/ERC20/ERC20PermitPermissionedMint.sol   #3

      2         pragma solidity ^0.8.0;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L2

      File: main/src/sfrxETH.sol   #4

      2         pragma solidity ^0.8.0;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L2

## Use of Block.timestamp
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers , locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

    File: main/src/sfrxETH.sol   #1

    50        if (block.timestamp >= rewardsCycleEnd) { syncRewards(); } 

* https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L50

## Use Solidity version of atleast 0.8.

    File: main/src/OperatorRegistry.sol   #1

    2         pragma solidity ^0.8.0;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L2

      File: main/src/frxETHMinter.sol   #2

      2         pragma solidity ^0.8.0;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L2

      File: main/src/ERC20/ERC20PermitPermissionedMint.sol   #3

      2         pragma solidity ^0.8.0;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L2

      File: main/src/sfrxETH.sol   #4

      2         pragma solidity ^0.8.0;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L2

## APPROVE should be replaced with SAFEAPPROVE or SAFEINCREASEALLOWANCE/SAFEDECREASEALLOWANCE()
`approve` is subject to a known front-running attack. Consider using `safeapprove` instead.

    File: main/src/frxETHMinter.sol   #1

    75        frxETHToken.approve(address(sfrxETHToken), msg.value);

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L75

## Event is missing indexed fields
Each event should use three indexed fields if there are three or more fields

    File: main/src/OperatorRegistry.sol   #1

    212     event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L212

      File: main/src/OperatorRegistry.sol   #2

      214    event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);

      
* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L214

      File: main/src/frxETHMinter.sol   #3

      207    event ETHSubmitted(address indexed sender, address indexed recipient, uint256 sent_amount, uint256 withheld_amt);

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L207

      File: main/src/ERC20/ERC20PermitPermissionedMint.sol   #4

      102     event TokenMinterBurned(address indexed from, address indexed to, uint256 amount);

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L102

      File: main/src/ERC20/ERC20PermitPermissionedMint.sol   #5

      103    event TokenMinterMinted(address indexed from, address indexed to, uint256 amount);

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L103