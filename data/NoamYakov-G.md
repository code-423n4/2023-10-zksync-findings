## Summary
### Gas Optimization Issues
|Title|Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
|[G-1] Unnecessary require statement in `DiamondProxy`'s `fallback` function | [Unnecessary require statement in `DiamondProxy`'s `fallback` function](#unnecessary-require-statement-in-diamondproxys-fallback-function) | 1 | - |
|[G-2] Delete storage variables to earn gas refund | [Delete storage variables to earn gas refund](#delete-storage-variables-to-earn-gas-refund) | 1 | - |
|[G-3] Addition operations that could be unchecked | [Addition operations that could be unchecked](#addition-operations-that-could-be-unchecked) | 2 | 170 |
|[G-4] Subtraction operations that could be unchecked | [Subtraction operations that could be unchecked](#subtraction-operations-that-could-be-unchecked) | 3 | 255 |
|[G-5] Cache `calldatasize()` in `DiamondProxy`'s `fallback` function | [Cache `calldatasize()` on `DiamondProxy`'s `fallback` function](#cache-calldatasize-on-diamondproxys-fallback-function) | 1 | - |
|[G-6] Division operations between unsigned could be unchecked | [Division operations between unsigned could be unchecked](#division-operations-between-unsigned-could-be-unchecked) | 2 | 170 |
|[G-7] Modulus operations that could be unchecked | [Modulus operations that could be unchecked](#modulus-operations-that-could-be-unchecked) | 2 | 170 |
|[G-8] Inefficient use of abi.encode() | [Inefficient use of abi.encode()](#inefficient-use-of-abiencode) | 9 | 900 |
|[G-9] Optimizing Small Data Storage with Bytes32 | [Optimizing Small Data Storage with Bytes32](#optimizing-small-data-storage-with-bytes32) | 5 | 1890 |
|[G-10] Check Arguments Early | [Check Arguments Early](#check-arguments-early) | 2 | - |
|[G-11] Constants Variable Should Be Private for Gas Optimization | [Constants Variable Should Be Private for Gas Optimization](#constants-variable-should-be-private-for-gas-optimization) | 5 | 17000 |
|[G-12] Cache External Calls in Loops | [Cache External Calls in Loops](#cache-external-calls-in-loops) | 1 | 100 |
|[G-13] Inefficient Bytes Array Initialization | [Inefficient Bytes Array Initialization](#inefficient-bytes-array-initialization) | 2 | - |
|[G-14] Use Small Integer For Efficiency | [Use Small Integer For Efficiency](#use-small-integer-for-efficiency) | 2 | 12 |
|[G-15] Redundant Contract Existence Check in Consecutive External Calls | [Redundant Contract Existence Check in Consecutive External Calls](#redundant-contract-existence-check-in-consecutive-external-calls) | 1 | 100 |
|[G-16] Initialize Variables With Default Value | [Initialize Variables With Default Value](#initialize-variables-with-default-value) | 14 | 84 |
|[G-17] Use assembly to write address storage values | [Use assembly to write address storage values](#use-assembly-to-write-address-storage-values) | 16 | 1232 |

Total: 69 instances over 17 issues

### OOS Gas Optimization Issues [Found by Winning Bot]
|Title|Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
|[G-18] Use assembly to emit events | [Use assembly to emit events](#use-assembly-to-emit-events) | 45 | 1710 |
|[G-19] Avoid unnecessary storage updates | [Avoid unnecessary storage updates](#avoid-unnecessary-storage-updates) | 10 | 8000 |
|[G-20] Multiplication and Division by 2 Should use in Bit Shifting | [Multiplication and Division by 2 Should use in Bit Shifting](#multiplication-and-division-by-2-should-use-in-bit-shifting) | 3 | 60 |
|[G-21] Using bools for storage incurs overhead | [Using bools for storage incurs overhead](#using-bools-for-storage-incurs-overhead) | 3 | 51300 |
|[G-22] Caching Global Variable is Inefficiency | [Caching Global Variable is Inefficiency](#caching-global-variable-is-inefficiency) | 2 | 24 |
|[G-23] Caching msg.sender Variable is Inefficiency | [Caching msg.sender Variable is Inefficiency](#caching-msgsender-variable-is-inefficiency) | 4 | 48 |
|[G-24] Potential Optimization by Combining Multiple Mappings into a Struct | [Potential Optimization by Combining Multiple Mappings into a Struct](#potential-optimization-by-combining-multiple-mappings-into-a-struct) | 2 | 1000 |
|[G-25] Use of emit inside a loop | [Use of emit inside a loop](#use-of-emit-inside-a-loop) | 5 | 5130 |
|[G-26] Functions Guaranteed to Revert When Called by Normal Users Should Be Marked Payable | [Functions Guaranteed to Revert When Called by Normal Users Should Be Marked Payable](#functions-guaranteed-to-revert-when-called-by-normal-users-should-be-marked-payable) | 30 | 630 |
|[G-27] Use a More Recent Version of Solidity | [Use a More Recent Version of Solidity](#use-a-more-recent-version-of-solidity) | 37 | - |
|[G-28] Inefficient Parameter Storage | [Inefficient Parameter Storage](#inefficient-parameter-storage) | 10 | 500 |
|[G-29] Using Storage Instead of Memory for structs/arrays Saves Gas | [Using Storage Instead of Memory for structs/arrays Saves Gas](#using-storage-instead-of-memory-for-structsarrays-saves-gas) | 6 | 25200 |
|[G-30] Internal or Private Function that Called Once Should Be Inlined to Save Gas | [Internal or Private Function that Called Once Should Be Inlined to Save Gas](#internal-or-private-function-that-called-once-should-be-inlined-to-save-gas) | 32 | 640 |
|[G-31] Inefficient Gas Usage in Solidity Smart Contracts Due to Long Error Messages | [Inefficient Gas Usage in Solidity Smart Contracts Due to Long Error Messages](#inefficient-gas-usage-in-solidity-smart-contracts-due-to-long-error-messages) | 21 | 378 |
|[G-32] Declare Constructor Function as Payable to Save Gas | [Declare Constructor Function as Payable to Save Gas](#declare-constructor-function-as-payable-to-save-gas) | 7 | 147 |
|[G-33] Optimize address(0) Checks Using Assembly | [Optimize address(0) Checks Using Assembly](#optimize-address0-checks-using-assembly) | 5 | 290 |
|[G-34] Optimize Names to Save Gas | [Optimize Names to Save Gas](#optimize-names-to-save-gas) | 8 | 176 |
|[G-35] Optimal Struct Variable Order | [Optimal Struct Variable Order](#optimal-struct-variable-order) | 3 | 15000 |
|[G-36] Consider activating via-ir for deploying | [Consider activating via-ir for deploying](#consider-activating-via-ir-for-deploying) | 1 | - |
|[G-37] State variable access within a loop | [State variable access within a loop](#state-variable-access-within-a-loop) | 6 | 1590 |
|[G-38] Cache Array Length Outside of Loop | [Cache Array Length Outside of Loop](#cache-array-length-outside-of-loop) | 7 | 21 |
|[G-39] State variables should be cached in stack variables rather than re-reading them from storage | [State variables should be cached in stack variables rather than re-reading them from storage](#state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage) | 26 | 2522 |
|[G-40] Unnecessary Casting of Variables | [Unnecessary Casting of Variables](#unnecessary-casting-of-variables) | 7 | - |
|[G-41] Detection of Unnecessary Checked Increments in Solidity Loop Statements | [Detection of Unnecessary Checked Increments in Solidity Loop Statements](#detection-of-unnecessary-checked-increments-in-solidity-loop-statements) | 2 | 120 |
|[G-42] Use Assembly for Hash Calculation | [Use Assembly for Hash Calculation](#use-assembly-for-hash-calculation) | 16 | 1280 |
|[G-43] Usage of Custom Errors for Gas Efficiency | [Usage of Custom Errors for Gas Efficiency](#usage-of-custom-errors-for-gas-efficiency) | 121 | 33396 |

Total: 419 instances over 26 issues

#


## Unnecessary require statement in `DiamondProxy`'s `fallback` function
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: -

### Description
There's no reason to require `msg.data.length` to be equal to 0 or greater than 3. `msg.sig` is defined when `msg.data.length` is 1, 2 or 3. It's simply padded with zero bytes from the right if necessary. Such a require statement is unnecessary and is a large waste of gas. `DiamondProxy`'s `fallback` function is the entry to the protocol, so this optimization will save gas on every call to the protocol!

<details>

<summary>
There is 1 instance of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

25        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
```
Delete this unnecessay require statement.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L25](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L25)

</details>

# 


## Delete storage variables to earn gas refund
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: -

### Description
Storage variables should be deleted (set to zero) when are no longer relevant in order to earn gas refund.

<details>

<summary>
There is 1 instance of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

262    function _removeFacet(address _facet) private {
```
This function should delete `ds.facetToSelectors[_facet]`.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L262](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L262)

</details>

# 


## Addition operations that could be unchecked
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 170

### Description
Addition operations that have no risk of overflow should be unchecked to save gas. Saves about 85 gas.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

           // This addition was already calculated with checked arithmetic in line 279
280        s.totalDepositedAmountPerUser[_depositor] += _amount;
```
Execute this addition operation inside an `unchecked` block.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L280](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L280)

#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

           // `block.timestamp` and `PRIORITY_EXPIRATION` are too small for an overflow
295        uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast
```
Execute this addition operation inside an `unchecked` block.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L295](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L295)

</details>

# 


## Subtraction operations that could be unchecked
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 255

### Description
Subtraction operations that have no risk of underflow should be unchecked to save gas. Saves about 85 gas.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

           // This function is only called when `ds.facetToSelectors[_facet].selectors` isn't empty
238        uint256 lastSelectorPosition = ds.facetToSelectors[_facet].selectors.length - 1;
```
Execute this subtraction operation inside an `unchecked` block.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L238](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L238)

#

```
File: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

           // This function is only called when `ds.facets` isn't empty
267        uint256 lastFacetPosition = ds.facets.length - 1;
```
Execute this subtraction operation inside an `unchecked` block.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L267](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L267)

#

```
File: code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol

          // `_queue.tail` is always greater than or equal to `_queue.head`
46        return uint256(_queue.tail - _queue.head);
```
Execute this subtraction operation inside an `unchecked` block.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L46](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L46)

</details>

# 


## Cache `calldatasize()` on `DiamondProxy`'s `fallback` function
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: -

### Description
There's no reason to call `calldatasize()` twice. You should cache its result. `DiamondProxy`'s `fallback` function is the entry to the protocol, so this optimization will save gas on every call to the protocol!

<details>

<summary>
There is 1 instance of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

              // `calldatasize()` is also called on line 37
39            let result := delegatecall(gas(), facetAddress, ptr, calldatasize(), 0, 0)
```
Call `calldatasize()` once and cache its result.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L39](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L39)

</details>

# 


## Division operations between unsigned could be unchecked
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 170

### Description
Division operations on unsigned integers should be unchecked to save gas since they cannot overflow or underflow. Because unsigned integers cannot have negative values, execution of division operations outside `unchecked` blocks adds nothing but overhead. Saves about 85 gas.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

25        uint256 bytecodeLenInWords = _bytecode.length / 32;
```
Execute division on unsigned integers operations inside `unchecked` blocks.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L25](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L25)

#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

181        uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;
```
Execute division on unsigned integers operations inside `unchecked` blocks.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L181](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L181)

</details>

# 


## Modulus operations that could be unchecked
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 170

### Description
Modulus operations should be unchecked to save gas since they cannot overflow or underflow. Execution of modulus operations outside `unchecked` blocks adds nothing but overhead.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

23        require(_bytecode.length % 32 == 0, "pq");
```
Execute modulus operations inside `unchecked` blocks.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L23](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L23)

#

```
File: code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

27        require(bytecodeLenInWords % 2 == 1, "ps"); // bytecode length in words must be odd
```
Execute modulus operations inside `unchecked` blocks.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L27](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L27)

</details>

# 


## Inefficient use of abi.encode()
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 900

### Description
The `abi.encode()` function is less gas efficient than `abi.encodePacked()`, especially when encoding only static types. This detector identifies instances where `abi.encode()` is used and all arguments are static, suggesting that `abi.encodePacked()` could potentially be used instead for better gas efficiency. Note: `abi.encodePacked()` should not be used if any of the arguments are dynamic types, as it could lead to hash collisions.

<details>

<summary>
There are 9 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

356    bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""))
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L356](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L356)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

207    return keccak256(abi.encode(_operation))
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L207](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L207)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

175    bytes memory encodedTransaction = abi.encode(_l2ProtocolUpgradeTx)
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L175](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L175)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

79    s.storedBatchHashes[0] = keccak256(abi.encode(storedBatchZero))
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L79](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L79)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

267    concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash))
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L267](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L267)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

424    return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash))
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L424](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L424)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

450    return
451                abi.encode(
452                    l2ToL1LogsHash,
453                    _stateDiffHash,
454                    _batch.bootloaderHeapInitialContentsHash,
455                    _batch.eventsQueueStateHash
456                )
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L450-L456](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L450-L456)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

461    return keccak256(abi.encode(_storedBatchInfo))
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L461](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L461)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

365    bytes memory transactionEncoding = abi.encode(transaction)
```
 use  abi.encodepacked() instead.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L365](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L365)


</details>

# 


## Optimizing Small Data Storage with Bytes32
- Severity: Gas Optimization
- Confidence: Medium
- Total Gas Saved: 1890

### Description
This detector flags contracts that inefficiently use bytes and strings for small data that could fit into bytes32. Using bytes32 is cheaper in Solidity when the data can fit into 32 bytes.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

17    string public constant override getName = "AdminFacet"
```
 should be convert to `bytes32`
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L17](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L17)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Getters.sol

21    string public constant override getName = "GettersFacet"
```
 should be convert to `bytes32`
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L21](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L21)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

26    string public constant override getName = "ValidatorTimelock"
```
 should be convert to `bytes32`
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L26](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L26)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

24    string public constant override getName = "ExecutorFacet"
```
 should be convert to `bytes32`
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L24)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

43    string public constant override getName = "MailboxFacet"
```
 should be convert to `bytes32`
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L43](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L43)


</details>

# 


## Check Arguments Early
- Severity: Gas Optimization
- Confidence: High

### Description
Checks that require() or revert() statements that check input arguments are at the top of the function. Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a gas load in a function that may ultimately revert in the unhappy case.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

187    require(
188                _l2ProtocolUpgradeTx.nonce == _newProtocolVersion,
189                "The new protocol version should be included in the L2 system upgrade tx"
190            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L187-L190](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L187-L190)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

259    require(_l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp")
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L259](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L259)


</details>

# 


## Constants Variable Should Be Private for Gas Optimization
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 17000

### Description
This detector flags contracts that inefficiently use `public` for constant variables. Using `private` for constant variables is cheaper in terms of gas usage. If the value of the constant variable is needed, it can be accessed via a getter function. In case of multiple constant variables, a single getter function could be used to return a tuple of the values of all currently-private constants.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

17    string public constant override getName = "AdminFacet"
```
 `getName` is a public. Consider making it private to save gas.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L17](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L17)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Getters.sol

21    string public constant override getName = "GettersFacet"
```
 `getName` is a public. Consider making it private to save gas.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L21](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L21)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

26    string public constant override getName = "ValidatorTimelock"
```
 `getName` is a public. Consider making it private to save gas.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L26](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L26)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

24    string public constant override getName = "ExecutorFacet"
```
 `getName` is a public. Consider making it private to save gas.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L24)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

43    string public constant override getName = "MailboxFacet"
```
 `getName` is a public. Consider making it private to save gas.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L43](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L43)


</details>

# 


## Cache External Calls in Loops
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 100

### Description
This detector identifies instances of repeated external calls within loops. By moving the first external call outside of the loop and using low-level calls inside the loop, it is possible to save gas by avoiding repeated EXTCODESIZE opcodes, as there is no need to check again if the contract exists. 

Note: This detector only triggers if the function call does not return any value.

Prior to 0.8.10, the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent Solidity versions the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low-level calls never check for contract existence. 

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

88    committedBatchTimestamp.set(_newBatchesData[i].batchNumber, timestamp)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L88](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L88)


</details>

# 


## Inefficient Bytes Array Initialization
- Severity: Gas Optimization
- Confidence: High

### Description
Detects the inefficient initialization of dynamic bytes arrays with 0 size. Initializations of the form `new bytes(0)` create a new, empty dynamic bytes array. This is less gas efficient than the alternative ``, which achieves the same result.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

207    txCalldata = abi.encodeCall(
208                IL2Bridge.finalizeDeposit,
209                (_l1Sender, _l2Receiver, _l1Token, _amount, new bytes(0))
210            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L207-L210](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L207-L210)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

336    transaction = L2CanonicalTransaction({
337                txType: PRIORITY_OPERATION_L2_TX_TYPE,
338                from: uint256(uint160(_priorityOpParams.sender)),
339                to: uint256(uint160(_priorityOpParams.contractAddressL2)),
340                gasLimit: _priorityOpParams.l2GasLimit,
341                gasPerPubdataByteLimit: _priorityOpParams.l2GasPricePerPubdata,
342                maxFeePerGas: uint256(_priorityOpParams.l2GasPrice), // @audit g: Unnecessary casting
343                maxPriorityFeePerGas: uint256(0),
344                paymaster: uint256(0),
345                // Note, that the priority operation id is used as "nonce" for L1->L2 transactions
346                nonce: uint256(_priorityOpParams.txId), // @audit g: Unnecessary casting
347                value: _priorityOpParams.l2Value,
348                reserved: [_priorityOpParams.valueToMint, uint256(uint160(_priorityOpParams.refundRecipient)), 0, 0],
349                data: _calldata,
350                signature: new bytes(0),
351                factoryDeps: _hashFactoryDeps(_factoryDeps),
352                paymasterInput: new bytes(0),
353                reservedDynamic: new bytes(0)
354            })
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L336-L354](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L336-L354)


</details>

# 


## Use Small Integer For Efficiency
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 12

### Description
This detector flags contracts that inefficiently use `uint` or `int` of sizes smaller than 32 bytes. The EVM operates on 32 bytes at a time, thus using elements smaller than this may cause your contract's gas usage to be higher. Refer to the Solidity documentation for more details: https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol

24    uint160 constant offset = uint160(0x1111000000000000000000000000000000001111)
```
 `offset` use 256 bites instead of 160

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L24)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

44    uint32 public executionDelay
```
 `executionDelay` use 256 bites instead of 32

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L44](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L44)


</details>

# 


## Redundant Contract Existence Check in Consecutive External Calls
- Severity: Gas Optimization
- Confidence: Medium
- Total Gas Saved: 100

### Description
This detector identifies instances where there are consecutive external calls to the same contract, where the subsequent calls could use a low-level call to save gas.

Note: This detector only triggers if the function call does not return any value. 

Prior to 0.8.10, the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent Solidity versions the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low-level calls never check for contract existence. 

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

183    TransactionValidator.validateUpgradeTransaction(_l2ProtocolUpgradeTx)
```
This call could be replaced with a low-level call because the contract `TransactionValidator` has already been checked in <br>`Line: 177    TransactionValidator.validateL1ToL2Transaction(
            _l2ProtocolUpgradeTx,
            encodedTransaction,
            s.priorityTxMaxGasLimit
        )`<br>
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L183](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L183)


</details>

# 


## Initialize Variables With Default Value
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 84

### Description
It costs more gas to initialize variables to their default values than to let the default be applied.

<details>

<summary>
There are 14 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

64    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L64](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L64)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

98    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L98](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L98)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

227    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L227](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L227)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

206    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L206](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L206)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

87    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L87](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L87)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

118    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L118](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L118)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

125    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L125](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L125)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

211    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L211](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L211)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

243    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L243](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L243)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

265    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L265](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L265)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

295    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L295](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L295)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

332    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L332](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L332)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Getters.sol

184    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L184](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L184)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

397    uint256 i = 0
```
 no need to initialized with default value
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L397](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L397)


</details>

# 


## Use assembly to write address storage values
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 1232

### Description
Using assembly to write address storage values can potentially save gas costs. This detector flags instances where address storage values are written without using assembly.

<details>

<summary>
There are 16 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

99    l2TokenBeacon = _l2TokenBeacon
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L99](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L99)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

125    l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
126                zkSync,
127                _deployBridgeProxyFee,
128                l2BridgeProxyBytecodeHash,
129                l2BridgeProxyConstructorData,
130                // No factory deps are needed for L2 bridge proxy, because it is already passed in previous step
131                new bytes[](0)
132            )
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L125-L132](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L125-L132)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

67    l1WethAddress = _l1WethAddress
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L67](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L67)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

128    l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
129                zkSync,
130                _deployBridgeProxyFee,
131                l2WethBridgeProxyBytecodeHash,
132                l2WethBridgeProxyConstructorData,
133                // No factory deps are needed for L2 bridge proxy, because it is already passed in the previous step
134                new bytes[](0)
135            )
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L128-L135](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L128-L135)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

98    l2WethAddress = _l2WethAddress
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L98](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L98)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

48    securityCouncil = _securityCouncil
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L48](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L48)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

260    securityCouncil = _newSecurityCouncil
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L260](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L260)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

65    s.admin = _initalizeData.admin
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L65](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L65)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

64    s.governor = _initalizeData.governor
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L64](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L64)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

50    validator = _validator
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L50](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L50)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

48    zkSyncContract = _zkSyncContract
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L48](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L48)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

56    validator = _newValidator
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L56](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L56)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

26    s.pendingGovernor = _newPendingGovernor
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L26](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L26)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

36    s.governor = pendingGovernor
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L36](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L36)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

50    s.pendingAdmin = _newPendingAdmin
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L50](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L50)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

60    s.admin = pendingAdmin
```
writes to an address storage value without using assembly.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L60](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L60)


</details>

# 


## Use assembly to emit events
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 1710

### Description
This detector checks for instances where a contract uses assembly code to emit events. While it's technically possible to emit events using inline assembly in Solidity, it's generally discouraged due to readability concerns and potential for errors. Events should usually be emitted using higher-level Solidity constructs.

<details>

<summary>
There are 45 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

213    emit DepositInitiated(l2TxHash, msg.sender, _l2Receiver, _l1Token, amount)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L213](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L213)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

286    emit ClaimedFailedDeposit(_depositSender, _l1Token, amount)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L286](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L286)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

321    emit WithdrawalFinalized(l1Receiver, l1Token, amount)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L321](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L321)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

197    emit DepositInitiated(txHash, msg.sender, _l2Receiver, _l1Token, _amount)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L197](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L197)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

269    emit WithdrawalFinalized(l1WethWithdrawReceiver, l1WethAddress, amount)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L269](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L269)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

314    emit EthReceived(msg.value)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L314](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L314)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

75    emit UpdateAccessMode(_target, accessMode, _accessMode)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L75](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L75)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

123    emit UpdateCallPermission(_caller, _target, _functionSig, _enable)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L123](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L123)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

124    emit NewVerifier(address(oldVerifier), address(_newVerifier))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L124](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L124)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

140    emit NewVerifierParams(oldVerifierParams, _newVerifierParams)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L140](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L140)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

231    emit NewProtocolVersion(previousProtocolVersion, _newProtocolVersion)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L231](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L231)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

91    emit NewL2DefaultAccountBytecodeHash(previousDefaultAccountBytecodeHash, _l2DefaultAccountBytecodeHash)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L91](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L91)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

108    emit NewL2BootloaderBytecodeHash(previousBootloaderBytecodeHash, _l2BootloaderBytecodeHash)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L108](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L108)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

243    emit NewAllowList(address(oldAllowList), address(_newAllowList))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L243](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L243)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

52    emit ChangeMinDelay(0, _minDelay)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L52](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L52)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

49    emit ChangeSecurityCouncil(address(0), _securityCouncil)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L49](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L49)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

134    emit TransparentOperationScheduled(id, _delay, _operation)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L134](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L134)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

146    emit ShadowOperationScheduled(_id, _delay)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L146](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L146)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

159    emit OperationCancelled(_id)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L159](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L159)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

182    emit OperationExecuted(id)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L182](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L182)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

201    emit OperationExecuted(id)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L201](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L201)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

252    emit ChangeMinDelay(minDelay, _newDelay)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L252](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L252)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

259    emit ChangeSecurityCouncil(securityCouncil, _newSecurityCouncil)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L259](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L259)


#

```
File: code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol

45    emit UpgradeComplete(_proposedUpgrade.newProtocolVersion, txHash, _proposedUpgrade)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L45](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L45)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

57    emit NewValidator(oldValidator, _newValidator)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L57](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L57)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

63    emit NewExecutionDelay(_executionDelay)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L63](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L63)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

27    emit NewPendingGovernor(oldPendingGovernor, _newPendingGovernor)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L27](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L27)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

39    emit NewPendingGovernor(pendingGovernor, address(0))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L39](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L39)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

40    emit NewGovernor(previousGovernor, pendingGovernor)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L40](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L40)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

51    emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L51](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L51)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

64    emit NewAdmin(previousAdmin, pendingAdmin)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L64](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L64)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

63    emit NewPendingAdmin(pendingAdmin, address(0))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L63](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L63)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

72    emit ValidatorStatusUpdate(_validator, _active)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L72](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L72)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

80    emit IsPorterAvailableStatusUpdate(_zkPorterIsAvailable)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L80](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L80)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

