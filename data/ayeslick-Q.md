OperatorRegistry:

setTimeLock: 
The function doesn’t make sure the address is a contract 

Recommendation:
Check the address to make sure it’s a contract. 



addValidator:
The function allows for duplicate validators 

Recommendation:
Check if the validator is already within the array. If it is revert if not add. 



popValidators: 
Should set the upper limit of the loop by the number of validators there are in the array. 


swapValidators: 
An admin can swap indexes using the same index i.e. swapping 0 index with the 0 index. 

Recommendations:
Enforce swaps with indexes different from each other, i.e. swaps should be between 0 index and 1 index not 0 index and 0 index. 