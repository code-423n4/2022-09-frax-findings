1.
Title: Gas savings for using solidity 0.8.10

Proof of Concept:
All contract in scope

Recommended Mitigation Steps:
Consider to upgrade pragma to at least 0.8.10.

Solidity 0.8.10 has a useful change which reduced gas costs of external calls
Reference: [here](https://blog.soliditylang.org/2021/11/09/solidity-0.8.10-release-announcement/)
______________________________________________________________________

2.
Title: Boolean comparison

Proof of Concept:
[ERC20PermitPermissionedMint.sol#L46](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L46)
[ERC20PermitPermissionedMint.sol#L68](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L68)
[ERC20PermitPermissionedMint.sol#L78](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78)

Recommended Mitigation Steps:
I suggest using `require(minters[minter_address], "Address nonexistant");` or `require(!minters[minter_address], "Address already exists");` instead of `require(minters[minter_address] == true, "Address nonexistant");` or `require(minters[minter_address] == false, "Address already exists");`
________________________________________________________________________

3.
Title: Reduce the size of error messages (Long revert Strings)

Impact:
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

Proof of Concept:
[frxETHMinter.sol#L167](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167)

Recommended Mitigation Steps:
Consider shortening the revert strings to fit in 32 bytes
________________________________________________________________________

4.
Title: Custom errors from Solidity 0.8.4 are cheaper than revert strings

Impact:
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information

Custom errors are defined using the error statement
reference: https://blog.soliditylang.org/2021/04/21/custom-errors/

Proof of Concept:
[ERC20PermitPermissionedMint.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol) (various line)
[frxETHMinter.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol) (various line)

Recommended Mitigation Steps:
Replace require statements with custom errors.
________________________________________________________________________

5.
Title: Using `!=` in `require` statement is more gas efficient

Proof of Concept:
[frxETHMinter.sol#L79](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79)
[frxETHMinter.sol#L126](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126)

Recommended Mitigation Steps:
Change `> 0` to `!= 0`
________________________________________________________________________

6.
Title: Default value initialization

Impact:
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

Proof of Concept:
[ERC20PermitPermissionedMint.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84)
[frxETHMinter.sol#L94](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94)
[frxETHMinter.sol#L129](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129)
[OperatorRegistry.sol#L63](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63)
[OperatorRegistry.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84)
[OperatorRegistry.sol#L114](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114)

Recommended Mitigation Steps:
Remove explicit initialization for default values.
________________________________________________________________________

7.
Title: Consider make constant as private to save gas

Proof of Concept:
[frxETHMinter.sol#L38-L39](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L38-L39)

Recommended Mitigation Steps:
I suggest changing the visibility from `public` to `internal` or `private`
________________________________________________________________________

8.
Title: Unchecking arithmetics operations that can't underflow/overflow

Proof of Concept:
[frxETHMinter.sol#L168](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L168) Should be unchecked due to L#167

Recommended Mitigation Steps:
Use `unchecked`
________________________________________________________________________

9.
Title: Set as `immutable` can save gas

Proof of Concept:
[OperatorRegistry.sol#L38](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L38)

Recommended Mitigation Steps:
can be set as immutable, which already set once in the constructor
________________________________________________________________________

10.
Title: Using unchecked and prefix increment is more effective for gas saving:

Proof of Concept:
[OperatorRegistry.sol#L63](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63)
[OperatorRegistry.sol#L84](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84)
[OperatorRegistry.sol#L114](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114)

Recommended Mitigation Steps:
Change to:

```
    for (uint256 i = 0; i < arrayLength;) {
    // ...
    unchecked { ++i; }
    }
```
________________________________________________________________________

11.
Title: Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

Impact: 
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Reference: [Here](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)

Proof of Concept:
[xERC4626.sol#L24-L30](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L24-L30)
________________________________________________________________________