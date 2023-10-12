# L1 contracts - zkSync 
# STORAGE VARIABLE CACHING IN MEMORY

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L39-L39

The contract ValidatorTimelock is using the state variable validator multiple times in the function setValidator.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).

Storage variables read multiple times inside a function should instead be cached in the memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

# PUBLIC CONSTANTS CAN BE PRIVATE

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19-L19
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41-L41
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15-L15
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24-L24
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22-L22

Public constant variables cost more gas because the EVM automatically creates getter functions for them and adds entries to the method ID table. The values can be read from the source code instead.
The following variable is affected: getName

If reading the values for the constants are not necessary, consider changing the public visibility to private.

# CHEAPER CONDITIONAL OPERATORS
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L24-L24
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L312-L312
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L185-L185
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L354

During compilation, x != 0 is cheaper than x > 0 for unsigned integers in solidity inside conditional statements.

Consider using x != 0 in place of x > 0 in uint wherever possible.

# CHEAPER INEQUALITIES IN REQUIRE()

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L59-L59
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L129-L129
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L279-L279
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L294-L294
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L306-L306
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L421-L421
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L25-L25
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L84-L84
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L123-L123
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L30-L30
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L32-L32
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L37-L41
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L50-L50
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L51-L51
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L55-L55
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L117-L117
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L93-L93
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L94-L94
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L300-L300
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L346-L346
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L400-L400
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L453-L453

The contract was found to be performing comparisons using inequalities inside the require statement. When inside the require statements, non-strict inequalities (>=, <=) are usually costlier than strict equalities (>, <).

It is recommended to go through the code logic, and, if possible, modify the non-strict inequalities with the strict ones to save ~3 gas as long as the logic of the code is not affected.

# VARIABLES DECLARED BUT NEVER USED

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19-L19
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41-L41
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15-L15
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24-L24
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22-L22

The contract ExecutorFacet has declared a variable getName but it is not used anywhere in the code. This represents dead code or missing logic.
Unused variables increase the contract's size and complexity, potentially leading to higher gas costs and a larger attack surface.

To remediate this vulnerability, developers should perform a code review and remove any variables that are declared but never used.

# ARRAY LENGTH CACHING

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L100-L117
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L123-L161
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L209-L218
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L241-L256

During each iteration of the loop, reading the length of the array uses more gas than is necessary. In the most favorable scenario, in which the length is read from a memory variable, storing the array length in the stack can save about 3 gas per iteration. In the least favorable scenario, in which external calls are made during each iteration, the amount of gas wasted can be significant.

Consider storing the array length of the variable before the loop and use the stored length instead of fetching it in each iteration.

# FUNCTION SHOULD RETURN STRUCT
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L101-L171

The function _processL2Logs was detected to be returning multiple values.
Consider using a struct instead of multiple return values for the function. It can improve code readability.
Use struct for returning multiple values inside a function, which returns several parameters and improves code readability.

# CUSTOM ERRORS TO SAVE GAS

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L115-L115
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L292-L292
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L159-L159

The contract was found to be using revert() statements. Since Solidity v0.8.4, custom errors have been introduced which are a better alternative to the revert.
This allows the developers to pass custom errors with dynamic data while reverting the transaction and also making the whole implementation a bit cheaper than using revert.

It is recommended to replace all the instances of revert() statements with error() to save gas.

# INTERNAL FUNCTIONS NEVER USED
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L48-L61
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L417-L419

The contract declared internal functions but was not using them in any of the functions or contracts.
Since internal functions can only be called from inside the contracts, it makes no sense to have them if they are not used. This uses up gas and causes issues for auditors when understanding the contract logic.

Having dead code in the contracts uses up unnecessary gas and increases the complexity of the overall smart contract.
It is recommended to remove the internal functions from the contracts if they are never used.

# CHEAPER INEQUALITIES IN IF()
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L312-L312
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L354
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L402-L402
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L409-L409

The contract was found to be doing comparisons using inequalities inside the if statement.
When inside the if statements, non-strict inequalities (>=, <=) are usually cheaper than the strict equalities (>, <).

