# Gas report

| G-N    |Issue|Instances|
|:------:|:----|:-------:|
| [G&#x2011;01] | No need to initialize variables with default values | 8 |
| [G&#x2011;02] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 1 |
| [G&#x2011;03] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 5 |
| [G&#x2011;04] | `<array>.length` should not be looked up in every loop of a `for`-loop | 2 |
| [G&#x2011;05] | Don't compare boolean expressions to boolean literals | 3 |
| [G&#x2011;06] | Unused `minters_array` variable | 1 |
| [G&#x2011;07] | Overemited event parameter | 2 |
| [G&#x2011;08] | Redundant `require` | 1 |
| [G&#x2011;09] | Redundant `onlyByOwnGov` | 1 |
| [G&#x2011;10] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 3 |
| [G&#x2011;11] | `public` functions to `external` functions | 8 |
| [G&#x2011;12] | Use directly `validators.length` | 1 |

Total: 36 instances over 12 issues

## [G-01] No need to initialize variables with default values

In solidity all variables initialize in 0, address(0), false, etc.

```solidity
File: /src/frxETHMinter.sol

 63        withholdRatio = 0; // No ETH is withheld initially

 64        currentWithheldETH = 0;

 94        uint256 withheld_amt = 0;

129        for (uint256 i = 0; i < numDeposits; ++i) {
```

```solidity
File: /src/OperatorRegistry.sol

 63        for (uint256 i = 0; i < arrayLength; ++i) {

 84        for (uint256 i = 0; i < times; ++i) {

114            for (uint256 i = 0; i < original_validators.length; ++i) {
```

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

84        for (uint i = 0; i < minters_array.length; i++){
```

## [G‑02] `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)

Saves 5 gas per loop

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

84        for (uint i = 0; i < minters_array.length; i++){
```

## [G‑03] `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

```solidity
File: /src/frxETHMinter.sol

129        for (uint256 i = 0; i < numDeposits; ++i) {
```

```solidity
File: /src/OperatorRegistry.sol

 63        for (uint256 i = 0; i < arrayLength; ++i) {

 84        for (uint256 i = 0; i < times; ++i) {

114            for (uint256 i = 0; i < original_validators.length; ++i) {
```

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

84        for (uint i = 0; i < minters_array.length; i++){
```

## [G‑04] `<array>.length` should not be looked up in every loop of a `for`-loop

The overheads outlined below are PER LOOP, excluding the first loop

 * storage arrays incur a Gwarmaccess (100 gas)
 * memory arrays use `MLOAD` (3 gas)
 * calldata arrays use `CALLDATALOAD` (3 gas)

Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset

```solidity
File: /src/OperatorRegistry.sol

114            for (uint256 i = 0; i < original_validators.length; ++i) {
```

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

84        for (uint i = 0; i < minters_array.length; i++){
```

## [G‑05] Don't compare boolean expressions to boolean literals

`require(<x> == true)` => `require(<x>)`
`require(<x> == false)` => `require(!<x>)`

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

46       require(minters[msg.sender] == true, "Only minters");

68        require(minters[minter_address] == false, "Address already exists");

78        require(minters[minter_address] == true, "Address nonexistant");
```

## [G‑06] Unused `minters_array` variable

The data storage in `minters_array` it's never used also the `minters` mapping storage the same data

Remove the `minters_array` and his logic and use `MinterAdded` event to get off-chain the minters array

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

19    address[] public minters_array; // Allowed to mint

70        minters_array.push(minter_address);

83        // 'Delete' from the array by setting the address to 0x0
84        for (uint i = 0; i < minters_array.length; i++){
85            if (minters_array[i] == minter_address) {
86                minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
87                break;
88            }
89        }
```

## [G-07] Overemited event parameter

In the `Transfer` event of openzeppelin-contracts/contracts/token/ERC20/ERC20.sol contract, the `from`, `to` and `amount` parameters are emited
No need to emit them in the `TokenMinterBurned` and `TokenMinterMinted` events

> Combime this with N-06

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

From:
102    event TokenMinterBurned(address indexed from, address indexed to, uint256 amount);
To:
102    event TokenMinterBurned(address indexed to);

From:
103    event TokenMinterMinted(address indexed from, address indexed to, uint256 amount);
To:
103    event TokenMinterMinted(address indexed from);
```

## [G-08] Redundant `require`

The `address(0)` can be able to mint for the `require` in line 66

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

77        require(minter_address != address(0), "Zero address detected");
```

## [G-09] Redundant `onlyByOwnGov`

This alaready cheks in `addValidator` functione in line 53

```solidity
File: File: /src/OperatorRegistry.sol

61    function addValidators(Validator[] calldata validatorArray) external onlyByOwnGov {
```

## [G-10] Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead.

If the array is passed to an `internal` function which passes the array to another `internal` function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the `internal` functions from being called. Structs have the same overhead as an array of length one

```solidity
File: /src/OperatorRegistry.sol

172        bytes memory pubKey,

173        bytes memory signature,

181    function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {
```

## [G-11] `public` functions to `external` functions

```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

53    function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {

59    function minter_mint(address m_address, uint256 m_amount) public onlyMinters {

65    function addMinter(address minter_address) public onlyByOwnGov {

76    function removeMinter(address minter_address) public onlyByOwnGov {

94    function setTimelock(address _timelock_address) public onlyByOwnGov {
```

```solidity
File: /src/OperatorRegistry.sol

82    function popValidators(uint256 times) public onlyByOwnGov {

93    function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
```

```solidity
File: /src/sfrxETH.sol

54    function pricePerShare() public view returns (uint256) {
```

## [G-12] Use directly `validators.length`

> This enables mark as `external` the `numValidators` function

```solidity
File: /src/OperatorRegistry.sol

136        uint numVals = numValidators();

182        require(numValidators() == 0, "Clear validator array first");
```