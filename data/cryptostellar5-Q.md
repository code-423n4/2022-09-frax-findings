### 1. EVENT IS MISSING INDEXED FIELDS

*Number of Instances Identified: 29*

Each `event` should use three `indexed` fields if there are three or more fields

### 2. FLOATING PRAGMA VERSION SHOULD NOT BE USED

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)

This should be applied accross all inscope contracts

### 3. PREFER SAFETRANSFER INSTEAD OF TRANSFER

*Number of Instances Identified: 1*

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L200

```
require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```

### 4. THE CONTRACT SHOULD SAFEAPPROVE(0) FIRST


*Number of Instances Identified: 1*

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
75: frxETHToken.approve(address(sfrxETHToken), msg.value);
```

### 5. MISSING ZERO-ADDRESS CHECK

*Number of Instances Identified: 2*

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#L36-L41

```
   constructor( 
      address _creator_address,
      address _timelock_address
    ) 
    ERC20PermitPermissionedMint(_creator_address, _timelock_address, "Frax Ether", "frxETH") 
    {}
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L40-L43

```
    constructor(address _owner, address _timelock_address, bytes memory _withdrawal_pubkey) Owned(_owner) {
        timelock_address = _timelock_address;
        curr_withdrawal_pubkey = _withdrawal_pubkey;
    }
```


### 6. USE OF BLOCK.TIMESTAMP

*Number of Instances Identified: 1*

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L50

```
if (block.timestamp >= rewardsCycleEnd) { syncRewards(); }
```


### 6. USE LATEST SOLIDITY VERSION

When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives [security fixes](https://github.com/ethereum/solidity/security/policy#supported-versions). Furthermore, breaking changes as well as new features are introduced regularly.
Latest Version is 0.8.17

### 7. NATSPEC IS INCOMPLETE

*Number of Instances Identified: 21*
Several functions and constructors throughout the contract are missing `@param` NATSPEC.
