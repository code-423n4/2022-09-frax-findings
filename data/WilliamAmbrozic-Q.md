## QA (low / non-critical)
22/09/2022 16:07
[Frax](https://github.com/code-423n4/2022-09-frax)
* * *

### Floating Pragma
- Contracts in scope do not lock the pragma.

Example: [Line 4](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L2) of sfrxETH.sol

[reference](https://swcregistry.io/docs/SWC-103)