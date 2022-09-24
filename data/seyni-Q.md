# QA

# Low Severity

## [L-01] Typo in variable

The variable is called `sfrxeth_recieved` instead of `sfrxeth_received`.

### Files links :

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L78-L81

### Findings :

```solidity
2022-09-frax/src/frxETHMinter.sol:78:        uint256 sfrxeth_recieved = sfrxETHToken.deposit(msg.value, recipient);
2022-09-frax/src/frxETHMinter.sol:79:        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
2022-09-frax/src/frxETHMinter.sol:81:        return sfrxeth_recieved;
```

# Non-critical

## [N-01] Floating pragma compiler version

Contracts should not use a floating pragma in order to ensure that the code has been tested and deployed with the same version.

### Files links :

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

### Findings :

```solidity
2022-09-frax/src/ERC20/ERC20PermitPermissionedMint.sol:2: 	 pragma solidity ^0.8.0;
2022-09-frax/src/OperatorRegistry.sol:2: 	 pragma solidity ^0.8.0;
2022-09-frax/src/frxETHMinter.sol:2: 	 pragma solidity ^0.8.0;
2022-09-frax/src/sfrxETH.sol:2: 	 pragma solidity ^0.8.0;
```