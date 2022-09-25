## L001 PRAGMA VERSION

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)

```solidity
frax-scope/ERC20PermitPermissionedMint.sol::2 => pragma solidity ^0.8.0;
frax-scope/OperatorRegistry.sol::2 => pragma solidity ^0.8.0;
frax-scope/frxETH.sol::2 => pragma solidity ^0.8.0;
frax-scope/frxETHMinter.sol::2 => pragma solidity ^0.8.0;
frax-scope/sfrxETH.sol::2 => pragma solidity ^0.8.0;
frax-scope/xERC4626.sol::4 => pragma solidity ^0.8.0;
```

## **L002 - Unsafe ERC20 Operation(s)**

### **Description**

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's `SafeERC20` library or at least to wrap each operation in a `require` statement.

To circumvent ERC20's `approve` functions race-condition vulnerability use OpenZeppelin's `SafeERC20` library's `safe{Increase|Decrease}Allowance` functions.

In case the vulnerability is of no danger for your implementation, provide enough documentation explaining the reasonings.

```solidity
frxETHToken.approve(address(sfrxETHToken), msg.value);
```

[https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L75](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L75)

### **Background Information**

- [OpenZeppelin's IERC20 documentation](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/IERC20.sol#L43)