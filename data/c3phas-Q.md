## QA

### Missing checks for address(0x0) when assigning values to address state variables

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L34
```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol
34:      timelock_address = _timelock_address;
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L41
```solidity
File: /src/OperatorRegistry.sol
41:        timelock_address = _timelock_address;
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L60-L62
```solidity
File: /src/frxETHMinter.sol
60:        depositContract = IDepositContract(depositContractAddress);
61:        frxETHToken = frxETH(frxETHAddress);
62:        sfrxETHToken = IsfrxETH(sfrxETHAddress);
```

### Constants should be defined rather than using magic numbers
There are several occurrences of literal values with unexplained meaning .Literal values in the codebase without an explained meaning make the code harder to read, understand and maintain, thus hindering the experience of developers, auditors and external contributors alike.

Developers should define a constant variable for every magic value used , giving it a clear and self-explanatory name. 

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L55
```solidity
File: /src/sfrxETH.sol

//@audit: 1e18
55:        return convertToAssets(1e18);
```

### Prevent accidentally burning tokens

Transferring tokens to the zero address is usually prohibited to accidentally avoid "burning" tokens by sending them to an unrecoverable zero address.

Consider adding a check to prevent accidentally burning tokens here:

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L166-L174
```solidity
File: /src/frxETHMinter.sol
166:    function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {
167:        require(amount <= currentWithheldETH, "Not enough withheld ETH in contract");
168:        currentWithheldETH -= amount;

170:        (bool success,) = payable(to).call{ value: amount }("");
171:        require(success, "Invalid transfer");

173:        emit WithheldETHMoved(to, amount);
174:    }
```