90    emit NewPriorityTxMaxGasLimit(oldPriorityTxMaxGasLimit, _newPriorityTxMaxGasLimit)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L90](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L90)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

102    emit ExecuteUpgrade(_diamondCut)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L102](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L102)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

117    emit Freeze()
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L117](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L117)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

128    emit Unfreeze()
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L128](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L128)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

253    emit BlockCommit(
254                    _lastCommittedBatchData.batchNumber,
255                    _lastCommittedBatchData.batchHash,
256                    _lastCommittedBatchData.commitment
257                )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L253-L257](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L253-L257)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

215    emit BlockCommit(
216                    _lastCommittedBatchData.batchNumber,
217                    _lastCommittedBatchData.batchHash,
218                    _lastCommittedBatchData.commitment
219                )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L215-L219](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L215-L219)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

297    emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L297](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L297)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

364    emit BlocksVerification(s.totalBatchesVerified, currentTotalBatchesVerified)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L364](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L364)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

406    emit BlocksRevert(s.totalBatchesCommitted, s.totalBatchesVerified, s.totalBatchesExecuted)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L406](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L406)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

217    emit EthWithdrawalFinalized(_l1WithdrawReceiver, _amount)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L217](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L217)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

380    emit NewPriorityRequest(
381                _priorityOpParams.txId,
382                canonicalTxHash,
383                _priorityOpParams.expirationTimestamp,
384                transaction,
385                _factoryDeps
386            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L380-L386](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L380-L386)


