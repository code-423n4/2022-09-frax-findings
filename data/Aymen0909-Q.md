# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1      | Immutable state variables lack zero address checks | Low | 3 |
| 2      | Redundant check in the `submitAndDeposit` function | NC | 1 |
| 3      | Non-library/interface files should use fixed compiler versions, not floating ones | NC| 1  |
| 4      | Named return variables not used anywhere in the functions | NC | 3 |
| 5      | `public` functions not called by the contract should be declared `external` instead   | NC | 5 |


## Findings

### 1- Immutable state variables lack zero address checks  :

Constructor should check the values written in an immutable state variables in this case addresses to verify that it is not the zero address (address(0))

#### Impact - Low Risk

#### Proof of Concept
Instances include:

File: src/frxETHMinter.sol

[depositContract = IDepositContract(depositContractAddress);](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L60)

[frxETHToken = frxETH(frxETHAddress);](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L61)

[sfrxETHToken = IsfrxETH(sfrxETHAddress);](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L62)

#### Mitigation
Add non-zero address checks in the constructor for the instances aforementioned.

### 2- Redundant check in the `submitAndDeposit` function :

The check for the amount received after calling `sfrxETHToken.deposit(msg.value, recipient);` is redundant as the `deposit` function from the [solmat ERC4626 contract](https://github.com/transmissions11/solmate/blob/main/src/mixins/ERC4626.sol#L46-L58) already contain a check for the non-zero shares amount, so it will automaticaly revert if the returned amount (shares) is 0.

#### Impact - NON CRITICAL

#### Proof of Concept
Instances include:

File: src/frxETHMinter.sol

[require(sfrxeth_recieved > 0, 'No sfrxETH was returned');](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79)

#### Mitigation

Remove the check aforementioned because it's redundant.

### 3- Non-library/interface files should use fixed compiler versions, not floating ones :

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

check this [source](https://swcregistry.io/docs/SWC-103)

#### Impact - NON CRITICAL

#### Proof of Concept
All contracts use a floating solidity version : 

```
pragma solidity ^0.8.0;
```

#### Mitigation
Lock the pragma version to the same version as used in the other contracts and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile it locally.

### 4- Named return variables not used anywhere in the function :

When Named return variable are declared they should be used inside the function instead of the return statement or if not they should be removed to avoid confusion.

#### Impact - NON CRITICAL

#### Proof of Concept
Instances include:

File: src/frxETHMinter.sol

[returns (uint256 shares)](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L70)

File: src/sfrxETH.sol

[returns (uint256 shares)](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L67)

[returns (uint256 assets)](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L83)

#### Mitigation

Either use the named return variables inplace of the return statement or remove them.

### 5- `public` functions not called by the contract should be declared `external` instead  :

#### Impact - NON CRITICAL

#### Proof of Concept
Instances include:

File: src/ERC20/ERC20PermitPermissionedMint.sol

[function minter_burn_from(address b_address, uint256 b_amount) public](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L53)

[function minter_mint(address m_address, uint256 m_amount) public](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L59)

[function addMinter(address minter_address) public](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L65)

[function removeMinter(address minter_address) public](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L76)

[function setTimelock(address _timelock_address) public](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L94)

#### Mitigation

Declare the functions aforementioned external.