### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01] | Use a more recent version of solidity | 6 |
| [N-02] | Function writing that does not comply with the `Solidity Style Guide` | 4 |
| [N-03] | ```0``` address check | 3 |
| [N-04] | Missing NatSpec  comment | 5 |
| [N-05] | There is no need to cast a variable that is already an address, such as address(x) | 1 | 
| [N-06] | Add to `indexed` parameter for countable `Events` | 3 |
| [N-07] | Include return parameters in NatSpec comments | All contracts |

Total 7 issues


### Low Risk Issues List
| Number |Issues Details|
|:--:|:-------|
| [L-01] | Floating pragma |
| [L-02] | Add to blacklist function |
| [L-03] | Define the ETH 2.0 staking address directly |
| [L-04] | Add updatable ETH 2.0 staking address function |

Total 4 issues

## [N-01] Use a more recent version of solidity

**Context:**
[frxETH.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol))
[sfrxETH.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol))
[ERC20PermitPermissionedMint.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol))
[frxETHMinter.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol))
[OperatorRegistry.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol))
[xERC4626.sol#L4](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L4)

**Description:**
Old version of Solidity is used (```^0.8.0```), newer version should be used.

## [N-02] Function writing that does not comply with the `Solidity Style Guide`

**Context:**
[frxETHMinter.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol)
[OperatorRegistry.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol)
[sfrxETH.sol](https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol)
[xERC4626.sol](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol)

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.15/style-guide.html

Functions should be grouped according to their visibility and ordered:

- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

## [N-03] ```0``` address check

**Context:**
[ERC20PermitPermissionedMint.sol#L26](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L26)
[frxETHMinter.sol#L61-L62](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L61-L62)
[OperatorRegistry.sol#L41](https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L41)

**Description:**
Also check of the address to protect the code from 0x0 address  problem just in case. This is best practice or instead of suggesting that they verify address != 0x0, you could add some good natspec comments explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.

**Recommendation:**
like this ;
```if (_treasury == address(0)) revert ADDRESS_ZERO();```


## [N-04] Missing NatSpec comment

**Context:**
(lib\ERC4626\src\xERC4626.sol)
(src\ERC20\ERC20PermitPermissionedMint.sol)
(src\frxETH.sol)
(src\frxETHMinter.sol)
(src\OperatorRegistry.sol)
(src\sfrxETH.sol)

**Description:**
Solidity contracts can use a special form of comments to provide rich documentation for functions, return variables and more. This special form is named the Ethereum Natural Language Specification Format (NatSpec).
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

NatSpec comments are very few on all contracts of the audited Project.

**Recommendation:**
NatSpec comments should be added as below.
```js
/// @title A simulator for trees
/// @author Larry A. Gardner
/// @notice You can use this contract for only the most basic simulation
/// @dev All function calls are currently implemented without side effects
/// @custom:experimental This is an experimental contract.
contract Tree {
    /// @notice Calculate tree age in years, rounded up, for live trees
    /// @dev The Alexandr N. Tetearing algorithm could increase precision
    /// @param rings The number of rings from dendrochronological sample
    /// @return Age in years, rounded up for partial years
    function age(uint256 rings) external virtual pure returns (uint256) {
        return rings + 1;
    }
```

## [N-05] There is no need to cast a variable that is already an address, such as address(x)

**Context:**
[frxETHMinter.sol#L192](https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L192)

**Description:**
There is no need to cast a variable that is already an address, such as address(auctioneer_), auctioneer also address.

**Recommendation:**
Use directly variable.

```js
//current code
 function recoverEther(uint256 amount) external onlyByOwnGov {
        (bool success,) = address(owner).call{ value: amount }("");
        require(success, "Invalid transfer");

        emit EmergencyEtherRecovered(amount);
    }


//recommendation
 function recoverEther(uint256 amount) external onlyByOwnGov {
        (bool success,) = owner.call{ value: amount }("");
        require(success, "Invalid transfer");

        emit EmergencyEtherRecovered(amount);
    }
```

## [N-06] Add to `indexed` parameter for countable `Events`

**Context:**
[ERC20PermitPermissionedMint.sol#L104](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L104)
[ERC20PermitPermissionedMint.sol#L105](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L105)
[ERC20PermitPermissionedMint.sol#L106](https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L106)

**Recommendation:**
Add to indexed parameter for countable Events.


## [N-07] Include return parameters in NatSpec comments

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
Include return parameters in NatSpec comments

_Recommendation  Code Style: (from Uniswap3)_
```js
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```

## [L-01]  Floating Pragma

**Description:**
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
https://swcregistry.io/docs/SWC-103

Floating Pragma List: 
```js
src/frxETH.sol:
  2: pragma solidity ^0.8.0;

src/frxETHMinter.sol:
  2: pragma solidity ^0.8.0;

src/OperatorRegistry.sol:
  2: pragma solidity ^0.8.0;

src/sfrxETH.sol:
  2: pragma solidity ^0.8.0;

src/ERC20/ERC20PermitPermissionedMint.sol:
  2: pragma solidity ^0.8.0;

src/xERC4626.sol:
  2: pragma solidity ^0.8.0;
```

**Recommendation:**
Lock the pragma version and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

## [L-02] Add to Blacklist function

**Description:**
Cryptocurrency mixing service, Tornado Cash, has been blacklisted in the OFAC.
A lot of blockchain companies, token projects, NFT Projects have ```blacklisted``` all Ethereum addresses owned by Tornado Cash listed in the US Treasury Department's sanction against the protocol.
https://home.treasury.gov/policy-issues/financial-sanctions/recent-actions/20220808
In addition, these platforms even ban accounts that have received ETH on their account with Tornadocash.


**Recommendation:**
Add to Blacklist function and modifier.

## [L-03] Define the ETH 2.0 staking address directly

**Description:**
The ETH 2.0 Stake address definition is important and should not be wrong, whereas the address is clear. The official address should be written directly, this will prevent possible incorrect identification.

The stake contract definition is very critical and should not be defined like a regular address definition.

https://etherscan.io/address/0x00000000219ab540356cBB839Cbe05303d7705Fa
https://ethereum.org/en/staking/deposit-contract/
https://cointelegraph.com/news/did-the-eth-2-0-deposit-contract-just-launch
https://consensys.net/blog/news/eth2-phase-0-deposit-contract-address/

The announcement of the Ethereum Foundation on the subject is remarkable:
```We expect there to be a lot of fake addresses and scams out there. To be safe, check the staking contract address you're using against the address on this page. We recommend checking it with other trustworthy sources too.```

## [L-04] Add updatable ETH 2.0 staking address function

**Description:**
The ETH 2.0 staking address may change in the future, so add a function that ensures that this address can be changed safely.