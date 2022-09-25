## .length should not be looked up in every loop of a for-loop\
Even memory arrays incur the overhead of bit tests and bit shifts to calculate the array length. Storage array length checks incur an extra Gwarmaccess (100 gas) PER-LOOP.

    FIle: main/src/OperatorRegistry.sol   #1

    114           for (uint256 i = 0; i < original_validators.length; ++i) {

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

      File: main/src/ERC20/ERC20PermitPermissionedMint.sol   #2

      84        for (uint i = 0; i < minters_array.length; i++){ 

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

## ++i costs less gas than ++i, especially when it's used in for-loops (--i/i-- too)

     File: main/src/ERC20/ERC20PermitPermissionedMint.sol   #1

     84        for (uint i = 0; i < minters_array.length; i++){ 

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

## ++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow, as is the case when used in for- and while-loops
The unchecked keyword is new in solidity version 0.8.0, so  this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

    FIle: main/src/OperatorRegistry.sol   #1

    114           for (uint256 i = 0; i < original_validators.length; ++i) {

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

      FIle: main/src/OperatorRegistry.sol   #2

      84           for (uint256 i = 0; i < times; ++i) {

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84

      File: main/src/ERC20/ERC20PermitPermissionedMint.sol   #3

      84        for (uint i = 0; i < minters_array.length; i++){ 

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

## Use a more recent version of solidity
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

    FIle: main/src/OperatorRegistry.sol   #1

    2            pragma solidity ^0.8.0;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L2

       FIle: main/src/frxETHMinter.sol   #2

       2            pragma solidity ^0.8.0;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L2

      File: main/src/ERC20/ERC20PermitPermissionedMint.sol   #3

       2            pragma solidity ^0.8.0;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L2

      File: main/src/sfrxETH.sol   #4

      2            pragma solidity ^0.8.0;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L2

##  Using > 0 costs more gas than != 0 when used on a uint in a require() statement

    File: main/src/frxETHMinter.sol   #1

    79        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79

      File: main/src/frxETHMinter.sol   #2

      126        require(numDeposits > 0, "Not enough ETH in contract");

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126

## It costs more gas to initialize variables to zero than to let the default of zero be applied

    File: main/src/OperatorRegistry.sol   #1

    63        for (uint256 i = 0; i < arrayLength; ++i) {

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63

      File: main/src/OperatorRegistry.sol   #2

      84        for (uint256 i = 0; i < times; ++i) {

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84

      File: main/src/OperatorRegistry.sol   #3

      114           for (uint256 i = 0; i < original_validators.length; ++i) {

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

      File: main/src/frxETHMinter.sol   #4

      94        uint256 withheld_amt = 0;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94

      File: main/src/frxETHMinter.sol   #5

      129        for (uint256 i = 0; i < numDeposits; ++i) {

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129

      File: main/src/ERC20/ERC20PermitPermissionedMint.sol   #6

      84        for (uint i = 0; i < minters_array.length; i++){ 

* https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

## Using private rather than public for constants, saves gas
If needed, the value can be read from the verified contract source code

    File: main/src/frxETHMinter.sol   #1

    38     uint256 public constant DEPOSIT_SIZE = 32 ether;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L38

      File: main/src/frxETHMinter.sol   #2

      39    uint256 public constant RATIO_PRECISION = 1e6;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L39

## Use custom errors rather than revert()/require() strings to save deployment gas
Custom errors are available from solidity version 0.8.4. Use Solidity latest solidity version

    File: main/src/OperatorRegistry.sol   #1

    46        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L46

       File: main/src/OperatorRegistry.sol   #2

      137        require(numVals != 0, "Validator stack is empty");

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L137

      File: main/src/OperatorRegistry.sol   #3

      182        require(numValidators() == 0, "Clear validator array first");

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L182

      File: main/src/OperatorRegistry.sol   #4

      203        require(_timelock_address != address(0), "Zero address detected");

* https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L203

      File: main/src/frxETHMinter.sol   #5

      79        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79

      File: main/src/frxETHMinter.sol   #6

      87         require(!submitPaused, "Submit is paused");

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L87

      File: main/src/frxETHMinter.sol   #7

      88        require(msg.value != 0, "Cannot submit 0");

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L88

      File: main/src/frxETHMinter.sol   #8

      122       require(!depositEtherPaused, "Depositing ETH is paused");

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L122

      File: main/src/frxETHMinter.sol   #9

      126        require(numDeposits > 0, "Not enough ETH in contract");

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126

## <x> += <y> costs more gas than <x> = <x> + <y> for state variables

    File: main/src/frxETHMinter.sol   #1

    97            currentWithheldETH += withheld_amt;

* https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L97

