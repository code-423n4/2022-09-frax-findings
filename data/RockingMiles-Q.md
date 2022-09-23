## Duplicates in array


        You allow in some arrays to have duplicates. Sometimes you assumes there are no duplicates in the array.
        
### Code instances:

```OperatorRegistry.addValidator``` pushed ```validator``` without checking if already exist (other similar code samples are checking if exists, therefore I think it might be medium)


## Not verified owner


        owner param should be validated to make sure the owner address is not address(0).
        Otherwise if not given the right input all only owner accessible functions will be unaccessible.
        
        
### Code instance:

        Owned.sol.nominateNewOwner _owner



## Named return issue

Users can mistakenly think that the return value is the named return, but it is actually the actualreturn statement that comes after. To know that the user needs to read the code and is confusing.
Furthermore, removing either the actual return or the named return will save gas. 

### Code instances:

        sfrxETH.sol, mintWithSignature
        sfrxETH.sol, depositWithSignature



## Add a timelock

To give more trust to users: functions that set key/critical variables should be put behind a timelock.

### Code instances:

        https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L202
        https://github.com/code-423n4/2022-09-frax/tree/main/src/ERC20/ERC20PermitPermissionedMint.sol#L94
        https://github.com/code-423n4/2022-09-frax/tree/main/src/OperatorRegistry.sol#L181


## Unbounded loop on array that can only grow can lead to DoS

A malicious attacker that is also a protocol owner can push unlimitedly to an array, that some function loop over this array.
If increasing the array size enough, calling the function that does a loop over the array will always revert since there is a gas limit.
This is a Med Risk issue since it can lead to DoS with a reasonable chance of having untrusted owner or even an owner that did a mistake in good faith.

### Code instances:

        ERC20PermitPermissionedMint.sol (L83): Unbounded loop on the array minters_array that can be publicly pushed by ['addMinter'] and can't be pulled
        OperatorRegistry.sol (L113): Unbounded loop on the array validators that can be publicly pushed by ['addValidator', 'removeValidator'] and can't be pulled




## Not verified input


external / public functions parameters should be validated to make sure the address is not 0.
Otherwise if not given the right input it can mistakenly lead to loss of user funds.    

### Code instances:

        ERC20PermitPermissionedMint.sol.minter_burn_from b_address
        ERC20PermitPermissionedMint.sol.setTimelock _timelock_address
        ERC20PermitPermissionedMint.sol.addMinter minter_address
        ERC20PermitPermissionedMint.sol.minter_mint m_address
        OperatorRegistry.sol.setTimelock _timelock_address
        ERC20PermitPermissionedMint.sol.removeMinter minter_address
        sfrxETH.sol.depositWithSignature receiver
        sfrxETH.sol.mintWithSignature receiver