</details>

# 


## Avoid unnecessary storage updates
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 8000

### Description
Avoid updating storage when the value hasn't changed. If the old value is equal to the new value, not re-storing the value will avoid a `SSTORE` operation (costing 2900 gas), potentially at the expense of a `SLOAD` operation (2100 gas) or a `WARMACCESS` operation (100 gas).

<details>

<summary>
There are 10 instances of this issue:

</summary>

###
#
The function `setDepositLimit()` changes the state variable without first verifying if the values are different.


```
File: code/contracts/ethereum/contracts/common/AllowList.sol

132    tokenDeposit[_l1Token].depositLimitation = _depositLimitation
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L132](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L132)


#
The function `setDepositLimit()` changes the state variable without first verifying if the values are different.


```
File: code/contracts/ethereum/contracts/common/AllowList.sol

133    tokenDeposit[_l1Token].depositCap = _depositCap
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L133](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L133)


#
The function `setExecutionDelay()` changes the state variable without first verifying if the values are different.


```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

62    executionDelay = _executionDelay
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L62](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L62)


#
The function `setPendingAdmin()` changes the state variable without first verifying if the values are different.


```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

50    s.pendingAdmin = _newPendingAdmin
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L50](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L50)


#
The function `setPendingGovernor()` changes the state variable without first verifying if the values are different.


```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

26    s.pendingGovernor = _newPendingGovernor
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L26](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L26)


#
The function `setPorterAvailability()` changes the state variable without first verifying if the values are different.


```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

79    s.zkPorterIsAvailable = _zkPorterIsAvailable
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L79](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L79)


#
The function `setValidator()` changes the state variable without first verifying if the values are different.


```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

56    validator = _newValidator
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L56](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L56)


#
The function `setValidator()` changes the state variable without first verifying if the values are different.


```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

71    s.validators[_validator] = _active
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L71](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L71)


#
The function `updateDelay()` changes the state variable without first verifying if the values are different.


```
File: code/contracts/ethereum/contracts/governance/Governance.sol

253    minDelay = _newDelay
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L253](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L253)


#
The function `updateSecurityCouncil()` changes the state variable without first verifying if the values are different.


```
File: code/contracts/ethereum/contracts/governance/Governance.sol

260    securityCouncil = _newSecurityCouncil
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L260](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L260)


</details>

# 


## Multiplication and Division by 2 Should use in Bit Shifting
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 60

### Description
The expressions 'x * 2' and 'x / 2' can be optimized for gas efficiency by utilizing bitwise operations. In Solidity, you can achieve the same results by using bitwise left shift (x << 1) for multiplication and bitwise right shift (x >> 1) for division.

Using bitwise shift operations (SHL and SHR) instead of multiplication (MUL) and division (DIV) opcodes can lead to significant gas savings. The MUL and DIV opcodes cost 5 gas, while the SHL and SHR opcodes incur a lower cost of only 3 gas.

By leveraging these more efficient bitwise operations, you can reduce the gas consumption of your smart contracts and enhance their overall performance.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

89    assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32)
```
 instead `2` use bit shifting `1` 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L89](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L89)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

89    assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32)
```
 instead `32` use bit shifting `5` 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L89](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L89)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

402    mstore(add(hashedFactoryDeps, mul(add(i, 1), 32)), hashedBytecode)
```
 instead `32` use bit shifting `5` 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L402](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L402)


</details>

# 


## Using bools for storage incurs overhead
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 51300

### Description
Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

41    mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L41](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L41)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

62    mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L62](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L62)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

28    mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L28](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L28)


</details>

# 


## Caching Global Variable is Inefficiency
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 24

### Description
Detects instances where global variables cached into local variables within a function. Using global variables directly is always cheaper than caching it into a local variable. The reason being the `CALLER` operation (which reads the global variable) costs 2 gas. On the other hand, the `MLOAD/MSTORE` operations, which are used for memory access (where local variables are stored), cost 3 gas. Caching and then accessing a global variable like `msg.sender` from a local variable, therefore, introduces an unnecessary overhead in terms of gas. You can check the gas costs for each operation [here](https://docs.google.com/spreadsheets/d/1n6mRqkBz3iWcOlRem_mO09GtSKEKrAsfO7Frgx18pNU/edit#gid=0).

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

264    canonicalTxHash = _requestL2Transaction(
265                sender,
266                _contractL2,
267                _l2Value,
268                _calldata,
269                _l2GasLimit,
270                _l2GasPerPubdataByteLimit,
271                _factoryDeps,
272                false,
273                _refundRecipient
274            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L264-L274](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L264-L274)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

325    params.valueToMint = msg.value
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L325](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L325)


</details>

# 


## Caching msg.sender Variable is Inefficiency
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 48

### Description
Detects instances where `msg.sender` is cached into local variables within a function. Using `msg.sender` directly is always cheaper than caching it into a local variable. The reason being the `CALLER` operation (which reads the `msg.sender` global variable) costs 2 gas. On the other hand, the `MLOAD/MSTORE` operations, which are used for memory access (where local variables are stored), cost 3 gas. Caching and then accessing a global variable like `msg.sender` from a local variable, therefore, introduces an unnecessary overhead in terms of gas. You can check the gas costs for each operation [here](https://docs.google.com/spreadsheets/d/1n6mRqkBz3iWcOlRem_mO09GtSKEKrAsfO7Frgx18pNU/edit#gid=0).

<details>

<summary>
There are 4 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

153    l2TxHash = deposit(_l2Receiver, _l1Token, _amount, _l2TxGasLimit, _l2TxGasPerPubdataByte, address(0))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L153](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L153)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

198    refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L198](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L198)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

185    refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L185](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L185)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

249    address sender = msg.sender
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L249](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L249)


</details>

# 


## Potential Optimization by Combining Multiple Mappings into a Struct
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 1000

### Description
This detector identifies instances where multiple mappings of an address or ID could be combined into a single mapping to a struct. This optimisation not only saves a storage slot for each mapping that is combined, but also makes the contract more efficient in terms of gas usage. Depending on the circumstances and sizes of types, it can avoid a 20000 gas cost (Gsset) per combined mapping. Also, it can make reads and subsequent writes cheaper when a function requires both values and they both fit in the same storage slot. Lastly, if both fields are accessed in the same function, it can save approximately 42 gas per access due to not having to recalculate the key's keccak256 hash and its associated stack operations.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

45    mapping(address => mapping(address => mapping(bytes32 => uint256))) internal depositAmount
```
Consider to combine with <br>-    ```Line: 56 mapping(address => uint256) public __DEPRECATED_lastWithdrawalLimitReset```<br>-    ```Line: 59 mapping(address => uint256) public __DEPRECATED_withdrawnAmountInWindow```<br>-    ```Line: 63 mapping(address => mapping(address => uint256)) public totalDepositedAmountPerUser```<br><br><br>
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L45](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L45)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

24    mapping(address => AccessMode) public getAccessMode
```
Consider to combine with <br>-    ```Line: 28 mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall```<br>-    ```Line: 31 mapping(address => Deposit) public tokenDeposit```<br><br><br>
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L24)


</details>

# 


## Use of emit inside a loop
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 5130

### Description
Emitting an event inside a loop performs a LOG op N times, where N is the loop length. This can lead to significant gas costs.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

75    emit UpdateAccessMode(_target, accessMode, _accessMode)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L75](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L75)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

123    emit UpdateCallPermission(_caller, _target, _functionSig, _enable)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L123](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L123)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

215    emit BlockCommit(
216                    _lastCommittedBatchData.batchNumber,
217                    _lastCommittedBatchData.batchHash,
218                    _lastCommittedBatchData.commitment
219                )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L215-L219](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L215-L219)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

253    emit BlockCommit(
254                    _lastCommittedBatchData.batchNumber,
255                    _lastCommittedBatchData.batchHash,
256                    _lastCommittedBatchData.commitment
257                )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L253-L257](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L253-L257)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

297    emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L297](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L297)


</details>

# 


## Functions Guaranteed to Revert When Called by Normal Users Should Be Marked Payable
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 630

### Description
This detector identifies functions that are guaranteed to revert when called by normal users. Such functions should be marked as payable, given that they will never succeed in processing Ether transactions.

<details>

<summary>
There are 30 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

51    function setAccessMode(address _target, AccessMode _accessMode) external onlyOwner 
```
 `setAccessMode` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L51-L53](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L51-L53)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

