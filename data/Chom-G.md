## Using assembly to optimize ETH sending

Change

```
        (bool success,) = payable(to).call{ value: amount }("");
        require(success, "Invalid transfer");
```

to

```
        bool success;

        assembly {
            // Transfer the ETH and store if it succeeded or not.
            success := call(gas(), to, amount, 0, 0, 0, 0)
        }

        if(!success) revert ETH_TRANSFER_FAILED();
```

## Use unchecked { ++i; }

```
for (uint256 i = 0; i < numDeposits; ++i) {
```

Should be

```
for (uint256 i = 0; i < numDeposits; ) {
    ...
    unchecked { ++i; }
}
```

## Use custom errors instead of requires

```
require(!depositEtherPaused, "Depositing ETH is paused");
```

Should be

```
error Paused();
...
if (depositEtherPaused) revert Paused();
```