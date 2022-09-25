## [L-1] USE SAFEERC20.SAFEAPPROVE INSTEAD OF APPROVE
This is probably an oversight since SafeERC20 was imported and safeTransfer() was used for ERC20 token transfers. Nevertheless, note that approve() will fail for certain token implementations that do not return a boolean value (). Hence it is recommend to use safeApprove().
```solidity
frxETHMinter.sol:75:        frxETHToken.approve(address(sfrxETHToken), msg.value);
```

## [N-1] Maintain consistency in single and double quotes inside `require error msg`
```solidity

frxETHMinter.sol:79:        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
```