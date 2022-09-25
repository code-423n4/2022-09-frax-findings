1
++I COSTS LESS GAS COMPARED TO I++ OR I += 1 IN FOR LOOPS (~5 GAS PER ITERATION)
++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i.++i returns the actual incremented value with i++ the compiler has to create a temporary variable (when used) for returning 1 instead of 2.Ex:

frax/blob/main/src/ERC20/ERC20PermitPermissionedMint.sol

84 for (uint i = 0; i < minters_array.length; i++) {

2 
Cache storage values in memory to minimize SLOADs

The code can be optimized by minimising the number of SLOADs. SLOADs are expensive 100 gas compared to MLOADs/MSTOREs(3gas)
Storage value should get cached in memory.

- blob/main/src/frxETHMinter.sol

85 function _submit(address recipient) internal nonReentrant {
86      // Initial pause and value checks
 87       require(!submitPaused, "Submit is paused");
88      require(msg.value != 0, "Cannot submit 0");
89
90       // Give the sender frxETH
91      frxETHToken.minter_mint(recipient, msg.value);
92
93       // Track the amount of ETH that we are keeping
94      uint256 withheld_amt = 0;
95     if (withholdRatio != 0) {
96        withheld_amt = (msg.value * withholdRatio) / RATIO_PRECISION;
97       currentWithheldETH += withheld_amt;


In the above function, there are two SLOADS(withholdRatio) that can be replaced with a cached variable.

-blob/main/src/frxETHMinter.sol

166  function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
167       require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
168      currentWithheldETH -= amount;
169
170       (bool success,) = payable(to).call{ value: amount }("");
171        require(success, "Invalid transfer");
172
173       emit WithheldETHMoved(to, amount);
174  }
In the above function, there are two SLOADS(currentWithheldETH) that can be replaced with a cached variable.







