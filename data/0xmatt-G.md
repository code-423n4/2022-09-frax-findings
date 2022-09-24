# CodeArena Gas Optimization Report

## G01 - Cache array length when using loops

#### Vulnerability Details
Enumerating storage array length inside a loop creates an extra SLOAD operation for each iteration after the first. It is more gas efficient to use a temporary in-function variable to store the length, then read that value in the loop.

In [ERC20PermitPermissionedMint.sol's removeMinter() function](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84-L89) the loop uses minters_array.length as a boundary. As minters_array's length only increases, this means that the cost of removing a minter will increase over use. Instead of the current loop code, the following would be more optimal and scale over time:

```
uint256 minters_array_len = minters_array.length;
for (uint i = 0; i < minters_array_len; ++i){
	if (minters_array[i] == minter_address) {
		minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
		break;
	}
}
```

The code above also uses ++i instead of i++ to save 3 gas per iteration.

Another instance also exists in [OperatorRegistry.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114-L118)

## G02 - Use uint instead of bool for minters mapping

#### Vulnerability Details
The minters mapping uses an address => bool mapping. When a minter is added, the address value is set to true. Removing a minter requires an array iteration that will become more expensive whenever minters are added or removed. Instead of a true/false value, the minters mapping could be used to store an array reference as a uint256. By storing the reference, this improves scalability and eliminates the need for a loop.

To support this the following changes need to be made in [ERC20PermitPermissionedMint.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol).

In the [constructor](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L24-L35):

```
constructor(
	address _creator_address,
	address _timelock_address,
	string memory _name,
	string memory _symbol
)
ERC20(_name, _symbol)
ERC20Permit(_name)
Owned(_creator_address)
{
	timelock_address = _timelock_address;
	minters_array.push(address(0)); // Needed for lookups to work properly
}
```

In [addMinter()](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L64-L73):

```
// Adds whitelisted minters
function addMinter(address minter_address) public onlyByOwnGov {
	require(minter_address != address(0), "Zero address detected");
	
	require(minters[minter_address] == 0, "Address already exists");
	minters[minter_address] = minters_array.length; // will match on push
	minters_array.push(minter_address);
	
	emit MinterAdded(minter_address);
}
```

In [removeMinter()](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L75-L92):

```
// Remove a minter

function removeMinter(address minter_address) public onlyByOwnGov {
	require(minter_address != address(0), "Zero address detected");
	require(minters[minter_address] != 0, "Address nonexistant");
	
	// 'Delete' from the array by setting the address to 0x0
	uint256 idx = minters[minter_address];
	minters_array[idx] = address(0); // Leave a null in array and match indices
	
	// Delete from the mapping
	delete minters[minter_address];
	
	emit MinterRemoved(minter_address);
}
```

