- [Low](#low)
    - [**1. Not compatible with some tokens like USDT**](#1-not-compatible-with-some-tokens-like-usdt)
    - [**2. Mixing and Outdated compiler**](#2-mixing-and-outdated-compiler)
    - [**3. Lack of checks**](#3-lack-of-checks)
    - [**4. Minters can burn any amount of tokens from an arbitrary address**](#4-minters-can-burn-any-amount-of-tokens-from-an-arbitrary-address)
    - [**5. Minters can mint any amount of tokens to an arbitrary address**](#5-minters-can-mint-any-amount-of-tokens-to-an-arbitrary-address)
    - [**6. Useless time lock**](#6-useless-time-lock)
- [Non critical](#non-critical)
    - [**7. Gap in minters_array**](#7-gap-in-minters_array)

# Low

## **1. Not compatible with some tokens like USDT**

`frxETHMinter` contract is incompatible with major tokens such as USDT, that it's because when performing an 'approve', it is required to do an `approve(0)` first, to avoid some front-running token protections.

**Affected source code:**

- [frxETHMinter.sol:75](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L75)

## **2. Mixing and Outdated compiler**

The pragma version used are:

```
pragma solidity ^0.8.0;
pragma solidity >=0.8.0;
```

*Note that mixing pragma is not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.*

The minimum required version must be [0.8.16](https://github.com/ethereum/solidity/releases/tag/v0.8.16); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.3](https://blog.soliditylang.org/2021/03/23/solidity-0.8.3-release-announcement/):

- Optimizer: Fix bug on incorrect caching of Keccak-256 hashes.

[0.8.4](https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/):

- ABI Decoder V2: For two-dimensional arrays and specially crafted data in memory, the result of abi.decode can depend on data elsewhere in memory. Calldata decoding is not affected.

[0.8.9](https://blog.soliditylang.org/2021/09/29/solidity-0.8.9-release-announcement/):

- Immutables: Properly perform sign extension on signed immutables.
- User Defined Value Type: Fix storage layout of user defined value types for underlying types shorter than 32 bytes.

[0.8.13](https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/):
- Code Generator: Correctly encode literals used in `abi.encodeCall` in place of fixed bytes arguments.

[0.8.14](https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/):

- ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against `calldatasize()` in all cases.
- Override Checker: Allow changing data location for parameters only when overriding external functions.

[0.8.15](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/)

- Code Generation: Avoid writing dirty bytes to storage when copying `bytes` arrays.
- Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

[0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)

- Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

Apart from these, there are several minor bug fixes and improvements.

## **3. Lack of checks**

The following methods have a lack of checks if the received argument is an address, it's good practice in order to reduce human error to check that the address specified in the constructor or initialize is different than `address(0)`.

**Affected source code for `address(0)`:**

- In [ERC20PermitPermissionedMint.sol:34](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L34) is not checked but it was in [setTimelock](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L95)
- In [OperatorRegistry.sol:41](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L41) is not checked but it was in [setTimelock](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L203)
- [frxETHMinter.sol:53-55](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L53-L55)

## **4. `Minters` can burn any amount of tokens from an arbitrary address**

`miters` role can burn any amount of tokens from an arbitrary address.

This is needless and poses a severe risk of centralization. A malicious or compromised owner can take advantage of this.

**Affected source code:**

- [ERC20PermitPermissionedMint.sol:54](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L54)

## **5. `Minters` can mint any amount of tokens to an arbitrary address**

The `minters` can mint an arbitrary amount of tokens to an arbitrary address.

This is needless and poses a severe risk of centralization. A malicious or compromised owner can take advantage of this.

**Affected source code:**

- [ERC20PermitPermissionedMint.sol:54](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L54)

## **6. Useless time lock**

The advantages provided by the timelock in terms of decentralization disappear by having an owner who can manage the contract in the same way, so the contract is centralized to the actions of the owner

**Affected source code:**

- [ERC20PermitPermissionedMint.sol:41](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L41)
- [OperatorRegistry.sol:46](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L46)

---

# Non critical

## **7. Gap in `minters_array`**

When an entry from the `ERC20PermitPermissionedMint.minters_array` array is deleted, the last element of the array is not placed in the deleted position and the size of the array is reduced, so there is a gap that is not necessary and can cause problems in dApps.

**Affected source code:**

- [ERC20PermitPermissionedMint.sol:86](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L86)
