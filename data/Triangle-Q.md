1. See https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L69
We need to check if validators[from_idx] and validators[to_idx] exist before performing the swap.

2. See https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L82
We need to ensure times <= validators.length before popping out all the validators.

3. See https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol
The name onlyByOwnGov should be changed to onlyOwnedByGov.