### Public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents' functions and change the visibility from external to public.
Functions marked by external use call data to read arguments, where public will first allocate in local memory and then load them.

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L54
```solidity
File: /src/sfrxETH.sol
54:    function pricePerShare() public view returns (uint256) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L53
```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol
53:    function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {


65:    function addMinter(address minter_address) public onlyByOwnGov {


76:    function removeMinter(address minter_address) public onlyByOwnGov {

94:    function setTimelock(address _timelock_address) public onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L82
```solidity
File: /src/OperatorRegistry.sol
82:    function popValidators(uint256 times) public onlyByOwnGov {

93:    function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {
```

### Lock pragmas to specific compiler version
Contracts should be deployed with the same compiler version and flags that they have been tested the most with. Locking the pragma helps ensure that contracts do not accidentally get deployed using, for example, the latest compiler which may have higher risks of undiscovered bugs. Contracts may also be deployed by others and the pragma indicates the compiler version intended by the original authors.

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETH.sol#L2
```solidity
File: /src/frxETH.sol
2:pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L2
```solidity
File: /src/sfrxETH.sol
2:pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L2
```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol
2:pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L2
```solidity
File: /src/frxETHMinter.sol
2:pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L2
```solidity
File: /src/OperatorRegistry.sol
2:pragma solidity ^0.8.0;
```

https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol#L4
```solidity
File: /src/xERC4626.sol
4:pragma solidity ^0.8.0;
```

### Unused named return
Using both named returns and a return statement isn’t necessary in  a function.To  improve code quality, consider using only one of those.

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L59-L67
```solidity
File: /src/sfrxETH.sol

//@audit:  Named shares
59:    function depositWithSignature(
60:        uint256 assets,
61:        address receiver,
62:        uint256 deadline,
63:        bool approveMax,
64:        uint8 v,
65:        bytes32 r,
66:        bytes32 s
67:    ) external nonReentrant returns (uint256 shares) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L75-L83
```solidity
File: /src/sfrxETH.sol

//@audit: Named assets
75:    function mintWithSignature(
76:        uint256 shares,
77:        address receiver,
78:        uint256 deadline,
79:        bool approveMax,
80:        uint8 v,
81:        bytes32 r,
82:        bytes32 s
83:    ) external nonReentrant returns (uint256 assets) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L70
```solidity
File: /src/frxETHMinter.sol

//@audit: Named shares, not used anywhere
70:    function submitAndDeposit(address recipient) external payable returns (uint256 shares) {
```

### Natspec is incomplete/Missing
Solidity contracts can use a special form of comments to provide rich documentation for functions, return variables and more.It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). [see official docs](https://docs.soliditylang.org/en/v0.8.13/style-guide.html#natspec)

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L59-L67
```solidity
File: /src/sfrxETH.sol
//@audit: Missing @param assets,@param receiver,@param deadline,@param approveMax,@param v,@param r,@param s
//@audit:  Missing @return shares
59:    function depositWithSignature(
60:        uint256 assets,
61:        address receiver,
62:        uint256 deadline,
63:        bool approveMax,
64:        uint8 v,
65:        bytes32 r,
66:        bytes32 s
67:    ) external nonReentrant returns (uint256 shares) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol#L75-L83
```solidity
File: /src/sfrxETH.sol
//@audit: Missing @param shares,@param receiver,@param deadline,@param approveMax,@param v,@param r,@param s
//@audit: Missing @return
75:    function mintWithSignature(
76:        uint256 shares,
77:        address receiver,
78:        uint256 deadline,
79:        bool approveMax,
80:        uint8 v,
81:        bytes32 r,
82:        bytes32 s
83:    ) external nonReentrant returns (uint256 assets) {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L53
```solidity
File: /src/ERC20/ERC20PermitPermissionedMint.sol

//@audit: Missing @param b_address,@param b_amount
53:    function minter_burn_from(address b_address, uint256 b_amount) public onlyMinters {

//@audit: Missing @param m_address,@param m_amount
59:    function minter_mint(address m_address, uint256 m_amount) public onlyMinters {

//@audit: Missing @param minter_address
65:    function addMinter(address minter_address) public onlyByOwnGov {

//@audit: Missing @param minter_address
76:    function removeMinter(address minter_address) public onlyByOwnGov {

//@audit: Missing @param _timelock_address
94:    function setTimelock(address _timelock_address) public onlyByOwnGov {

```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol#L70
```solidity
File: /src/frxETHMinter.sol

//@audit: Missing @param recipient,@return shares
70:    function submitAndDeposit(address recipient) external payable returns (uint256 shares) {

//@audit: Missing @param recipient
109:    function submitAndGive(address recipient) external payable {

//@audit: Missing @param newRatio
159:    function setWithholdRatio(uint256 newRatio) external onlyByOwnGov {

//@audit: Missing @param to,@param amount
166:    function moveWithheldETH(address payable to, uint256 amount) external onlyByOwnGov {

//@audit: Missing @param amount
191:    function recoverEther(uint256 amount) external onlyByOwnGov {

//@audit: Missing @param tokenAddress, @param tokenAmount
199:    function recoverERC20(address tokenAddress, uint256 tokenAmount) external onlyByOwnGov {
```

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L53
```solidity
File: /src/OperatorRegistry.sol
//@audit: Missing @param validator
53:    function addValidator(Validator calldata validator) public onlyByOwnGov {

//@audit: Missing @param validatorArray
61:    function addValidators(Validator[] calldata validatorArray) external onlyByOwnGov {

//@audit: Missing @param from_idx, @param to_idx
69:    function swapValidator(uint256 from_idx, uint256 to_idx) public onlyByOwnGov {

//@audit: Missing @param times
82:    function popValidators(uint256 times) public onlyByOwnGov {

//@audit: Missing @param remove_idx,@param dont_care_about_ordering
93:    function removeValidator(uint256 remove_idx, bool dont_care_about_ordering) public onlyByOwnGov {

//@audit: Missing @param i
151:    function getValidator(uint i) 


//@audit: Missing @param pubkey, @param signature,@param depositDataRoot
171:    function getValidatorStruct(
172:        bytes memory pubKey, 
173:        bytes memory signature, 
174:        bytes32 depositDataRoot
175:    ) external pure returns (Validator memory) {

//@audit: Missing @param _new_withdrawal_pubkey
181:    function setWithdrawalCredential(bytes memory _new_withdrawal_pubkey) external onlyByOwnGov {

//@audit: Missing @param _timelock_address
202:    function setTimelock(address _timelock_address) external onlyByOwnGov {
```

### Typos/Grammer
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L27
```solidity
File: /src/OperatorRegistry.sol
//@audit: removed ---> remove
27:/// @notice A permissioned owner can add and removed them at will
```

## Code Structure Deviates From Best-Practice

### Description:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier. Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. 
Some constructs deviate from this recommended best-practice: Modifiers are in the middle of contracts. External/public functions are mixed with internal/private ones. Few events are declared at the bottom

**External/public functions are mixed with internal/private ones and Events are declared at the bottom**
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/frxETHMinter.sol


**External/public functions are mixed with internal/private ones**
https://github.com/corddry/ERC4626/blob/643cd044fac34bcbf64e1c3790a5126fec0dbec1/src/xERC4626.sol

**Events are declared at the bottom**
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/ERC20/ERC20PermitPermissionedMint.sol#L100
**External/public functions are mixed with internal/private ones**
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/sfrxETH.sol

**External/public functions are mixed with internal/private ones**
https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L151-L160

### The view keyword should come after the visibility specifier(external)
See [Official docs](https://docs.soliditylang.org/en/latest/style-guide.html#order-of-functions)

```solidity
File: /src/OperatorRegistry.sol
151:    function getValidator(uint i) 
152:        view
153:        external
154:        returns (
155:            bytes memory pubKey,
156:            bytes memory withdrawalCredentials,
157:            bytes memory signature,
158:            bytes32 depositDataRoot
159:        )
160:    {
```
### Recommendation:
Consider adopting recommended best-practice for code structure and layout.


## Events are missing indexed files
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L208-L214
```solidity
File: /src/OperatorRegistry.sol
208:    event TimelockChanged(address timelock_address);
209:    event WithdrawalCredentialSet(bytes _withdrawalCredential);
210:    event ValidatorAdded(bytes pubKey, bytes withdrawalCredential);

212:    event ValidatorRemoved(bytes pubKey, uint256 remove_idx, bool dont_care_about_ordering);
213:    event ValidatorsPopped(uint256 times);
214:    event ValidatorsSwapped(bytes from_pubKey, bytes to_pubKey, uint256 from_idx, uint256 to_idx);
```


