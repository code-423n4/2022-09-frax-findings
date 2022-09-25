### [L-01] Use Of Depricated Solidity Functions

Several functions and operators in Solidity are deprecated. Using them leads to reduced code quality. With new major versions of the Solidity compiler, deprecated functions and operators may result in side effects and compile errors.

There are 5 instances of this issue:

> ** File :  ** => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L77
> ** File :  ** => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L85
> ** File :  ** => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L87
> ** File :  ** => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L90-L95
> ** File :  ** => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L129-L137

#### Recommended Mitigation

Solidity provides alternatives to the deprecated constructions. Most of them are aliases, thus replacing old constructions will not break current behavior. For example, sha3 can be replaced with keccak256



### [L-02] Use Require Instead of Assert

Assert Consume all gases when a function call failed,
Where as Require returns all remaining gas to caller on same situation, so on gas saving point of view its recommended to use require() instead of assert()

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L158




### [L-03] Floating Pragma

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

There are 7 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L02
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L02
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#L02
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L02
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/IsfrxETH.sol#L2
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L02
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L02




### [L-04] Absence of Zero Address Check 

Write code for Zero address check before assigning them to state variable .

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L34
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L61-L62




### [L-05] _to Address not checked before making low level function calling

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L170

#### Recommended Mitigation

_to address should be checked if it was  Zero address or not




### [L-06] Instead of using transfer/transferFrom use SafeTransfer/SafeTransferFrom

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L200

### [N-01] Visibility for Variables and Array not present

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L36
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L37
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L69-L72

#### Recommended Mitigation
Should add visibility to them.




### [N-02] removeValidator() Function logic can be Improved

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L106-L119

#### Recommended Mitigation

Instead of Deleting whole array, and then pushing individuals into a new except the one(that need to delet from array), there could be a more better gas efficient approch for this,

. Step-1 : Find the Index which need to be removed

. Step-2 : From That index iterate to the last index of array
               With each iteration you have to swap current index with upcoming one i.e swap i with i+1

. Step-3 : When loop ends, your require number(Validator) will be on Array's last position
               So simply Pop it out  




### [N-03] Contract does not follow proper Solidity Code Structure

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L208-L216
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L102-L107
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L205-L213

#### Recommended Mitigation

events should declared on top of the contract.