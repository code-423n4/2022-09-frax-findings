# 	NON-CRITICAL

## 1. Floading Pragma version


In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

Proof of Concept
https://swcregistry.io/docs/SWC-103

```
All contracts
```

## 2. Use a more recent version of Solidity


- Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions
- Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>) Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)

```
All contracts
```

