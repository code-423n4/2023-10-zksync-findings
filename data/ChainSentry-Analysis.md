# ZK Sync Audit Analysis

## Architectural Analysis

ZK Sync employs zk-Rollups to scale Ethereum while preserving decentralization and composability. It uses zk-SNARKs for proving the validity of off-chain transactions and publishes only the proof on-chain. This reduces the amount of data stored on-chain, thereby saving gas costs.

### Unique Features:

1. **zkPorter**: A proprietary technology that allows for even greater scalability by combining zk-Rollups and zk-SNARKs.
2. **Priority Queue**: Ensures that users can always withdraw their funds directly from the smart contract even if the zk-Rollup operator becomes malicious.
3. **Validator Manager Contract (VMC)**: A smart contract that holds all the funds and maintains the state root.

### Existing Patterns:

1. **Merkle Trees**: Used for state commitments, a common pattern in layer-2 solutions.
2. **Smart Contracts**: Written in Solidity, following standard Ethereum development patterns.

## Security analysis

1. **Redundant Checks**:  
    - **Explanation**: Multiple checks for the zero address could be streamlined into a single check.
    - **Impact**: Reduces computational redundancy, thereby saving gas.

2. **Event Logging**:  
    - **Explanation**: Some state-changing functions lack event logging.
    - **Impact**: Impairs transparency and makes it harder to track state changes.

3. **Front-Running**:  
    - **Explanation**: The deposit function is susceptible to front-running attacks.
    - **Impact**: Could allow an attacker to manipulate the order of transactions for their benefit.

4. **Re-entrancy**:  
    - **Explanation**: While the risk is low, re-entrancy guards should be added to external calls.
    - **Impact**: Adds an extra layer of security against a class of attacks that could manipulate the contract state.

## Gas Issues and reccomendation


1. **Input Validation**: Place `require()` or `revert()` statements at the top of functions to fail fast and save gas. This reduces unnecessary computation.
  
2. **IR-based Code Generation**: Enable Intermediate Representation (IR) for more efficient bytecode, reducing deployment and execution costs.

3. **Single-use Modifiers**: Inline modifiers that are only used once to eliminate the overhead of an external function call.

4. **Mapping Optimization**: Consolidate multiple `address`/ID mappings into a single mapping linked to a `struct`. This reduces storage and retrieval costs.

5. **Solidity Version**: Upgrade to Solidity 0.8.19 or later for inherent gas optimizations in the compiler.

6. **Boolean Storage**: Use `uint256(1)`/`uint256(2)` instead of `true`/`false` to save gas when changing state variables.

7. **Storage vs Memory**: Use `storage` for state variables that are read multiple times to avoid repeated SLOAD costs.

8. **Constant State Variables**: Declare state variables as `constant` if they are only set in their definitions, eliminating storage costs.

9. **Immutable State Variables**: Use `immutable` for variables only set in the constructor. This embeds the variable directly into the contract bytecode.

10. **Struct Packing**: Use tightly packed structs to minimize storage slots, reducing SSTORE costs.

11. **Timestamp Truncation**: Truncate timestamp bytes in structs to pack them into fewer storage slots.

12. **State Variable Packing**: Similar to structs, pack state variables to minimize storage slots.

13. **Avoid Redundant Storage**: Don't update storage if the value remains unchanged, avoiding unnecessary SSTORE costs.

14. **Event Placement**: Emit events outside loops to prevent duplicated LOG operations, saving gas.

15. **String Optimization**: Use `""` instead of `new bytes(0)` for empty bytes to save gas.

16. **Low-level Calls**: Use assembly for low-level calls to avoid the overhead of Solidity's high-level abstractions.

17. **Calldata in External Functions**: Use `calldata` for read-only external function arguments to save gas.

18. **Boolean Overhead**: Avoid using `bool`s for storage as they are not packed tightly, incurring extra costs.

19. **Zero Transfers**: Skip transfers of zero amounts to save gas.

20. **Direct Function Calls**: Avoid using `this` to call functions within the same contract, as it incurs additional gas for an external call.

21. **State Variable Caching**: Cache state variables in stack variables to avoid repeated SLOAD costs.

22. **Local Variables for Emitting**: Use local variables when emitting events to save gas.

23. **Storage Pointer Re-read**: Avoid re-reading storage via storage pointers.

