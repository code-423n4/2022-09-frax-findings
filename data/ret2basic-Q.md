# Frax Ether Liquid Staking Contest QA Report

## Summary

The following gas optimization issues were found during the code audit:

1. Unsafe ERC20 Operation(s) (2 instances)
2. Unspecific Compiler Version Pragma (6 instances)

Total 8 instances of 2 issues.

## 1. Unsafe ERC20 Operation(s) (2 instances)

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard. It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

```solidity
src/frxETHMinter.sol::75 => frxETHToken.approve(address(sfrxETHToken), msg.value);

src/frxETHMinter.sol::200 => require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```

## 2. Unspecific Compiler Version Pragma (6 instances)

Avoid floating pragmas for non-library contracts. While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. It is recommended to pin to a concrete compiler version.

```solidity
src/DepositContract.sol::12 => pragma solidity ^0.8.0;

src/frxETHMinter.sol::2 => pragma solidity ^0.8.0;

src/frxETH.sol::2 => pragma solidity ^0.8.0;

src/IsfrxETH.sol::2 => pragma solidity >=0.8.0;

src/OperatorRegistry.sol::2 => pragma solidity ^0.8.0;

src/sfrxETH.sol::2 => pragma solidity ^0.8.0;
```
