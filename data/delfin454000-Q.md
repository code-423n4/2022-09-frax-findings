### Typos
___
[ERC20PermitPermissionedMint.sol: L78](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L78)
```solidity
        require(minters[minter_address] == true, "Address nonexistant");
```
Change `nonexistant` to `nonexistent`
___
[frxETHMinter.sol: L78-81](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L78-L81)
```solidity
        uint256 sfrxeth_recieved = sfrxETHToken.deposit(msg.value, recipient);
        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

        return sfrxeth_recieved;
```
Change `sfrxeth_recieved` to `sfrxeth_received` in each case
___
[frxETHMinter.sol: L176](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L176)
```solidity
    /// @notice Toggle allowing submites
```
Change `submites` to `submits`
___
[xERC4626.sol: L43](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L43)
```solidity
    /// @notice Compute the amount of tokens available to share holders.
```
Change `share holders` to `shareholders`
___
___


### Long single line comments 
In theory, comments over 79 characters should wrap using multi-line comment syntax. Even if somewhat longer comments are acceptable and a scroll bar is provided, there are cases where very long comments interfere with readability. Below are five instances of extra-long comments whose readability could be improved through wrapping, as shown:
___
[sfrxETH.sol: L31-39](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L31-L39)
```solidity
/** @dev Exchange rate between frxETH and sfrxETH floats, you can convert your sfrxETH for more frxETH over time.
    Exchange rate increases as the frax msig mints new frxETH corresponding to the staking yield and drops it into the vault (sfrxETH contract).
    There is a short time period, “cycles” which the exchange rate increases linearly over. This is to prevent gaming the exchange rate (MEV).
    The cycles are constant length, but calling syncRewards slightly into a would-be cycle keeps the same would-be endpoint (so cycle ends are every X seconds).
    Someone must call syncRewards, which queues any new frxETH in the contract to be added to the redeemable amount.
    sfrxETH adheres to ERC-4626 vault specs 
    Mint vs Deposit
    mint() - deposit targeting a specific number of sfrxETH out
    deposit() - deposit knowing a specific number of frxETH in */
```
Suggestion:
```solidity
/** 
 @dev Exchange rate between frxETH and sfrxETH floats, you can convert
      your sfrxETH for more frxETH over time.
     Exchange rate increases as the frax msig mints new frxETH corresponding 
         to the staking yield and drops it into the vault (sfrxETH contract).
     There is a short time period, “cycles” which the exchange rate increases 
         linearly over — this is to prevent gaming the exchange rate (MEV).
     The cycles are constant length, but calling syncRewards slightly into a would-be cycle
         keeps the same would-be endpoint (so cycle ends are every X seconds).
     Someone must call syncRewards, which queues any new frxETH 
         in the contract to be added to the redeemable amount.
     sfrxETH adheres to ERC-4626 vault specs 
     Mint vs Deposit:
         mint() - deposit targeting a specific number of sfrxETH out
         deposit() - deposit knowing a specific number of frxETH in
*/
```
___
[frxETHMinter.sol: L33-36](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L33-L36)
```solidity
/// @notice Accepts user-supplied ETH and converts it to frxETH (submit()), and also optionally inline stakes it for sfrxETH (submitAndDeposit())
/** @dev Has permission to mint frxETH. 
    Once +32 ETH has accumulated, adds it to a validator, which then deposits it for ETH 2.0 staking (depositEther())
    Withhold ratio refers to what percentage of ETH this contract keeps whenever a user makes a deposit. 0% is kept initially */
```
Suggestion:
```solidity
/// @notice Accepts user-supplied ETH and converts it to frxETH (submit()), 
     and also optionally inline stakes it for sfrxETH (submitAndDeposit()).
/** 
 @dev Has permission to mint frxETH. 
     Once +32 ETH has accumulated, adds it to a validator, which then 
         deposits it for ETH 2.0 staking (depositEther()).
     Withhold ratio refers to what percentage of ETH this contract keeps 
         whenever a user makes a deposit. 0% is kept initially.
*/
```
___
[frxETHMinter.sol: L138-139](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L138-L139)
```solidity
            // Make sure the validator hasn't been deposited into already, to prevent stranding an extra 32 eth
            // until withdrawals are allowed
```
Suggestion:
```solidity
            // Make sure the validator hasn't been deposited into already, to prevent  
            //    stranding an extra 32 eth until withdrawals are allowed.
```
___
[xERC4626.sol: L11-19](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L11-L19)
```solidity
/** 
 @title  An xERC4626 Single Staking Contract
 @notice This contract allows users to autocompound rewards denominated in an underlying reward token. 
         It is fully compatible with [ERC4626](https://eips.ethereum.org/EIPS/eip-4626) allowing for DeFi composability.
         It maintains balances using internal accounting to prevent instantaneous changes in the exchange rate.
         NOTE: an exception is at contract creation, when a reward cycle begins before the first deposit. After the first deposit, exchange rate updates smoothly.

         Operates on "cycles" which distribute the rewards surplus over the internal balance to users linearly over the remainder of the cycle window.
*/
```
Suggestion:
```solidity
/** 
 @title  An xERC4626 Single Staking Contract
 @notice This contract allows users to autocompound rewards denominated in an underlying reward token. 
     It is fully compatible with [ERC4626](https://eips.ethereum.org/EIPS/eip-4626),
         allowing for DeFi composability.
     It maintains balances using internal accounting to prevent instantaneous 
         changes in the exchange rate.
     NOTE: an exception is at contract creation, when a reward cycle begins before
         the first deposit. After the first deposit, exchange rate updates smoothly.
     Operates on "cycles" which distribute the rewards surplus over the 
         internal balance to users linearly over the remainder of the cycle window.
*/
```
___
[xERC4626.sol: L77](https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L77)
```solidity
    /// All surplus `asset` balance of the contract over the internal balance becomes queued for the next cycle.
```
Suggestion:
```solidity
    /// All surplus `asset` balance of the contract over the internal balance 
    ///    becomes queued for the next cycle. 
```
___
___

