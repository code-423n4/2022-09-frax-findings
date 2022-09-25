
## Impact
not checking address(0) in frxEth constructor

## Proof of Concept
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETH.sol#L40

## Tools Used

## Recommended Mitigation Steps
check
`     require(_creator_address!=address(0))`
`     require(_timelock_address!=address(0))`



## Impact
both timelock_address and owner have admin privilages. 

## Proof of Concept
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L41
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L45

## Tools Used

## Recommended Mitigation Steps
need to make sure both addresses are multi-sig



## Impact
To avoid potentially assigning to the wrong time_lock address, pull model can be implemented instead

## Proof of Concept
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L94
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L202

## Tools Used

## Recommended Mitigation Steps
assign new time_lock to pending_time_lock, let pending_time_lock claim explicitly



## Impact
removeValidator() and keeping order can be made more gas efficient

## Proof of Concept
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L108

## Tools Used

## Recommended Mitigation Steps
```
function orderedArray(uint index) public{
    for(uint i = remove_idx; i < validators.length-1; ){
      validators[i] = validators[i+1];      
    }
    validators.pop();
    unchecked {
        ++i;
    }
  }
```



## Impact
gas saving for loops

## Proof of Concept
as one example
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L84

## Tools Used

## Recommended Mitigation Steps
check to
```
        for (uint256 i; i < times; ++i) {
            validators.pop();
            unchecked {
                ++i;
            }
        }
```


## Impact
gas saving from revert with ERROR instead of require

## Proof of Concept
as one example
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L46

## Tools Used

## Recommended Mitigation Steps
change to
```
if (msg.sender != timelock_address && msg.sender != owner) revert NOT_OWNER_OR_TIMELOCK()");
```

 