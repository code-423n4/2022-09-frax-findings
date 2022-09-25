## 01  NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

This issue exists on all In-scope contracts 

### Recommended Mitigation Steps

Lock the pragma version

------------

## 02  USE A MORE RECENT VERSION OF SOLIDITY

When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives security fixes. Furthermore, breaking changes as well as new features are introduced regularly.

### Proof of Concept

This issue exists on all In-scope contracts 

### Recommended Mitigation Steps

Update to the latest released version of Solidity

-------------------

## 03  NATSPEC IS MISSING/INCOMPLETE

### Proof of Concept

There are 35 instances of this issue throughout the In-scope contracts

Missing: `@param`, `@return`

--------------

## 04  EVENT IS MISSING INDEXED FIELDS

### Proof of Concept

There are  instances of this issue

Each `event` should use three `indexed` fields if there are three or more fields

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
File: src/frxETHMinter.sol

205: event EmergencyEtherRecovered(uint256 amount);
206: event EmergencyERC20Recovered(address tokenAddress, uint256 tokenAmount);
207: event ETHSubmitted(address indexed sender, address indexed recipient, uint256 sent_amount, uint256 withheld_amt);
208: event DepositEtherPaused(bool new_status);
209: event DepositSent(bytes indexed pubKey, bytes withdrawalCredential);
210: event SubmitPaused(bool new_status);
211: event WithheldETHMoved(address indexed to, uint256 amount);
212: event WithholdRatioSet(uint256 newRatio);
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

```
File: src/ERC20/ERC20PermitPermissionedMint.sol  

102: event TokenMinterBurned(address indexed from, address indexed to, uint256 amount);
103: event TokenMinterMinted(address indexed from, address indexed to, uint256 amount);
104: event MinterAdded(address minter_address);
105: event MinterRemoved(address minter_address);
106: event TimelockChanged(address timelock_address);
```

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol

```
File: src/OperatorRegistry.sol

208: event TimelockChanged(address timelock_address);
209: event WithdrawalCredentialSet(bytes _withdrawalCredential);
210: event ValidatorAdded(bytes pubKey, bytes withdrawalCredential);
211: event ValidatorArrayCleared();
212: event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);
213: event ValidatorsPopped(uint256 times);
214: event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);
215: event KeysCleared();
```

------------

## 05 USE OF BLOCK.TIMESTAMP

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### Proof of concept

https://github.com/code-423n4/2022-09-frax/blob/main/src/sfrxETH.sol

```
File: src/sfrxETH.sol 

50: if (block.timestamp >= rewardsCycleEnd) { syncRewards(); } 
```

### Recommended Mitigation Steps

Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.

----------

## 06 USE SAFETRANSFER FUNCTION

Token like [USDT](https://etherscan.io/address/0xdac17f958d2ee523a2206206994597c13d831ec7#contracts) known for using non-standard ERC20. ([Missing return boolean on transfer](https://forum.openzeppelin.com/t/can-not-call-the-function-approve-of-the-usdt-contract/2130/4)).

Contract function func() will always revert when try to transfer this kind of tokens.

### Proof of concept

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
File: src/frxETHMinter.sol

200: require(IERC20(tokenAddress).transfer(owner, tokenAmount), "recoverERC20: Transfer failed");
```

----------

## 07 USE SAFEAPPROVE()

### Proof of concept

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol

```
File: src/frxETHMinter.sol

75: frxETHToken.approve(address(sfrxETHToken), msg.value);
```
---------
