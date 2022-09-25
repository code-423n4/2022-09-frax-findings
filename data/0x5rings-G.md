 ## `++i` cost less gas compared to `i++`. Consider using `++{variable}` instead of `{variable}++` 
 --- 
 ### Files Found: 
 There are 3 instances of this issue. 
- File: src/DepositContract.sol - line 76 
- File: src/DepositContract.sol - line 83 
- File: src/DepositContract.sol - line 148 

 ### Mitigation: 

 Consider using `++i` instead of `i++`
 --- 	

 ## `Require()` should be used instead of `assert()` 
 --- 
 ### Files Found: 
 There are 1 instances of this issue. 
 - File: src/DepositContract.sol - line 158 
 
 ### Mitigation: 
 Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it. assert() should be avoided even past solidity version 0.8.0 as its documentation states that.
 --- 
 ## Using private rather than public for constants, saves gas 
 --- 
 ### Files Found: 
 There are 2 instances of this issue. 
 - File: src/frxETHMinter.sol - line 38 
 - File: src/frxETHMinter.sol - line 39 
 
 ### Mitigation: 
 If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata.
 --- 
	
 ## `x += y` costs more gas than `x = x + y` for state variables 
 --- 
 ### Files Found: 
 There are 2 instances of this issue. 
 - File: src/DepositContract.sol - line 146 
 - File: src/frxETHMinter.sol - line 97 
 
 ### Mitigation: 
 Use `x = x + y` instead of `x += y`
 --- 

 ## It costs more gas to initialise non-constant/non-immutable variables to zero than to let the default of zero be applied 
 --- 
 ### Files Found: 
 There are 4 instances of this issue. 
 - File: src/OperatorRegistry.sol - line 63 
- File: src/OperatorRegistry.sol - line 84 
- File: src/OperatorRegistry.sol - line 114 
- File: src/frxETHMinter.sol - line 129 

 ### Mitigation: 
 uint is initialised at 0. It cost more gas to initialise variable at 0
 --- 
