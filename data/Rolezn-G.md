Total Gas Optimizations: 13

## (1) <Array>.length Should Not Be Looked Up In Every Loop Of A For-loop

Severity: Gas Optimizations

The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)

Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

## Proof Of Concept

	for (uint256 i = 0; i < original_validators.length; ++i) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L114

	for (uint i = 0; i < minters_array.length; i++){ 
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84






## (2) Don’t Compare Boolean Expressions To Boolean Literals

Severity: Gas Optimizations

For cases of: if (<x> == true), use if (<x>) instead
For cases of: if (<x> == false), use if (!<x>) instead

## Proof Of Concept

	require(minters[minter_address] == false, "Address already exists");
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L68

	require(minters[minter_address] == true, "Address nonexistant");
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78





## (3) ++i Costs Less Gas Than i++, Especially When It’s Used In For-loops (--i/i-- Too)

Severity: Gas Optimizations

Saves 6 gas per loop

## Proof Of Concept


	for (uint i = 0; i < minters_array.length; i++){ 
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84



## Recommended Mitigation Steps

For example, use ++i instead of i++



## (4) It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied

Severity: Gas Optimizations

## Proof Of Concept


	for (uint256 i = 0; i < numDeposits; ++i) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L129

	for (uint256 i = 0; i < arrayLength; ++i) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L63

	for (uint256 i = 0; i < times; ++i) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L84

	for (uint256 i = 0; i < original_validators.length; ++i) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L114






## (5) Using > 0 Costs More Gas Than != 0 When Used On A Uint In A Require() Statement

Severity: Gas Optimizations

This change saves 6 gas per instance

## Proof Of Concept


	require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L79

	require(numDeposits > 0, "Not enough ETH in contract");
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L126





## (6) Not Using The Named Return Variables When A Function Returns, Wastes Deployment Gas

Severity: Gas Optimizations

## Proof Of Concept


	function submitAndDeposit(address recipient) external payable returns (uint256 shares) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L70

	function depositWithSignature(
        uint256 assets,
        address receiver,
        uint256 deadline,
        bool approveMax,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external nonReentrant returns (uint256 shares) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/sfrxETH.sol#L59

	function mintWithSignature(
        uint256 shares,
        address receiver,
        uint256 deadline,
        bool approveMax,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external nonReentrant returns (uint256 assets) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/sfrxETH.sol#L75






## (7) <x> += <y> Costs More Gas Than <x> = <x> + <y> For State Variables

Severity: Gas Optimizations

## Proof Of Concept


	currentWithheldETH += withheld_amt;
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L97

	currentWithheldETH -= amount;
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L168





## (8) Using Private Rather Than Public For Constants, Saves Gas

Severity: Gas Optimizations

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

## Proof Of Concept

    uint256 public constant DEPOSIT_SIZE = 32 ether; 
    uint256 public constant RATIO_PRECISION = 1e6; 
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L38-L39



## Recommended Mitigation Steps

Set variable to private.



## (9) Public Functions To External

Severity: Gas Optimizations

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

## Proof Of Concept

	function popValidators(uint256 times) public onlyByOwnGov {
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L82

	function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L93

	function pricePerShare() public view returns (uint256) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/sfrxETH.sol#L54

	function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L53

	function addMinter(address minter_address) public onlyByOwnGov {
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L65

	function removeMinter(address minter_address) public onlyByOwnGov {
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L76

	function setTimelock(address _timelock_address) public onlyByOwnGov {
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L94






## (10) require() Strings Longer Than 32 Bytes Cost Extra Gas

Severity: Gas Optimizations

## Proof Of Concept


	require(!activeValidators[pubKey], "Validator already has 32 ETH");
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L140

	require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L167




## (11) ++i/i++ Should Be Unchecked{++i}/unchecked{i++} When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

Severity: Gas Optimizations

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

## Proof Of Concept


	for (uint256 i = 0; i < numDeposits; ++i) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L129

	for (uint256 i = 0; i < arrayLength; ++i) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L63

	for (uint256 i = 0; i < times; ++i) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L84

	for (uint256 i = 0; i < original_validators.length; ++i) {
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L114

	for (uint i = 0; i < minters_array.length; i++){ 
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84





## (12) Use assembly to check for address(0)

Severity: Gas Optimizations

Save 6 gas per instance if using assembly to check for address(0)

e.g.
assembly {
	if iszero(_addr) {
		mstore(0x00, "AddressZero")
		revert(0x00, 0x20)
	}
}

## Proof Of Concept


	function setTimelock(address _timelock_address) external onlyByOwnGov {
https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L202

	function addMinter(address minter_address) public onlyByOwnGov {
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L65

	function removeMinter(address minter_address) public onlyByOwnGov {
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L76

	function setTimelock(address _timelock_address) public onlyByOwnGov {
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L94






## (13) Using Bools For Storage Incurs Overhead

Severity: Gas Optimizations

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

## Proof Of Concept


    mapping(bytes => bool) public activeValidators;
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L43

	bool public submitPaused;
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L49

	bool public depositEtherPaused;
https://github.com/code-423n4/2022-09-frax/tree/main/src/frxETHMinter.sol#L50

    mapping(address => bool) public minters;
https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L20