60    function setBatchAccessMode(address[] calldata _targets, AccessMode[] calldata _accessModes) external onlyOwner 
```
 `setBatchAccessMode` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L60-L67](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L60-L67)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

85    function setBatchPermissionToCall(
86            address[] calldata _callers,
87            address[] calldata _targets,
88            bytes4[] calldata _functionSigs,
89            bool[] calldata _enables
90        ) external onlyOwner 
```
 `setBatchPermissionToCall` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L85-L101](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L85-L101)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

108    function setPermissionToCall(
109            address _caller,
110            address _target,
111            bytes4 _functionSig,
112            bool _enable
113        ) external onlyOwner 
```
 `setPermissionToCall` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L108-L115](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L108-L115)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

131    function setDepositLimit(address _l1Token, bool _depositLimitation, uint256 _depositCap) external onlyOwner 
```
 `setDepositLimit` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L131-L134](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L131-L134)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

131    function scheduleTransparent(Operation calldata _operation, uint256 _delay) external onlyOwner 
```
 `scheduleTransparent` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L131-L135](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L131-L135)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

144    function scheduleShadow(bytes32 _id, uint256 _delay) external onlyOwner 
```
 `scheduleShadow` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L144-L147](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L144-L147)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

156    function cancel(bytes32 _id) external onlyOwnerOrSecurityCouncil 
```
 `cancel` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L156-L160](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L156-L160)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

169    function execute(Operation calldata _operation) external onlyOwnerOrSecurityCouncil 
```
 `execute` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L169-L183](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L169-L183)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

188    function executeInstant(Operation calldata _operation) external onlySecurityCouncil 
```
 `executeInstant` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L188-L202](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L188-L202)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

251    function updateDelay(uint256 _newDelay) external onlySelf 
```
 `updateDelay` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L251-L254](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L251-L254)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

258    function updateSecurityCouncil(address _newSecurityCouncil) external onlySelf 
```
 `updateSecurityCouncil` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L258-L261](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L258-L261)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

54    function setValidator(address _newValidator) external onlyOwner 
```
 `setValidator` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L54-L58](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L54-L58)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

61    function setExecutionDelay(uint32 _executionDelay) external onlyOwner 
```
 `setExecutionDelay` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L61-L64](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L61-L64)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

79    function commitBatches(
80            StoredBatchInfo calldata,
81            CommitBatchInfo[] calldata _newBatchesData
82        ) external onlyValidator 
```
 `commitBatches` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L79-L93](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L79-L93)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

98    function revertBatches(uint256) external onlyValidator 
```
 `revertBatches` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L98-L100](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L98-L100)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

105    function proveBatches(
106            StoredBatchInfo calldata,
107            StoredBatchInfo[] calldata,
108            ProofInput calldata
109        ) external onlyValidator 
```
 `proveBatches` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L105-L111](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L105-L111)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

115    function executeBatches(StoredBatchInfo[] calldata _newBatchesData) external onlyValidator 
```
 `executeBatches` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L115-L130](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L115-L130)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

22    function setPendingGovernor(address _newPendingGovernor) external onlyGovernor 
```
 `setPendingGovernor` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L22-L28](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L22-L28)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

46    function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin 
```
 `setPendingAdmin` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L46-L52](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L46-L52)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

70    function setValidator(address _validator, bool _active) external onlyGovernorOrAdmin 
```
 `setValidator` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L70-L73](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L70-L73)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

77    function setPorterAvailability(bool _zkPorterIsAvailable) external onlyGovernor 
```
 `setPorterAvailability` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L77-L81](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L77-L81)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

85    function setPriorityTxMaxGasLimit(uint256 _newPriorityTxMaxGasLimit) external onlyGovernor 
```
 `setPriorityTxMaxGasLimit` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L85-L91](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L85-L91)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

100    function executeUpgrade(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor 
```
 `executeUpgrade` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L100-L103](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L100-L103)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

111    function freezeDiamond() external onlyGovernor 
```
 `freezeDiamond` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L111-L118](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L111-L118)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

122    function unfreezeDiamond() external onlyGovernorOrAdmin 
```
 `unfreezeDiamond` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L122-L129](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L122-L129)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

179    function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)
180            external
181            override
182            nonReentrant
183            onlyValidator
184        
```
 `commitBatches` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L179-L202](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L179-L202)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

293    function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator 
```
 `executeBatches` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L293-L309](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L293-L309)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

313    function proveBatches(
314            StoredBatchInfo calldata _prevBatch,
315            StoredBatchInfo[] calldata _committedBatches,
316            ProofInput calldata _proof
317        ) external nonReentrant onlyValidator 
```
 `proveBatches` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L313-L366](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L313-L366)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

391    function revertBatches(uint256 _newLastBatch) external nonReentrant onlyValidator 
```
 `revertBatches` is guaranteed to revert and should be marked payable
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L391-L407](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L391-L407)


</details>

# 


## Use a More Recent Version of Solidity
- Severity: Gas Optimization
- Confidence: High

### Description

`solc` frequently releases new compiler versions. Using an old version prevents access to new Solidity security checks.
Addition new version gain some gas boost.
We also recommend avoiding complex `pragma` statement.

<details>

<summary>
There are 37 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L1)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L1)


#

```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L1)


#

```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL1BridgeLegacy.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1BridgeLegacy.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1BridgeLegacy.sol#L1)


#

```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol#L1)


#

```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL2ERC20Bridge.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2ERC20Bridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2ERC20Bridge.sol#L1)


#

```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol#L1)


#

```
File: code/contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol#L1)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L1)


#

```
File: code/contracts/ethereum/contracts/common/AllowListed.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L1)


#

```
File: code/contracts/ethereum/contracts/common/L2ContractAddresses.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/L2ContractAddresses.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/L2ContractAddresses.sol#L1)


#

```
File: code/contracts/ethereum/contracts/common/ReentrancyGuard.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L1)


#

```
File: code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol#L1)


#

```
File: code/contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol#L1)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L1)


#

```
File: code/contracts/ethereum/contracts/governance/IGovernance.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L1)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L1)


#

```
File: code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L1)


#

```
File: code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/Config.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/Storage.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Getters.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/interfaces/IBase.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IBase.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IBase.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/interfaces/ILegacyGetters.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/ILegacyGetters.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/ILegacyGetters.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L1)


#

```
File: code/contracts/ethereum/contracts/zksync/interfaces/IZkSync.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.19`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IZkSync.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IZkSync.sol#L1)


#
solc-0.8.17 is not recommended for deployment

</details>

# 


## Inefficient Parameter Storage
- Severity: Gas Optimization
- Confidence: Medium
- Total Gas Saved: 500

### Description
When passing function parameters, using the `calldata` area instead of `memory` can improve gas efficiency. Calldata is a read-only area where function arguments and external function calls' parameters are stored.

By using `calldata` for function parameters, you avoid unnecessary gas costs associated with copying data from `calldata` to memory. This is particularly beneficial when the parameter is read-only and doesn't require modification within the contract.

Using `calldata` for function parameters can help optimize gas usage, especially when making external function calls or when the parameter values are provided externally and don't need to be stored persistently within the contract.

<details>

<summary>
There are 10 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

13    Diamond.DiamondCutData memory _diamondCut
```
 should be declared as `calldata` instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L13](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L13)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

30    StoredBatchInfo memory _previousBatch
```
 should be declared as `calldata` instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L30](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L30)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

372    VerifierParams memory _verifierParams
```
 should be declared as `calldata` instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L372](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L372)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

54    L2Message memory _message
```
 should be declared as `calldata` instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L54](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L54)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

69    L2Log memory _log
```
 should be declared as `calldata` instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L69](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L69)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

128    L2Log memory _log
```
 should be declared as `calldata` instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L128](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L128)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

151    L2Message memory _message
```
 should be declared as `calldata` instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L151](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L151)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

332    WritePriorityOpParams memory _priorityOpParams
```
 should be declared as `calldata` instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L332](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L332)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

359    WritePriorityOpParams memory _priorityOpParams
```
 should be declared as `calldata` instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L359](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L359)


#

```
File: code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol

101    L2Log memory _log
```
 should be declared as `calldata` instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L101](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L101)


</details>

# 


## Using Storage Instead of Memory for structs/arrays Saves Gas
- Severity: Gas Optimization
- Confidence: Medium
- Total Gas Saved: 25200

### Description
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct / array to be read from storage. This incurs a Gcoldsload (2100 gas) for each field of the struct / array. If the fields are read from the new memory variable, they incur an additional MLOAD, which is more expensive than a simple stack read.

Instead of declaring the variable with the memory keyword, a more cost-effective approach is to declare the variable with the storage keyword. In this case, you can cache any fields that need to be re-read in stack variables. This approach significantly reduces gas costs, as you only incur the Gcoldsload for the fields actually read.

The only scenario where it makes sense to read the whole struct / array into a memory variable is if the full struct / array is being returned by the function or passed to a function that requires memory. Additionally, if the array / struct is being read from another memory array / struct, using a memory variable may be appropriate.

By carefully considering the storage and memory usage in your contract, you can optimize gas costs and improve the efficiency of your smart contract operations.

<details>

<summary>
There are 6 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

304    L2Message memory l2ToL1Message = L2Message({
305                txNumberInBatch: _l2TxNumberInBatch,
306                sender: l2Bridge,
307                data: _message
308            })
```
 should be declared as `storage` 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L304-L308](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L304-L308)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

343    IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token)
```
 should be declared as `storage` 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L343](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L343)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

138    VerifierParams memory oldVerifierParams = s.verifierParams
```
 should be declared as `storage` 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L138](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L138)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

266    PriorityOperation memory priorityOp = s.priorityQueue.popFront()
```
 should be declared as `storage` 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L266](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L266)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

323    VerifierParams memory verifierParams = s.verifierParams
```
 should be declared as `storage` 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L323](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L323)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

278    IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0))
```
 should be declared as `storage` 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L278](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L278)


</details>

# 


## Internal or Private Function that Called Once Should Be Inlined to Save Gas
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 640

### Description
Setting the internal/private function that called once to inline would save gas

<details>

<summary>
There are 32 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

201    function _getDepositL2Calldata(
202            address _l1Sender,
203            address _l2Receiver,
204            address _l1Token,
205            uint256 _amount
206        ) internal pure returns (bytes memory txCalldata) 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L201-L211](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L201-L211)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

274    function _parseL2EthWithdrawalMessage(
275            bytes memory _message
276        ) internal view returns (address l1WethReceiver, uint256 ethAmount) 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L274-L301](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L274-L301)


#

```
File: code/contracts/ethereum/contracts/common/ReentrancyGuard.sol

48    function _initializeReentrancyGuard() private 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L48-L61](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L48-L61)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

79    function _setL2DefaultAccountBytecodeHash(bytes32 _l2DefaultAccountBytecodeHash) private 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L79-L92](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L79-L92)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

96    function _setL2BootloaderBytecodeHash(bytes32 _l2BootloaderBytecodeHash) private 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L96-L109](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L96-L109)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

113    function _setVerifier(IVerifier _newVerifier) private 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L113-L125](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L113-L125)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

129    function _setVerifierParams(VerifierParams calldata _newVerifierParams) private 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L129-L141](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L129-L141)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

202    function _verifyFactoryDeps(bytes[] calldata _factoryDeps, uint256[] calldata _expectedHashes) private pure 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L202-L212](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L202-L212)


#

```
File: code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol

17    function _upgradeL1Contract(bytes calldata _customCallDataForUpgrade) internal virtual 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L17](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L17)


#

