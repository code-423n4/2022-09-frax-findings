## Private Function Embedded Modifier to Reduce Contract Size
Consider having the logic of a modifier embedded through an internal or private function to reduce contract size if need be. For instance, the following instance of modifier may be rewritten as follows:

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L40-L43

```
    function _onlyByOwnGov() private view {
        require(msg.sender == timelock_address || msg.sender == owner, "Not owner or timelock");
    }

    modifier onlyByOwnGov() {
        _onlyByOwnGov();
        _;
    }
```
## Use Custom Errors Instead of Require to Save Gas
Consider replacing all require statements with custom errors which are cheaper both in deployment and runtime cost starting from Solidity 0.8.4. Here are some of the instances entailed:

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L41
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L66-L68
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L77-L78
https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L95

## ++i costs less gas compared to i++
++i costs less gas compared to i++ or i += 1 for unsigned integers considering the pre-increment operation is cheaper (about 5 GAS per iteration).

i++ increments i and makes the compiler create a temporary variable for returning the initial value of i. In contrast, ++i returns the actual incremented value without making the compiler do extra job.

As an example, the for loop below could be refactored as follows:

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84-L89

```
        for (uint i = 0; i < minters_array.length; ){ 
            if (minters_array[i] == minter_address) {
                minters_array[i] = address(0); // This will leave a null in the array and keep the indices the same
                break;
            }
            unchecked {
                ++i;
            }
        }
```
Note: "Checked" math, which is default in 0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. Considering no arithmetic overflow/underflow is going to happen here, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas in the above for loop.

Similarly, the following example line of code may also be rewritten as:

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L67

```
        storedTotalAssets = storedTotalAssets - amount;
```

## Function Order Affects Gas Consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the Optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
  solidity: {
    version: "0.8.14",
    settings: {
      optimizer: {
        enabled: true,
        runs: 1000,
      },
    },
  },
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

## No Need to Initialize Variables with Default Values
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). If you explicitly initialize it with its default value, you will be incurring more gas. Here are some of the instances entailed:

https://github.com/code-423n4/2022-09-frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol#L84
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L63-L64
https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94

## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (e.g. < + =). Consider replacing <= or >= respectively with the strict counterpart < or >. For instance, the following line of code could be rewritten as:

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L52

```
        if (block.timestamp > rewardsCycleEnd_ - 1)
```

##  Shorten Require Messages to Less Than 32 characters
Strings that are more than 32 characters will require more than 1 storage slot, costing more gas. Consider reducing the message length to less than 32 characters on all require() statements not intended to be replaced by custom errors. Here is one of the instances entailed:

https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L167

## calldata and memory
When running a function we could pass the function parameters as calldata or memory for variables such as strings, bytes, structs, arrays etc. If we are not modifying the passed parameter we should pass it as calldata because calldata is more gas efficient than memory. Here is one of the instances entailed:

https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L129-L131