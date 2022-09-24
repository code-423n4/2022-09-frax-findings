Cheaper For Loops - 25 to 80 gas per instance 
You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting:

for (uint256 i = 0; i <  minters_array.length; /** NOTE: Removed i++ **/ ) {
                // Do the thing
                // Unchecked pre-increment is cheapest
                unchecked { ++i; }
        }       

instances:

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84


