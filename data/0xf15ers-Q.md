### 1. Address(0) check in `submitAndGive()` in `frxETHMinter.sol`
In https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L109

submitAndGive() expects recipient address. It is a good practice to check for address(0) to protect from unnecessary complications.
Consider adding `require(recipient != address(0));`

### 2. Withheld ratio can be set to 100%
In https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L159

In the `setWithholdRatio` function, the withhold ratio can be set to 100%, When the ratio is 100%, if someone makes a very large ETH deposit then the entirity of the ETH will be added to `currentWithheldETH` and non will be added for validators.

It is recommended to set the withHeld ratio is limited to some reasonable amount rather than 100%.

### 3. Public Constants are not necessary
In https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L38-L39

Some constants used for internal calculation are made public. It is recommended to set them to private since they dont need to be exposed through public funcitons. This also increases codesize.

### 4. The minters_array will contain more elements than the number of minters
in https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L86

When calling removeMinter, the minter is not removed from `minters_array`, instead it is replaced by zero address. this will cause the array to grow very large as more minters are added and removed.

### 5. Operation on an unbounded loop
In https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L61

It is generally recommended to put a reasonable limit on the loop size rather than having unbounded loop as argument on a function.
