### [L-01] ```approve()``` and ```safeApprove()``` should be replaced with ```safeIncreaseAllowance()``` / ```safeDecreaseAllowance()```


#### Impact
```approve()``` & ```safeApprove()``` are deprecated and subject to a known front-running attack. Consider using  ```safeIncreaseAllowance()``` & ```safeDecreaseAllowance()``` instead.


#### Findings:
```
src/frxETHMinter.sol:L75                 frxETHToken.approve(address(sfrxETHToken), msg.value);

```
### [L-02] Avoid floating pragmas for non-library contracts.


#### Impact
While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
src/frxETH.sol:L2     pragma solidity ^0.8.0;

src/frxETHMinter.sol:L2     pragma solidity ^0.8.0;

src/sfrxETH.sol:L2     pragma solidity ^0.8.0;

src/OperatorRegistry.sol:L2     pragma solidity ^0.8.0;

src/ERC20/ERC20PermitPermissionedMint.sol:L2     pragma solidity ^0.8.0;

```