It is recommended to go through the code logic, and, if possible, modify the strict inequalities with the non-strict ones to save ~3 gas as long as the logic of the code is not affected.

# DEFINE CONSTRUCTOR AS PAYABLE
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L50-L50
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L11-L16
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L44-L49

Developers can save around 10 opcodes and some gas if the constructors are defined as payable.
However, it should be noted that it comes with risks because payable constructors can accept ETH during deployment.

It is suggested to mark the constructors as payable to save some gas. Make sure it does not lead to any adverse effects in case an upgrade pattern is involved.

# ABI ENCODE IS LESS EFFICIENT THAN ABI ENCODEPACKED
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L77-L77
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L363-L363
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L265-L265
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L431-L431
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L458-L458
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L468-L468

The contract is using abi.encode() in the function _hashStoredBatchInfo. In abi.encode(), all elementary types are padded to 32 bytes and dynamic arrays include their length, whereas abi.encodePacked() will only use the minimal required memory to encode the data.

Unless explicitly needed , it is recommended to use abi.encodePacked() instead of abi.encode().

# BYTES CONSTANT MORE EFFICIENT THAN STRING LITERAL
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19-L19
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41-L41
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15-L15
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24-L24
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22-L22

The contract was found to be using getName string constant. This can be optimized by using bytes32 constant to save gas.

Unless explicitly required, if the string is lesser than 32 bytes, it is recommended to use bytes32 constant instead of a string constant as it’ll save some gas.

# CONSTANT STATE VARIABLES
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L15-L15
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L36-L36

The contract has defined state variables whose values are never modified throughout the contract.
The variables whose values never change should be marked as constant to save gas.

Make sure that the values stored in the variables flagged above do not change throughout the contract. If this is the case, then consider setting these variables as constant.

---

# L1 contracts - Bridges
# ABI ENCODE IS LESS EFFICIENT THAN ABI ENCODEPACKED

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L119-L119
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L244-L244
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L354-L354
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L118-L118

The contract is using abi.encode() in the function initialize. In abi.encode(), all elementary types are padded to 32 bytes and dynamic arrays include their length, whereas abi.encodePacked() will only use the minimal required memory to encode the data.

Unless explicitly needed , it is recommended to use abi.encodePacked() instead of abi.encode().

# DEFINE CONSTRUCTOR AS PAYABLE

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L65-L68
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L64-L68

Developers can save around 10 opcodes and some gas if the constructors are defined as payable.
However, it should be noted that it comes with risks because payable constructors can accept ETH during deployment.

It is suggested to mark the constructors as payable to save some gas. Make sure it does not lead to any adverse effects in case an upgrade pattern is involved.

# CHEAPER INEQUALITIES IN REQUIRE()

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L347-L347

The contract was found to be performing comparisons using inequalities inside the require statement. When inside the require statements, non-strict inequalities (>=, <=) are usually costlier than strict equalities (>, <).

# CUSTOM ERRORS TO SAVE GAS

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L223-L223

The contract was found to be using revert() statements. Since Solidity v0.8.4, custom errors have been introduced which are a better alternative to the revert.
This allows the developers to pass custom errors with dynamic data while reverting the transaction and also making the whole implementation a bit cheaper than using revert.

It is recommended to replace all the instances of revert() statements with error() to save gas.

# CHEAPER CONDITIONAL OPERATORS

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L275-L275

During compilation, x != 0 is cheaper than x > 0 for unsigned integers in solidity inside conditional statements.

Consider using x != 0 in place of x > 0 in uint wherever possible.

# LONG REQUIRE/REVERT STRINGS

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L90-L90
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L91-L94
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L223-L223
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L279-L279
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L282-L285
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L295-L295

The require() and revert() functions take an input string to show errors if the validation fails.
This strings inside these functions that are longer than 32 bytes require at least one additional MSTORE, along with additional overhead for computing memory offset, and other parameters.

It is recommended to short the strings passed inside require() and revert() to fit under 32 bytes. This will decrease the gas usage at the time of deployment and at runtime when the validation condition is met.

# VARIABLES DECLARED BUT NEVER USED

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L54-L54
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L57-L57

