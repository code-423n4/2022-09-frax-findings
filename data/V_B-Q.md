### 1. usage of non-ERC20 standard function "permit"

It is unsafe to rely on logic that underlying token have `permit` function implemented. It is considered good practice to use ERC20 standard [modification](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/5e8e8bb9f0c6c5e1a8d3a38bcedd7861906f692b/contracts/token/ERC20/utils/SafeERC20.sol#L83) that contains logic of safe calling of `permit` function. This is so, because of [possibility of existence of phantom function](https://media.dedaub.com/phantom-functions-and-the-billion-dollar-no-op-c56f062ae49f) that will be used when contract calls `permit` and, as result, incorrect logic of usage of such function.

### 2. indexed parameters of events

It is reasonable to use `indexed` for some of the parameters of the events `EmergencyERC20Recovered`, `TimelockChanged`, `ValidatorsSwapped`, `MinterAdded`, `MinterRemoved`, `TimelockChanged`, `OwnerNominated`, `OwnerChanged`.

### 3. possibility of arbitrary call for owner account

It is reasonable to add allowance for the owner account to call any contract by `call`/`delegate call` on behalf of contract. As we can see owner already have an ability to withdraw any amount of ETH or any ERC20 token, so it will be good to give owner rights to call any logic and to not have many special `recover` functions.

### 4. external minter_burn_from and minter_mint functions

`minter_burn_from` and `minter_mint` functions declared as `public`, but it is reasonable to delcare them as `external`, since they are not used throught internal calls.

### 5. clearValidatorArray function DoS

There is a function `clearValidatorArray` in `OperatorRegistry` contract. It clears all the validator array, so for each of the validators it writes to the corresponding storage slot a zero value. In case of big amount of validators there will be no chance to call this function due to very big amount of gas to be used, which actually can be even greater than the block gas limit.