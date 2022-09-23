-> X = X + Y IS CHEAPER THAN X += Y (same for X = X - Y IS CHEAPER THAN X -= Y)

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#:~:text=currentWithheldETH%20%2B%3D%20withheld_amt%3B
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#:~:text=currentWithheldETH%20%2D%3D%20amount%3B
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#:~:text=storedTotalAssets%20%2B%3D%20amount%3B


-> ++i costs less gas compared to i++ or i += 1 (Also --i costs less gas compared to i--- or i -= 1)

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#:~:text=minters_array.length%3B-,i%2B%2B),-%7B


->USING BOOLS FOR STORAGE INCURS OVERHEAD

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#:~:text=mapping(address%20%3D%3E%20bool)
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#:~:text=mapping(bytes%20%3D%3E%20bool)%20public


