1. OZ Lib can't found

This location file in directory has changed or it can't found, and u must update them and adding/import some contract to avoid running/error. This list down below for some file must be imported : 

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L4-L7

```
openzeppelin-contracts/contracts/token/ERC20/ERC20.sol
openzeppelin-contracts/contracts/token/ERC20/IERC20.sol
openzeppelin-contracts/contracts/token/ERC20/extensions/draft-ERC20Permit.sol
openzeppelin-contracts/contracts/token/ERC20/extensions/ERC20Burnable.sol
```
2. Need to validate if `minters[minter_address]` was false

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L76-L92

on the fn of removeMinter() you need to validate if minters[minter_address] = false; 

Since it was the same as [fn addMinter()](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L68-L69) was used so it can be remain the same for removeMinter()

Recommendation by adding `minters[minter_address] = false;` after `require(minters[minter_address] == true, "Address nonexistant");`

3. Used `received` than `recieved`

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L78-L81

change the naming `recieved` into `received` for good readibility and style.

4. Missing Indexed 

Each event should be using three indexed fields if there are three or more fields

Files : 

1.) https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L207

```
    event ETHSubmitted(address indexed sender, address indexed recipient, uint256 sent_amount, uint256 withheld_amt);
```
2,) https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L104-L105

```
 event MinterAdded(address minter_address); //missing @indexed
 event MinterRemoved(address minter_address); //missing @indexed
```

5. Simple reason string

Files :

1.) https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L77

```
Zero address
```
Same case : 

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L95

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L66


2.) https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L78

```
Address non-existent
```

6. Locked Pragma Compiler 

File : 

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

Since it was used ^0.8.0. As the compiler can be use for example 0.8.14 and consider locking at this version the same as another. It can be consider using  locking the pragma version whenever possible and avoid using a floating pragma in the final deployment. Since it can be problematic, if there are publicly disclosed bugs and issues that affect the current compiler version used.