The contract L1ERC20Bridge has declared a variable __DEPRECATED_lastWithdrawalLimitReset but it is not used anywhere in the code. This represents dead code or missing logic.

The contract L1ERC20Bridge has declared a variable __DEPRECATED_withdrawnAmountInWindow but it is not used anywhere in the code. This represents dead code or missing logic.

Unused variables increase the contract's size and complexity, potentially leading to higher gas costs and a larger attack surface.

# OPTIMIZING ADDRESS ID MAPPING

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L54-L54
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L57-L57
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L61-L61

Combining multiple address/ID mappings into a single mapping using a struct enhances storage efficiency, simplifies code, and reduces gas costs, resulting in a more streamlined and cost-effective smart contract design.
It saves storage slot for the mapping and depending on the circumstances and sizes of types, it can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they fit in the same storage slot.

It is suggested to modify the code so that multiple mappings using the address->id parameter are combined into a struct.

# STORAGE VARIABLE CACHING IN MEMORY

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L35-L35
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L52-L52
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L43-L43
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L39-L39
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L61-L61
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L50-L50
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L44-L44
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L60-L60

SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).

Storage variables read multiple times inside a function should instead be cached in the memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

---
# L1 contract - Governance
# ABI ENCODE IS LESS EFFICIENT THAN ABI ENCODEPACKED

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L205-L205
The contract is using abi.encode() in the function hashOperation. In abi.encode(), all elementary types are padded to 32 bytes and dynamic arrays include their length, whereas abi.encodePacked() will only use the minimal required memory to encode the data.
Unless explicitly needed , it is recommended to use abi.encodePacked() instead of abi.encode().

# DEFINE CONSTRUCTOR AS PAYABLE

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L41-L51

Developers can save around 10 opcodes and some gas if the constructors are defined as payable.
However, it should be noted that it comes with risks because payable constructors can accept ETH during deployment.

It is suggested to mark the constructors as payable to save some gas. Make sure it does not lead to any adverse effects in case an upgrade pattern is involved.

# UNNECESSARY CHECKED ARITHMETIC IN LOOP
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L225-L225

Increments inside a loop could never overflow due to the fact that the transaction will run out of gas before the variable reaches its limits. Therefore, it makes no sense to have checked arithmetic in such a place.
It is recommended to have the increment value inside the unchecked block to save some gas.

# CHEAPER INEQUALITIES IN REQUIRE()

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L217-L217

The contract was found to be performing comparisons using inequalities inside the require statement. When inside the require statements, non-strict inequalities (>=, <=) are usually costlier than strict equalities (>, <).

It is recommended to go through the code logic, and, if possible, modify the non-strict inequalities with the strict ones to save ~3 gas as long as the logic of the code is not affected.

# ARRAY LENGTH CACHING

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L225-L233
During each iteration of the loop, reading the length of the array uses more gas than is necessary. In the most favorable scenario, in which the length is read from a memory variable, storing the array length in the stack can save about 3 gas per iteration. In the least favorable scenario, in which external calls are made during each iteration, the amount of gas wasted can be significant.
The following array was detected to be used inside loop without caching it's value in memory: _calls.

Consider storing the array length of the variable before the loop and use the stored length instead of fetching it in each iteration.

# LONG REQUIRE/REVERT STRINGS
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L172-L172
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L177-L177
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L191-L191
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L196-L196
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L216-L216
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L217-L217
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L240-L240

The require() and revert() functions take an input string to show errors if the validation fails.
This strings inside these functions that are longer than 32 bytes require at least one additional MSTORE, along with additional overhead for computing memory offset, and other parameters.

It is recommended to short the strings passed inside require() and revert() to fit under 32 bytes. This will decrease the gas usage at the time of deployment and at runtime when the validation condition is met.

# STORAGE VARIABLE CACHING IN MEMORY
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L35-L35
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L26-L26

The contract Governance is using the state variable minDelay multiple times in the function updateDelay.
The contract Governance is using the state variable securityCouncil multiple times in the function updateSecurityCouncil.

SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).


---
# L1 contract - Upgrades
# ABI ENCODE IS LESS EFFICIENT THAN ABI ENCODEPACKED

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L173-L173

