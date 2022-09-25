
## [G-1] Used unchecked for post increment:
```solidity
frxETHMinter.sol:129:        for (uint256 i = 0; i < numDeposits; ++i) {
OperatorRegistry.sol:63:        for (uint256 i = 0; i < arrayLength; ++i) {
OperatorRegistry.sol:84:        for (uint256 i = 0; i < times; ++i) {
OperatorRegistry.sol:114:            for (uint256 i = 0; i < original_validators.length; ++i) {
```


## [G-2] Use preincrement
```solidity
ERC20/ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){ 
```
## [G-3] Cache .length in loops to save gas
```solidity
ERC20/ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){ 

OperatorRegistry.sol:114:            for (uint256 i = 0; i < original_validators.length; ++i) {
```

## [G-4] Donot use default values Explicit initialization with zero is not required for variable declaration of numTokens because `uints are 0` by default.removeing this will reduce contract size and save a bit of gas.

```solidity
ERC20/ERC20PermitPermissionedMint.sol:84:        for (uint i = 0; i < minters_array.length; i++){ 
frxETHMinter.sol:129:        for (uint256 i = 0; i < numDeposits; ++i) {
OperatorRegistry.sol:63:        for (uint256 i = 0; i < arrayLength; ++i) {
OperatorRegistry.sol:84:        for (uint256 i = 0; i < times; ++i) {
OperatorRegistry.sol:114:            for (uint256 i = 0; i < original_validators.length; ++i) {
```

## [G-5] `public` functions not called by the contract should be declared `external` instead   
```solidity
xERC4626.sol:45:    function totalAssets() public view override returns (uint256) {
ERC20/ERC20PermitPermissionedMint.sol:65:    function addMinter(address minter_address) public onlyByOwnGov {
ERC20/ERC20PermitPermissionedMint.sol:76:    function removeMinter(address minter_address) public onlyByOwnGov {
ERC20/ERC20PermitPermissionedMint.sol:94:    function setTimelock(address _timelock_address) public onlyByOwnGov {
sfrxETH.sol:54:    function pricePerShare() public view returns (uint256) {
OperatorRegistry.sol:82:    function popValidators(uint256 times) public onlyByOwnGov {
```

## [G-6] Variable == false|0 -> !variable or variable ==  true -> variable
```solidity
ERC20/ERC20PermitPermissionedMint.sol:46:       require(minters[msg.sender] == true, "Only minters");
ERC20/ERC20PermitPermissionedMint.sol:68:        require(minters[minter_address] == false, "Address already exists");
ERC20/ERC20PermitPermissionedMint.sol:78:        require(minters[minter_address] == true, "Address nonexistant");
OperatorRegistry.sol:182:        require(numValidators() == 0, "Clear validator array first");
```

## [G-07] Use `!= 0` instead of `> 0` 
```solidity
frxETHMinter.sol:79:        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
frxETHMinter.sol:126:        require(numDeposits > 0, "Not enough ETH in contract");
```

## [G-8] <x> += <y>` costs more gas than `<x> = <x> + <y>` 
```solidity
xERC4626.sol:67:        storedTotalAssets -= amount;
xERC4626.sol:72:        storedTotalAssets += amount;
frxETHMinter.sol:97:            currentWithheldETH += withheld_amt;
frxETHMinter.sol:168:        currentWithheldETH -= amount;
```


