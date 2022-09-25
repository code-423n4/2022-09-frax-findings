# 1. rewardsCycleEnd Calculation

Lines of code:

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L40

and 

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L89


Pseudocode:
```
rewardsCycleEnd = (timestamp / rewardsCycleLength) * rewardsCycleLength;
```

Using a DIV opcode and a MUL opcode with gas cost (5+5) .

Optimization:
```
rewardsCycleEnd = timestamp - (timestamp % rewardsCycleLength)
```

gas cost (3+5) 

# 2. frxETHMinter constructor with no need for zero initialization

Lines of code:

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L63-L64

Delete the two lines.
```
        withholdRatio = 0; 
        currentWithheldETH = 0;
```
It will save about 4660 gas during the contract deployment.

# 3. Dynamic array elements deletion optimization

Lines of code:

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L106-L119

The solidity `delete` is not an evm opcode. It's essentially a series of write operation with zero. It's very expensive especially when dealing with dynamically nested structures.

Optimization:

```
If you don't consider changing the overall algorithm (such as adding the soft delete flag bit), 

You can iterate the array from the index to be deleted, overwriting the previous element with the latter element, and finally POP the last element. This will reduce GAS usage by at least about 2/3.
```

# 4. change some functions modifier from public to external 

Lines of code:

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L82

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L93

These functions should be external to save gas.