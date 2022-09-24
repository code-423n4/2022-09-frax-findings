## 1. typo in comments

submites --> submits

- [frxETHMinter.sol#L176](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L176)

nonexistant --> nonexistent

- [ERC20PermitPermissionedMint.sol#L78](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78)


## 2. use of floating pragma

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

- [frxETH.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol)
- [sfrxETH.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol)
- [ERC20PermitPermissionedMint.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol)
- [frxETHMinter.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol)
- [OperatorRegistry.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol)
- [xERC4626.sol](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol)


## 3. event is missing indexed fields
Each event should use three indexed fields if there are three or more fields

- [OperatorRegistry.sol#L212](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L212)
- [OperatorRegistry.sol#L214](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L214)

- [frxETHMinter.sol#L207](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L207)

- [ERC20PermitPermissionedMint.sol#L102](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L102)
- [ERC20PermitPermissionedMint.sol#L103](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L103)


## 4. _safemint() should be used rather than _mint() wherever possible

- [ERC20PermitPermissionedMint.sol#L60](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L60)


## 5. constants should be defined rather than using magic numbers 
Even assembly can benefit from using readable constants instead of hex/numeric literals

- [xERC4626.sol#L91](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L91)

- [sfrxETH.sol#L55](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L55)


## 6. Outdated compiler version
Using very old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs
