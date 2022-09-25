## (1) Solidity Version

The project specifies version ^0.8.0.

Consider using the most recent or at least a more recent version of solidity.
0.8.17 is already released.

Also a fixed version of solidity should be specified in all non-library files instead of a floating one.

## (2) AVOID THE USE OF SENSITIVE TERMS IN FAVOR OF NEUTRAL ONES

Use allowlist rather than whitelist.

// Adds whitelisted minters
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L64

## (3) Missing Indexed Fields in Events

Each event should use three indexed fields if there are three or more fields in the event.

***

event ETHSubmitted(address indexed sender, address indexed recipient, uint256 sent_amount, uint256 withheld_amt);
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L207

event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L214

## (4) Avoid approve()

There exist known attacks against approve().
Consider safeIncreaseAllowance() or safeDecreaseAllowance() instead.

***

frxETHToken.approve(address(sfrxETHToken), msg.value);
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L75

## (5) Typo

“nonexistant”

***

require(minters[minter_address] == true, "Address nonexistant");
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L78