```
File: code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol

23    function _postUpgrade(bytes calldata _customCallDataForUpgrade) internal virtual 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L23](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L23)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

76    function _verifyBatchTimestamp(
77            uint256 _packedBatchAndL2BlockTimestamp,
78            uint256 _expectedBatchTimestamp,
79            uint256 _previousBatchTimestamp
80        ) internal view 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L76-L97](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L76-L97)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

103    function _processL2Logs(CommitBatchInfo calldata _newBatch, bytes32 _expectedSystemContractUpgradeTxHash)
104            internal
105            pure
106            returns (
107                uint256 numberOfLayer1Txs,
108                bytes32 chainedPriorityTxsHash,
109                bytes32 previousBatchHash,
110                bytes32 stateDiffHash,
111                bytes32 l2LogsTreeRoot,
112                uint256 packedBatchAndL2BlockTimestamp
113            )
114        
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L103-L173](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L103-L173)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

207    function _commitBatchesWithoutSystemContractsUpgrade(
208            StoredBatchInfo memory _lastCommittedBatchData,
209            CommitBatchInfo[] calldata _newBatchesData
210        ) internal 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L207-L221](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L207-L221)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

227    function _commitBatchesWithSystemContractsUpgrade(
228            StoredBatchInfo memory _lastCommittedBatchData,
229            CommitBatchInfo[] calldata _newBatchesData,
230            bytes32 _systemContractUpgradeTxHash
231        ) internal 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L227-L259](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L227-L259)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

262    function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L262-L269](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L262-L269)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

275    function _executeOneBatch(StoredBatchInfo memory _storedBatch, uint256 _executedBatchIdx) internal 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L275-L288](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L275-L288)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

369    function _getBatchProofPublicInput(
370            bytes32 _prevBatchCommitment,
371            bytes32 _currentBatchCommitment,
372            VerifierParams memory _verifierParams
373        ) internal pure returns (uint256) 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L369-L385](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L369-L385)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

410    function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) 
```
 this function can be delete, the function is never called:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L410-L412](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L410-L412)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

415    function _createBatchCommitment(CommitBatchInfo calldata _newBatchData, bytes32 _stateDiffHash)
416            internal
417            view
418            returns (bytes32)
419        
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L415-L425](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L415-L425)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

427    function _batchPassThroughData(CommitBatchInfo calldata _batch) internal pure returns (bytes memory) 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L427-L435](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L427-L435)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

437    function _batchMetaParameters() internal view returns (bytes memory) 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L437-L439](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L437-L439)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

441    function _batchAuxiliaryOutput(CommitBatchInfo calldata _batch, bytes32 _stateDiffHash)
442            internal
443            pure
444            returns (bytes memory)
445        
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L441-L457](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L441-L457)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

465    function _checkBit(uint256 _bitMap, uint8 _index) internal pure returns (bool) 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L465-L467](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L465-L467)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

470    function _setBit(uint256 _bitMap, uint8 _index) internal pure returns (uint256) 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L470-L472](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L470-L472)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

115    function _withdrawFunds(address _to, uint256 _amount) internal 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L115-L122](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L115-L122)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

151    function _L2MessageToLog(L2Message memory _message) internal pure returns (L2Log memory) 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L151-L161](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L151-L161)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

277    function _verifyDepositLimit(address _depositor, uint256 _amount) internal 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L277-L283](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L277-L283)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

285    function _requestL2Transaction(
286            address _sender,
287            address _contractAddressL2,
288            uint256 _l2Value,
289            bytes calldata _calldata,
290            uint256 _l2GasLimit,
291            uint256 _l2GasPerPubdataByteLimit,
292            bytes[] calldata _factoryDeps,
293            bool _isFree,
294            address _refundRecipient
295        ) internal returns (bytes32 canonicalTxHash) 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L285-L329](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L285-L329)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

331    function _serializeL2Transaction(
332            WritePriorityOpParams memory _priorityOpParams,
333            bytes calldata _calldata,
334            bytes[] calldata _factoryDeps
335        ) internal pure returns (L2CanonicalTransaction memory transaction) 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L331-L355](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L331-L355)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

358    function _writePriorityOp(
359            WritePriorityOpParams memory _priorityOpParams,
360            bytes calldata _calldata,
361            bytes[] calldata _factoryDeps
362        ) internal returns (bytes32 canonicalTxHash) 
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L358-L387](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L358-L387)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

390    function _hashFactoryDeps(bytes[] calldata _factoryDeps)
391            internal
392            pure
393            returns (uint256[] memory hashedFactoryDeps)
394        
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L390-L405](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L390-L405)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

408    function _parseL2WithdrawalMessage(bytes memory _message)
409            internal
410            pure
411            returns (address l1Receiver, uint256 amount)
412        
```
 should be inline to save gas:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L408-L430](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L408-L430)


</details>

# 


## Inefficient Gas Usage in Solidity Smart Contracts Due to Long Error Messages
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 378

### Description
If the string parameter of a require() or revert() function is longer than 32 bytes, it can lead to inefficiencies. This is because each extra memory word of bytes past the original 32 incurs an MSTORE operation, which costs 3 gas.

<details>

<summary>
There are 21 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

92    require(_factoryDeps.length == 2, "Invalid factory deps length provided")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L92](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L92)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

93    require(
94                msg.value == _deployBridgeImplementationFee + _deployBridgeProxyFee,
95                "Miscalculated deploy transactions fees"
96            )
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L93-L96](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L93-L96)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

225    revert("Method not supported. Failed deposit funds are sent to the L2 refund recipient address.")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L225](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L225)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

281    require(_message.length == 96, "Incorrect ETH message with additional data length")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L281](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L281)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

284    require(
285                bytes4(functionSignature) == IMailbox.finalizeEthWithdrawal.selector,
286                "Incorrect ETH message function selector"
287            )
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L284-L287](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L284-L287)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

297    require(l2Sender == l2Bridge, "The withdrawal was not initiated by L2 bridge")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L297](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L297)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

174    require(isOperationReady(id), "Operation must be ready before execution")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L174](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L174)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

179    require(isOperationReady(id), "Operation must be ready after execution")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L179](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L179)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

193    require(isOperationPending(id), "Operation must be pending before execution")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L193](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L193)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

198    require(isOperationPending(id), "Operation must be pending after execution")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L198](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L198)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

218    require(!isOperation(_id), "Operation with this proposal id already exists")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L218](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L218)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

219    require(_delay >= minDelay, "Proposed delay is less than minimum delay")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L219](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L219)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

242    require(_predecessorId == bytes32(0) || isOperationDone(_predecessorId), "Predecessor operation not completed")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L242](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L242)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

61    require(msg.sender == address(this), "Only governance contract itself allowed to call this function")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L61](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L61)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

67    require(msg.sender == securityCouncil, "Only security council allowed to call this function")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L67](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L67)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

73    require(
74                msg.sender == owner() || msg.sender == securityCouncil,
75                "Only the owner and security council are allowed to call this function"
76            )
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L73-L76](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L73-L76)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

173    require(_l2ProtocolUpgradeTx.txType == SYSTEM_UPGRADE_L2_TX_TYPE, "L2 system upgrade tx type is wrong")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L173](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L173)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

187    require(
188                _l2ProtocolUpgradeTx.nonce == _newProtocolVersion,
189                "The new protocol version should be included in the L2 system upgrade tx"
190            )
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L187-L190](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L187-L190)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

218    require(
219                _newProtocolVersion > previousProtocolVersion, // @audit m: If `_newProtocolVersion` is `type(uint256).max`, no further upgrades could take place
220                "New protocol version is not greater than the current one"
221            )
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L218-L221](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L218-L221)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

224    require(s.l2SystemContractsUpgradeTxHash == bytes32(0), "Previous upgrade has not been finalized")
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L224](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L224)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

225    require(
226                s.l2SystemContractsUpgradeBatchNumber == 0,
227                "The batch number of the previous upgrade has not been cleaned"
228            )
```
 make the message shorter than 32 bytes

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L225-L228](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L225-L228)


</details>

# 


## Declare Constructor Function as Payable to Save Gas
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 147

### Description
Constructors should be declared `payable` to save gas

<details>

<summary>
There are 7 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

67    constructor(IZkSync _zkSync, IAllowList _allowList) reentrancyGuardInitializer 
```
 should be declared payable:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L67-L70](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L67-L70)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

66    constructor(address payable _l1WethAddress, IZkSync _zkSync, IAllowList _allowList) reentrancyGuardInitializer 
```
 should be declared payable:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L66-L70](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L66-L70)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

33    constructor(address _initialOwner) 
```
 should be declared payable:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L33-L35](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L33-L35)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

43    constructor(address _admin, address _securityCouncil, uint256 _minDelay) 
```
 should be declared payable:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L43-L53](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L43-L53)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

52    constructor() reentrancyGuardInitializer 
```
 should be declared payable:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L52](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L52)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

13    constructor(uint256 _chainId, Diamond.DiamondCutData memory _diamondCut) 
```
 should be declared payable:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L13-L18](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L13-L18)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

46    constructor(address _initialOwner, address _zkSyncContract, uint32 _executionDelay, address _validator) 
```
 should be declared payable:

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L46-L51](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L46-L51)


</details>

# 


## Optimize address(0) Checks Using Assembly
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 290

### Description
Solidity provides `address(0)` as a constant for the zero address. While it is generally safe to use, explicit checks against `address(0)` in your code might be slightly more gas efficient if implemented in inline assembly, due to reduced overhead.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

197    _refundRecipient == address(0)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L197](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L197)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

184    _refundRecipient == address(0)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L184](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L184)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

237    _newAllowList == IAllowList(address(0))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L237](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L237)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

118    _newVerifier == IVerifier(address(0))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L118](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L118)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

312    address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L312](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L312)


</details>

# 


## Optimize Names to Save Gas
- Severity: Gas Optimization
- Confidence: Medium
- Total Gas Saved: 176

### Description
Optimize public/external function names and public member variable names to save gas. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, per sorted position shifted. Note: This detector does not mention special functions like constructors or functions that override other functions which cannot change their names due to the requirements of the overriding process.

<details>

<summary>
There are 8 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

39    contract L1WethBridge is IL1Bridge, AllowListed, ReentrancyGuard 
```
``` 
/// @audit: initialize()  
``` 


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L39-L316](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L39-L316)


#

```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol

8    interface IL2Bridge 
```
``` 
/// @audit: finalizeDeposit()  ,withdraw()  ,l1TokenAddress()  ,l2TokenAddress()  ,l1Bridge()  
``` 


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol#L8-L28](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol#L8-L28)


#

```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL2ERC20Bridge.sol

8    interface IL2ERC20Bridge 
```
``` 
/// @audit: initialize()  
``` 


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2ERC20Bridge.sol#L8-L14](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2ERC20Bridge.sol#L8-L14)


#

```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol

7    interface IL2WethBridge 
```
``` 
/// @audit: initialize()  
``` 


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol#L7-L13](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol#L7-L13)


#

```
File: code/contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol

6    interface IWETH9 
```
``` 
/// @audit: deposit()  ,withdraw()  
``` 


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol#L6-L10](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol#L6-L10)


#

```
File: code/contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol

11    interface IL2ContractDeployer 
```
``` 
/// @audit: forceDeployOnAddresses()  ,create2()  
``` 


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol#L11-L38](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol#L11-L38)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

21    contract DiamondInit is Base 
```
``` 
/// @audit: initialize()  
``` 


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L21-L93](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L21-L93)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

22    contract ValidatorTimelock is IExecutor, Ownable2Step 
```
``` 
/// @audit: setValidator()  ,setExecutionDelay()  ,getCommittedBatchTimestamp()  
``` 


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L22-L157](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L22-L157)


</details>

# 


## Optimal Struct Variable Order
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 15000

