# Index
1. Post-increment/decrement cost more gas then pre-increment/decrement
2. Array length should not be looked up in every loop of a for-loop
3. != 0 is cheaper than > 0 especially in state variables
4. I = I + (-) X is cheaper in gas cost than I += (-=) X
5. Require / Revert strings longer than 32 bytes cost extra gas
6. Don't compare boolean expressions to boolean literals
7. Use custom errors rather than REVERT()/REQUIRE() strings to save deployment gas
8. Using private rather than public for constants, saves gas
9. Using bools for storage incurs overhead
10. Should use unchecked in arithmetic operations when it's not possible to overflow
11. Initialize variables with default values are not needed
12. OperatorRegistry.removeValidator operate in an auxiliar variable instead of access to storage in every loop
13. ERC20PermitPermissionedMint.removeMinter operate in an auxiliar variable instead of access to storage in every loop
14. Declare local variable before are going to be used
15. Using storage instead of memory for structs/arrays
16. Usage of uints/ints smaller than 32 Bytes (256 bits) incurs overhead
17. Use a more recent version of solidity

# Details
## 1. Post-increment/decrement cost more gas then pre-increment/decrement
### Description
++I (--I) cost less gas than I++ (I--) especially in a loop.

### Proof of concept

```solidity
contract TestPost {
	function testPost() public {
		uint256 i;
		i++;
	}
}
contract TestPre {
	function testPre() public {
		uint256 i;
		++i;
	}
}
```

- Transaction cost of testPost is 21333 gas
- Transaction cost of testPre is 21328 gas 
- After the test it's possible to save **5 gas per ocurrence** with this optimization.

### Lines in the code
[ERC20PermitPermissionedMint.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)

## 2. Array length should not be looked up in every loop of a for-loop
### Description
Storage array length checks incur an extra Gwarmaccess (100 gas) per loop. Store the array length in a variable and use it in the for loop helps to save gas

