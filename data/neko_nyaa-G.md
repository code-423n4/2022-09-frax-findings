### [G-01] Using `bool` instead of `uint256` causes extra gas overhead

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L20
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L43

### [G-02] Don't compare `array.length` in every iteration of the for loop

It is more gas-efficient to cache the length first, then compare them. E.g.

```solidity=
uint len = minters_array.length;
for (uint i = 0; i < len; i++) { 
    if (minters_array[i] == minter_address) {
        minters_array[i] = address(0);
        break;
    }
}
```

Two applicable instances:

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L83-L89
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

### [G-03] Don't compare a boolean with `true`, use the value itself

e.g. This line of code
```solidity=
require(minters[msg.sender] == true, "Only minters");
```

Can be changed to

```solidity=
require(minters[msg.sender], "Only minters");
```

Or, if **[G-01]** is implemented

```solidity=
require(minters[msg.sender] != 0, "Only minters");
```

Two applicable instances

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L46
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78