### Description
Detect optimal variable order in struct definitions to decrease the number of slots used. Each storage slot used can cost at least 5,000 gas for each write operation, and potentially up to 20,000 gas if you're turning a zero value into a non-zero value. Hence, optimizing storage usage can result in significant gas savings. The real-world savings could vary depending on your contract's specific logic and the state of the Ethereum network.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
#
Optimization opportunity in struct 
```
File: code/contracts/ethereum/contracts/zksync/Storage.sol

81    struct AppStorage {
82        /// @dev Storage of variables needed for deprecated diamond cut facet
83        uint256[7] __DEPRECATED_diamondCutStorage;
84        /// @notice Address which will exercise critical changes to the Diamond Proxy (upgrades, freezing & unfreezing)
85        address governor;
86        /// @notice Address that the governor proposed as one that will replace it
87        address pendingGovernor;
88        /// @notice List of permitted validators
89        mapping(address => bool) validators;
90        /// @dev Verifier contract. Used to verify aggregated proof for batches
91        IVerifier verifier;
92        /// @notice Total number of executed batches i.e. batches[totalBatchesExecuted] points at the latest executed batch
93        /// (batch 0 is genesis)
94        uint256 totalBatchesExecuted;
95        /// @notice Total number of proved batches i.e. batches[totalBatchesProved] points at the latest proved batch
96        uint256 totalBatchesVerified;
97        /// @notice Total number of committed batches i.e. batches[totalBatchesCommitted] points at the latest committed
98        /// batch
99        uint256 totalBatchesCommitted;
100        /// @dev Stored hashed StoredBatch for batch number
101        mapping(uint256 => bytes32) storedBatchHashes;
102        /// @dev Stored root hashes of L2 -> L1 logs
103        mapping(uint256 => bytes32) l2LogsRootHashes;
104        /// @dev Container that stores transactions requested from L1
105        PriorityQueue.Queue priorityQueue;
106        /// @dev The smart contract that manages the list with permission to call contract functions
107        IAllowList allowList;
108        /// @notice Part of the configuration parameters of ZKP circuits. Used as an input for the verifier smart contract
109        VerifierParams verifierParams;
110        /// @notice Bytecode hash of bootloader program.
111        /// @dev Used as an input to zkp-circuit.
112        bytes32 l2BootloaderBytecodeHash;
113        /// @notice Bytecode hash of default account (bytecode for EOA).
114        /// @dev Used as an input to zkp-circuit.
115        bytes32 l2DefaultAccountBytecodeHash;
116        /// @dev Indicates that the porter may be touched on L2 transactions.
117        /// @dev Used as an input to zkp-circuit.
118        bool zkPorterIsAvailable;
119        /// @dev The maximum number of the L2 gas that a user can request for L1 -> L2 transactions
120        /// @dev This is the maximum number of L2 gas that is available for the "body" of the transaction, i.e.
121        /// without overhead for proving the batch.
122        uint256 priorityTxMaxGasLimit;
123        /// @dev Storage of variables needed for upgrade facet
124        UpgradeStorage __DEPRECATED_upgrades;
125        /// @dev A mapping L2 batch number => message number => flag.
126        /// @dev The L2 -> L1 log is sent for every withdrawal, so this mapping is serving as
127        /// a flag to indicate that the message was already processed.
128        /// @dev Used to indicate that eth withdrawal was already processed
129        mapping(uint256 => mapping(uint256 => bool)) isEthWithdrawalFinalized;
130        /// @dev The most recent withdrawal time and amount reset
131        uint256 __DEPRECATED_lastWithdrawalLimitReset;
132        /// @dev The accumulated withdrawn amount during the withdrawal limit window
133        uint256 __DEPRECATED_withdrawnAmountInWindow;
134        /// @dev A mapping user address => the total deposited amount by the user
135        mapping(address => uint256) totalDepositedAmountPerUser;
136        /// @dev Stores the protocol version. Note, that the protocol version may not only encompass changes to the
137        /// smart contracts, but also to the node behavior.
138        uint256 protocolVersion;
139        /// @dev Hash of the system contract upgrade transaction. If 0, then no upgrade transaction needs to be done.
140        bytes32 l2SystemContractsUpgradeTxHash;
141        /// @dev Batch number where the upgrade transaction has happened. If 0, then no upgrade transaction has happened
142        /// yet.
143        uint256 l2SystemContractsUpgradeBatchNumber;
144        /// @dev Address which will exercise non-critical changes to the Diamond Proxy (changing validator set & unfreezing)
145        address admin;
146        /// @notice Address that the governor or admin proposed as one that will replace admin role
147        address pendingAdmin;
148    }
```

- original variable order (count: 27 slots)
    - uint256[7] __DEPRECATED_diamondCutStorage
    - address governor
    - address pendingGovernor
    - mapping(address => bool) validators
    - IVerifier verifier
    - uint256 totalBatchesExecuted
    - uint256 totalBatchesVerified
    - uint256 totalBatchesCommitted
    - mapping(uint256 => bytes32) storedBatchHashes
    - mapping(uint256 => bytes32) l2LogsRootHashes
    - PriorityQueue.Queue priorityQueue
    - IAllowList allowList
    - VerifierParams verifierParams
    - bytes32 l2BootloaderBytecodeHash
    - bytes32 l2DefaultAccountBytecodeHash
    - bool zkPorterIsAvailable
    - uint256 priorityTxMaxGasLimit
    - UpgradeStorage __DEPRECATED_upgrades
    - mapping(uint256 => mapping(uint256 => bool)) isEthWithdrawalFinalized
    - uint256 __DEPRECATED_lastWithdrawalLimitReset
    - uint256 __DEPRECATED_withdrawnAmountInWindow
    - mapping(address => uint256) totalDepositedAmountPerUser
    - uint256 protocolVersion
    - bytes32 l2SystemContractsUpgradeTxHash
    - uint256 l2SystemContractsUpgradeBatchNumber
    - address admin
    - address pendingAdmin


 - optimized variable order (count: 26 slots)
    - address governor
    - bool zkPorterIsAvailable
    - address pendingGovernor
    - IVerifier verifier
    - IAllowList allowList
    - address admin
    - address pendingAdmin
    - uint256[7] __DEPRECATED_diamondCutStorage
    - mapping(address => bool) validators
    - uint256 totalBatchesExecuted
    - uint256 totalBatchesVerified
    - uint256 totalBatchesCommitted
    - mapping(uint256 => bytes32) storedBatchHashes
    - mapping(uint256 => bytes32) l2LogsRootHashes
    - PriorityQueue.Queue priorityQueue
    - VerifierParams verifierParams
    - bytes32 l2BootloaderBytecodeHash
    - bytes32 l2DefaultAccountBytecodeHash
    - uint256 priorityTxMaxGasLimit
    - UpgradeStorage __DEPRECATED_upgrades
    - mapping(uint256 => mapping(uint256 => bool)) isEthWithdrawalFinalized
    - uint256 __DEPRECATED_lastWithdrawalLimitReset
    - uint256 __DEPRECATED_withdrawnAmountInWindow
    - mapping(address => uint256) totalDepositedAmountPerUser
    - uint256 protocolVersion
    - bytes32 l2SystemContractsUpgradeTxHash
    - uint256 l2SystemContractsUpgradeBatchNumber


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L81-L148](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L81-L148)


#
Optimization opportunity in struct 
```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

36    struct InitializeData {
37            IVerifier verifier;
38            address governor;
39            address admin;
40            bytes32 genesisBatchHash;
41            uint64 genesisIndexRepeatedStorageChanges;
42            bytes32 genesisBatchCommitment;
43            IAllowList allowList;
44            VerifierParams verifierParams;
45            bool zkPorterIsAvailable;
46            bytes32 l2BootloaderBytecodeHash;
47            bytes32 l2DefaultAccountBytecodeHash;
48            uint256 priorityTxMaxGasLimit;
49        }
```

- original variable order (count: 12 slots)
    - IVerifier verifier
    - address governor
    - address admin
    - bytes32 genesisBatchHash
    - uint64 genesisIndexRepeatedStorageChanges
    - bytes32 genesisBatchCommitment
    - IAllowList allowList
    - VerifierParams verifierParams
    - bool zkPorterIsAvailable
    - bytes32 l2BootloaderBytecodeHash
    - bytes32 l2DefaultAccountBytecodeHash
    - uint256 priorityTxMaxGasLimit


 - optimized variable order (count: 10 slots)
    - IVerifier verifier
    - uint64 genesisIndexRepeatedStorageChanges
    - bool zkPorterIsAvailable
    - address governor
    - address admin
    - IAllowList allowList
    - bytes32 genesisBatchHash
    - bytes32 genesisBatchCommitment
    - VerifierParams verifierParams
    - bytes32 l2BootloaderBytecodeHash
    - bytes32 l2DefaultAccountBytecodeHash
    - uint256 priorityTxMaxGasLimit


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L36-L49](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L36-L49)


#
Optimization opportunity in struct 
```
File: code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol

40    struct StoredBatchInfo {
41            uint64 batchNumber;
42            bytes32 batchHash;
43            uint64 indexRepeatedStorageChanges;
44            uint256 numberOfLayer1Txs;
45            bytes32 priorityOperationsHash;
46            bytes32 l2LogsTreeRoot;
47            uint256 timestamp;
48            bytes32 commitment;
49        }
```

- original variable order (count: 8 slots)
    - uint64 batchNumber
    - bytes32 batchHash
    - uint64 indexRepeatedStorageChanges
    - uint256 numberOfLayer1Txs
    - bytes32 priorityOperationsHash
    - bytes32 l2LogsTreeRoot
    - uint256 timestamp
    - bytes32 commitment


 - optimized variable order (count: 7 slots)
    - uint64 batchNumber
    - uint64 indexRepeatedStorageChanges
    - bytes32 batchHash
    - uint256 numberOfLayer1Txs
    - bytes32 priorityOperationsHash
    - bytes32 l2LogsTreeRoot
    - uint256 timestamp
    - bytes32 commitment



Recomended optimizations will use 4 less slots.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L40-L49](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L40-L49)


</details>

# 


## Consider activating via-ir for deploying
- Severity: Gas Optimization
- Confidence: Medium

### Description
The IR-based code generator was developed to make code generation more transparent and auditable, while also enabling powerful optimization passes that can be applied across functions. 

It is possible to activate the IR-based code generator through the command line by using the flag --via-ir or by including the option {'viaIR': true}. 

Keep in mind that compiling with this option may take longer. However, you can simply test it before deploying your code. If you find that it provides better performance, you can add the --via-ir flag to your deploy command.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
#
 
</details>

# 


## State variable access within a loop
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 1590

### Description
State variable reads and writes are more expensive than local variable reads and writes. Therefore, it is recommended to replace state variable reads and writes within loops with a local variable. Gas savings should be multiplied by the average loop length.

<details>

<summary>
There are 6 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

88    committedBatchTimestamp.set(_newBatchesData[i].batchNumber, timestamp)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L88](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L88)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

119    uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L119](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L119)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

214    s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L214](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L214)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

252    s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L252](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L252)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

266    PriorityOperation memory priorityOp = s.priorityQueue.popFront()
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L266](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L266)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

334    require(
335                    _hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],
336                    "o1"
337                )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L334-L337](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L334-L337)


</details>

# 


## Cache Array Length Outside of Loop
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 21

### Description
When using arrays in a for loop, calling .length on every iteration will result in unnecessary gas expenses. Instead, it is recommended to cache the array length beforehand, which saves 3 gas per iteration. For storage arrays, an additional 100 gas per iteration (except the first) can be saved by pre-caching the length

<details>

<summary>
There are 7 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

227    i < _calls.length
```
 caching the length of array
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L227](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L227)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

206    i < _factoryDeps.length
```
 caching the length of array
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L206](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L206)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

87    i < _newBatchesData.length
```
 caching the length of array
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L87](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L87)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

118    i < _newBatchesData.length
```
 caching the length of array
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L118](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L118)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

125    i < emittedL2Logs.length
```
 caching the length of array
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L125](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L125)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

211    i < _newBatchesData.length
```
 caching the length of array
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L211](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L211)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

