# Quality Assurance Report

## Issues 


|   |      Issue     |  Instances |
|----------|-------------|:------:|
| 1 |  abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccack256() | 1 |
| 2  |    Minor oversight - Internal Function is named as if it was public Function is `internal` not prefixed with `_`    |   4 |


### 1. abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccack256()
Uses abi.encode and then inconsitently uses abi.encodePacked below
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456) , but abi.encode(0x123,0x456) => 0x0...1230...456 ).
### POC 
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/Utils/SigUtils.sol#L32
```solidity
    function getTypedDataHash(Permit memory _permit)
        public
        view
        returns (bytes32)
    {
        return
            keccak256(
                abi.encodePacked(
                    "\x19\x01",
                    DOMAIN_SEPARATOR,
                    getStructHash(_permit)
                )
            );
    }
} 
```
### Mitigation 
 “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.



### 2. Minor oversight - Internal Function is named as if it was public Function is `internal` not prefixed with `_`

Function getNextValidator() is `internal` but not prefixed with `_`
### POC 

```solidity
    function getNextValidator()
        internal
        returns (
            bytes memory pubKey,
            bytes memory withdrawalCredentials,
            bytes memory signature,
            bytes32 depositDataRoot
        )
    {
```
### Instances
```
2022-09-frax/src/sfrxETH.sol Line #48
2022-09-frax/src/Utils/SigUtils.sol Line #25
2022-09-frax/src/OperatorRegistry.sol Line #126
2022-09-frax/src/DepositContract.sol Line #165
```
Mitigation: Naming convetion as _getNextValidator()


