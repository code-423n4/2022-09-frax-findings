 ## Approve should be replaced with `safeapprove` or `safeincreaseallowance()` / `safedecreaseallowance()` 
 --- 
 ### Files Found: 
 There are 2 instances of this issue. 
 - File: src/IsfrxETH.sol - line 8 
 - File: src/frxETHMinter.sol - line 75 
 

 ### Mitigation: 
 approve is subject to a known front-running attack. Consider using safeApprove instead:
 --- 