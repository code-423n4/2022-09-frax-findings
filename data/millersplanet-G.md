## **ERC20PermitPermissionedMint.sol**

- The `onlyByOwnGov()` modifier has a `require` statement that can be refactored with an `if` statement and a custom error. (Line 41)

  Declare error:

  `error NotOwnerOrTimelock();`

  and replace the mentioned line with the following:

  ```
  if (msg.sender != timelock_address || msg.sender != owner) revert NotOwnerOrTimelock();
  ```

- The `onlyMinters()` modifier has a `require` statement that can be refactored with an `if` statement and a custom error. (Line 46)

  Declare error:

  `error OnlyMinters();`

  and replace the mentioned line with the following:

  ```
  if (!minters[msg.sender]) revert OnlyMinters();
  ```

- The `addMinter()` function has two `require` statements that can be refactored with an `if` statement and a custom error. (Lines 66, 68)

  Declare errors:

  `error ZeroAddressDetected();`
  `error AddressAlreadyExists();`

  and replace the mentioned lines with the following:

  ```
  if (minter_address == address(0)) revert ZeroAddressDetected();
  if (minters[minter_address]) revert AddressAlreadyExists();
  ```

- The `removeMinter()` function has two `require` statements that can be refactored with an `if` statement and a custom error. (Lines 77, 78)

  Declare error:

  `error AddressNonExistant();`

  and replace the mentioned lines with the following:

  ```
  if (minter_address == address(0)) revert ZeroAddressDetected();
  if (!minters[minter_address]) revert AddressNonExistant();
  ```

- The `setTimelock()` function has a `require` statement that can be refactored with an `if` statement and a custom error. (Line 95):

  Replace the mentioned line with the following:

  ```
  if (_timelock_address == address(0)) revert ZeroAddressDetected();
  ```

  Note that `ZeroAddressDetected();` is declared only once and is used three times without being re-declared with this access control implementation.
