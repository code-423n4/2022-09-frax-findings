## Low Risk ##

### [L-01]: Loops may exceed gas limit 

**Context:**
 
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
+ https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L129

```
 for (uint256 i = 0; i < length; i++) {
            _createClaimUnchecked(_recipients[i], _startTimestamps[i], _endTimestamps[i], _cliffReleaseTimestamps[i], _releaseIntervalsSecs[i], _linearVestAmounts[i], _cliffAmounts[i]);
        }
```

https://github.com/code-423n4/2022-09-vtvl/blob/main/contracts/VTVLVesting.sol#L353

**Description:**

Either explicitly or just due to normal operation, the number of iterations in a loop can grow beyond the block gas limit, which can cause the complete contract to be stalled at a certain point.


## Non-Critical Issues ##

### [N-01]: Floating Pragma 

**Context:**
 
 + https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L2
 + https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L2
 + https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L2
 + https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L2
 + https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L4
 + https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#L2 

**Recommendation:**

https://swcregistry.io/docs/SWC-103

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### [N-02]: Public function can be external 
**Context:** 

+ [sfrxETH.pricePerShare](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L54)
+ [ERC20PermitPermissionedMint.minter_burn_from](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L53)
+ [ERC20PermitPermissionedMint.minter_mint](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L59)
+ [ERC20PermitPermissionedMint.addMinter](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L65)
+ [ERC20PermitPermissionedMint.removeMinter](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L76)
+ [ERC20PermitPermissionedMint.setTimelock](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L94)
+ [OperatorRegistry.popValidators](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L82)
+ [OperatorRegistry.removeValidator](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L93)
+ [xERC4626.totalAssets](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L45)
+ [xERC4626.syncRewards](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L78)

**Description:**

Public functions can be declared external if they are not called by the contract.

**Recommendation:**

Declare these functions as external instead of public.


### [N-03]: NatSpec is incomplete 

1. https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol (no '@param' and '@return' tags where they are needed) 
2. https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol (NatSpec is missing)
3. https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol (no '@param' tags where they are needed) 
4. https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol (no '@param' and '@return' tags where they are needed) 
5. https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol (no '@param' and '@return' tags where they are needed)