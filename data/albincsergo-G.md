### Gas optimisations
1. When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0.
```
src/frxETHMinter.sol::79 => require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
src/frxETHMinter.sol::126 => require(numDeposits > 0, "Not enough ETH in contract");
```

2. Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.
```
src/DepositContract.sol::76 => for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH - 1; height++)
src/DepositContract.sol::83 => for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++)
src/DepositContract.sol::148 => for (uint height = 0; height < DEPOSIT_CONTRACT_TREE_DEPTH; height++)
src/ERC20/ERC20PermitPermissionedMint.sol::84 => for (uint i = 0; i < minters_array.length; i++)
src/OperatorRegistry.sol::63 => for (uint256 i = 0; i < arrayLength; ++i)
src/OperatorRegistry.sol::84 => for (uint256 i = 0; i < times; ++i)
src/OperatorRegistry.sol::114 => for (uint256 i = 0; i < original_validators.length; ++i)
src/frxETHMinter.sol::129 => for (uint256 i = 0; i < numDeposits; ++i)
```

3. Avoid floating pragmas for non-library contracts. While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. It is recommended to pin to a concrete compiler version.
```
src/DepositContract.sol::12 => pragma solidity ^0.8.0;
src/ERC20/ERC20PermitPermissionedMint.sol::2 => pragma solidity ^0.8.0;
src/IsfrxETH.sol::2 => pragma solidity >=0.8.0;
src/OperatorRegistry.sol::2 => pragma solidity ^0.8.0;
src/Utils/Owned.sol::2 => pragma solidity ^0.8.0;
src/Utils/SigUtils.sol::2 => pragma solidity ^0.8.0;
src/frxETH.sol::2 => pragma solidity ^0.8.0;
src/frxETHMinter.sol::2 => pragma solidity ^0.8.0;
src/sfrxETH.sol::2 => pragma solidity ^0.8.0;
```
4. Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
src/DepositContract.sol::108 => require(pubkey.length == 48, "DepositContract: invalid pubkey length");
src/DepositContract.sol::109 => require(withdrawal_credentials.length == 32, "DepositContract: invalid withdrawal_credentials length");
src/DepositContract.sol::110 => require(signature.length == 96, "DepositContract: invalid signature length");
src/ERC20/ERC20PermitPermissionedMint.sol::84 => for (uint i = 0; i < minters_array.length; i++)
src/OperatorRegistry.sol::62 => uint arrayLength = validatorArray.length;
src/OperatorRegistry.sol::100 => swapValidator(remove_idx, validators.length - 1);
src/OperatorRegistry.sol::114 => for (uint256 i = 0; i < original_validators.length; ++i) 
src/OperatorRegistry.sol::198 => return validators.length;
src/sfrxETH.sol::34 => The cycles are constant length, but calling syncRewards slightly into a would-be cycle keeps the same would-be endpoint (so cycle ends are every X seconds).
```