The contract is using abi.encode() in the function _setL2SystemContractUpgrade. In abi.encode(), all elementary types are padded to 32 bytes and dynamic arrays include their length, whereas abi.encodePacked() will only use the minimal required memory to encode the data.
Unless explicitly needed , it is recommended to use abi.encodePacked() instead of abi.encode().

# UNNECESSARY CHECKED ARITHMETIC IN LOOP
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204-L204

Increments inside a loop could never overflow due to the fact that the transaction will run out of gas before the variable reaches its limits. Therefore, it makes no sense to have checked arithmetic in such a place.
It is recommended to have the increment value inside the unchecked block to save some gas.

# CHEAPER INEQUALITIES IN REQUIRE()

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L72-L72
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L202-L202

The contract was found to be performing comparisons using inequalities inside the require statement. When inside the require statements, non-strict inequalities (>=, <=) are usually costlier than strict equalities (>, <).
It is recommended to go through the code logic, and, if possible, modify the non-strict inequalities with the strict ones to save ~3 gas as long as the logic of the code is not affected.

# ARRAY LENGTH CACHING

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204-L209

During each iteration of the loop, reading the length of the array uses more gas than is necessary. In the most favorable scenario, in which the length is read from a memory variable, storing the array length in the stack can save about 3 gas per iteration. In the least favorable scenario, in which external calls are made during each iteration, the amount of gas wasted can be significant.
The following array was detected to be used inside loop without caching it's value in memory: _factoryDeps.

Consider storing the array length of the variable before the loop and use the stored length instead of fetching it in each iteration.

# LONG REQUIRE/REVERT STRINGS

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L171-L171
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L185-L188
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L216-L219
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L222-L222
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L223-L226

The require() and revert() functions take an input string to show errors if the validation fails.
This strings inside these functions that are longer than 32 bytes require at least one additional MSTORE, along with additional overhead for computing memory offset, and other parameters.
It is recommended to short the strings passed inside require() and revert() to fit under 32 bytes. This will decrease the gas usage at the time of deployment and at runtime when the validation condition is met.

---
# L1 contract - Other
# CODE OPTIMIZATION BY USING MAX AND MIN

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L26-L26

The contract was using the expression 2**16 to calculate the maximum storage capacity of the data type. This both lacks readability and costs more gas.

2**16 can be replaced by type(uint16).max; or type(uint16).min; which is more readable and will also save some gas.
However, keep in mind that type(uint16).max; is equal to 2**16 - 1

# DEFINE CONSTRUCTOR AS PAYABLE

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L31-L33

Developers can save around 10 opcodes and some gas if the constructors are defined as payable.
However, it should be noted that it comes with risks because payable constructors can accept ETH during deployment.

It is suggested to mark the constructors as payable to save some gas. Make sure it does not lead to any adverse effects in case an upgrade pattern is involved.

# SPLITTING REQUIRE STATEMENTS

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L41-L41

Require statements when combined using operators in a single statement usually lead to a larger deployment gas cost but with each runtime calls, the whole thing ends up being cheaper by some gas units.

It is recommended to separate the require statements with one statement/validation per line.

# INTERNAL FUNCTIONS NEVER USED

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol#L33-L38
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol#L26-L31
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol#L19-L24
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol#L40-L45
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UncheckedMath.sol#L17-L21
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L39-L44
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L21-L33
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L60-L72

The contract declared internal functions but was not using them in any of the functions or contracts.
Since internal functions can only be called from inside the contracts, it makes no sense to have them if they are not used. This uses up gas and causes issues for auditors when understanding the contract logic.

Having dead code in the contracts uses up unnecessary gas and increases the complexity of the overall smart contract.
It is recommended to remove the internal functions from the contracts if they are never used.

# STORAGE VARIABLE CACHING IN MEMORY
The contract AllowList is using the state variable getAccessMode multiple times in the function _setAccessMode.
The contract AllowList is using the state variable hasSpecialAccessToCall multiple times in the function _setPermissionToCall.
The contract AllowList is using the state variable tokenDeposit multiple times in the function setDepositLimit.
SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).

