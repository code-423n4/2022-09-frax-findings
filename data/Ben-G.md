## Storage variables can be packed into a single slot in frxETHMinter.sol

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L41-L50 \
**withholdRatio** cannot exceed 1e6 = 1,000,000. A uint24 can be used as this has a maximum value of 16,777,215. \
**currentWithheldETH** can be changed to uint216, this is more than enough to store an amount of ETH, e.g Uniswap V2 uses a uint112 to store its reserve amounts.\
With two booleans which use a byte each, the total storage is 24 + 216 + 8 + 8 = 256. \
Change to:

```
mapping(bytes => bool) public activeValidators; // Tracks validators (via their pubkeys) that already have 32 ETH in them

uint24 public withholdRatio; // What we keep and don't deposit whenever someone submit()'s ETH
uint216 public currentWithheldETH; // Needed for internal tracking

IDepositContract public immutable depositContract; // ETH 2.0 deposit contract
frxETH public immutable frxETHToken;
IsfrxETH public immutable sfrxETHToken;

bool public submitPaused;
bool public depositEtherPaused;
```

---

## Cache storage variables that are read multiple times in a function

saves 97 gas per access after variable is cached. Incurs a gas cost of 3 to write variable to memory.

### findings

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L95-L96 \
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167-L168

---
Changes: 29586 average gas used in submit function, down from 33464.