### Update sensitive terms in both comments and code
Terms incorporating "black," "white," "slave" or "master" are potentially problematic. Substituting more neutral terminology is becoming [common practice](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/).
___
[ERC20PermitPermissionedMint.sol: L64](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L64)
```solidity
    // Adds whitelisted minters 
```
Suggestion: Change `whitelisted` to `allowlisted` or `allowed`
___
___

### Missing `@param` statements
`@param` statements, which document parameters, are missing for most of the `functions` and `constructors` in the `Frax Ether` in-scope contracts (a `@param` statement is given for one function parameter: [frxETHMinter.sol: L157](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L157)). Below are examples of missing `@param` statements, however there are dozens more that need to be addressed.   

`function` example:

[sfrxETH.sol: L47-51](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L47-L51)
```solidity
    /// @notice Syncs rewards if applicable beforehand. Noop if otherwise 
    function beforeWithdraw(uint256 assets, uint256 shares) internal override {
        super.beforeWithdraw(assets, shares); // call xERC4626's beforeWithdraw first
        if (block.timestamp >= rewardsCycleEnd) { syncRewards(); } 
    }
```
`@param` statements missing for `assets` and `shares` 

`constructor` example:

[sfrxETH.sol: L41-44](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L41-L44)
```solidity
    /* ========== CONSTRUCTOR ========== */
    constructor(ERC20 _underlying, uint32 _rewardsCycleLength)
        ERC4626(_underlying, "Staked Frax Ether", "sfrxETH")
        xERC4626(_rewardsCycleLength)
```
`@param` statements missing for `_underlying` and `_rewardsCycleLength` 
___
___

### Inconsistent `require` string punctuation
While `require` messages may be punctuated as either "message" or 'message', the treatment should be consistent within a contest. Below is the only message surrounded by ' ' instead of " ":

[frxETHMinter.sol: L79](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L79)
```solidity
        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');
```
Suggestion: Change message to `"No sfrxETH was returned"`
___
___

### Incorrect `require` statement syntax

[frxETHMinter.sol: L160](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L160)
```solidity
        require (newRatio <= RATIO_PRECISION, "Ratio cannot surpass 100%");
```
Recommendation: Remove unneeded space between `require` and `(`
___
___
