### [G-01] For loop can be Optimizable

There are 8 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129
> ** File :  **  =>  https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L76
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L83
> ** File :  ** => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L148

#### Recommended Mitigation

. Should not initialize uint with default value i.e uint i=0 **TO** uint i;
. Should uncheck i++




### [G-02] removeValidator() Function logic can be Improved

Instead of Deleting whole array, and then pushing individuals into a new except the one(that need to delet from array), there could be a more better gas efficient approch for this,

. Step-1 : Find the Index which need to be removed

. Step-2 : From That index iterate to the last index of array
               With each iteration you have to swap current index with upcoming one i.e swap i with i+1

. Step-3 : When loop ends, your require number(Validator) will be on Array's last position
               So simply Pop it out  


There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L106-L119





### [G-03] Function that could be External

The functions which not called inside that contract, by making them external you can save some gas during deployment.

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L54

#### Recommended Mitigation

Make Those functions external.




### [G-04] Assigning default value to uints

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L63-L64

#### Recommended Mitigation

Uints by default value is 0. 
So by assigning 0 values to them, contract consume extra gas



### [G-05] >= Costs less gas than >

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L143

#### Recommended Mitigation
Try to use >= whenever possible instead of > 




### [G-06] <x> = <x> + <y> is more gas efficient than use of <x> += <y>

In many instances <x> += <y> This type of syntax are used, Those can optimized to previous one i.e  <x> = <x> + <y>

There are 6 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L97
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L168
> ** File :  **  => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L146




### [G-07] Constant variables should be Fixed value, Instead of Formula

Constant Variables value should be a value of that type, 
Try to avoid assigning them with any formula, that cause more gas as some computaion will goes on.
Do computation on your own, just assign value only.

There are 1 instances of this issue:

> ** File : ** => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L67




### [G-08] Divide and Multiplication can replace by Bit shifts which will reduce the gas

Bit Shifts costs less gas than actual arithmetic calculation,
So its recommended to use left or right shift whenever possible instead of multiplication and division 

There are 3 instances of this issue:

> ** File : ** => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L88
> ** File : ** => https://github.com/code-423n4/2022-09-frax/blob/main/src/DepositContract.sol#L154