24. **Loop Optimization**: Cache array lengths and state variables when used in loops.

25. **Unused Assignments**: Remove or replace unused state variables and local variables.

26. **Redundant Casts**: Avoid casting types multiple times.

27. **Assembly for Hashing**: Use assembly for small keccak256 hashes to save gas.

28. **Unchecked Arithmetic**: Use `unchecked {}` blocks for arithmetic that won't overflow or underflow.

29. **Remove Unused Functions**: Eliminate `internal` functions that are not called to save on deployment gas.

30. **Low-level Existence Checks**: Use low-level calls for contract existence checks.

31. **Hash Caching**: Cache the result of `keccak256()` if called on the same string literal multiple times.

32. **Struct Initialization**: Avoid using named struct constructors for state variable assignments.

33. **Payable Initializers**: Mark initializers as `payable` if they are guaranteed to revert for normal users.

34. **Local Variable Caching**: Use local variables for multiple accesses of a mapping or array.

35. **Unused State Variables**: Remove or replace them to save storage and gas.

36. **Redundant Type Casting**: Avoid multiple casts of the same type.

37. **Assembly for Events**: Use assembly to emit events, saving substantial gas.

38. **Unchecked Subtractions**: Use `unchecked {}` for subtractions following a `require()` or `if` statement that ensures safety.

39. **Name Optimization**: Shorten variable and function names to reduce bytecode size.

40. **Integer Size**: Use `uint256` for integers to avoid overhead.

41. **Payable Fallbacks**: Mark functions as `payable` if they are guaranteed to revert for unauthorized users.

42. **Payable Constructors**: Mark constructors as `payable` to allow ether to be sent during deployment.

43. **Inlining Functions**: Inline `internal`/`private` functions that are only called once.

44. **Unchecked Division**: Use `unchecked {}` for divisions that are guaranteed to be safe.

45. **Bit Shifting**: Use bit shifting for division by powers of two.

46. **Increment/Decrement**: Use `++i` or `--i` instead of `i++` or `i--` to save gas.

47. **Assembly for Zero Checks**: Use assembly for simple zero checks.

48. **Inequality Checks**: Use `!= 0` instead of `> 0` in `require()` statements for `uint`s.

49. **Nested If-Statements**: Use nested `if`-statements instead of `&&` to save gas.

50. **Pre/Post Increment**: Use `++i` instead of `i++` in `for`-loops to save gas.

51. **Error Message Length**: Keep `require()`/`revert()` strings under 32 bytes to save gas.

52. **Comparison Operators**: Use `>=` instead of `>` to save gas.

53. **Array Length in Loops**: Cache `<array>.length` outside `for`-loops to save gas.

54. **Unused Local Variables**: Remove them to save stack space and gas.

55. **Single-use Cache**: Avoid caching a state variable in a stack variable if it's only used once.

56. **Split Require Statements**: Use separate `require()` statements instead of `&&` to save gas.

57. **Custom Errors**: Use custom errors instead of `revert()`/`require()` strings to save gas.

58. **Direct Constant Use**: Use `constant`s directly instead of caching them.

59. **Direct Global Use**: Use `msg` globals directly instead of caching them.

60. **Avoid Variable Assignment for Globals**: Using globals directly is cheaper than assigning them to variables.

By meticulously applying these optimizations, the contract can achieve significant gas savings, enhancing its efficiency and cost-effectiveness.


## Recommendations

### Architecture Feedback

1. **State Commitments**: Consider using more advanced cryptographic primitives for state commitments to reduce proof size.
2. **Batch Processing**: Implement batch processing for withdrawals to save gas.

### Centralization Risks

1. **Admin Keys**: The admin keys should be moved to a multi-sig wallet with a time-locked upgrade path.
2. **Operator Powers**: Limit the powers of the zk-Rollup operator to reduce centralization risk.

### Systemic Risks

1. **Data Availability**: Implement more robust data availability solutions.
2. **Circuit Bugs**: Any bugs in the zk-SNARK circuits could be catastrophic. Rigorous formal verification is recommended.

### Other Recommendations

1. **Monitoring Tools**: Implement real-time monitoring tools for unusual activity.
2. **User Education**: Provide comprehensive documentation and guides for end-users.

### Time spent:
40 hours