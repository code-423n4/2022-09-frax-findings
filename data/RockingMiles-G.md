## Unnecessary equals boolean


Boolean variables can be checked within conditionals directly without the use of equality operators to true/false.

### Code instances:

        ERC20PermitPermissionedMint.sol, 68: require(minters[minter_address] == false, "Address already exists");
        ERC20PermitPermissionedMint.sol, 78: require(minters[minter_address] == true, "Address nonexistant");



## State variables that could be set immutable

In the following files there are state variables that could be set immutable to save gas. 

### Code instance:

        DOMAIN_SEPARATOR in SigUtils.sol



## Caching array length can save gas


Caching the array length is more gas efficient.
This is because access to a local variable in solidity is more efficient than query storage / calldata / memory.
We recommend to change from:    

    for (uint256 i=0; i<array.length; i++) { ... }

to: 

    uint len = array.length  
    for (uint256 i=0; i<len; i++) { ... }


### Code instance:

        ERC20PermitPermissionedMint.sol, minters_array, 84



## Prefix increments are cheaper than postfix increments

Prefix increments are cheaper than postfix increments. 
Further more, using unchecked {++x} is even more gas efficient, and the gas saving accumulates every iteration and can make a real change
There is no risk of overflow caused by increamenting the iteration index in for loops (the `++i` in `for (uint256 i = 0; i < numIterations; ++i)`).
But increments perform overflow checks that are not necessary in this case.
These functions use not using prefix increments (`++x`) or not using the unchecked keyword: 

### Code instances:

        just change to unchecked: OperatorRegistry.sol, i, 84
        just change to unchecked: OperatorRegistry.sol, i, 63
        change to prefix increment and unchecked: ERC20PermitPermissionedMint.sol, i, 84



## Unnecessary index init


In for loops you initialize the index to start from 0, but it already initialized to 0 in default and this assignment cost gas. 
It is more clear and gas efficient to declare without assigning 0 and will have the same meaning:

### Code instances:

        OperatorRegistry.sol, 84
        ERC20PermitPermissionedMint.sol, 84
        OperatorRegistry.sol, 63



## Rearrange state variables

You can change the order of the storage variables to decrease memory uses.

### Code instance:

In deployGoerli.s.sol,rearranging the storage fields can optimize to: 4 slots from: 5 slots.
The new order of types (you choose the actual variables):
        1. bytes
        2. address
        3. uint32
        4. address
        5. address




## Inline one time use functions


The following functions are used exactly once. Therefore you can inline them and save gas and improve code clearness.
    

### Code instances:

        sfrxETH.sol, beforeWithdraw
        SigUtils.sol, getStructHash