243    i < _newBatchesData.length
```
 caching the length of array
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L243](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L243)


</details>

# 


## State variables should be cached in stack variables rather than re-reading them from storage
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 2522

### Description
State variables should be cached instead of re-reading them.Subsequent reads incur a 100 gas penalty.Caching such variables replaces the Gwarmaccess with much cheaper stack reads.

<details>

<summary>
There are 26 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

257    function claimFailedDeposit(
258            address _depositSender,
259            address _l1Token,
260            bytes32 _l2TxHash,
261            uint256 _l2BatchNumber,
262            uint256 _l2MessageIndex,
263            uint16 _l2TxNumberInBatch,
264            bytes32[] calldata _merkleProof
265        ) external nonReentrant senderCanCallFunction(allowList) 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

45    mapping(address => mapping(address => mapping(bytes32 => uint256))) internal depositAmount
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L257-L287](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L257-L287)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

342    function _verifyDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

63    mapping(address => mapping(address => uint256)) public totalDepositedAmountPerUser
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L342-L352](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L342-L352)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

131    function setDepositLimit(address _l1Token, bool _depositLimitation, uint256 _depositCap) external onlyOwner 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/common/AllowList.sol

31    mapping(address => Deposit) public tokenDeposit
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L131-L134](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L131-L134)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

79    function _setL2DefaultAccountBytecodeHash(bytes32 _l2DefaultAccountBytecodeHash) private 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L79-L92](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L79-L92)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

96    function _setL2BootloaderBytecodeHash(bytes32 _l2BootloaderBytecodeHash) private 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L96-L109](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L96-L109)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

113    function _setVerifier(IVerifier _newVerifier) private 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L113-L125](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L113-L125)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

129    function _setVerifierParams(VerifierParams calldata _newVerifierParams) private 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L129-L141](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L129-L141)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

163    function _setL2SystemContractUpgrade(
164            IMailbox.L2CanonicalTransaction calldata _l2ProtocolUpgradeTx,
165            bytes[] calldata _factoryDeps,
166            uint256 _newProtocolVersion
167        ) internal returns (bytes32) 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L163-L197](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L163-L197)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

216    function _setNewProtocolVersion(uint256 _newProtocolVersion) internal 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L216-L232](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L216-L232)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

236    function _setAllowList(IAllowList _newAllowList) internal 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L236-L244](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L236-L244)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

57    function initialize(InitializeData calldata _initalizeData) external reentrancyGuardInitializer returns (bytes32) 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L57-L92](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L57-L92)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

22    function setPendingGovernor(address _newPendingGovernor) external onlyGovernor 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L22-L28](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L22-L28)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

31    function acceptGovernor() external 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L31-L41](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L31-L41)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

46    function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L46-L52](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L46-L52)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

55    function acceptAdmin() external 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L55-L65](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L55-L65)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

85    function setPriorityTxMaxGasLimit(uint256 _newPriorityTxMaxGasLimit) external onlyGovernor 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L85-L91](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L85-L91)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

179    function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)
180            external
181            override
182            nonReentrant
183            onlyValidator
184        
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L179-L202](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L179-L202)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

227    function _commitBatchesWithSystemContractsUpgrade(
228            StoredBatchInfo memory _lastCommittedBatchData,
229            CommitBatchInfo[] calldata _newBatchesData,
230            bytes32 _systemContractUpgradeTxHash
231        ) internal 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L227-L259](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L227-L259)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

275    function _executeOneBatch(StoredBatchInfo memory _storedBatch, uint256 _executedBatchIdx) internal 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L275-L288](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L275-L288)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

293    function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L293-L309](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L293-L309)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

313    function proveBatches(
314            StoredBatchInfo calldata _prevBatch,
315            StoredBatchInfo[] calldata _committedBatches,
316            ProofInput calldata _proof
317        ) external nonReentrant onlyValidator 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L313-L366](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L313-L366)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

391    function revertBatches(uint256 _newLastBatch) external nonReentrant onlyValidator 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L391-L407](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L391-L407)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

125    function _proveL2LogInclusion(
126            uint256 _batchNumber,
127            uint256 _index,
128            L2Log memory _log,
129            bytes32[] calldata _proof
130        ) internal view returns (bool) 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L125-L148](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L125-L148)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

194    function finalizeEthWithdrawal(
195            uint256 _l2BatchNumber,
196            uint256 _l2MessageIndex,
197            uint16 _l2TxNumberInBatch,
198            bytes calldata _message,
199            bytes32[] calldata _merkleProof
200        ) external override nonReentrant senderCanCallFunction(s.allowList) 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L194-L218](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L194-L218)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

277    function _verifyDepositLimit(address _depositor, uint256 _amount) internal 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L277-L283](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L277-L283)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

358    function _writePriorityOp(
359            WritePriorityOpParams memory _priorityOpParams,
360            bytes calldata _calldata,
361            bytes[] calldata _factoryDeps
362        ) internal returns (bytes32 canonicalTxHash) 
```
 should cache the following state variables:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L358-L387](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L358-L387)


</details>

# 


## Unnecessary Casting of Variables
- Severity: Gas Optimization
- Confidence: High

### Description
This detector scans for instances where a variable is casted to its own type. This is unnecessary and can be safely removed to improve code readability.

<details>

<summary>
There are 7 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

343    IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token)
```
Unnecessary cast: `IAllowList(allowList)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L343](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L343)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

356    bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""))
```
Unnecessary cast: `address(l2TokenBeacon)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L356](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L356)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

24    uint256 internal constant EXECUTED_PROPOSAL_TIMESTAMP = uint256(1)
```
Unnecessary cast: `uint256(1)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L24)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

278    IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0))
```
Unnecessary cast: `IAllowList(s.allowList)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L278](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L278)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

336    transaction = L2CanonicalTransaction({
337                txType: PRIORITY_OPERATION_L2_TX_TYPE,
338                from: uint256(uint160(_priorityOpParams.sender)),
339                to: uint256(uint160(_priorityOpParams.contractAddressL2)),
340                gasLimit: _priorityOpParams.l2GasLimit,
341                gasPerPubdataByteLimit: _priorityOpParams.l2GasPricePerPubdata,
342                maxFeePerGas: uint256(_priorityOpParams.l2GasPrice), // @audit g: Unnecessary casting
343                maxPriorityFeePerGas: uint256(0),
344                paymaster: uint256(0),
345                // Note, that the priority operation id is used as "nonce" for L1->L2 transactions
346                nonce: uint256(_priorityOpParams.txId), // @audit g: Unnecessary casting
347                value: _priorityOpParams.l2Value,
348                reserved: [_priorityOpParams.valueToMint, uint256(uint160(_priorityOpParams.refundRecipient)), 0, 0],
349                data: _calldata,
350                signature: new bytes(0),
351                factoryDeps: _hashFactoryDeps(_factoryDeps),
352                paymasterInput: new bytes(0),
353                reservedDynamic: new bytes(0)
354            })
```
Unnecessary cast: `uint256(_priorityOpParams.l2GasPrice)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L336-L354](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L336-L354)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

336    transaction = L2CanonicalTransaction({
337                txType: PRIORITY_OPERATION_L2_TX_TYPE,
338                from: uint256(uint160(_priorityOpParams.sender)),
339                to: uint256(uint160(_priorityOpParams.contractAddressL2)),
340                gasLimit: _priorityOpParams.l2GasLimit,
341                gasPerPubdataByteLimit: _priorityOpParams.l2GasPricePerPubdata,
342                maxFeePerGas: uint256(_priorityOpParams.l2GasPrice), // @audit g: Unnecessary casting
343                maxPriorityFeePerGas: uint256(0),
344                paymaster: uint256(0),
345                // Note, that the priority operation id is used as "nonce" for L1->L2 transactions
346                nonce: uint256(_priorityOpParams.txId), // @audit g: Unnecessary casting
347                value: _priorityOpParams.l2Value,
348                reserved: [_priorityOpParams.valueToMint, uint256(uint160(_priorityOpParams.refundRecipient)), 0, 0],
349                data: _calldata,
350                signature: new bytes(0),
351                factoryDeps: _hashFactoryDeps(_factoryDeps),
352                paymasterInput: new bytes(0),
353                reservedDynamic: new bytes(0)
354            })
```
Unnecessary cast: `uint256(0)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L336-L354](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L336-L354)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

336    transaction = L2CanonicalTransaction({
337                txType: PRIORITY_OPERATION_L2_TX_TYPE,
338                from: uint256(uint160(_priorityOpParams.sender)),
339                to: uint256(uint160(_priorityOpParams.contractAddressL2)),
340                gasLimit: _priorityOpParams.l2GasLimit,
341                gasPerPubdataByteLimit: _priorityOpParams.l2GasPricePerPubdata,
342                maxFeePerGas: uint256(_priorityOpParams.l2GasPrice), // @audit g: Unnecessary casting
343                maxPriorityFeePerGas: uint256(0),
344                paymaster: uint256(0),
345                // Note, that the priority operation id is used as "nonce" for L1->L2 transactions
346                nonce: uint256(_priorityOpParams.txId), // @audit g: Unnecessary casting
347                value: _priorityOpParams.l2Value,
348                reserved: [_priorityOpParams.valueToMint, uint256(uint160(_priorityOpParams.refundRecipient)), 0, 0],
349                data: _calldata,
350                signature: new bytes(0),
351                factoryDeps: _hashFactoryDeps(_factoryDeps),
352                paymasterInput: new bytes(0),
353                reservedDynamic: new bytes(0)
354            })
```
Unnecessary cast: `uint256(_priorityOpParams.txId)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L336-L354](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L336-L354)


</details>

# 


## Detection of Unnecessary Checked Increments in Solidity Loop Statements
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 120

### Description
Smart contract code that contains checked increments (++i) within loops.Those operations not require overflow checking. In these cases, it is more efficient to use unchecked{++i} or unchecked{i++} to save on gas costs, as this keyword was introduced in Solidity version 0.8.0. By using unchecked, unnecessary overflow checks can be avoided, resulting in a savings of 30-40 gas per loop iteration.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

227    ++i
```
 should be inside uncheck block
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L227](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L227)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

206    ++i
```
 should be inside uncheck block
 
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L206](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L206)


</details>

# 


## Use Assembly for Hash Calculation
- Severity: Gas Optimization
- Confidence: Medium
- Total Gas Saved: 1280

### Description
Detects places in the code where hash calculation could be done using inline assembly to optimize gas usage.

<details>

<summary>
There are 16 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

356    bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L356](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L356)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

194    bytes32 l2ProtocolUpgradeTxHash = keccak256(encodedTransaction)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L194](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L194)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

207    return keccak256(abi.encode(_operation))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L207](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L207)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

79    s.storedBatchHashes[0] = keccak256(abi.encode(storedBatchZero))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L79](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L79)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

424    return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L424](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L424)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

122    bytes32 providedL2ToL1PubdataHash = keccak256(_newBatch.totalL2ToL1Pubdata)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L122](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L122)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

448    bytes32 l2ToL1LogsHash = keccak256(_batch.systemLogs)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L448](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L448)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

420    bytes32 passThroughDataHash = keccak256(_batchPassThroughData(_newBatchData))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L420](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L420)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

421    bytes32 metadataHash = keccak256(_batchMetaParameters())
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L421](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L421)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

422    bytes32 auxiliaryOutputHash = keccak256(_batchAuxiliaryOutput(_newBatchData, _stateDiffHash))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L422](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L422)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

461    return keccak256(abi.encode(_storedBatchInfo))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L461](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L461)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

267    concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L267](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L267)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

374    return
375                uint256(
376                    keccak256(
377                        abi.encodePacked(
378                            _prevBatchCommitment,
379                            _currentBatchCommitment,
380                            _verifierParams.recursionNodeLevelVkHash,
381                            _verifierParams.recursionLeafLevelVkHash
382                        )
383                    )
384                ) & INPUT_MASK
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L374-L384](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L374-L384)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

133    bytes32 hashedLog = keccak256(
134                abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBatch, _log.sender, _log.key, _log.value)
135            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L133-L135](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L133-L135)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