### Lines in the code
[ERC20PermitPermissionedMint.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)
[OperatorRegistry.sol#L114](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114)

## 3. != 0 is cheaper than > 0 especially in state variables
### Description
In cases where we used a variable from state assigned to a local variable it's the same gas save.

### Proof of concept
```solidity
contract TestA {
    uint256 _paramA;

    function Test () public returns (bool) {
        return _paramA > 0;
    }

}

contract TestB {
    uint256 _paramA;

    function Test () public returns (bool) {
        return _paramA != 0;
    }

}

contract TestC {
    uint256 _paramA;

    function Test () public returns (bool) {
        uint256 aux = _paramA;
        return aux > 0;
    }

}

contract TestD {
    uint256 _paramA;

    function Test () public returns (bool) {
        uint256 aux = _paramA;
        return aux != 0;
    }

}
```

- Transaction cost of TestA/C is 23491/23504 gas
- Transaction cost of TestB/D is 23494/23507 gas 
- After the test it's possible to save **3 gas per ocurrence** with this optimization.

### Lines in the code
[frxETHMinter.sol#L79](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79)
[frxETHMinter.sol#L126](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L126)

## 4. I = I + (-) X is cheaper in gas cost than I += (-=) X
### Description
This is especially with state variables. In the following example I'm trying to demostrate 
the save gas in a loop of 10 iterations.

### Proof of concept
```diff
contract TestA {
    uint256 i;
    function Test () public {
        
        for(;i<10;){
            i += 1;
        }
    }

}

contract TestB {
    uint256 i;
    function Test () public {
        
        for(;i<10;){
            i = i + 1;
        }
    }
}
```
- Transaction cost of TestA is 48793 gas
- Transaction cost of TestB is 48663 gas
- After the test it's possible to save **130 gas (13 gas per iteration/ocurrence)** with this optimization.

### Lines in the code
[frxETHMinter.sol#L97](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L97)
[frxETHMinter.sol#L168](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L168)
[xERC4626.sol#L67](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L67)
[xERC4626.sol#L72](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L72)

## 5. Require / Revert strings longer than 32 bytes cost extra gas
### Description
Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

### Lines in the code
[frxETHMinter.sol#L167](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L167)

## 6. Don't compare boolean expressions to boolean literals
### Description
if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)

### Lines in the code
[ERC20PermitPermissionedMint.sol#L46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L46)
[ERC20PermitPermissionedMint.sol#L68](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L68)
[ERC20PermitPermissionedMint.sol#L78](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L78)

## 7. Use custom errors rather than REVERT()/REQUIRE() strings to save deployment gas
### Description
Custom errors are available from solidity version 0.8.4. 
Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. 
Not defining the strings also save deployment gas.

### Lines in the code
[ERC20PermitPermissionedMint.sol#L41](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L41)
[ERC20PermitPermissionedMint.sol#L46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L46)
[ERC20PermitPermissionedMint.sol#L66](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L66)
[ERC20PermitPermissionedMint.sol#L68](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L68)
[ERC20PermitPermissionedMint.sol#L77](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L77)
[ERC20PermitPermissionedMint.sol#L78](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L78)
[ERC20PermitPermissionedMint.sol#L95](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L95)
[frxETHMinter.sol#L79](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79)
[frxETHMinter.sol#L87](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L87)
[frxETHMinter.sol#L88](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L88)
[frxETHMinter.sol#L122](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L122)
[frxETHMinter.sol#L126](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L126)
[frxETHMinter.sol#L140](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L140)
[frxETHMinter.sol#L160](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L160)
[frxETHMinter.sol#L167](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L167)
[frxETHMinter.sol#L171](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L171)
[frxETHMinter.sol#L193](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L193)
[frxETHMinter.sol#L200](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L200)
[OperatorRegistry.sol#L46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L46)
[OperatorRegistry.sol#L137](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L137)
[OperatorRegistry.sol#L182](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L182)
[OperatorRegistry.sol#L203](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L203)

## 8. Using private rather than public for constants, saves gas
### Description
If needed, the value can be read from the verified contract source code. 
Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, 
and not adding another entry to the method ID table.

### Lines in the code
[frxETHMinter.sol#L38](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L38)
[frxETHMinter.sol#L39](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L39)

## 9. Using bools for storage incurs overhead
### Description
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) 
when changing from 'false' to 'true', after having been 'true' in the past.

### Lines in the code
[ERC20PermitPermissionedMint.sol#L20](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L20)
[frxETHMinter.sol#L43](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L43)
[frxETHMinter.sol#L49](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L49)
[frxETHMinter.sol#L50](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L50)

## 10. Should use unchecked in arithmetic operations when it's not possible to overflow
### Description
This situation ocurrs especially in loops

Example,

```diff
- for (uint256 i = 0; i < arrayLength; ++i) {
+ for (uint256 i = 0; i < arrayLength;) {

    addValidator(validatorArray[i]);
+	unchecked {
+		++i;
+	}
}
```
### Lines in the code
[ERC20PermitPermissionedMint.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)
[frxETHMinter.sol#L129](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L129)
[OperatorRegistry.sol#L63](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63)
[OperatorRegistry.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84)
[OperatorRegistry.sol#L114](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114)

## 11. Initialize variables with default values are not needed
### Description
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address,...). 
Explicitly initializing it with its default value is an anti-pattern and wastes gas.

### Lines in the code
[ERC20PermitPermissionedMint.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84)
[frxETHMinter.sol#L63](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L63)
[frxETHMinter.sol#L64](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L64)
[frxETHMinter.sol#L94](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L94)
[frxETHMinter.sol#L129](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L129)
[OperatorRegistry.sol#L63](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L63)
[OperatorRegistry.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84)
[OperatorRegistry.sol#L114](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L114)

## 12. OperatorRegistry.removeValidator operate in an auxiliar variable instead of access to storage in every loop
### Description 
In the for-loop inside the function removeValidator it's accessing to the storage in every loop.
Consider to operate with an auxiliar variable and after to assign it to the storage variable and avoid
to write in the storage every loop.

```diff
	// Save the original validators
	Validator[] memory original_validators = validators;
+	Validator[] memory auxiliar_validators;
	// Clear the original validators list
	delete validators;

	// Fill the new validators array with all except the value to remove
	for (uint256 i = 0; i < original_validators.length; ++i) {
		if (i != remove_idx) {
-			validators.push(original_validators[i]);
+			auxiliar_validators.push(original_validators[i]);
		}
	}
+	validators = auxiliar_validators;
```
### Lines in the code
[OperatorRegistry.sol#L108-L118](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L108-L118)

## 13. ERC20PermitPermissionedMint.removeMinter operate in an auxiliar variable instead of access to storage in every loop
### Description
It's important to avoid the accessing to the storage if it's possible. 

```diff
+ address[] storage minter_array_aux = minters_array;
+ uint256 arrayLength = minter_array_aux.length;
-  for (uint i = 0; i < minters_array.length; i++){ 
+  for (uint i = 0; i < arrayLength; i++){ 
-     if (minters_array[i] == minter_address) {
+     if (minter_array_aux[i] == minter_address) {
          minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
          break;
      }
  }
```
### Lines in the code
[OperatorRegistry.sol#L84-L89](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84-L89)

## 14. Declare local variable before are going to be used
### Description
Declare local variables when are going to be used if it's possible to revert before it's used.
For example, variable lastRewardAmount_ in syncRewards. Move this variable to line 84 due to in line 82
there is an if that allow to revert the transaction. 

### Lines in the code
[xERC4626.sol#L79](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L79)

## 15. Using storage instead of memory for structs/arrays
### Description
When retrieving data from a memory location, assigning the data to a memory variable causes all fields 
of the struct/array to be read from memory, resulting in a Gcoldsload (2100 gas) for each field of the struct/array. 
When reading fields from new memory variables, they cause an extra MLOAD instead of a cheap stack read. 
Rather than declaring a variable with the memory keyword, it is much cheaper to declare a variable with the storage 
keyword and cache all fields that need to be read again in a stack variable, because the fields actually read will 
only result in a Gcoldsload. The only case where the entire struct/array is read into a memory variable is when 
the entire struct/array is returned by a function, passed to a function that needs memory, or when the array/struct 
is read from another store array/struct.

### Lines in the code
[OperatorRegistry.sol#L108](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L108)

## 16. Usage of uints/ints smaller than 32 Bytes (256 bits) incurs overhead
### Description
When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. 
This is because the EVM operates on 32 bytes at a time. 
Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce 
the size of the element from 32 bytes to the desired size.
Use a larger size then downcast where needed

### Lines in the code
[xERC4626.sol#L24](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L24)
[xERC4626.sol#L27](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L27)
[xERC4626.sol#L30](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L30)
[xERC4626.sol#L33](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L33)

## 17. Use a more recent version of solidity
### Description
 Use a solidity version of at least 0.8.2 to get compiler automatic inlining 
 Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
 Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
 Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value. https://github.com/ethereum/solidity/releases

### Lines in the code
[frxETH.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETH.sol#L2)
[sfrxETH.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L2)
[ERC20PermitPermissionedMint.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L2)
[frxETHMinter.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L2)
[OperatorRegistry.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L2)
[xERC4626.sol#L4](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L4)

