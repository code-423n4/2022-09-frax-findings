ISSUE 1.
Do not shrink Variables ..Use uint256 instead of using uint in L151...

repo: https://github.com/code-423n4/2022-09-frax/blob/main/src/OperatorRegistry.sol#L151

explanation: This means that if you use uint, EVM has to first convert it uint256 to work on it
                    and the conversion costs extra gas! You may wonder, What were the devs thinking? 
                    Why did they create smaller variables then? The answer lies in packing. 
                    In solidity, you can pack multiple small variables into one slot, but if you are defining 
                    a lone variable and can’t pack it, it’s optimal to use a uint256 rather than uint.

for further...https://mudit.blog/solidity-gas-optimization-tips/... 2) uint8 is not always cheaper than uint256

Issue 2.

Wrong require statement declared as sfrxeth_recieved in L79 already declared as uint256 
hence it get never less than 0 ..
        
        uint256 sfrxeth_recieved = sfrxETHToken.deposit(msg.value, recipient);
        require(sfrxeth_recieved > 0, 'No sfrxETH was returned');

....instead of using >(greater than)operator use !=(not equal to)operator..

repohttps://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L79

same as in L126..instead of using >(greater than)operator use !=(not equal to)operator

repo: https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L126


Issue 3.

Do not initialize withheld_amt = 0 ..Every variable assignment in Solidity costs gas. When initializing variables,
 we often waste gas by assigning default values that will never be used.

uint256 withheld_amt; is cheaper than uint256 withheld_amt = 0;.

repo: https://github.com/code-423n4/2022-09-frax/blob/main/src/frxETHMinter.sol#L94