Storage variables read multiple times inside a function should instead be cached in the memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

---
# L2 contract – System Contracts
# CONSTANT STATE VARIABLES

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L80-L80

The contract has defined state variables whose values are never modified throughout the contract.
The variables whose values never change should be marked as constant to save gas.

Make sure that the values stored in the variables flagged above do not change throughout the contract. If this is the case, then consider setting these variables as constant.

# UNUSED NAMED RETURNS

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L125-L130

Using both named returns and a return statement isn't necessary. Removing unused named return variables can reduce gas usage and improve code clarity.

To save gas and improve code quality, consider using either the named returns or a return statement.

# MULTIPLICATION/DIVISION BY TWO SHOULD USE BIT SHIFTING

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L97-L97

x * 2 is equal to x << 1 and x / 2 is equal to x >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas.

It is recommended to use left and right shift instead of multiplying and dividing by 2 to save some gas.

# SPLITTING REQUIRE STATEMENTS

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol#L77-L77

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L74-L78

Require statements when combined using operators in a single statement usually lead to a larger deployment gas cost but with each runtime calls, the whole thing ends up being cheaper by some gas units.

It is recommended to separate the require statements with one statement/validation per line.

# ABI ENCODE IS LESS EFFICIENT THAN ABI ENCODEPACKED

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L130-L130
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L198-L198
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L374-L374
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L110-L110
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L125-L125
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L168-L168
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L217-L217
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L231-L231
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L248-L248
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L265-L265
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L120-L120
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L139-L139

The contract is using abi.encode() in the function _encodeHashEIP712Transaction. In abi.encode(), all elementary types are padded to 32 bytes and dynamic arrays include their length, whereas abi.encodePacked() will only use the minimal required memory to encode the data.
Unless explicitly needed , it is recommended to use abi.encodePacked() instead of abi.encode().

# FUNCTION SHOULD BE EXTERNAL

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L82-L97
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L65-L76


A function with public visibility modifier was detected that is not called internally.
public and external differs in terms of gas usage. The former use more than the latter when used with large arrays of data. This is due to the fact that Solidity copies arguments to memory on a public function while external read from calldata which a cheaper than memory allocation.

If you know the function you create only allows for external calls, use the external visibility modifier instead of public. It provides performance benefits and you will save on gas.

# CODE OPTIMIZATION BY USING MAX AND MIN

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L62-L62
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L28-L28
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L31-L31
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L87-L87

The contract was using the expression 2**16 to calculate the maximum storage capacity of the data type. This both lacks readability and costs more gas.

2**16 can be replaced by type(uint16).max; or type(uint16).min; which is more readable and will also save some gas.
However, keep in mind that type(uint16).max; is equal to 2**16 - 1

# INTERNAL FUNCTIONS NEVER USED
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L26-L31
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L202-L207
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L323-L328
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L265-L267
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L212-L217
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L151-L163
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L297-L303
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L256-L258
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L88-L95
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L32-L36
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L94-L96

The contract declared internal functions but was not using them in any of the functions or contracts.
Since internal functions can only be called from inside the contracts, it makes no sense to have them if they are not used. This uses up gas and causes issues for auditors when understanding the contract logic.

Having dead code in the contracts uses up unnecessary gas and increases the complexity of the overall smart contract.
It is recommended to remove the internal functions from the contracts if they are never used.

# CHEAPER INEQUALITIES IN IF()
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L102-L102
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L132-L132
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L115-L115

The contract was found to be doing comparisons using inequalities inside the if statement.
When inside the if statements, non-strict inequalities (>=, <=) are usually cheaper than the strict equalities (>, <).
It is recommended to go through the code logic, and, if possible, modify the strict inequalities with the non-strict ones to save ~3 gas as long as the logic of the code is not affected.

# CUSTOM ERRORS TO SAVE GAS
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L241-L241
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L163-L163
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L165-L165
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L37-L37
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L365-L365
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L112-L112
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L388-L388

The contract was found to be using revert() statements. Since Solidity v0.8.4, custom errors have been introduced which are a better alternative to the revert.
This allows the developers to pass custom errors with dynamic data while reverting the transaction and also making the whole implementation a bit cheaper than using revert.
It is recommended to replace all the instances of revert() statements with error() to save gas.

