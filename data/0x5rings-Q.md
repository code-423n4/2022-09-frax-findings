 ## Approve should be replaced with `safeapprove` or `safeincreaseallowance()` / `safedecreaseallowance()` 
 --- 
 ### Files Found: 
 There are 2 instances of this issue. 
 - File: src/IsfrxETH.sol - line 8 
 - File: src/frxETHMinter.sol - line 75 
 

 ### Mitigation: 
 approve is subject to a known front-running attack. Consider using safeApprove instead:
 --- 

 ## Zero Address Checks are missing for _owner, tokenAddress, tokenAmount
 --- 
 ### Files Found: 
 There are 3 found instances of this issue. 
- File: src/frxETHMinter.sol - line 52-65
- - File: src/frxETHMinter.sol - line 200

 ### Mitigation: 
require(_<argument> != address(0), "Zero-address");
 --- 