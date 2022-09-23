-> EVENT IS MISSING INDEXED FIELDS
Each event should use three indexed fields if there are three or more fields.

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#:~:text=event%20TokenMinterBurned(address%20indexed%20from%2C%20address%20indexed%20to%2C%20uint256%20amount)%3B
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#:~:text=event%20TokenMinterMinted(address%20indexed%20from%2C%20address%20indexed%20to%2C%20uint256%20amount)%3B
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#:~:text=event%20ETHSubmitted(address%20indexed%20sender%2C%20address%20indexed%20recipient%2C%20uint256%20sent_amount%2C%20uint256%20withheld_amt)%3B
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#:~:text=event%20ValidatorRemoved(bytes%20pubKey%2C%20uint256%20remove_idx%2C%20bool%20dont_care_about_ordering)%3B
https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#:~:text=event%20ValidatorsSwapped(bytes%20from_pubKey%2C%20bytes%20to_pubKey%2C%20uint256%20from_idx%2C%20uint256%20to_idx)%3B

->USE A MORE RECENT VERSION OF SOLIDITY

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETH.sol#:~:text=pragma%20solidity%20%5E0.8.0%3B

