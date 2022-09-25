1. **Lack of zero address checks**

Zero-address checks as input validation closest to the function beginning is a best-practice. There are two places where an explicit zero-address check is missing which may lead to a later revert, gas wastage or even token burn.

##### - src/OperatorRegistry.sol:[41](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L41)

```diff
diff --git a/src/OperatorRegistry.sol b/src/OperatorRegistry.sol
index f81094c..117ac8d 100644
--- a/src/OperatorRegistry.sol
+++ b/src/OperatorRegistry.sol
@@ -27,6 +27,8 @@ import "./Utils/Owned.sol";
   27,  27: /// @notice A permissioned owner can add and removed them at will
   28,  28: contract OperatorRegistry is Owned {
   29,  29:
+       30:+    error ZeroAddress();
+       31:+
   30,  32:     struct Validator {
   31,  33:         bytes pubKey;
   32,  34:         bytes signature;
@@ -38,6 +40,7 @@ contract OperatorRegistry is Owned {
   38,  40:     address public timelock_address;
   39,  41:
   40,  42:     constructor(address _owner, address _timelock_address, bytes memory _withdrawal_pubkey) Owned(_owner) {
+       43:+        if (_timelock_address == address(0)) revert ZeroAddress();
   41,  44:         timelock_address = _timelock_address;
   42,  45:         curr_withdrawal_pubkey = _withdrawal_pubkey;
   43,  46:     }
```

##### - src/frxETHMinter.sol:[60-62](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L60-L62), [86](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L86), [170](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L170)

```diff
diff --git a/src/frxETHMinter.sol b/src/frxETHMinter.sol
index 4565883..dc090d8 100644
--- a/src/frxETHMinter.sol
+++ b/src/frxETHMinter.sol
@@ -57,6 +57,9 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
   57,  57:         address _timelock_address,
   58,  58:         bytes memory _withdrawalCredential
   59,  59:     ) OperatorRegistry(_owner, _timelock_address, _withdrawalCredential) {
+       60:+        if (
+       61:+            depositContractAddress == address(0) || frxETHAddress == address(0) || sfrxETHAddress == address(0)
+       62:+        ) revert ZeroAddress();
   60,  63:         depositContract = IDepositContract(depositContractAddress);
   61,  64:         frxETHToken = frxETH(frxETHAddress);
   62,  65:         sfrxETHToken = IsfrxETH(sfrxETHAddress);
@@ -68,6 +71,7 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
   68,  71:     /** @dev Could try using EIP-712 / EIP-2612 here in the future if you replace this contract,
   69,  72:         but you might run into msg.sender vs tx.origin issues with the ERC4626 */
   70,  73:     function submitAndDeposit(address recipient) external payable returns (uint256 shares) {
+       74:+        if (recipient == address(0)) revert ZeroAddress();
   71,  75:         // Give the frxETH to this contract after it is generated
   72,  76:         _submit(address(this));
   73,  77:
@@ -83,6 +87,7 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
   83,  87:
   84,  88:     /// @notice Mint frxETH to the recipient using sender's funds. Internal portion
   85,  89:     function _submit(address recipient) internal nonReentrant {
+       90:+        if (recipient == address(0)) revert ZeroAddress();
   86,  91:         // Initial pause and value checks
   87,  92:         require(!submitPaused, "Submit is paused");
   88,  93:         require(msg.value != 0, "Cannot submit 0");
@@ -164,6 +169,7 @@ contract frxETHMinter is OperatorRegistry, ReentrancyGuard {
  164, 169:
  165, 170:     /// @notice Give the withheld ETH to the "to" address
  166, 171:     function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
+      172:+        if (to == address(0)) revert ZeroAddress();
  167, 173:         require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
  168, 174:         currentWithheldETH -= amount;
  169, 175:
```
