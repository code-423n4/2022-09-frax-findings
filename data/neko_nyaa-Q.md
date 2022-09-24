### [N-01] `ERC20PermitPermissionedMint.sol`: It is possible to implement `removeMinter()` without having to loop through the entire array

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L76-L92

We can use the `minters` mapping to store the ($1$-based) *index* of the minter, instead of simply a boolean flag. This will immediately tell us which position to set `minters_array` to $0$, and also has the virtue of saving gas, for not having to read the entire storage array, as well as using `uint256` is actually more gas-efficient than `bool`.

Such an implementation can be as follow:

```solidity=
address[] public minters_array;
mapping(address => uint256) public minters; // not bool

function addMinter(address minter_address) public onlyByOwnGov {
    minters[minter_address] = minters_array.length + 1; 
    minters_array.push(minter_address);
}

function removeMinter(address minter_address) public onlyByOwnGov {
    require(minters[minter_address] != 0, "Address nonexistant");
    minters_array[minters[minter_address]] = address(0);
    delete minters[minter_address];
}
```

The `onlyMinters` modifier would then also need to be changed, albeit that too is quite simple

```solidity=
modifier onlyMinters() {
    require(minters[msg.sender] != 0, "Only minters");
    _;
} 
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L45-L48

Alternatively, one can also take an approach to remove minter by index, instead of by address (similar to how `OperatorRegistry.removeValidator()` works), so that the `minters` mapping can retain a boolean-like value structure (however that would require finding the minter's index off-chain first).

### [N-02] Inconsistent spacing between brackets

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84-L85

### [N-03] Misleading function name

`OperatorRegistry`'s `getNextValidator()` can be renamed to `getLastValidatorAndPop()` (the current function name does not mention popping behavior, and `Next` is usually not interpreted to be `Last`)

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L126

### [N-04] `public` functions that are not called by the contract itself can be made `external`

Applies to all `public` functions within `ERC20PermitPermissionedMint.sol`

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L53
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L59
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L65
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L76
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L94

### [N-05] It is recommended to use `safeTransfer` instead of `transfer` for ERC20

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L200
