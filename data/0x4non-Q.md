# LOW

## Use a Timelock or Ownable, avoid combine them
On [ERC20PermitPermissionedMint.sol#L40-L43](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L40-L43) `modifier onlyByOwnGov()`, is checking for the msg.sender to be owner or timelock, so whats the point of having a timelock if you could do whatever?

Same issue on [OperatorRegistry.sol#L45-L48](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L45-L48)

My recommendation its to remove this modifier and use the `onlyOwner` modifier, if you want to use a timelock just transfer the ownership to the timelock.


## Missing address(0) check
On [ERC20PermitPermissionedMint.sol#L34](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L34);
```solidity
    Owned(_creator_address)
    {
      timelock_address = _timelock_address;
    }
```
You should revert if `_timelock_address` is `address(0)` to ensure that you start with a valid timelock, if you want to start with address(0) remove this line.

Same issue on;
[OperatorRegistry.sol#L41](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L41)

## Minter is not being corretly deleted from `minters_array` on `ERC20PermitPermissionedMint.sol`

Affected lines;
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L84-L89

When you remove a minter the `minters_array` its just setting the minter index into `address(0)`, think on this escenario;
- You add a minter
- `minters_array.length` is 1
- You remove a minter
- `minters_array.length` still is 1, you have remove the minter!

My recommendation its to avoid the [`minters_array`](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L19), you could rely on events to track the minters.

If you still want to use and array please read this article;
https://blog.finxter.com/how-to-delete-an-element-from-an-array-in-solidity/ 
And use this pattern to delete an object of the array
```solidity
    firstArray[index] = firstArray[firstArray.length - 1];
    firstArray.pop();
```

## Function `depositEther` will always revert if (balance / 32 ether) is greater than validators.length

Checkout [https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L136](frxETHMinter.sol#L136);
```) = getNextValidator(); // Will revert if there are not enough free validators```
Instead of reverting on this line you could change the [loop guard](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L129) insted of `numDeposits` you will have to use `numIterations`, and `numIterations` is the minimum between `numDeposits` and `numValidators()`

## On recover `recoverERC20` the ERC20 will always go to the `owner`
What if `owner` is `address(0)` or a contract? Also governance could call this function, but all tokens will be send to owner.
I think you should add a parameter to specify where you want to send the funds, or change `onlyByOwnGov` to `onlyOwner`

Lines:
[frxETHMinter.sol#L200](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L200)

## On recover `recoverEther` the ERC20 will always go to the `owner`
What if `owner` is `address(0)` or a contract that rejects ETH? Also governance could call this function, but all tokens will be send to owner.
I think you should add a parameter to specify where you want to send the funds, or change `onlyByOwnGov` to `onlyOwner`

Lines:
[frxETHMinter.sol#L191](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L191)

## Use `approve(0)` before approve
Its consider a good practice to reset the approve before changing it. Mainly because some tokens (like USDT) do not work when changing the allowance from an existing non-zero allowance value.They must first be approved by zero and then the actual allowance must be approved.
Lines: [frxETHMinter.sol#L75](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L75)

You could also remove this line and add an infinite approve on the constructor for gas savings.

# NON CRITICAL

## Draft OpenZeppelin Dependencies	

The `ERC20PermitPermissionedMint.sol` contract utilised draft-EIP712, an OpenZeppelin contract. This contract is still a draft and is not considered ready for mainnet use. OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.
[ERC20PermitPermissionedMint.sol#L6](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L6)

Ensure the development team is aware of the risks of using a draft contract or consider waiting until the contract is finalised.

Otherwise, make sure that development team are aware of the risks of using a draft OpenZeppelin contract and accept the risk-benefit trade-off.

## Non-library/interface files should use fixed compiler versions, not floating ones
[ERC20PermitPermissionedMint.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L2)
[frxETHMinter.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L2)
[sfrxETH.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L2)
[frxETH.sol#L2](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETH.sol#L2)