# CHEAPER INEQUALITIES IN REQUIRE()
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L62-L62
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L126-L126
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L100-L100
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L186-L186
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L41-L41
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L66-L66
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L323-L323
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L206-L206
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L303-L303
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L21-L21
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L27-L27
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L33-L33
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L363-L363
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L368-L368

The contract was found to be performing comparisons using inequalities inside the require statement. When inside the require statements, non-strict inequalities (>=, <=) are usually costlier than strict equalities (>, <).

It is recommended to go through the code logic, and, if possible, modify the non-strict inequalities with the strict ones to save ~3 gas as long as the logic of the code is not affected.

# GAS OPTIMIZATION FOR STATE VARIABLES
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L65-L65
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L455-L455

Plus equals (+=) costs more gas than addition operator. The same thing happens with minus equals (-=). Therefore, x +=y costs more gas than x = x + y.

# VARIABLES DECLARED BUT NEVER USED
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L37-L37
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L385-L385

The contract SystemContext has declared a variable prevBatchHash but it is not used anywhere in the code. This represents dead code or missing logic.
Unused variables increase the contract's size and complexity, potentially leading to higher gas costs and a larger attack surface.

To remediate this vulnerability, developers should perform a code review and remove any variables that are declared but never used.

# CHEAPER CONDITIONAL OPERATORS
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol#L24-L24
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L102-L102
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L216-L216
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L258-L258
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L326-L326
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L384-L384
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L123-L123
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L304-L304
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L333-L333
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L340-L340

During compilation, x != 0 is cheaper than x > 0 for unsigned integers in solidity inside conditional statements.
Consider using x != 0 in place of x > 0 in uint wherever possible.

# LONG REQUIRE/REVERT STRINGS

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L126-L126
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L163-L163
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L192-L192
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L35-L35
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol#L22-L22
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L100-L100
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L204-L204
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L33-L38
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L66-L66
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L37-L37
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L48-L48
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L57-L57
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L213-L213
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L216-L216
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L258-L258
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L322-L325
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L326-L326
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L338-L338
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L339-L339
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L340-L343
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L344-L344
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L356-L356
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L357-L360
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L384-L384
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L401-L404
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L424-L424
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol#L77-L77
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L219-L222
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L250-L253
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L274-L277
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L284-L288
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L320-L320
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L74-L78
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L239-L242
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L251-L251
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L265-L265
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L351-L351
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L335-L335
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L363-L363
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L367-L370

The require() and revert() functions take an input string to show errors if the validation fails.
This strings inside these functions that are longer than 32 bytes require at least one additional MSTORE, along with additional overhead for computing memory offset, and other parameters.

It is recommended to short the strings passed inside require() and revert() to fit under 32 bytes. This will decrease the gas usage at the time of deployment and at runtime when the validation condition is met.

# STORAGE VARIABLE CACHING IN MEMORY
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L20-L20
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L36-L36
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L28-L28
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L23-L23
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L77-L77
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L80-L80
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L61-L61
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L36-L36
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L40-L40
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L44-L44
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L49-L49

The contract L2EthToken is using the state variable balance multiple times in the function transferFromTo ,
rawNonces multiple times in the function increaseMinNonce,
DEPLOY_NONCE_MULTIPLIER multiple times in the function _splitRawNonce,
currentVirtualL2BlockInfo multiple times in the function _setVirtualBlock,
virtualBlockUpgradeInfo multiple times in the function _setVirtualBlock,
currentL2BlockTxsRollingHash multiple times in the function appendTransactionToCurrentL2Block,
chainedLogsHash multiple times in the function _processL2ToL1Log,
numberOfLogsToProcess multiple times in the function _processL2ToL1Log,
chainedMessagesHash multiple times in the function sendToL1,
chainedL1BytecodesRevealDataHash multiple times in the function requestBytecodeL1Publication,
chainedLogsHash multiple times in the function publishPubdataAndClearState,
chainedMessagesHash multiple times in the function publishPubdataAndClearState,
chainedL1BytecodesRevealDataHash multiple times in the function requestBytecodeL1Publication.

