## Risk Rating: Low

## Title
Missing Zero-Address Checks

Zero-address checks are a common best practice for input validation of critical address parameters, particularly in constructors and setters. Accidental use of zero-addresses may result in unexpected exceptions, failures or require the redeployment of contracts.

To implement a zero-address check, use a pattern like the following:

```
error ZeroAddress(); // This would go with other error definitions
require(to != address(0), ZeroAddress()); // inside function where 'to' address is used as parameter
```

The following instances were identified where zero-address checks should be added.

In frxETHMinter.sol:

* [moveWithheldEth()](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L165-L174): check address payable to for zero address value.
* [_submit()](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L85-L101): check address recipient for zero address value. This covers submitAndDeposit(), submitAndGive(), and submit().
