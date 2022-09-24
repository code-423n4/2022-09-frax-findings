#1 Looping

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114

default uint is 0 so remove unnecassary explicit can reduce gas.
pre increment e.g ++i more cheaper gas than post increment e.g i++. i suggest to use pre increment.
caching the array length can reduce gas it caused access to a local variable is more cheap than query storage / calldata / memory in solidity.

#2 Use calldata instead of memory

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L171

In the external functions where the function argument is read-only, the function() has an inputed parameter that using memory, if this function didnt change the parameter, its cheaper to use calldata then memory. so we suggest to change it.

#3 Use storage instead memory

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L71-L72

Use storage instead of  memory to reduce the gas fee. i suggest to change this.

#4 Visibility

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L38-L39

change visibility from public to private or internal can save the gas.

#5 Use  x = x + y or  x = x - y more cheap than x += y or x -= y for state variables

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L168

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L72

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L67

Change the state to x = x + y or x = x - y for gas efficiency