152    return
153                L2Log({
154                    l2ShardId: 0,
155                    isService: true,
156                    txNumberInBatch: _message.txNumberInBatch,
157                    sender: L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR,
158                    key: bytes32(uint256(uint160(_message.sender))),
159                    value: keccak256(_message.data)
160                })
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L152-L160](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L152-L160)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

369    canonicalTxHash = keccak256(transactionEncoding)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L369](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L369)


</details>

# 


## Usage of Custom Errors for Gas Efficiency
- Severity: Gas Optimization
- Confidence: High
- Total Gas Saved: 33396

### Description
This detector flags functions that use revert()/require() strings, which are less gas efficient than custom errors. Custom errors, available from Solidity version 0.8.4, save approximately 50 gas each time they're used by avoiding the need to allocate and store the revert string.

<details>

<summary>
There are 121 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

92    require(_l2TokenBeacon != address(0), "nf")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L92](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L92)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

93    require(_governor != address(0), "nh")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L93](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L93)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

95    require(_factoryDeps.length == 3, "mk")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L95](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L95)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

97    require(msg.value == _deployBridgeImplementationFee + _deployBridgeProxyFee, "fee")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L97](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L97)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

186    require(_amount != 0, "2T")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L186](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L186)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

188    require(amount == _amount, "1T")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L188](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L188)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

274    require(proofValid, "yn")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L274](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L274)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

277    require(amount > 0, "y1")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L277](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L277)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

302    require(!isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "pw")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L302](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L302)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

314    require(success, "nq")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L314](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L314)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

331    require(_l2ToL1message.length == 76, "kk")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L331](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L331)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

334    require(bytes4(functionSignature) == this.finalizeWithdrawal.selector, "nt")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L334](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L334)


#

```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

349    require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L349](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L349)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

90    require(_l2WethAddress != address(0), "L2 WETH address cannot be zero")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L90](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L90)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

91    require(_governor != address(0), "Governor address cannot be zero")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L91](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L91)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

92    require(_factoryDeps.length == 2, "Invalid factory deps length provided")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L92](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L92)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

93    require(
94                msg.value == _deployBridgeImplementationFee + _deployBridgeProxyFee,
95                "Miscalculated deploy transactions fees"
96            )
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L93-L96](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L93-L96)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

169    require(_l1Token == l1WethAddress, "Invalid L1 token address")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L169](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L169)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

170    require(_amount != 0, "Amount cannot be zero")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L170](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L170)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

225    revert("Method not supported. Failed deposit funds are sent to the L2 refund recipient address.")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L225](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L225)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

242    require(!isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "Withdrawal is already finalized")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L242](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L242)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

256    require(success, "vq")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L256](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L256)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

281    require(_message.length == 96, "Incorrect ETH message with additional data length")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L281](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L281)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

284    require(
285                bytes4(functionSignature) == IMailbox.finalizeEthWithdrawal.selector,
286                "Incorrect ETH message function selector"
287            )
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L284-L287](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L284-L287)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

291    require(l1EthReceiver == address(this), "Wrong L1 ETH withdraw receiver")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L291](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L291)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

297    require(l2Sender == l2Bridge, "The withdrawal was not initiated by L2 bridge")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L297](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L297)


#

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

313    require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L313](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L313)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

62    require(targetsLength == _accessModes.length, "yg")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L62](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L62)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

94    require(callersLength == _targets.length, "yw")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L94](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L94)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

95    require(callersLength == _functionSigs.length, "yx")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L95](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L95)


#

```
File: code/contracts/ethereum/contracts/common/AllowList.sol

96    require(callersLength == _enables.length, "yy")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L96](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L96)


#

```
File: code/contracts/ethereum/contracts/common/AllowListed.sol

15    require(_allowList.canCall(msg.sender, address(this), msg.sig), "nr")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L15](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L15)


#

```
File: code/contracts/ethereum/contracts/common/ReentrancyGuard.sol

60    require(lockSlotOldValue == 0, "1B")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L60](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L60)


#

```
File: code/contracts/ethereum/contracts/common/ReentrancyGuard.sol

77    require(_status == _NOT_ENTERED, "r1")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L77](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L77)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

44    require(_admin != address(0), "Admin should be non zero address")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L44](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L44)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

157    require(isOperationPending(_id), "Operation must be pending")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L157](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L157)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

174    require(isOperationReady(id), "Operation must be ready before execution")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L174](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L174)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

179    require(isOperationReady(id), "Operation must be ready after execution")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L179](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L179)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

193    require(isOperationPending(id), "Operation must be pending before execution")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L193](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L193)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

198    require(isOperationPending(id), "Operation must be pending after execution")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L198](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L198)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

218    require(!isOperation(_id), "Operation with this proposal id already exists")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L218](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L218)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

219    require(_delay >= minDelay, "Proposed delay is less than minimum delay")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L219](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L219)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

242    require(_predecessorId == bytes32(0) || isOperationDone(_predecessorId), "Predecessor operation not completed")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L242](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L242)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

61    require(msg.sender == address(this), "Only governance contract itself allowed to call this function")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L61](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L61)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

67    require(msg.sender == securityCouncil, "Only security council allowed to call this function")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L67](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L67)


#

```
File: code/contracts/ethereum/contracts/governance/Governance.sol

73    require(
74                msg.sender == owner() || msg.sender == securityCouncil,
75                "Only the owner and security council are allowed to call this function"
76            )
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L73-L76](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L73-L76)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

74    require(block.timestamp >= _proposedUpgrade.upgradeTimestamp, "Upgrade is not ready yet")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L74](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L74)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

173    require(_l2ProtocolUpgradeTx.txType == SYSTEM_UPGRADE_L2_TX_TYPE, "L2 system upgrade tx type is wrong")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L173](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L173)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

187    require(
188                _l2ProtocolUpgradeTx.nonce == _newProtocolVersion,
189                "The new protocol version should be included in the L2 system upgrade tx"
190            )
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L187-L190](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L187-L190)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

203    require(_factoryDeps.length == _expectedHashes.length, "Wrong number of factory deps")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L203](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L203)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

204    require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

207    require(
208                    L2ContractHelper.hashL2Bytecode(_factoryDeps[i]) == bytes32(_expectedHashes[i]),
209                    "Wrong factory dep hash"
210                )
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L207-L210](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L207-L210)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

218    require(
219                _newProtocolVersion > previousProtocolVersion, // @audit m: If `_newProtocolVersion` is `type(uint256).max`, no further upgrades could take place
220                "New protocol version is not greater than the current one"
221            )
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L218-L221](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L218-L221)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

224    require(s.l2SystemContractsUpgradeTxHash == bytes32(0), "Previous upgrade has not been finalized")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L224](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L224)


#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

225    require(
226                s.l2SystemContractsUpgradeBatchNumber == 0,
227                "The batch number of the previous upgrade has not been cleaned"
228            )
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L225-L228](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L225-L228)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

58    require(address(_initalizeData.verifier) != address(0), "vt")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L58](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L58)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

59    require(_initalizeData.governor != address(0), "vy")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L59](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L59)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

60    require(_initalizeData.admin != address(0), "hc")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L60](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L60)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

61    require(_initalizeData.priorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "vu")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L61](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L61)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

16    require(_chainId == block.chainid, "pr")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L16](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L16)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

27    require(msg.data.length >= 4 || msg.data.length == 0, "Ut")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L27](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L27)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

32    require(facetAddress != address(0), "F")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L32](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L32)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

33    require(!diamondStorage.isFrozen || !facet.isFreezable, "q1")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L33](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L33)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

125    require(block.timestamp >= commitBatchTimestamp + delay, "5c")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L125](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L125)


#

```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

68    require(msg.sender == validator, "8h")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L68](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L68)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

33    require(msg.sender == pendingGovernor, "n4")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L33](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L33)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

57    require(msg.sender == pendingAdmin, "n4")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L57](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L57)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

86    require(_newPriorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "n5")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L86](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L86)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

114    require(!diamondStorage.isFrozen, "a9")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L114](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L114)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

125    require(diamondStorage.isFrozen, "a7")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L125](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L125)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

21    require(msg.sender == s.governor, "1g")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L21](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L21)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

27    require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L27](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L27)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

33    require(s.validators[msg.sender], "1h")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L33](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L33)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

34    require(_newBatch.batchNumber == _previousBatch.batchNumber + 1, "f")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L34](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L34)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

47    require(_previousBatch.batchHash == previousBatchHash, "l")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L47](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L47)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

49    require(expectedPriorityOperationsHash == _newBatch.priorityOperationsHash, "t")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L49](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L49)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

51    require(expectedNumberOfLayer1Txs == _newBatch.numberOfLayer1Txs, "ta")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L51](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L51)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

83    require(batchTimestamp == _expectedBatchTimestamp, "tb")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L83](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L83)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

87    require(_previousBatchTimestamp < batchTimestamp, "h3")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L87](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L87)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

95    require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L95](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L95)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

96    require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L96](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L96)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

132    require(!_checkBit(processedLogs, uint8(logKey)), "kp")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L132](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L132)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

137    require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lm")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L137](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L137)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

140    require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "ln")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L140](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L140)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

141    require(providedL2ToL1PubdataHash == logValue, "wp")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L141](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L141)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

143    require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lb")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L143](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L143)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

146    require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sc")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L146](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L146)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

149    require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sv")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L149](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L149)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

152    require(logSender == L2_BOOTLOADER_ADDRESS, "bl")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L152](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L152)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

155    require(logSender == L2_BOOTLOADER_ADDRESS, "bk")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L155](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L155)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

158    require(logSender == L2_BOOTLOADER_ADDRESS, "bu")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L158](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L158)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

159    require(_expectedSystemContractUpgradeTxHash == logValue, "ut")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L159](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L159)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

161    revert("ul")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L161](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L161)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

169    require(processedLogs == 127, "b7")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L169](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L169)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

171    require(processedLogs == 255, "b8")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L171](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L171)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

186    require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L186](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L186)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

187    require(_newBatchesData.length > 0, "No batches to commit")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L187](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L187)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

238    require(s.l2SystemContractsUpgradeBatchNumber == 0, "ik")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L238](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L238)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

277    require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L277](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L277)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

278    require(
279                _hashStoredBatchInfo(_storedBatch) == s.storedBatchHashes[currentBatchNumber],
280                "exe10" // executing batch should be committed
281            )
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L278-L281](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L278-L281)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

284    require(priorityOperationsHash == _storedBatch.priorityOperationsHash, "x")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L284](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L284)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

302    require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L302](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L302)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

329    require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L329](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L329)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

334    require(
335                    _hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],
336                    "o1"
337                )
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L334-L337](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L334-L337)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

348    require(currentTotalBatchesVerified <= s.totalBatchesCommitted, "q")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L348](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L348)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

361    require(successVerifyProof, "p")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L361](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L361)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

392    require(s.totalBatchesCommitted > _newLastBatch, "v1")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L392](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L392)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

393    require(_newLastBatch >= s.totalBatchesExecuted, "v2")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L393](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L393)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

446    require(_batch.systemLogs.length <= MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES, "pu")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L446](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L446)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Getters.sol

162    require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L162](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L162)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

121    require(callSuccess, "pz")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L121](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L121)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

131    require(_batchNumber <= s.totalBatchesExecuted, "xx")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L131](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L131)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

138    require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L138](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L138)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

201    require(!s.isEthWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "jj")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L201](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L201)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

212    require(proofValid, "pi")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L212](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L212)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

259    require(_l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L259](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L259)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

281    require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L281](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L281)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

296    require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "uj")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L296](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L296)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

308    require(msg.value >= baseCost + _l2Value, "mv")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L308](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L308)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

423    require(_message.length >= 56, "pm")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L423](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L423)


#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

426    require(bytes4(functionSignature) == this.finalizeEthWithdrawal.selector, "is")
```
 use custom error instead 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L426](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L426)


</details>

# 
