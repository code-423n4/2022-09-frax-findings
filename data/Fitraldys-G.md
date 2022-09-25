1. Useage of uint / int smaller than 32 bytes incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

POC :

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L64
https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L80

2. Cheaper foor loops

You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting:

```
for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
  // Do the thing
  // Unchecked pre-increment is cheapest
  unchecked { ++i; }
}     
```

POC :

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84

3. Consider making some constants as non-public to save gas

Reducing from `public` to `private` or `internal` can save gas when a constant isn’t used outside of its contract. I suggest changing the visibility from `public` to `internal` or `private`.

POC :

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L38
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L39

4. check `msg.value` first for require can save gas

Reading msg.value costs 2 gas, while reading from memory costs 3, this will save 1 gas with no downside

POC :

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L88

5. Declaring `uint256 i` can save gas

Declaring `uint256 i = 0;` means doing an MSTORE of the value 0 Instead you could just declare `uint256 i` to declare the variable without assigning it’s default value, saving 3 gas per declaration

POC :

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L63
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L114

6. != is more efficient than > in require

`!= 0` cost less gas compare to `> 0` for unsigned integers in require statement with optimizer enable save 6 gas

because for uint the minimum value would be 0 and never a negative value, that mean check `> 0` is essentially checking that the value is not 0.

resource : https://twitter.com/gzeon/status/1485428085885640706

i suggest changing `> 0` to `!= 0`

POC :

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79