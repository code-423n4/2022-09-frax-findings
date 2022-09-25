Table Of Content
================

* [GAS REPORT](#gas-report)
        * [Don't cache msg.sender](#dont-cache-msgsender)
        * [Caching array size](#caching-array-size)

# GAS REPORT

## Don't cache msg.sender
reading msg.sender is 2 gas units which is less than a read of a local var + the unnecessary store operation.

### Code Instances:
- [frxETH_sfrxETH_combo.t.sol#L999](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L999)
- [frxETH_sfrxETH_combo.t.sol#L742](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETH_sfrxETH_combo.t.sol#L742)
- [frxETHMinter.t.sol#L251](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETHMinter.t.sol#L251)
- [frxETHMinter.t.sol#L253](https://github.com/code-423n4/2022-09-frax/tree/main/test/frxETHMinter.t.sol#L253)

## Caching array size
In the following for loops consider caching the array size instead of loading it every iteration.

For instance, [OperatorRegistry.sol#L113](https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L113)

--