SLOADs are expensive (100 gas after the 1st one) compared to MLOAD/MSTORE (3 gas each).

Storage variables read multiple times inside a function should instead be cached in the memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

---
# L2 contract - Bridges
# ABI ENCODE IS LESS EFFICIENT THAN ABI ENCODEPACKED

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L132-L132
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L154-L154

The contract is using abi.encode() in the function l2TokenAddress. In abi.encode(), all elementary types are padded to 32 bytes and dynamic arrays include their length, whereas abi.encodePacked() will only use the minimal required memory to encode the data.

Unless explicitly needed , it is recommended to use abi.encodePacked() instead of abi.encode().

# DEFINE CONSTRUCTOR AS PAYABLE
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L29-L32
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L36-L38
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L36-L39
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L36-L38

Developers can save around 10 opcodes and some gas if the constructors are defined as payable.
However, it should be noted that it comes with risks because payable constructors can accept ETH during deployment.

It is suggested to mark the constructors as payable to save some gas. Make sure it does not lead to any adverse effects in case an upgrade pattern is involved.

# CUSTOM ERRORS TO SAVE GAS
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L71-L71
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L125-L125
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L131-L131
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L137-L137
The contract was found to be using revert() statements. Since Solidity v0.8.4, custom errors have been introduced which are a better alternative to the revert.
This allows the developers to pass custom errors with dynamic data while reverting the transaction and also making the whole implementation a bit cheaper than using revert.

It is recommended to replace all the instances of revert() statements with error() to save gas

# CHEAPER CONDITIONAL OPERATORS
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L69-L69

During compilation, x != 0 is cheaper than x > 0 for unsigned integers in solidity inside conditional statements.
Consider using x != 0 in place of x > 0 in uint wherever possible.

# LONG REQUIRE/REVERT STRINGS
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L54-L54
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L71-L71
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L50-L50
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L51-L51
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L52-L52
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L95-L98
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L73-L73

The require() and revert() functions take an input string to show errors if the validation fails.
This strings inside these functions that are longer than 32 bytes require at least one additional MSTORE, along with additional overhead for computing memory offset, and other parameters.

It is recommended to short the strings passed inside require() and revert() to fit under 32 bytes. This will decrease the gas usage at the time of deployment and at runtime when the validation condition is met.

# STORAGE VARIABLE CACHING IN MEMORY
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L32-L32
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L27-L27
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L26-L26
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L32-L32

The contract L2WethBridge is using the state variable l2WethAddress multiple times in the function withdraw.
The contract L2StandardERC20 is using the state variable decimals_ multiple times in the function bridgeInitialize.
The contract L2ERC20Bridge is using the state variable l2TokenBeacon multiple times in the function initialize.
The contract L2ERC20Bridge is using the state variable l1TokenAddress multiple times in the function finalizeDeposit.

Storage variables read multiple times inside a function should instead be cached in the memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

---
# L2 contract - Other
# CHEAPER INEQUALITIES IN REQUIRE()

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/SystemContractsCaller.sol#L28-L28

The contract was found to be performing comparisons using inequalities inside the require statement. When inside the require statements, non-strict inequalities (>=, <=) are usually costlier than strict equalities (>, <).

It is recommended to go through the code logic, and, if possible, modify the non-strict inequalities with the strict ones to save ~3 gas as long as the logic of the code is not affected.

# INTERNAL FUNCTIONS NEVER USED
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/L2ContractHelper.sol#L105-L117
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/L2ContractHelper.sol#L94-L96
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/SystemContractsCaller.sol#L81-L98
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/vendor/AddressAliasHelper.sol#L38-L42
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/vendor/AddressAliasHelper.sol#L28-L32

The contract declared internal functions but was not using them in any of the functions or contracts.
Since internal functions can only be called from inside the contracts, it makes no sense to have them if they are not used. This uses up gas and causes issues for auditors when understanding the contract logic.

Having dead code in the contracts uses up unnecessary gas and increases the complexity of the overall smart contract.
It is recommended to remove the internal functions from the contracts if they are never used. 
