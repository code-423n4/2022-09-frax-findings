warning[2462]: Warning: Visibility for constructor is ignored. If you want the contract to be non-deployable, making it "abstract" is sufficient.
  --> src/DepositContract.sol:74:5:
   |
74 |     constructor() public {
   |     ^ (Relevant source part starts here and spans across multiple lines).



warning[9302]: Warning: Return value of low-level calls not used.
   --> test/frxETHMinter.t.sol:222:9:
    |
222 |         address(minter).call{ value: 15 ether }("");
    |         ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
