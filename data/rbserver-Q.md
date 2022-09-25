## [L-01] CALLING `togglePauseSubmits` OR `togglePauseDepositEther` FUNCTION BY OWNER TAKES EFFECT IMMEDIATELY, WHICH LEAVES NO TIME FOR USERS TO REACT
The following `togglePauseSubmits` and `togglePauseDepositEther` functions are callable by the owner as allowed by the `onlyByOwnGov` modifier that is used by these functions. Unlike being called by the governance timelock contract, calling one of these functions by the owner will take effect immediately. Before calling the `_submit` or `depositEther` function below, the user can check the `submitPaused` or `depositEtherPaused` value at that moment; if such value is false, then she or he will send the `_submit` or `depositEther` transaction. However, without any prior notice, the owner calls `togglePauseSubmits` or `togglePauseDepositEther` just before the user calls `_submit` or `depositEther`. As a result, the user's transaction reverts when calling `_submit` or `depositEther`. The user wastes gas and feels that the user experience is degraded. To mitigate the centralization risk and build trust among users, please consider making the `togglePauseSubmits` and `togglePauseDepositEther` functions only callable by the governance timelock contract.

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L177-L181
```solidity
    function togglePauseSubmits() external onlyByOwnGov {
        submitPaused = !submitPaused;

        emit SubmitPaused(submitPaused);
    }
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L184-L188
```solidity
    function togglePauseDepositEther() external onlyByOwnGov {
        depositEtherPaused = !depositEtherPaused;

        emit DepositEtherPaused(depositEtherPaused);
    }
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L85-L101
```solidity
    function _submit(address recipient) internal nonReentrant {
        // Initial pause and value checks
        require(!submitPaused, "Submit is paused");
        ...
    }
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L120-L155
```solidity
    function depositEther() external nonReentrant {
        // Initial pause check
        require(!depositEtherPaused, "Depositing ETH is paused");

        ...
    }
```

## [L-02] CALLING `moveWithheldETH` FUNCTION CAN REVERT IF THE `to` INPUT CORRESPONDS TO A CONTRACT THAT DOES NOT INTEND TO RECEIVE ETH THROUGH ITS `receive` OR `fallback` FUNCTION
Calling the following `moveWithheldETH` function will send `amount` ETH from the `frxETHMinter` contract to the `to` address. It is possible that `to` corresponds to a contract that does not intend to receive ETH through its `receive` or `fallback` function. In this case, calling `moveWithheldETH` will revert. To allow sending ETH to such contract, please consider adding another function, which is similar to `moveWithheldETH`, that has a `data` input in which `(bool success,) = payable(to).call{ value: amount }(data)` can be executed for sending ETH through calling such contract's relevant function.

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L166-L174
```solidity
    function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
        currentWithheldETH -= amount;

        (bool success,) = payable(to).call{ value: amount }("");
        require(success, "Invalid transfer");

        emit WithheldETHMoved(to, amount);
    }
```

## [L-03] MISSING ZERO-ADDRESS CHECKS FOR CRITICAL ADDRESSES
To prevent unintended behaviors, critical address inputs should be checked against `address(0)`.

Please consider checking `_timelock_address` in the following `constructor`.
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#L36-L41
```solidity
    constructor(
      address _creator_address,
      address _timelock_address
    ) 
    ERC20PermitPermissionedMint(_creator_address, _timelock_address, "Frax Ether", "frxETH") 
    {}
```

Please consider checking `depositContractAddress`, `frxETHAddress`, `sfrxETHAddress`, and `_timelock_address` in the following `constructor`.
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L52-L65
```solidity
    constructor(
        address depositContractAddress, 
        address frxETHAddress, 
        address sfrxETHAddress, 
        address _owner, 
        address _timelock_address,
        bytes memory _withdrawalCredential
    ) OperatorRegistry(_owner, _timelock_address, _withdrawalCredential) {
        depositContract = IDepositContract(depositContractAddress);
        frxETHToken = frxETH(frxETHAddress);
        sfrxETHToken = IsfrxETH(sfrxETHAddress);
        withholdRatio = 0; // No ETH is withheld initially
        currentWithheldETH = 0;
    }
```

## [N-01] CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS
To improve readability and maintainability, constants can be used instead of magic numbers. Please consider replacing the magic numbers used in the following code with constants.

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L54-L56
```solidity
    function pricePerShare() public view returns (uint256) {
        return convertToAssets(1e18);
    }
```

https://github.com/corddry/ERC4626/blob/main/src/xERC4626.sol#L91-L93
```solidity
        if (end - timestamp < rewardsCycleLength / 20) {
            end += rewardsCycleLength;
        }
```

## [N-02] MISSING INDEXED EVENT FIELDS
Querying events can be optimized with index. Please consider adding `indexed` to the relevant fields of the following events.

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L212
```solidity
    event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L214
```solidity
    event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);
```

## [N-03] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. NatSpec comments are missing for the following functions. Please consider adding them.

```solidity
lib\ERC4626\src\xERC4626.sol
  65: function beforeWithdraw(uint256 amount, uint256 shares) internal virtual override {
  71: function afterDeposit(uint256 amount, uint256 shares) internal virtual override {
```

## [N-04] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. @param or @return comments are missing for the following functions. Please consider completing NatSpec comments for them.

```solidity
src\frxETHMinter.sol
  70: function submitAndDeposit(address recipient) external payable returns (uint256 shares) {    
  85: function _submit(address recipient) internal nonReentrant {    
  109: function submitAndGive(address recipient) external payable {    
  166: function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {    
  191: function recoverEther(uint256 amount) external onlyByOwnGov {   

src\OperatorRegistry.sol
  53: function addValidator(Validator calldata validator) public onlyByOwnGov {   
  69: function swapValidator(uint256 from_idx, uint256 to_idx) public onlyByOwnGov {  
  93: function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {  
  126: function getNextValidator() 
  151: function getValidator(uint i)  
  171: function getValidatorStruct( 
  181: function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov { 
  197: function numValidators() public view returns (uint256) {    

src\sfrxETH.sol
  48: function beforeWithdraw(uint256 assets, uint256 shares) internal override {    
  54: function pricePerShare() public view returns (uint256) {    
  59: function depositWithSignature(    
  75: function mintWithSignature(    

lib\ERC4626\src\xERC4626.sol
  45: function totalAssets() public view override returns (uint256) {
```

## [N-05] FLOATING PRAGMAS
It is a best practice to lock pragmas instead of using floating pragmas to ensure that contracts are tested and deployed with the intended compiler version. Accidentally deploying contracts with different compiler versions can lead to unexpected risks and undiscovered bugs. Please consider locking pragma for the following files.

```solidity
src\frxETHMinter.sol
  2: pragma solidity ^0.8.0;

src\OperatorRegistry.sol
  2: pragma solidity ^0.8.0;

src\sfrxETH.sol
  2: pragma solidity ^0.8.0;

src\ERC20\ERC20PermitPermissionedMint.sol
  2: pragma solidity ^0.8.0;

lib\ERC4626\src\xERC4626.sol
  4: pragma solidity ^0.8.0;
```