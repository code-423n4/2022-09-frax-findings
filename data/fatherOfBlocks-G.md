**ERC20PermitPermissionedMint**
- L41/46 - The require generates a lot of gas costs, this can be replaced by an if and a custom error.

- L46/68/78 - When we are validating a variable and it is bool, making variable == true or variable == false generates extra gas costs. The least expensive way to validate it would be: variable or !variable.

- L84 - When we initialize a variable and we want to set its default value, it is not necessary to set it, since it has that value by default.

- L84 - When we are going through an array in a for loop, it is less expensive to create a uint variable and store the length there and not be in each iteration consulting the length.

- L84 - When we are using a counter in a for loop, gas can be saved by making it unchecked, since it will never generate an overflow and it is less expensive to do ++i than i++.

**frxETHMinter**
- L63/64/94/129 - When we initialize a variable and we want to set its default value, it is not necessary to set it, since it has that default value.

- L79/126 - It is less expensive to validate uint256() > 0 than to validate uint256() != 0, this generates an extra cost of gas and no understanding is gained.


**Operator Registry**
- L46/137/182/203 - The require generates a lot of gas costs, this can be replaced by a custom if and error.

- L63/84/114 - When we initialize a variable and we want to set its default value, it is not necessary to set it, since it has that default value.

- L114 - When we are going through an array in a for loop, it is less expensive to create a uint variable and store the length there and not be in each iteration consulting the length.
