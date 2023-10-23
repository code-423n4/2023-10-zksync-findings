# Summary
### Low Risk Issues
|Title|Issue|Instances|
|-|:-|:-:|
|[L-1] The new `recursionCircuitsSetVksHash` isn't allowed to be zero even though it's deprecated | [The new `recursionCircuitsSetVksHash` isn't allowed to be zero even though it's deprecated](#the-new-recursioncircuitssetvkshash-isnt-allowed-to-be-zero-even-though-its-deprecated) | 1 |
|[L-2] `PriorityOperation.layer2Tip` should be set to `transaction.maxPriorityFeePerGas` | [`PriorityOperation.layer2Tip` should be set to `transaction.maxPriorityFeePerGas`](#priorityoperationlayer2tip-should-be-set-to-transactionmaxpriorityfeepergas) | 1 |
|[L-3] Bytecode hash isn't validated on initialization | [Bytecode hash isn't validated on initialization](#bytecode-hash-isnt-validated-on-initialization) | 2 |
|[L-4] `Diamond.diamondCut` might remove selectors from the wrong facet | [`Diamond.diamondCut` might remove selectors from the wrong facet](#diamonddiamondcut-might-remove-selectors-from-the-wrong-facet) | 1 |
|[L-5] Wrong event emission | [Wrong event emission](#wrong-event-emission) | 1 |
|[L-6] Array out of bounds accesses | [Array out of bounds accesses](#array-out-of-bounds-accesses) | 1 |
|[L-7] Casting block.timestamp can reduce the lifespan of a contract | [Casting block.timestamp can reduce the lifespan of a contract](#casting-blocktimestamp-can-reduce-the-lifespan-of-a-contract) | 1 |
|[L-8] Empty Uncontrolled Ether Flow in receive()/payable fallback() | [Empty Uncontrolled Ether Flow in receive()/payable fallback()](#empty-uncontrolled-ether-flow-in-receivepayable-fallback) | 4 |
|[L-9] Local variable shadowing | [Local variable shadowing](#local-variable-shadowing) | 2 |
|[L-10] Upgradeable contract uses non-upgradeable version of the OpenZeppelin libraries/contracts | [Upgradeable contract uses non-upgradeable version of the OpenZeppelin libraries/contracts](#upgradeable-contract-uses-non-upgradeable-version-of-the-openzeppelin-librariescontracts) | 2 |
|[L-11] name() is not a part of the ERC-20 standard | [name() is not a part of the ERC-20 standard](#name-is-not-a-part-of-the-erc-20-standard) | 1 |
|[L-12] symbol() is not a part of the ERC-20 standard | [symbol() is not a part of the ERC-20 standard](#symbol-is-not-a-part-of-the-erc-20-standard) | 1 |
|[L-13] Controlled Delegatecall | [Controlled Delegatecall](#controlled-delegatecall) | 1 |
|[L-14] Contracts that lock Ether | [Contracts that lock Ether](#contracts-that-lock-ether) | 3 |

Total: 22 instances over 14 issues

### Non-Critical Issues
|Title|Issue|Instances|
|-|:-|:-:|
|[N-1] Complex math in a single line | [Complex math in a single line](#complex-math-in-a-single-line) | 64 |
|[N-2] Control structures do not follow the Solidity Style Guide | [Control structures do not follow the Solidity Style Guide](#control-structures-do-not-follow-the-solidity-style-guide) | 55 |
|[N-3] Dead-code | [Dead-code](#dead-code) | 26 |
|[N-4] Avoid double casting | [Avoid double casting](#avoid-double-casting) | 72 |
|[N-5] Function names should differ | [Function names should differ](#function-names-should-differ) | 5 |
|[N-6] Inconsistency Spacing in Comments | [Inconsistency Spacing in Comments](#inconsistency-spacing-in-comments) | 4 |
|[N-7] Inconsistent usage of require/error | [Inconsistent usage of require/error](#inconsistent-usage-of-requireerror) | 10 |
|[N-8] Functions Not Implementing an Interface | [Functions Not Implementing an Interface](#functions-not-implementing-an-interface) | 34 |
|[N-9] Use of old Solidity version | [Use of old Solidity version](#use-of-old-solidity-version) | 84 |
|[N-10] Consider Disable Ownership Renouncement in Ownable Contracts | [Consider Disable Ownership Renouncement in Ownable Contracts](#consider-disable-ownership-renouncement-in-ownable-contracts) | 3 |
|[N-11] Event Emission Preceding External Calls: A Best Practice | [Event Emission Preceding External Calls: A Best Practice](#event-emission-preceding-external-calls-a-best-practice) | 15 |
|[N-12] Variable names too similar | [Variable names too similar](#variable-names-too-similar) | 9 |
|[N-13] Enforce Underscore Prefix for Non-External Variable and Function Names | [Enforce Underscore Prefix for Non-External Variable and Function Names](#enforce-underscore-prefix-for-non-external-variable-and-function-names) | 124 |
|[N-14] Whitespace in Expressions | [Whitespace in Expressions](#whitespace-in-expressions) | 5 |
|[N-15] Variables with all caps names that are not constants or immutables | [Variables with all caps names that are not constants or immutables](#variables-with-all-caps-names-that-are-not-constants-or-immutables) | 1 |
|[N-16] Cast to bytes or bytes32 for clearer semantic meaning | [Cast to bytes or bytes32 for clearer semantic meaning](#cast-to-bytes-or-bytes32-for-clearer-semantic-meaning) | 3 |
|[N-17] Token contract should have a blacklist function or modifier | [Token contract should have a blacklist function or modifier](#token-contract-should-have-a-blacklist-function-or-modifier) | 2 |
|[N-18] Do not calculate constants | [Do not calculate constants](#do-not-calculate-constants) | 2 |
|[N-19] Empty function body | [Empty function body](#empty-function-body) | 2 |
|[N-20] Override function arguments that are unused should have the variable name removed or commented out | [Override function arguments that are unused should have the variable name removed or commented out](#override-function-arguments-that-are-unused-should-have-the-variable-name-removed-or-commented-out) | 1 |
|[N-21] Too many digits | [Too many digits](#too-many-digits) | 5 |
|[N-22] Unsafe use of pragma solidity >= without an upper bound | [Unsafe use of pragma solidity >= without an upper bound](#unsafe-use-of-pragma-solidity--without-an-upper-bound) | 1 |

Total: 527 instances over 22 issues

#


## The new `recursionCircuitsSetVksHash` isn't allowed to be zero even though it's deprecated
- Severity: Low Risk
- Confidence: High

### Description
In order to upgrade the `VerifierParams`, one have to provide the `BaseZkSyncUpgrade._setVerifierParams` function with non-zero values for all 3 of its fields: `recursionNodeLevelVkHash`, `recursionLeafLevelVkHash` and `recursionCircuitsSetVksHash`. However, the `recursionCircuitsSetVksHash` field is never used, and is considered deprecated. Since its value means nothing to us on upgrade, the most reasonable thing to do is to give it the zero value - but this will cancel the upgrade, because zero is not allowed for this field!

Such a cancelation of the upgrade might even be more dangerous, in case the one who called for that upgrade havn't noticed that the `VerifierParams` weren't updated as expected. (There is no revert!)

<details>

<summary>
There is 1 instance of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

128        if (
129            _newVerifierParams.recursionNodeLevelVkHash == bytes32(0) ||
130            _newVerifierParams.recursionLeafLevelVkHash == bytes32(0) ||
131            _newVerifierParams.recursionCircuitsSetVksHash == bytes32(0)
132        ) {
133            return;
134        }
```
Don't `return` if `_newVerifierParams.recursionCircuitsSetVksHash == bytes32(0)`. Zero is a legitimate value for this field.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L128-L134](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L128-L134)

</details>

# 


## `PriorityOperation.layer2Tip` should be set to `transaction.maxPriorityFeePerGas`
- Severity: Low Risk
- Confidence: High

### Description
When the function `Mailbox._writePriorityOp` pushes a `PriorityOperation` into the priority queue, it sets the `layer2Tip` to zero. While today this value is indeed zero, it may change in the future, but this isn't the place in the code where the actual tip should be determined. The tip (which is currently always zero), should be determined within the `_serializeL2Transaction` function, and be stored in `transaction.maxPriorityFeePerGas`, because according to the docs, the `maxPriorityFeePerGas` field of the `L2CanonicalTransaction` represents:

> The additional fee that is paid directly to the validator to incentivize them to include the transaction in a batch. Analog to the EIP-1559 `maxPriorityFeePerGas` on an L1 transactions

Then, the `Mailbox._writePriorityOp` function should set the `layer2Tip` value of its new `PriorityOperation` to `transaction.maxPriorityFeePerGas` (instead of to a hard-coded zero).

<details>

<summary>
There is 1 instance of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

370            PriorityOperation({
371                canonicalTxHash: canonicalTxHash,
372                expirationTimestamp: _priorityOpParams.expirationTimestamp,
373                layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
374            })
```
Set `layer2Tip` to `transaction.maxPriorityFeePerGas` instead of zero.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L370-L374](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L370-L374)

</details>

# 


## Bytecode hash isn't validated on initialization
- Severity: Low Risk
- Confidence: High

### Description
The function `DiamondInit.initialize` that initializes the system, should validate that the bytcode hashes that are given to it are valid. This is done on every update, but not on the original initialization.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

81        s.l2BootloaderBytecodeHash = _initalizeData.l2BootloaderBytecodeHash;
```
Validate `_initalizeData.l2BootloaderBytecodeHash` before initializing `s.l2BootloaderBytecodeHash` with its value.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L81](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L81)


#

```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

82        s.l2DefaultAccountBytecodeHash = _initalizeData.l2DefaultAccountBytecodeHash;
```
Validate `_initalizeData.l2DefaultAccountBytecodeHash` before initializing `s.l2DefaultAccountBytecodeHash` with its value.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L82](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L82)

</details>

# 


## `Diamond.diamondCut` might remove selectors from the wrong facet
- Severity: Low Risk
- Confidence: High

### Description
The function `Diamond.diamondCut` removes selectors from the facet-selector mapping if asked to (if `Action.Remove` was given). However, the `_removeFunctions` function never ensures that the selector that its removal is requested, is indeed mapped to the facet given as argument. It means that selectors can be deleted by mistake from that mapping.

<details>

<summary>
There is 1 instance of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

173    function _removeFunctions(address _facet, bytes4[] memory _selectors) private {
174        DiamondStorage storage ds = getDiamondStorage();
175
176        require(_facet == address(0), "a1"); // facet address must be zero
177
178        uint256 selectorsLength = _selectors.length;
179        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
180            bytes4 selector = _selectors[i];
181            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
182            require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet
183
184            _removeOneFunction(oldFacet.facetAddress, selector);
185        }
186    }
```
Add a require statement before line 184 to make sure `oldFacet.facetAddress` equals `_facet`.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L173-L186](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L173-L186)

</details>

# 


## Wrong event emission
- Severity: Low Risk
- Confidence: High

### Description
The function `Admin.setPendingAdmin` emits a `NewPendingGovernor` event instead of a `NewPendingAdmin` event.

<details>

<summary>
There is 1 instance of this issue:

</summary>

###
#

```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

49        emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);
```
Change to emit a `NewPendingAdmin` event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L49](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L49)

</details>

# 


## Array out of bounds accesses
- Severity: Low
- Confidence: Medium

### Description
If the lengths of arrays are not checked before they're accessed, user operations may not be executed properly due to the potential issue of accessing an array out of its bounds. In Solidity, accessing an array beyond its boundaries can cause a runtime error known as an out-of-bounds exception. This error arises when attempting to read or write to an element of an array that does not exist. Therefore, it is critical to avoid out-of-bounds exceptions while accessing arrays in Solidity code to prevent unexpected halts and possible loss of funds.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

229    CommitBatchInfo[] calldata _newBatchesData
```
 is accessed on 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

241    s.l2SystemContractsUpgradeBatchNumber = _newBatchesData[0].batchNumber
```
 index that might be out-of-bounds

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L229](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L229)


</details>

# 


## Casting block.timestamp can reduce the lifespan of a contract
- Severity: Low
- Confidence: High

### Description
Casting `block.timestamp` to smaller integer types can reduce the effective lifespan of a contract. `block.timestamp` returns the current block timestamp as seconds since the unix epoch as `uint`. When it's cast to a smaller integer type, this effectively reduces the maximum timestamp value the contract can handle. As a result, the contract might stop functioning correctly after a certain point in time, even though `block.timestamp` itself will continue to increase with each block.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

86    uint32 timestamp = uint32(block.timestamp)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L86](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L86)


</details>

# 


## Empty Uncontrolled Ether Flow in receive()/payable fallback()
- Severity: Low
- Confidence: Medium

### Description
This detector flags contracts with empty receive()/payable fallback() functions that do not contain an authorization check. This can potentially lead to loss of funds, as anyone can send Ether to the contract without a way of getting it back out.

<details>

<summary>
There are 4 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

264    receive() external payable 
```
 don't keep `receive` empty.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L264](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L264)


- 
```
File: code/system-contracts/contracts/DefaultAccount.sol

231    receive() external payable 
```
 don't keep `receive` empty.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L231-L233](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L231-L233)


- 
```
File: code/system-contracts/contracts/EmptyContract.sol

14    fallback() external payable 
```
 don't keep `fallback` empty.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L14](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L14)


- 
```
File: code/system-contracts/contracts/EmptyContract.sol

16    receive() external payable 
```
 don't keep `receive` empty.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L16](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L16)


</details>

# 


## Local variable shadowing
- Severity: Low
- Confidence: High

### Description
Detection of shadowing using local variables.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol

78    address[] memory facets
```
 shadows:
	- 
```
File: code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol

74    function facets() external view returns (Facet[] memory);
```
 (function)

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L78](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L78)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

88    uint256 bytecodeLenInWords = _bytecode.length / 32
```
 shadows:
	- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

46    function bytecodeLenInWords(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) 
```
 (function)

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L88](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L88)


</details>

# 


## Upgradeable contract uses non-upgradeable version of the OpenZeppelin libraries/contracts
- Severity: Low
- Confidence: High

### Description
Detects when upgradeable contracts use non-upgradeable OpenZeppelin libraries/contracts.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

7    import "@openzeppelin/contracts/proxy/beacon/BeaconProxy.sol";
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L7](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L7)


- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

8    import "@openzeppelin/contracts/proxy/beacon/UpgradeableBeacon.sol";
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L8](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L8)


</details>

# 


## name() is not a part of the ERC-20 standard
- Severity: Low
- Confidence: High

### Description
The name() function is not a part of the ERC-20 standard, and was added later as an optional extension. As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function. 

There are several ways to address this issue:
1. Check the return value: If the function name() doesn't exist in the contract, the call will return false.
2. Use a try-catch statement: This allows for error handling if the name() function does not exist.
3. Use an interface with optional name(): This provides a way to handle tokens that do not have the name() function.
Among these options, checking the return value is recommended for clarity and simplicity.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

128    return super.name()
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L128](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L128)


</details>

# 


## symbol() is not a part of the ERC-20 standard
- Severity: Low
- Confidence: High

### Description
The symbol() function is not a part of the ERC-20 standard, and was added later as an optional extension. As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function. 

There are several ways to address this issue:
1. Check the return value: If the function symbol() doesn't exist in the contract, the call will return false.
2. Use a try-catch statement: This allows for error handling if the symbol() function does not exist.
3. Use an interface with optional symbol(): This provides a way to handle tokens that do not have the symbol() function.
Among these options, checking the return value is recommended for clarity and simplicity.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

134    return super.symbol()
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L134](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L134)


</details>

# 


## Controlled Delegatecall
- Severity: Low
- Confidence: Medium

### Description
This detector identifies instances where a contract's code allows a user to control the target of a delegatecall or callcode operation. 
Delegatecalls and callcodes are powerful features in Solidity that allow one contract to execute the code of another contract within its own context.

However, they also pose significant security risks if misused or not properly secured.
When the target of a delegatecall or callcode is controlled by a user, that user may be able to manipulate the contract's state or behavior in unexpected and potentially harmful ways.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/ComplexUpgrader.sol

23    function upgrade(address _delegateTo, bytes calldata _calldata) external payable 
```
 uses delegatecall to a input-controlled function id
	- 
```
File: code/system-contracts/contracts/ComplexUpgrader.sol

27    (bool success, bytes memory returnData) = _delegateTo.delegatecall(_calldata)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol#L23-L33](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol#L23-L33)


</details>

# 


## Contracts that lock Ether
- Severity: Low
- Confidence: High

### Description
Contract with a `payable` function, but without a withdrawal capacity.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
- Contract locking ether found:
	Contract 
```
File: code/system-contracts/contracts/Compressor.sol

36    contract Compressor is ICompressor, ISystemContract 
```
 has payable functions:
	 - 
```
File: code/system-contracts/contracts/interfaces/ICompressor.sol

15    function publishCompressedBytecode(
16            bytes calldata _bytecode,
17            bytes calldata _rawCompressedData
18        ) external payable returns (bytes32 bytecodeHash);
```

	 - 
```
File: code/system-contracts/contracts/interfaces/ICompressor.sol

20    function verifyCompressedStateDiffs(
21            uint256 _numberOfStateDiffs,
22            uint256 _enumerationIndexSize,
23            bytes calldata _stateDiffs,
24            bytes calldata _compressedStateDiffs
25        ) external payable returns (bytes32 stateDiffHash);
```

	 - 
```
File: code/system-contracts/contracts/Compressor.sol

56    function publishCompressedBytecode(
57            bytes calldata _bytecode,
58            bytes calldata _rawCompressedData
59        ) external payable onlyCallFromBootloader returns (bytes32 bytecodeHash) 
```

	 - 
```
File: code/system-contracts/contracts/Compressor.sol

119    function verifyCompressedStateDiffs(
120            uint256 _numberOfStateDiffs,
121            uint256 _enumerationIndexSize,
122            bytes calldata _stateDiffs,
123            bytes calldata _compressedStateDiffs
124        ) external payable onlyCallFrom(address(L1_MESSENGER_CONTRACT)) returns (bytes32 stateDiffHash) 
```

	But does not have a function to withdraw the ether

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L36-L256](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L36-L256)


- Contract locking ether found:
	Contract 
```
File: code/system-contracts/contracts/EmptyContract.sol

13    contract EmptyContract 
```
 has payable functions:
	 - 
```
File: code/system-contracts/contracts/EmptyContract.sol

14    fallback() external payable 
```

	 - 
```
File: code/system-contracts/contracts/EmptyContract.sol

16    receive() external payable 
```

	But does not have a function to withdraw the ether

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L13-L17](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L13-L17)


- Contract locking ether found:
	Contract 
```
File: code/system-contracts/contracts/L2EthToken.sol

20    contract L2EthToken is IEthToken, ISystemContract 
```
 has payable functions:
	 - 
```
File: code/system-contracts/contracts/interfaces/IEthToken.sol

22    function withdraw(address _l1Receiver) external payable;
```

	 - 
```
File: code/system-contracts/contracts/interfaces/IEthToken.sol

24    function withdrawWithMessage(address _l1Receiver, bytes calldata _additionalData) external payable;
```

	 - 
```
File: code/system-contracts/contracts/L2EthToken.sol

74    function withdraw(address _l1Receiver) external payable override 
```

	 - 
```
File: code/system-contracts/contracts/L2EthToken.sol

87    function withdrawWithMessage(address _l1Receiver, bytes memory _additionalData) external payable override 
```

	But does not have a function to withdraw the ether

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L20-L145](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L20-L145)


</details>

# 


## Complex math in a single line
- Severity: Non-Critical
- Confidence: Medium

### Description
Complex arithmetic calculations in a single line can reduce code readability. It's often beneficial to split these into multiple lines or separate operations.

<details>

<summary>
There are 64 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

313    require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn")
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L313](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L313)


- 
```
File: code/contracts/ethereum/contracts/common/AllowList.sol

43    return
44                accessMode == AccessMode.Public ||
45                (accessMode == AccessMode.SpecialAccessOnly && hasSpecialAccessToCall[_caller][_target][_functionSig])
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L43-L45](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L43-L45)


- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

93    return state == OperationState.Waiting || state == OperationState.Ready
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L93](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L93)


- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

73    require(
74                msg.sender == owner() || msg.sender == securityCouncil,
75                "Only the owner and security council are allowed to call this function"
76            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L73-L76](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L73-L76)


- 
```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

131    _newVerifierParams.recursionNodeLevelVkHash == bytes32(0) ||
132                _newVerifierParams.recursionLeafLevelVkHash == bytes32(0) ||
133                _newVerifierParams.recursionCircuitsSetVksHash == bytes32(0)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L131-L133](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L131-L133)


- 
```
File: code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

27    require(msg.data.length >= 4 || msg.data.length == 0, "Ut")
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L27](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L27)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

27    require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin")
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L27](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L27)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

191    systemContractsUpgradeTxHash == bytes32(0) || s.l2SystemContractsUpgradeBatchNumber != 0
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L191](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L191)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

277    require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k")
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L277](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L277)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

305    batchWhenUpgradeHappened != 0 && batchWhenUpgradeHappened <= newTotalBatchesExecuted
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L305](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L305)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

466    return (_bitMap & (1 << _index)) > 0
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L466](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L466)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

183    uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L183](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L183)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

402    mstore(add(hashedFactoryDeps, mul(add(i, 1), 32)), hashedBytecode)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L402](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L402)


- 
```
File: code/system-contracts/contracts/AccountCodeStorage.sol

104    codeHash == 0x00 && NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(account) > 0
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L104](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L104)


- 
```
File: code/system-contracts/contracts/AccountCodeStorage.sol

134    uint160(account) > CURRENT_MAX_PRECOMPILE_ADDRESS &&
135                codeHash != 0x00 &&
136                !Utils.isContractConstructing(codeHash)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L134-L136](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L134-L136)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

94    require(vInt == 27 || vInt == 28, "Invalid v value")
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L94](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L94)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

99    vInt += 8 + block.chainid * 2
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L99](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L99)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

107    uint256 listLength = encodedNonce.length +
108                    encodedGasParam.length +
109                    encodedTo.length +
110                    encodedValue.length +
111                    encodedDataLength.length +
112                    _transaction.data.length +
113                    rEncoded.length +
114                    sEncoded.length +
115                    vEncoded.length
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L107-L115](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L107-L115)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

193    require(vInt == 27 || vInt == 28, "Invalid v value")
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L193](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L193)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

200    uint256 listLength = encodedFixedLengthParams.length +
201                    encodedDataLength.length +
202                    _transaction.data.length +
203                    encodedAccessListLength.length +
204                    rEncoded.length +
205                    sEncoded.length +
206                    vEncoded.length
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L200-L206](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L200-L206)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

288    require(vInt == 27 || vInt == 28, "Invalid v value")
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L288](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L288)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

295    uint256 listLength = encodedFixedLengthParams.length +
296                    encodedDataLength.length +
297                    _transaction.data.length +
298                    encodedAccessListLength.length +
299                    rEncoded.length +
300                    sEncoded.length +
301                    vEncoded.length
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L295-L301](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L295-L301)


- 
```
File: code/system-contracts/contracts/Compressor.sol

64    require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big")
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L64](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L64)


- 
```
File: code/system-contracts/contracts/Compressor.sol

236    _operation == 0 || _operation == 3
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L236](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L236)


- 
```
File: code/system-contracts/contracts/Compressor.sol

254    number >>= (256 - (_calldataSlice.length * 8))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L254](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L254)


- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

76    require(
77                _nonceOrdering == AccountNonceOrdering.Arbitrary &&
78                    currentInfo.nonceOrdering == AccountNonceOrdering.Sequential,
79                "It is only possible to change from sequential to arbitrary ordering"
80            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L76-L80](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L76-L80)


- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

241    require(
242                msg.sender == FORCE_DEPLOYER || msg.sender == address(COMPLEX_UPGRADER_CONTRACT),
243                "Can only be called by FORCE_DEPLOYER or COMPLEX_UPGRADER_CONTRACT"
244            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L241-L244](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L241-L244)


- 
```
File: code/system-contracts/contracts/DefaultAccount.sol

143    to == address(DEPLOYER_SYSTEM_CONTRACT) && data.length >= 4
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L143](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L143)


- 
```
File: code/system-contracts/contracts/DefaultAccount.sol

147    isSystemCall =
148                    selector == DEPLOYER_SYSTEM_CONTRACT.create.selector ||
149                    selector == DEPLOYER_SYSTEM_CONTRACT.create2.selector ||
150                    selector == DEPLOYER_SYSTEM_CONTRACT.createAccount.selector ||
151                    selector == DEPLOYER_SYSTEM_CONTRACT.create2Account.selector
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L147-L151](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L147-L151)


- 
```
File: code/system-contracts/contracts/DefaultAccount.sol

175    v := and(mload(add(_signature, 0x41)), 0xff)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L175](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L175)


- 
```
File: code/system-contracts/contracts/DefaultAccount.sol

177    require(v == 27 || v == 28, "v is neither 27 nor 28")
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L177](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L177)


- 
```
File: code/system-contracts/contracts/DefaultAccount.sol

192    return recoveredAddress == address(this) && recoveredAddress != address(0)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L192](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L192)


- 
```
File: code/system-contracts/contracts/KnownCodesStorage.sol

79    require(version == 1 && _bytecodeHash[1] == bytes1(0), "Incorrectly formatted bytecodeHash")
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol#L79](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol#L79)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

61    return KECCAK_ROUND_GAS_COST * (_length / KECCAK_ROUND_NUMBER_OF_BYTES + 1)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L61](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L61)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

72    return SHA256_ROUND_GAS_COST * ((_length + 8) / SHA256_ROUND_NUMBER_OF_BYTES + 1)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L72](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L72)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

95    uint256 gasToPay = keccakGasCost(L2_TO_L1_LOG_SERIALIZE_SIZE) + 3 * keccakGasCost(64)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L95](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L95)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

148    pubdataLen = 4 + _message.length + L2_TO_L1_LOG_SERIALIZE_SIZE
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L148](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L148)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

155    uint256 gasToPay = pubdataLen *
156                gasPerPubdataBytes +
157                keccakGasCost(L2_TO_L1_LOG_SERIALIZE_SIZE) +
158                4 *
159                keccakGasCost(64) +
160                gasSpentOnMessageHashing
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L155-L160](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L155-L160)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

185    uint256 gasToPay = pubdataLen * gasPerPubdataBytes + sha256GasCost(bytecodeLen) + keccakGasCost(64)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L185](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L185)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

232    l2ToL1LogsTreeArray[i] = keccak256(
233                        abi.encode(l2ToL1LogsTreeArray[2 * i], l2ToL1LogsTreeArray[2 * i + 1])
234                    )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L232-L234](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L232-L234)


- 
```
File: code/system-contracts/contracts/L2EthToken.sol

35    require(
36                msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
37                    msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
38                    msg.sender == BOOTLOADER_FORMAL_ADDRESS,
39                "Only system contracts with special access can call this method"
40            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L35-L40](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L35-L40)


- 
```
File: code/system-contracts/contracts/MsgValueSimulator.sol

32    isSystemCall = (mask & MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT) != 0
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L32](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L32)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

90    accountInfo.nonceOrdering == IContractDeployer.AccountNonceOrdering.Sequential && _key != 0
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L90](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L90)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

151    return (_nonce < getMinNonce(_address) || nonceValues[addressAsKey][_nonce] > 0)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L151](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L151)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

117    blockNumber <= _block || blockNumber - _block > 256
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L117](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L117)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

124    _block >= currentVirtualBlockUpgradeInfo.virtualBlockFinishL2Block &&
125                currentVirtualBlockUpgradeInfo.virtualBlockFinishL2Block > 0
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L124-L125](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L124-L125)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

250    currentVirtualL2BlockInfo.number == 0 && virtualBlockInfo.timestamp == 0
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L250](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L250)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

333    currentL2BlockNumber == 0 && currentL2BlockTimestamp == 0
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L333](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L333)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

390    uint256 packedTimestamps = (uint256(currentBatchTimestamp) << 128) | currentL2BlockTimestamp
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L390](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L390)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

472    blockInfo = (uint256(blockNumber) << 128) | uint256(blockTimestamp)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L472](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L472)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

263    uint32 shrinkTo = uint32(msg.data.length - (_data.length + dataOffset))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L263](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L263)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

39    uint256 shiftedVal = _val << (lbs * 8)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L39](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L39)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

71    encoded[0] = bytes1(uint8(_offset + hbs + 56))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L71](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L71)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

74    uint256 shiftedVal = uint256(_len) << (lbs * 8)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L74](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L74)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

239    uint256 shifted = (meta << (256 - size - offset))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L239](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L239)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

241    result = (shifted >> (256 - size))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L241](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L241)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

97    return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L97](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L97)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

192    uint256 listLength = encodedNonce.length +
193                    encodedGasParam.length +
194                    encodedTo.length +
195                    encodedValue.length +
196                    encodedDataLength.length +
197                    _transaction.data.length +
198                    encodedChainId.length
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L192-L198](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L192-L198)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

267    uint256 listLength = encodedFixedLengthParams.length +
268                    encodedDataLength.length +
269                    _transaction.data.length +
270                    encodedAccessListLength.length
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L267-L270](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L267-L270)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

339    uint256 listLength = encodedFixedLengthParams.length +
340                    encodedDataLength.length +
341                    _transaction.data.length +
342                    encodedAccessListLength.length
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L339-L342](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L339-L342)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

413    requiredBalance = _transaction.maxFeePerGas * _transaction.gasLimit + _transaction.value
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L413](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L413)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

48    codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L48](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L48)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

95    hashedBytecode = (hashedBytecode | bytes32(uint256(1 << 248)))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L95](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L95)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

97    hashedBytecode = hashedBytecode | bytes32(bytecodeLenInWords << 224)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L97](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L97)


</details>

# 


## Control structures do not follow the Solidity Style Guide
- Severity: Non-Critical
- Confidence: High

### Description
Control structures should follow the One True Brace [OTB style](https://docs.soliditylang.org/en/latest/style-guide.html#control-structures). This means: 1. Opening braces should be on the same line as the declaration. 
2. Closing braces should be on their own line, at the same indentation level as the beginning of the declaration. 
3. The opening brace should be preceded by a single space.

<details>

<summary>
There are 55 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

85    function initialize(
86            bytes[] calldata _factoryDeps,
87            address _l2TokenBeacon,
88            address _governor,
89            uint256 _deployBridgeImplementationFee,
90            uint256 _deployBridgeProxyFee
91        ) external payable reentrancyGuardInitializer 
```
 These control structures in the function `initialize` should follow the One True Brace (OTB) style:

    - Line: 115:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L85-L133](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L85-L133)


- 
```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

178    function deposit(
179            address _l2Receiver,
180            address _l1Token,
181            uint256 _amount,
182            uint256 _l2TxGasLimit,
183            uint256 _l2TxGasPerPubdataByte,
184            address _refundRecipient
185        ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) 
```
 These control structures in the function `deposit` should follow the One True Brace (OTB) style:

    - Line: 200:         l2TxHash = zkSync.requestL2Transaction{value: msg.value}(

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L178-L214](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L178-L214)


- 
```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

295    function finalizeWithdrawal(
296            uint256 _l2BatchNumber,
297            uint256 _l2MessageIndex,
298            uint16 _l2TxNumberInBatch,
299            bytes calldata _message,
300            bytes32[] calldata _merkleProof
301        ) external nonReentrant senderCanCallFunction(allowList) 
```
 These control structures in the function `finalizeWithdrawal` should follow the One True Brace (OTB) style:

    - Line: 304:         L2Message memory l2ToL1Message = L2Message({
    - Line: 308:         });
    - Line: 312:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L295-L322](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L295-L322)


- 
```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

83    function initialize(
84            bytes[] calldata _factoryDeps,
85            address _l2WethAddress,
86            address _governor,
87            uint256 _deployBridgeImplementationFee,
88            uint256 _deployBridgeProxyFee
89        ) external payable reentrancyGuardInitializer 
```
 These control structures in the function `initialize` should follow the One True Brace (OTB) style:

    - Line: 114:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L83-L136](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L83-L136)


- 
```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

161    function deposit(
162            address _l2Receiver,
163            address _l1Token,
164            uint256 _amount,
165            uint256 _l2TxGasLimit,
166            uint256 _l2TxGasPerPubdataByte,
167            address _refundRecipient
168        ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) 
```
 These control structures in the function `deposit` should follow the One True Brace (OTB) style:

    - Line: 187:         txHash = zkSync.requestL2Transaction{value: _amount + msg.value}(

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L161-L198](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L161-L198)


- 
```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

235    function finalizeWithdrawal(
236            uint256 _l2BatchNumber,
237            uint256 _l2MessageIndex,
238            uint16 _l2TxNumberInBatch,
239            bytes calldata _message,
240            bytes32[] calldata _merkleProof
241        ) external nonReentrant senderCanCallFunction(allowList) 
```
 These control structures in the function `finalizeWithdrawal` should follow the One True Brace (OTB) style:

    - Line: 250:             L2Message memory l2ToL1Message = L2Message({
    - Line: 254:             });
    - Line: 263:         IWETH9(l1WethAddress).deposit{value: amount}();

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L235-L270](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L235-L270)


- 
```
File: code/contracts/ethereum/contracts/common/AllowListed.sol

12    modifier senderCanCallFunction(IAllowList _allowList) 
```
 These control structures in the function `senderCanCallFunction` should follow the One True Brace (OTB) style:

    - Line: 14:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L12-L18](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L12-L18)


- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

226    function _execute(Call[] calldata _calls) internal 
```
 These control structures in the function `_execute` should follow the One True Brace (OTB) style:

    - Line: 228:             (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L226-L236](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L226-L236)


- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

264    receive() external payable 
```
 These control structures in the function `receive` should follow the One True Brace (OTB) style:

    - Line: 264: receive() external payable {}

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L264](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L264)


- 
```
File: code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol

17    function _upgradeL1Contract(bytes calldata _customCallDataForUpgrade) internal virtual 
```
 These control structures in the function `_upgradeL1Contract` should follow the One True Brace (OTB) style:

    - Line: 17: function _upgradeL1Contract(bytes calldata _customCallDataForUpgrade) internal virtual {}

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L17](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L17)


- 
```
File: code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol

23    function _postUpgrade(bytes calldata _customCallDataForUpgrade) internal virtual 
```
 These control structures in the function `_postUpgrade` should follow the One True Brace (OTB) style:

    - Line: 23: function _postUpgrade(bytes calldata _customCallDataForUpgrade) internal virtual {}

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L23](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L23)


- 
```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

52    constructor() reentrancyGuardInitializer 
```
 These control structures in the function `constructor` should follow the One True Brace (OTB) style:

    - Line: 52: constructor() reentrancyGuardInitializer {}

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L52](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L52)


- 
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
 These control structures in the function `_processL2Logs` should follow the One True Brace (OTB) style:

    - Line: 114:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L103-L173](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L103-L173)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

179    function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)
180            external
181            override
182            nonReentrant
183            onlyValidator
184        
```
 These control structures in the function `commitBatches` should follow the One True Brace (OTB) style:

    - Line: 184:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L179-L202](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L179-L202)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

415    function _createBatchCommitment(CommitBatchInfo calldata _newBatchData, bytes32 _stateDiffHash)
416            internal
417            view
418            returns (bytes32)
419        
```
 These control structures in the function `_createBatchCommitment` should follow the One True Brace (OTB) style:

    - Line: 419:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L415-L425](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L415-L425)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

441    function _batchAuxiliaryOutput(CommitBatchInfo calldata _batch, bytes32 _stateDiffHash)
442            internal
443            pure
444            returns (bytes memory)
445        
```
 These control structures in the function `_batchAuxiliaryOutput` should follow the One True Brace (OTB) style:

    - Line: 445:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L441-L457](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L441-L457)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

84    function proveL1ToL2TransactionStatus(
85            bytes32 _l2TxHash,
86            uint256 _l2BatchNumber,
87            uint256 _l2MessageIndex,
88            uint16 _l2TxNumberInBatch,
89            bytes32[] calldata _merkleProof,
90            TxStatus _status
91        ) public view override returns (bool) 
```
 These control structures in the function `proveL1ToL2TransactionStatus` should follow the One True Brace (OTB) style:

    - Line: 102:         L2Log memory l2Log = L2Log({
    - Line: 109:         });

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L84-L111](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L84-L111)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

151    function _L2MessageToLog(L2Message memory _message) internal pure returns (L2Log memory) 
```
 These control structures in the function `_L2MessageToLog` should follow the One True Brace (OTB) style:

    - Line: 153:             L2Log({
    - Line: 160:             });

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L151-L161](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L151-L161)


- 
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
 These control structures in the function `finalizeEthWithdrawal` should follow the One True Brace (OTB) style:

    - Line: 203:         L2Message memory l2ToL1Message = L2Message({
    - Line: 207:         });

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L194-L218](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L194-L218)


- 
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
 These control structures in the function `_requestL2Transaction` should follow the One True Brace (OTB) style:

    - Line: 305:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L285-L329](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L285-L329)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

331    function _serializeL2Transaction(
332            WritePriorityOpParams memory _priorityOpParams,
333            bytes calldata _calldata,
334            bytes[] calldata _factoryDeps
335        ) internal pure returns (L2CanonicalTransaction memory transaction) 
```
 These control structures in the function `_serializeL2Transaction` should follow the One True Brace (OTB) style:

    - Line: 336:         transaction = L2CanonicalTransaction({
    - Line: 354:         });

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L331-L355](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L331-L355)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

358    function _writePriorityOp(
359            WritePriorityOpParams memory _priorityOpParams,
360            bytes calldata _calldata,
361            bytes[] calldata _factoryDeps
362        ) internal returns (bytes32 canonicalTxHash) 
```
 These control structures in the function `_writePriorityOp` should follow the One True Brace (OTB) style:

    - Line: 372:             PriorityOperation({
    - Line: 376:             })

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L358-L387](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L358-L387)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

390    function _hashFactoryDeps(bytes[] calldata _factoryDeps)
391            internal
392            pure
393            returns (uint256[] memory hashedFactoryDeps)
394        
```
 These control structures in the function `_hashFactoryDeps` should follow the One True Brace (OTB) style:

    - Line: 394:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L390-L405](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L390-L405)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

408    function _parseL2WithdrawalMessage(bytes memory _message)
409            internal
410            pure
411            returns (address l1Receiver, uint256 amount)
412        
```
 These control structures in the function `_parseL2WithdrawalMessage` should follow the One True Brace (OTB) style:

    - Line: 412:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L408-L430](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L408-L430)


- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

42    function initialize(
43            address _l1Bridge,
44            bytes32 _l2TokenProxyBytecodeHash,
45            address _governor
46        ) external initializer 
```
 These control structures in the function `initialize` should follow the One True Brace (OTB) style:

    - Line: 54:         address l2StandardToken = address(new L2StandardERC20{salt: bytes32(0)}());
    - Line: 55:         l2TokenBeacon = new UpgradeableBeacon{salt: bytes32(0)}(l2StandardToken);

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L42-L57](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L42-L57)


- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

48    function bridgeInitialize(address _l1Address, bytes memory _data) external initializer 
```
 These control structures in the function `bridgeInitialize` should follow the One True Brace (OTB) style:

    - Line: 75:         } catch {
    - Line: 81:         } catch {
    - Line: 94:         } catch {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L48-L100](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L48-L100)


- 
```
File: code/system-contracts/contracts/bridge/L2Weth.sol

80    function bridgeBurn(address _from, uint256 _amount) external override onlyBridge 
```
 These control structures in the function `bridgeBurn` should follow the One True Brace (OTB) style:

    - Line: 83:         (bool success, ) = msg.sender.call{value: _amount}("");

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L80-L87](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L80-L87)


- 
```
File: code/system-contracts/contracts/bridge/L2Weth.sol

106    function withdrawTo(address _to, uint256 _amount) public override 
```
 These control structures in the function `withdrawTo` should follow the One True Brace (OTB) style:

    - Line: 108:         (bool success, ) = _to.call{value: _amount}("");

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L106-L110](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L106-L110)


- 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

65    function withdraw(
66            address _l1Receiver,
67            address _l2Token,
68            uint256 _amount
69        ) external override 
```
 These control structures in the function `withdraw` should follow the One True Brace (OTB) style:

    - Line: 80:         L2_ETH_ADDRESS.withdrawWithMessage{value: _amount}(l1Bridge, wethMessage);

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L65-L83](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L65-L83)


- 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

90    function finalizeDeposit(
91            address _l1Sender,
92            address _l2Receiver,
93            address _l1Token,
94            uint256 _amount,
95            bytes calldata // _data
96        ) external payable override 
```
 These control structures in the function `finalizeDeposit` should follow the One True Brace (OTB) style:

    - Line: 106:         IL2Weth(l2WethAddress).depositTo{value: msg.value}(_l2Receiver);

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L90-L109](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L90-L109)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

46    function encodeLegacyTransactionHash(Transaction calldata _transaction) internal view returns (bytes32 txHash) 
```
 These control structures in the function `encodeLegacyTransactionHash` should follow the One True Brace (OTB) style:

    - Line: 57:         {
    - Line: 68:         {
    - Line: 82:         {
    - Line: 87:         {
    - Line: 92:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L46-L136](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L46-L136)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

141    function encodeEIP2930TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) 
```
 These control structures in the function `encodeEIP2930TransactionHash` should follow the One True Brace (OTB) style:

    - Line: 144:         {
    - Line: 164:         {
    - Line: 181:         {
    - Line: 186:         {
    - Line: 191:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L141-L226](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L141-L226)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

231    function encodeEIP1559TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) 
```
 These control structures in the function `encodeEIP1559TransactionHash` should follow the One True Brace (OTB) style:

    - Line: 237:         {
    - Line: 259:         {
    - Line: 276:         {
    - Line: 281:         {
    - Line: 286:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L231-L321](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L231-L321)


- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

240    function forceDeployOnAddresses(ForceDeployment[] calldata _deployments) external payable 
```
 These control structures in the function `forceDeployOnAddresses` should follow the One True Brace (OTB) style:

    - Line: 256:             this.forceDeployOnAddress{value: _deployments[i].value}(_deployments[i], msg.sender);

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L240-L258](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L240-L258)


- 
```
File: code/system-contracts/contracts/DefaultAccount.sol

163    function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) 
```
 These control structures in the function `_isValidSignature` should follow the One True Brace (OTB) style:

    - Line: 181: b'        // the valid range for s in (301): 0 < s < secp256k1n \xc3\xb7 2 + 1, and for v in (302): v \xe2\x88\x88 {27, 28}. Most'

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L163-L193](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L163-L193)


- 
```
File: code/system-contracts/contracts/EmptyContract.sol

14    fallback() external payable 
```
 These control structures in the function `fallback` should follow the One True Brace (OTB) style:

    - Line: 14: fallback() external payable {}

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L14](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L14)


- 
```
File: code/system-contracts/contracts/EmptyContract.sol

16    receive() external payable 
```
 These control structures in the function `receive` should follow the One True Brace (OTB) style:

    - Line: 16: receive() external payable {}

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L16](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L16)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

77    function sendL2ToL1Log(
78            bool _isService,
79            bytes32 _key,
80            bytes32 _value
81        ) external onlyCallFromSystemContract returns (uint256 logIdInMerkleTree) 
```
 These control structures in the function `sendL2ToL1Log` should follow the One True Brace (OTB) style:

    - Line: 82:         L2ToL1Log memory l2ToL1Log = L2ToL1Log({
    - Line: 89:         });

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L77-L97](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L77-L97)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

121    function sendToL1(bytes calldata _message) external override returns (bytes32 hash) 
```
 These control structures in the function `sendToL1` should follow the One True Brace (OTB) style:

    - Line: 130:         L2ToL1Log memory l2ToL1Log = L2ToL1Log({
    - Line: 137:         });

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L121-L164](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L121-L164)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

287    function _setNewL2BlockData(uint128 _l2BlockNumber, uint128 _l2BlockTimestamp, bytes32 _prevL2BlockHash) internal 
```
 These control structures in the function `_setNewL2BlockData` should follow the One True Brace (OTB) style:

    - Line: 289:         currentL2BlockInfo = BlockInfo({number: _l2BlockNumber, timestamp: _l2BlockTimestamp});

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L287-L296](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L287-L296)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

418    function setNewBatch(
419            bytes32 _prevBatchHash,
420            uint128 _newTimestamp,
421            uint128 _expectedNewNumber,
422            uint256 _baseFee
423        ) external onlyCallFromBootloader 
```
 These control structures in the function `setNewBatch` should follow the One True Brace (OTB) style:

    - Line: 433:         BlockInfo memory newBlockInfo = BlockInfo({number: previousBatchNumber + 1, timestamp: _newTimestamp});

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L418-L441](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L418-L441)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

445    function unsafeOverrideBatch(
446            uint256 _newTimestamp,
447            uint256 _number,
448            uint256 _baseFee
449        ) external onlyCallFromBootloader 
```
 These control structures in the function `unsafeOverrideBatch` should follow the One True Brace (OTB) style:

    - Line: 450:         BlockInfo memory newBlockInfo = BlockInfo({number: uint128(_number), timestamp: uint128(_newTimestamp)});

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L445-L454](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L445-L454)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

126    function rawCall(
127            uint256 _gas,
128            address _address,
129            uint256 _value,
130            bytes calldata _data,
131            bool _isSystem
132        ) internal returns (bool success) 
```
 These control structures in the function `rawCall` should follow the One True Brace (OTB) style:

    - Line: 139:             }

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L126-L154](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L126-L154)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

217    function _verifyCallResult(bool _success) private pure returns (bytes memory returnData) 
```
 These control structures in the function `_verifyCallResult` should follow the One True Brace (OTB) style:

    - Line: 227:             }

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L217-L231](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L217-L231)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractsCaller.sol

78    function systemCall(uint32 gasLimit, address to, uint256 value, bytes memory data) internal returns (bool success) 
```
 These control structures in the function `systemCall` should follow the One True Brace (OTB) style:

    - Line: 104:             }

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L78-L115](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L78-L115)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

149    function _encodeHashLegacyTransaction(Transaction calldata _transaction) private view returns (bytes32) 
```
 These control structures in the function `_encodeHashLegacyTransaction` should follow the One True Brace (OTB) style:

    - Line: 160:         {
    - Line: 171:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L149-L217](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L149-L217)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

221    function _encodeHashEIP2930Transaction(Transaction calldata _transaction) private view returns (bytes32) 
```
 These control structures in the function `_encodeHashEIP2930Transaction` should follow the One True Brace (OTB) style:

    - Line: 229:         {
    - Line: 249:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L221-L287](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L221-L287)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

291    function _encodeHashEIP1559Transaction(Transaction calldata _transaction) private view returns (bytes32) 
```
 These control structures in the function `_encodeHashEIP1559Transaction` should follow the One True Brace (OTB) style:

    - Line: 299:         {
    - Line: 321:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L291-L359](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L291-L359)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

364    function processPaymasterInput(Transaction calldata _transaction) internal 
```
 These control structures in the function `processPaymasterInput` should follow the One True Brace (OTB) style:

    - Line: 386:             }

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L364-L392](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L364-L392)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

62    function sendValue(address payable recipient, uint256 amount) internal 
```
 These control structures in the function `sendValue` should follow the One True Brace (OTB) style:

    - Line: 68:         (bool success, ) = recipient.call{value: amount}("");

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L62-L73](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L62-L73)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

93    function functionCall(address target, bytes memory data)
94            internal
95            returns (bytes memory)
96        
```
 These control structures in the function `functionCall` should follow the One True Brace (OTB) style:

    - Line: 96:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L93-L104](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L93-L104)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

151    function functionCallWithValue(
152            address target,
153            bytes memory data,
154            uint256 value,
155            string memory errorMessage
156        ) internal returns (bytes memory) 
```
 These control structures in the function `functionCallWithValue` should follow the One True Brace (OTB) style:

    - Line: 161:         (bool success, bytes memory returndata) = target.call{value: value}(

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L151-L171](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L151-L171)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

179    function functionStaticCall(address target, bytes memory data)
180            internal
181            view
182            returns (bytes memory)
183        
```
 These control structures in the function `functionStaticCall` should follow the One True Brace (OTB) style:

    - Line: 183:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L179-L190](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L179-L190)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

219    function functionDelegateCall(address target, bytes memory data)
220            internal
221            returns (bytes memory)
222        
```
 These control structures in the function `functionDelegateCall` should follow the One True Brace (OTB) style:

    - Line: 222:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L219-L229](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L219-L229)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

294    function _revert(bytes memory returndata, string memory errorMessage)
295            private
296            pure
297        
```
 These control structures in the function `_revert` should follow the One True Brace (OTB) style:

    - Line: 297:     {
    - Line: 305:             }

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L294-L309](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L294-L309)


</details>

# 


## Dead-code
- Severity: Non-Critical
- Confidence: Medium

### Description
Functions that are not sued.

<details>

<summary>
There are 26 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol

40    function undoL1ToL2Alias(address l2Address) internal pure returns (address l1Address) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L40-L44](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L40-L44)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

410    function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L410-L412](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L410-L412)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

93    function functionCall(address target, bytes memory data)
94            internal
95            returns (bytes memory)
96        
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L93-L104](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L93-L104)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

131    function functionCallWithValue(
132            address target,
133            bytes memory data,
134            uint256 value
135        ) internal returns (bytes memory) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L131-L143](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L131-L143)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

219    function functionDelegateCall(address target, bytes memory data)
220            internal
221            returns (bytes memory)
222        
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L219-L229](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L219-L229)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

237    function functionDelegateCall(
238            address target,
239            bytes memory data,
240            string memory errorMessage
241        ) internal returns (bytes memory) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L237-L250](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L237-L250)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

179    function functionStaticCall(address target, bytes memory data)
180            internal
181            view
182            returns (bytes memory)
183        
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L179-L190](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L179-L190)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

198    function functionStaticCall(
199            address target,
200            bytes memory data,
201            string memory errorMessage
202        ) internal view returns (bytes memory) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L198-L211](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L198-L211)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

62    function sendValue(address payable recipient, uint256 amount) internal 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L62-L73](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L62-L73)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

282    function verifyCallResult(
283            bool success,
284            bytes memory returndata,
285            string memory errorMessage
286        ) internal pure returns (bytes memory) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L282-L292](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L282-L292)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

60    function call(
61            uint256 _gas,
62            address _address,
63            uint256 _value,
64            bytes calldata _data,
65            bool _isSystem
66        ) internal returns (bytes memory returnData) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L60-L69](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L60-L69)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

90    function delegateCall(
91            uint256 _gas,
92            address _address,
93            bytes calldata _data
94        ) internal returns (bytes memory returnData) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L90-L97](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L90-L97)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

175    function rawDelegateCall(uint256 _gas, address _address, bytes calldata _data) internal returns (bool success) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L175-L182](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L175-L182)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

204    function eventInitialize(uint256 initializer, uint256 value1) internal 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L204-L209](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L204-L209)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

214    function eventWrite(uint256 value1, uint256 value2) internal 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L214-L219](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L214-L219)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

267    function getAuxHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 auxHeapSize) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L267-L269](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L267-L269)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

325    function getCalldataPtr() internal view returns (uint256 ptr) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L325-L330](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L325-L330)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

284    function getCallerShardIdFromMeta(uint256 meta) internal pure returns (uint8 callerShardId) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L284-L286](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L284-L286)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

293    function getCodeShardIdFromMeta(uint256 meta) internal pure returns (uint8 codeShardId) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L293-L295](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L293-L295)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

258    function getHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 heapSize) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L258-L260](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L258-L260)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

275    function getShardIdFromMeta(uint256 meta) internal pure returns (uint8 shardId) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L275-L277](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L275-L277)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

299    function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L299-L305](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L299-L305)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

153    function packPrecompileParams(
154            uint32 _inputMemoryOffset,
155            uint32 _inputMemoryLength,
156            uint32 _outputMemoryOffset,
157            uint32 _outputMemoryLength,
158            uint64 _perPrecompileInterpreted
159        ) internal pure returns (uint256 rawParams) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L153-L165](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L153-L165)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

96    function isEthToken(uint256 _addr) internal pure returns (bool) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L96-L98](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L96-L98)


- 
```
File: code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol

28    function readUint32(bytes calldata _bytes, uint256 _start) internal pure returns (uint32 result) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L28-L33](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L28-L33)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

34    function safeCastToU24(uint256 _x) internal pure returns (uint24) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L34-L38](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L34-L38)


</details>

# 


## Avoid double casting
- Severity: Non-Critical
- Confidence: High

### Description
This detector identifies instances where values are double casted in Solidity. Double casting can introduce unexpected truncations and/or rounding issues. Additionally, it reduces the readability of the code and can make it harder to maintain. It's recommended to avoid double casting to ensure clearer, safer, and more maintainable smart contracts.

<details>

<summary>
There are 72 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

357    bytes32 salt = bytes32(uint256(uint160(_l1Token)))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L357](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L357)


- 
```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

118    _newVerifier == IVerifier(address(0))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L118](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L118)


- 
```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

237    _newAllowList == IAllowList(address(0))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L237](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L237)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

102    L2Log memory l2Log = L2Log({
103                l2ShardId: 0,
104                isService: true,
105                txNumberInBatch: _l2TxNumberInBatch,
106                sender: L2_BOOTLOADER_ADDRESS,
107                key: _l2TxHash,
108                value: bytes32(uint256(_status))
109            })
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L102-L109](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L102-L109)


- 
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


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

336    transaction = L2CanonicalTransaction({
337                txType: PRIORITY_OPERATION_L2_TX_TYPE,
338                from: uint256(uint160(_priorityOpParams.sender)),
339                to: uint256(uint160(_priorityOpParams.contractAddressL2)),
340                gasLimit: _priorityOpParams.l2GasLimit,
341                gasPerPubdataByteLimit: _priorityOpParams.l2GasPricePerPubdata,
342                maxFeePerGas: uint256(_priorityOpParams.l2GasPrice),
343                maxPriorityFeePerGas: uint256(0),
344                paymaster: uint256(0),
345                // Note, that the priority operation id is used as "nonce" for L1->L2 transactions
346                nonce: uint256(_priorityOpParams.txId),
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


- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

143    salt = bytes32(uint256(uint160(_l1Token)))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L143](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L143)


- 
```
File: code/system-contracts/contracts/AccountCodeStorage.sol

71    uint256 addressAsKey = uint256(uint160(_address))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L71](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L71)


- 
```
File: code/system-contracts/contracts/AccountCodeStorage.sol

81    uint256 addressAsKey = uint256(uint160(_address))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L81](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L81)


- 
```
File: code/system-contracts/contracts/AccountCodeStorage.sol

94    address account = address(uint160(_input))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L94](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L94)


- 
```
File: code/system-contracts/contracts/AccountCodeStorage.sol

122    address account = address(uint160(_input))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L122](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L122)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

63    bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L63](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L63)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

83    uint256 rInt = uint256(bytes32(_transaction.signature[0:32]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L83](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L83)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

88    uint256 sInt = uint256(bytes32(_transaction.signature[32:64]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L88](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L88)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

93    uint256 vInt = uint256(uint8(_transaction.signature[64]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L93](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L93)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

149    bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L149](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L149)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

182    uint256 rInt = uint256(bytes32(_transaction.signature[0:32]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L182](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L182)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

187    uint256 sInt = uint256(bytes32(_transaction.signature[32:64]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L187](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L187)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

192    uint256 vInt = uint256(uint8(_transaction.signature[64]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L192](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L192)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

243    bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L243](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L243)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

277    uint256 rInt = uint256(bytes32(_transaction.signature[0:32]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L277](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L277)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

282    uint256 sInt = uint256(bytes32(_transaction.signature[32:64]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L282](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L282)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

287    uint256 vInt = uint256(uint8(_transaction.signature[64]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L287](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L287)


- 
```
File: code/system-contracts/contracts/Compressor.sol

152    uint8 metadata = uint8(bytes1(_compressedStateDiffs[stateDiffPtr]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L152](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L152)


- 
```
File: code/system-contracts/contracts/Compressor.sol

181    uint8 metadata = uint8(bytes1(_compressedStateDiffs[stateDiffPtr]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L181](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L181)


- 
```
File: code/system-contracts/contracts/Compressor.sol

253    number = uint256(bytes32(_calldataSlice))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L253](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L253)


- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

104    bytes32 hash = keccak256(
105                bytes.concat(CREATE2_PREFIX, bytes32(uint256(uint160(_sender))), _salt, _bytecodeHash, constructorInputHash)
106            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L104-L106](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L104-L106)


- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

108    newAddress = address(uint160(uint256(hash)))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L108](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L108)


- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

120    bytes32 hash = keccak256(
121                bytes.concat(CREATE_PREFIX, bytes32(uint256(uint160(_sender))), bytes32(_senderNonce))
122            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L120-L122](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L120-L122)


- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

124    newAddress = address(uint160(uint256(hash)))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L124](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L124)


- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

270    require(
271                ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getCodeHash(uint256(uint160(_newAddress))) == 0x0,
272                "Code hash is non-zero"
273            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L270-L273](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L270-L273)


- 
```
File: code/system-contracts/contracts/DefaultAccount.sol

136    address to = address(uint160(_transaction.to))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L136](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L136)


- 
```
File: code/system-contracts/contracts/ImmutableSimulator.sol

30    return immutableDataStorage[uint256(uint160(_dest))][_index]
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L30](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L30)


- 
```
File: code/system-contracts/contracts/ImmutableSimulator.sol

43    immutableDataStorage[uint256(uint160(_dest))][index] = value
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L43](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L43)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

130    L2ToL1Log memory l2ToL1Log = L2ToL1Log({
131                l2ShardId: 0,
132                isService: true,
133                txNumberInBlock: SYSTEM_CONTEXT_CONTRACT.txNumberInBlock(),
134                sender: address(this),
135                key: bytes32(uint256(uint160(msg.sender))),
136                value: hash
137            })
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L130-L137](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L130-L137)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

207    uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L207](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L207)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

240    uint32 numberOfMessages = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L240](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L240)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

244    uint32 currentMessageLength = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L244](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L244)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

258    uint32 numberOfBytecodes = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L258](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L258)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

262    uint32 currentBytecodeLength = uint32(
263                    bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4])
264                )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L262-L264](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L262-L264)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

286    require(
287                uint256(uint8(bytes1(_totalL2ToL1PubdataAndStateDiffs[calldataPtr]))) ==
288                    STATE_DIFF_COMPRESSION_VERSION_NUMBER,
289                "state diff compression version mismatch"
290            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L286-L290](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L286-L290)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

293    uint24 compressedStateDiffSize = uint24(bytes3(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 3]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L293](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L293)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

296    uint8 enumerationIndexSize = uint8(bytes1(_totalL2ToL1PubdataAndStateDiffs[calldataPtr]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L296](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L296)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

307    uint32 numberOfStateDiffs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L307](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L307)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

325    SystemContractHelper.toL1(true, bytes32(uint256(SystemLogKey.L2_TO_L1_LOGS_TREE_ROOT_KEY)), l2ToL1LogsTreeRoot)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L325](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L325)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

326    SystemContractHelper.toL1(
327                true,
328                bytes32(uint256(SystemLogKey.TOTAL_L2_TO_L1_PUBDATA_KEY)),
329                EfficientCall.keccak(totalL2ToL1Pubdata)
330            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L326-L330](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L326-L330)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

331    SystemContractHelper.toL1(true, bytes32(uint256(SystemLogKey.STATE_DIFF_HASH_KEY)), stateDiffHash)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L331](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L331)


- 
```
File: code/system-contracts/contracts/L2EthToken.sol

59    return balance[address(uint160(_account))]
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L59](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L59)


- 
```
File: code/system-contracts/contracts/MsgValueSimulator.sol

34    to = address(uint160(addressAsUint))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L34](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L34)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

49    uint256 addressAsKey = uint256(uint160(_address))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L49](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L49)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

60    uint256 addressAsKey = uint256(uint160(_address))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L60](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L60)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

70    uint256 addressAsKey = uint256(uint160(msg.sender))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L70](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L70)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

94    uint256 addressAsKey = uint256(uint160(msg.sender))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L94](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L94)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

105    uint256 addressAsKey = uint256(uint160(msg.sender))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L105](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L105)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

113    uint256 addressAsKey = uint256(uint160(msg.sender))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L113](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L113)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

128    uint256 addressAsKey = uint256(uint160(_address))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L128](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L128)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

139    uint256 addressAsKey = uint256(uint160(_address))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L139](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L139)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

150    uint256 addressAsKey = uint256(uint160(_address))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L150](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L150)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

392    SystemContractHelper.toL1(
393                false,
394                bytes32(uint256(SystemLogKey.PACKED_BATCH_AND_L2_BLOCK_TIMESTAMP_KEY)),
395                bytes32(packedTimestamps)
396            )
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L392-L396](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L392-L396)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

440    SystemContractHelper.toL1(false, bytes32(uint256(SystemLogKey.PREV_BATCH_HASH_KEY)), _prevBatchHash)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L440](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L440)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

36    encoded[0] = bytes1(uint8(hbs + 0x81))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L36](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L36)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

31    encoded[0] = (_val == 0) ? bytes1(uint8(128)) : bytes1(uint8(_val))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L31](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L31)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

66    encoded[0] = bytes1(uint8(_len + _offset))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L66](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L66)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

71    encoded[0] = bytes1(uint8(_offset + hbs + 56))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L71](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L71)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

97    return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L97](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L97)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

166    bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L166](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L166)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

234    bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L234](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L234)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

305    bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L305](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L305)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

377    address paymaster = address(uint160(_transaction.paymaster))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L377](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L377)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

408    address(uint160(_transaction.paymaster)) != address(0)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L408](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L408)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

48    codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L48](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L48)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

95    hashedBytecode = (hashedBytecode | bytes32(uint256(1 << 248)))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L95](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L95)


</details>

# 


## Function names should differ
- Severity: Non-Critical
- Confidence: High

### Description
In Solidity, while function overriding allows for functions with the same name to coexist, it is advisable to avoid this practice to enhance code readability and maintainability. Having multiple functions with the same name, even with different parameters or in inherited contracts, can cause confusion and increase the likelihood of errors during development, testing, and debugging.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

178    function deposit(
179            address _l2Receiver,
180            address _l1Token,
181            uint256 _amount,
182            uint256 _l2TxGasLimit,
183            uint256 _l2TxGasPerPubdataByte,
184            address _refundRecipient
185        ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) 
```
```
/// @audit: function used on lines  146
```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L178-L214](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L178-L214)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

112    function functionCall(
113            address target,
114            bytes memory data,
115            string memory errorMessage
116        ) internal returns (bytes memory) 
```
```
/// @audit: function used on lines  93
```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L112-L118](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L112-L118)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

151    function functionCallWithValue(
152            address target,
153            bytes memory data,
154            uint256 value,
155            string memory errorMessage
156        ) internal returns (bytes memory) 
```
```
/// @audit: function used on lines  131
```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L151-L171](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L151-L171)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

198    function functionStaticCall(
199            address target,
200            bytes memory data,
201            string memory errorMessage
202        ) internal view returns (bytes memory) 
```
```
/// @audit: function used on lines  179
```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L198-L211](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L198-L211)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

237    function functionDelegateCall(
238            address target,
239            bytes memory data,
240            string memory errorMessage
241        ) internal returns (bytes memory) 
```
```
/// @audit: function used on lines  219
```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L237-L250](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L237-L250)


</details>

# 


## Inconsistency Spacing in Comments
- Severity: Non-Critical
- Confidence: High

### Description
Consistent and well-formatted comments contribute to code readability and maintainability. The 'InconsistencyCommentSpacing' detector helps enforce a uniform spacing convention within comments, ensuring a standardized and easily understandable commenting style. According to the style guide, it is recommended to add a space after the double forward slash (//) when commenting.

<details>

<summary>
There are 4 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

39    contract MailboxFacet is Base, IMailbox 
```

-    `//`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L39-L431](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L39-L431)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

18    contract BootloaderUtilities is IBootloaderUtilities 
```

-    `//`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L18-L322](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L18-L322)


- 
```
File: code/system-contracts/contracts/DefaultAccount.sol

21    contract DefaultAccount is IAccount 
```

-    `//`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L21-L234](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L21-L234)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

80    library TransactionHelper 
```

-    `//`
-    `//`
-    `//`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L80-L416](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L80-L416)


</details>

# 


## Inconsistent usage of require/error
- Severity: Non-Critical
- Confidence: High

### Description
Some parts of the codebase use require statements, while others use custom errors. Consider refactoring the code to use the same approach: the following findings represent the minority of require vs error, and they show one occurrence in each contract, for brevity.

<details>

<summary>
There are 10 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

39    contract L1WethBridge is IL1Bridge, AllowListed, ReentrancyGuard 
```

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

225    revert("Method not supported. Failed deposit funds are sent to the L2 refund recipient address.")
```
The contract `L1WethBridge` uses both `require` and custom `revert` for error handling.

There are 13 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L39-L316](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L39-L316)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

20    contract ExecutorFacet is Base, IExecutor 
```

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

161    revert("ul")
```
The contract `ExecutorFacet` uses both `require` and custom `revert` for error handling.

There are 36 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L20-L473](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L20-L473)


- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

13    contract L2StandardERC20 is ERC20PermitUpgradeable, IL2StandardToken 
```

```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

49    require(_l1Address != address(0), "in6")
```
The contract `L2StandardERC20` uses both `require` and custom `revert` for error handling.

There are 2 `require` statement(s) and 3 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L13-L152](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L13-L152)


- 
```
File: code/system-contracts/contracts/bridge/L2Weth.sol

23    contract L2Weth is ERC20PermitUpgradeable, IL2Weth, IL2StandardToken 
```

```
File: code/system-contracts/contracts/bridge/L2Weth.sol

73    revert("bridgeMint is not implemented! Use deposit/depositTo methods instead.")
```
The contract `L2Weth` uses both `require` and custom `revert` for error handling.

There are 5 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L23-L116](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L23-L116)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

18    contract BootloaderUtilities is IBootloaderUtilities 
```

```
File: code/system-contracts/contracts/BootloaderUtilities.sol

39    revert("Unsupported tx type")
```
The contract `BootloaderUtilities` uses both `require` and custom `revert` for error handling.

There are 3 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L18-L322](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L18-L322)


- 
```
File: code/system-contracts/contracts/Compressor.sol

36    contract Compressor is ICompressor, ISystemContract 
```

```
File: code/system-contracts/contracts/Compressor.sol

243    revert("unsupported operation")
```
The contract `Compressor` uses both `require` and custom `revert` for error handling.

There are 13 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L36-L256](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L36-L256)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

29    contract NonceHolder is INonceHolder, ISystemContract 
```

```
File: code/system-contracts/contracts/NonceHolder.sol

165    revert("Reusing the same nonce twice")
```
The contract `NonceHolder` uses both `require` and custom `revert` for error handling.

There are 5 `require` statement(s) and 2 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L29-L178](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L29-L178)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

19    contract SystemContext is ISystemContext, ISystemContextDeprecated, ISystemContract 
```

```
File: code/system-contracts/contracts/SystemContext.sol

367    revert("Invalid new L2 block number")
```
The contract `SystemContext` uses both `require` and custom `revert` for error handling.

There are 16 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L19-L486](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L19-L486)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

80    library TransactionHelper 
```

```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

114    revert("Encoding unsupported tx")
```
The contract `TransactionHelper` uses both `require` and custom `revert` for error handling.

There are 2 `require` statement(s) and 2 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L80-L416](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L80-L416)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

11    library Address 
```

```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

307    revert(errorMessage)
```
The contract `Address` uses both `require` and custom `revert` for error handling.

There are 4 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L11-L310](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L11-L310)


</details>

# 


## Functions Not Implementing an Interface
- Severity: Non-Critical
- Confidence: High

### Description
Contracts with public or external functions should typically implement an interface for clarity and modularity. If they do not, it could lead to issues with understanding the contract's intended API and maintainability concerns.

<details>

<summary>
There are 34 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

85    function initialize(
86            bytes[] calldata _factoryDeps,
87            address _l2TokenBeacon,
88            address _governor,
89            uint256 _deployBridgeImplementationFee,
90            uint256 _deployBridgeProxyFee
91        ) external payable reentrancyGuardInitializer 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L85-L133](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L85-L133)


- 
```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

83    function initialize(
84            bytes[] calldata _factoryDeps,
85            address _l2WethAddress,
86            address _governor,
87            uint256 _deployBridgeImplementationFee,
88            uint256 _deployBridgeProxyFee
89        ) external payable reentrancyGuardInitializer 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L83-L136](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L83-L136)


- 
```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

309    receive() external payable 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L309-L315](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L309-L315)


- 
```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

69    function upgrade(ProposedUpgrade calldata _proposedUpgrade) public virtual returns (bytes32) 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L69-L75](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L69-L75)


- 
```
File: code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol

27    function upgrade(ProposedUpgrade calldata _proposedUpgrade) public override returns (bytes32) 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L27-L48](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L27-L48)


- 
```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

57    function initialize(InitializeData calldata _initalizeData) external reentrancyGuardInitializer returns (bytes32) 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L57-L92](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L57-L92)


- 
```
File: code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

22    fallback() external payable 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L22-L57](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L22-L57)


- 
```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

54    function setValidator(address _newValidator) external onlyOwner 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L54-L58](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L54-L58)


- 
```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

61    function setExecutionDelay(uint32 _executionDelay) external onlyOwner 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L61-L64](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L61-L64)


- 
```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

73    function getCommittedBatchTimestamp(uint256 _l2BatchNumber) external view returns (uint256) 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L73-L75](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L73-L75)


- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

42    function initialize(
43            address _l1Bridge,
44            bytes32 _l2TokenProxyBytecodeHash,
45            address _governor
46        ) external initializer 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L42-L57](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L42-L57)


- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

48    function bridgeInitialize(address _l1Address, bytes memory _data) external initializer 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L48-L100](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L48-L100)


- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

144    function decodeString(bytes memory _input) external pure returns (string memory result) 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L144-L146](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L144-L146)


- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

149    function decodeUint8(bytes memory _input) external pure returns (uint8 result) 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L149-L151](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L149-L151)


- 
```
File: code/system-contracts/contracts/bridge/L2Weth.sol

41    function initialize(string memory name_, string memory symbol_) external initializer 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L41-L49](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L41-L49)


- 
```
File: code/system-contracts/contracts/bridge/L2Weth.sol

54    function initializeV2(address _l2Bridge, address _l1Address) external reinitializer(2) 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L54-L59](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L54-L59)


- 
```
File: code/system-contracts/contracts/bridge/L2Weth.sol

113    receive() external payable 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L113-L115](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L113-L115)


- 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

47    function initialize(
48            address _l1Bridge,
49            address _l1WethAddress,
50            address _l2WethAddress
51        ) external initializer 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L47-L59](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L47-L59)


- 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

121    receive() external payable 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L121-L124](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L121-L124)


- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

42    function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion) 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L42-L54](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L42-L54)


- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

216    function forceDeployOnAddress(ForceDeployment calldata _deployment, address _sender) external payable onlySelf 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L216-L235](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L216-L235)


- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

240    function forceDeployOnAddresses(ForceDeployment[] calldata _deployments) external payable 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L240-L258](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L240-L258)


- 
```
File: code/system-contracts/contracts/DefaultAccount.sol

224    fallback() external payable 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L224-L229](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L224-L229)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

201    function publishPubdataAndClearState(
202            bytes calldata _totalL2ToL1PubdataAndStateDiffs
203        ) external onlyCallFromBootloader 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L201-L338](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L201-L338)


- 
```
File: code/system-contracts/contracts/MsgValueSimulator.sol

37    fallback(bytes calldata _data) external onlySystemCall returns (bytes memory) 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L37-L60](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L37-L60)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

89    function setTxOrigin(address _newOrigin) external onlyCallFromBootloader 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L89-L91](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L89-L91)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

95    function setGasPrice(uint256 _gasPrice) external onlyCallFromBootloader 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L95-L97](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L95-L97)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

314    function setL2Block(
315            uint128 _l2BlockNumber,
316            uint128 _l2BlockTimestamp,
317            bytes32 _expectedPrevL2BlockHash,
318            bool _isFirstInBatch,
319            uint128 _maxVirtualBlocksToCreate
320        ) external onlyCallFromBootloader 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L314-L371](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L314-L371)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

375    function appendTransactionToCurrentL2Block(bytes32 _txHash) external onlyCallFromBootloader 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L375-L377](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L375-L377)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

381    function publishTimestampDataToL1() external onlyCallFromBootloader 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L381-L397](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L381-L397)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

418    function setNewBatch(
419            bytes32 _prevBatchHash,
420            uint128 _newTimestamp,
421            uint128 _expectedNewNumber,
422            uint256 _baseFee
423        ) external onlyCallFromBootloader 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L418-L441](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L418-L441)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

445    function unsafeOverrideBatch(
446            uint256 _newTimestamp,
447            uint256 _number,
448            uint256 _baseFee
449        ) external onlyCallFromBootloader 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L445-L454](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L445-L454)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

456    function incrementTxNumberInBatch() external onlyCallFromBootloader 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L456-L458](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L456-L458)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

460    function resetTxNumberInBatch() external onlyCallFromBootloader 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L460-L462](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L460-L462)


</details>

# 


## Use of old Solidity version
- Severity: Non-Critical
- Confidence: High

### Description
Using an old version of Solidity may result in missing bug fixes and features. Consider upgrading to a more recent version of Solidity. As of writing, the latest version is 0.8.20.

<details>

<summary>
There are 84 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL1BridgeLegacy.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1BridgeLegacy.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1BridgeLegacy.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL2ERC20Bridge.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2ERC20Bridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2ERC20Bridge.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/common/AllowList.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/common/AllowListed.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/common/L2ContractAddresses.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/L2ContractAddresses.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/L2ContractAddresses.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/common/ReentrancyGuard.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/governance/IGovernance.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/Config.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/Storage.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Getters.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/interfaces/IBase.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IBase.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IBase.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/interfaces/ILegacyGetters.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/ILegacyGetters.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/ILegacyGetters.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L1)


- 
```
File: code/contracts/ethereum/contracts/zksync/interfaces/IZkSync.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IZkSync.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IZkSync.sol#L1)


- solc-0.8.17 is not recommended for deployment

- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L1)


- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L1)


- 
```
File: code/system-contracts/contracts/bridge/L2Weth.sol

1    pragma solidity ^0.8.13;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L1)


- 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L1)


- solc-0.8.19 is not recommended for deployment

- 
```
File: code/system-contracts/contracts/AccountCodeStorage.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L1)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L1)


- 
```
File: code/system-contracts/contracts/ComplexUpgrader.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol#L1)


- 
```
File: code/system-contracts/contracts/Compressor.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L1)


- 
```
File: code/system-contracts/contracts/Constants.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Constants.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Constants.sol#L1)


- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L1)


- 
```
File: code/system-contracts/contracts/DefaultAccount.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L1)


- 
```
File: code/system-contracts/contracts/EmptyContract.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L1)


- 
```
File: code/system-contracts/contracts/ImmutableSimulator.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L1)


- 
```
File: code/system-contracts/contracts/KnownCodesStorage.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol#L1)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L1)


- 
```
File: code/system-contracts/contracts/L2EthToken.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L1)


- 
```
File: code/system-contracts/contracts/MsgValueSimulator.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L1)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L1)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/IAccount.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IAccount.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IAccount.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/IAccountCodeStorage.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IAccountCodeStorage.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IAccountCodeStorage.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/IBootloaderUtilities.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IBootloaderUtilities.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IBootloaderUtilities.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/IComplexUpgrader.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IComplexUpgrader.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IComplexUpgrader.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/ICompressor.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/ICompressor.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/ICompressor.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/IContractDeployer.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IContractDeployer.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IContractDeployer.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/IEthToken.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IEthToken.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IEthToken.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/IImmutableSimulator.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IImmutableSimulator.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IImmutableSimulator.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/IKnownCodesStorage.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IKnownCodesStorage.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IKnownCodesStorage.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/IL1Messenger.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IL1Messenger.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IL1Messenger.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/IL2StandardToken.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IL2StandardToken.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IL2StandardToken.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/IMailbox.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IMailbox.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IMailbox.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/INonceHolder.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/INonceHolder.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/INonceHolder.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/IPaymaster.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IPaymaster.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IPaymaster.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/IPaymasterFlow.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IPaymasterFlow.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IPaymasterFlow.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/ISystemContext.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/ISystemContext.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/ISystemContext.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/ISystemContextDeprecated.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/ISystemContextDeprecated.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/ISystemContextDeprecated.sol#L1)


- 
```
File: code/system-contracts/contracts/interfaces/ISystemContract.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/ISystemContract.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/ISystemContract.sol#L1)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L1)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L1)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L1)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractsCaller.sol

1    pragma solidity ^0.8;
```
 is too complex

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L1)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L1)


- 
```
File: code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol

1    pragma solidity ^0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L1)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

1    pragma solidity >=0.8.0;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L1)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

1    pragma solidity ^0.8.1;
```
 instead use `0.8.20`

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L1)


- solc-0.8.17 is not recommended for deployment

</details>

# 


## Consider Disable Ownership Renouncement in Ownable Contracts
- Severity: Non-Critical
- Confidence: High

### Description
Ownership renouncement is a feature provided by the Ownable contract in various smart contract frameworks, such as OpenZeppelin. It allows the contract owner to transfer ownership to another address, relinquishing their control over the contract. However, in certain cases, it may be undesirable or unnecessary to allow ownership renouncement. 

It is important to carefully consider the implications of allowing ownership renouncement. Disabling renouncement can provide additional security and prevent potential risks associated with unintended ownership transfers.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/common/AllowList.sol

20    contract AllowList is IAllowList, Ownable2Step 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L20-L141](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L20-L141)


- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

22    contract Governance is IGovernance, Ownable2Step 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L22-L265](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L22-L265)


- 
```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

22    contract ValidatorTimelock is IExecutor, Ownable2Step 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L22-L157](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L22-L157)


</details>

# 


## Event Emission Preceding External Calls: A Best Practice
- Severity: Non-Critical
- Confidence: Medium

### Description

Ensure that events follow the best practice of check-effects-interaction, and are emitted before external calls


<details>

<summary>
There are 15 instances of this issue:

</summary>

###
- Reentrancy in 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

169    function execute(Operation calldata _operation) external onlyOwnerOrSecurityCouncil 
```
:
	External calls:
	- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

176    _execute(_operation.calls)
```

		- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

228    (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data)
```

	Event emitted after the call(s):
	- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

182    emit OperationExecuted(id)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L169-L183](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L169-L183)


- Reentrancy in 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

188    function executeInstant(Operation calldata _operation) external onlySecurityCouncil 
```
:
	External calls:
	- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

195    _execute(_operation.calls)
```

		- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

228    (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data)
```

	Event emitted after the call(s):
	- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

201    emit OperationExecuted(id)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L188-L202](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L188-L202)


- Reentrancy in 
```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

100    function executeUpgrade(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor 
```
:
	External calls:
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

101    Diamond.diamondCut(_diamondCut)
```

	Event emitted after the call(s):
	- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

102    emit ExecuteUpgrade(_diamondCut)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L100-L103](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L100-L103)


- Reentrancy in 
```
File: code/system-contracts/contracts/bridge/L2Weth.sol

80    function bridgeBurn(address _from, uint256 _amount) external override onlyBridge 
```
:
	External calls:
	- 
```
File: code/system-contracts/contracts/bridge/L2Weth.sol

83    (bool success, ) = msg.sender.call{value: _amount}("")
```

	Event emitted after the call(s):
	- 
```
File: code/system-contracts/contracts/bridge/L2Weth.sol

86    emit BridgeBurn(_from, _amount)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L80-L87](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L80-L87)


- Reentrancy in 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

65    function finalizeDeposit(
66            address _l1Sender,
67            address _l2Receiver,
68            address _l1Token,
69            uint256 _amount,
70            bytes calldata _data
71        ) external payable override 
```
:
	External calls:
	- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

80    address deployedToken = _deployL2Token(_l1Token, _data)
```

		- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

97    L2StandardERC20(address(l2Token)).bridgeInitialize(_l1Token, _data)
```

	- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

87    IL2StandardToken(expectedL2Token).bridgeMint(_l2Receiver, _amount)
```

	Event emitted after the call(s):
	- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

89    emit FinalizeDeposit(_l1Sender, _l2Receiver, expectedL2Token, _amount)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L65-L90](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L65-L90)


- Reentrancy in 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

90    function finalizeDeposit(
91            address _l1Sender,
92            address _l2Receiver,
93            address _l1Token,
94            uint256 _amount,
95            bytes calldata // _data
96        ) external payable override 
```
:
	External calls:
	- 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

106    IL2Weth(l2WethAddress).depositTo{value: msg.value}(_l2Receiver)
```

	Event emitted after the call(s):
	- 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

108    emit FinalizeDeposit(_l1Sender, _l2Receiver, l2WethAddress, _amount)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L90-L109](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L90-L109)


- Reentrancy in 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

107    function withdraw(
108            address _l1Receiver,
109            address _l2Token,
110            uint256 _amount
111        ) external override 
```
:
	External calls:
	- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

112    IL2StandardToken(_l2Token).bridgeBurn(msg.sender, _amount)
```

	- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

118    L2ContractHelper.sendMessageToL1(message)
```

	Event emitted after the call(s):
	- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

120    emit WithdrawalInitiated(msg.sender, _l1Receiver, _l2Token, _amount)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L107-L121](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L107-L121)


- Reentrancy in 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

65    function withdraw(
66            address _l1Receiver,
67            address _l2Token,
68            uint256 _amount
69        ) external override 
```
:
	External calls:
	- 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

74    IL2StandardToken(l2WethAddress).bridgeBurn(msg.sender, _amount)
```

	- 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

80    L2_ETH_ADDRESS.withdrawWithMessage{value: _amount}(l1Bridge, wethMessage)
```

	External calls sending eth:
	- 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

80    L2_ETH_ADDRESS.withdrawWithMessage{value: _amount}(l1Bridge, wethMessage)
```

	Event emitted after the call(s):
	- 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

82    emit WithdrawalInitiated(msg.sender, _l1Receiver, l2WethAddress, _amount)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L65-L83](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L65-L83)


- Reentrancy in 
```
File: code/system-contracts/contracts/KnownCodesStorage.sol

49    function _markBytecodeAsPublished(bytes32 _bytecodeHash, bool _shouldSendToL1) internal 
```
:
	External calls:
	- 
```
File: code/system-contracts/contracts/KnownCodesStorage.sol

54    L1_MESSENGER_CONTRACT.requestBytecodeL1Publication(_bytecodeHash)
```

	Event emitted after the call(s):
	- 
```
File: code/system-contracts/contracts/KnownCodesStorage.sol

62    emit MarkedAsKnown(_bytecodeHash, _shouldSendToL1)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol#L49-L64](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol#L49-L64)


- Reentrancy in 
```
File: code/system-contracts/contracts/ContractDeployer.sol

285    function _performDeployOnAddress(
286            bytes32 _bytecodeHash,
287            address _newAddress,
288            AccountAbstractionVersion _aaVersion,
289            bytes calldata _input
290        ) internal 
```
:
	External calls:
	- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

299    _constructContract(msg.sender, _newAddress, _bytecodeHash, _input, false, true)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

315    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructingCodeHash(_newAddress, constructingBytecodeHash)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

336    ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo(address(this), _newAddress, value)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

348    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.markAccountCodeHashAsConstructed(_newAddress)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

351    IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT.setImmutables(_newAddress, immutables)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

355    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructedCodeHash(_newAddress, _bytecodeHash)
```

	Event emitted after the call(s):
	- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

300    emit ContractDeployed(msg.sender, _bytecodeHash, _newAddress)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L285-L301](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L285-L301)


- Reentrancy in 
```
File: code/system-contracts/contracts/ContractDeployer.sol

164    function create2Account(
165            bytes32 _salt,
166            bytes32 _bytecodeHash,
167            bytes calldata _input,
168            AccountAbstractionVersion _aaVersion
169        ) public payable override onlySystemCall returns (address) 
```
:
	External calls:
	- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

170    NONCE_HOLDER_SYSTEM_CONTRACT.incrementDeploymentNonce(msg.sender)
```

	- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

173    _nonSystemDeployOnAddress(_bytecodeHash, newAddress, _aaVersion, _input)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

315    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructingCodeHash(_newAddress, constructingBytecodeHash)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

336    ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo(address(this), _newAddress, value)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

348    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.markAccountCodeHashAsConstructed(_newAddress)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

351    IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT.setImmutables(_newAddress, immutables)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

355    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructedCodeHash(_newAddress, _bytecodeHash)
```

	Event emitted after the call(s):
	- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

300    emit ContractDeployed(msg.sender, _bytecodeHash, _newAddress)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

173    _nonSystemDeployOnAddress(_bytecodeHash, newAddress, _aaVersion, _input)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L164-L176](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L164-L176)


- Reentrancy in 
```
File: code/system-contracts/contracts/ContractDeployer.sol

185    function createAccount(
186            bytes32, // salt
187            bytes32 _bytecodeHash,
188            bytes calldata _input,
189            AccountAbstractionVersion _aaVersion
190        ) public payable override onlySystemCall returns (address) 
```
:
	External calls:
	- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

191    uint256 senderNonce = NONCE_HOLDER_SYSTEM_CONTRACT.incrementDeploymentNonce(msg.sender)
```

	- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

194    _nonSystemDeployOnAddress(_bytecodeHash, newAddress, _aaVersion, _input)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

315    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructingCodeHash(_newAddress, constructingBytecodeHash)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

336    ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo(address(this), _newAddress, value)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

348    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.markAccountCodeHashAsConstructed(_newAddress)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

351    IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT.setImmutables(_newAddress, immutables)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

355    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructedCodeHash(_newAddress, _bytecodeHash)
```

	Event emitted after the call(s):
	- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

300    emit ContractDeployed(msg.sender, _bytecodeHash, _newAddress)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

194    _nonSystemDeployOnAddress(_bytecodeHash, newAddress, _aaVersion, _input)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L185-L197](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L185-L197)


- Reentrancy in 
```
File: code/system-contracts/contracts/ContractDeployer.sol

216    function forceDeployOnAddress(ForceDeployment calldata _deployment, address _sender) external payable onlySelf 
```
:
	External calls:
	- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

225    _constructContract(
226                _sender,
227                _deployment.newAddress,
228                _deployment.bytecodeHash,
229                _deployment.input,
230                false,
231                _deployment.callConstructor
232            )
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

315    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructingCodeHash(_newAddress, constructingBytecodeHash)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

336    ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo(address(this), _newAddress, value)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

348    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.markAccountCodeHashAsConstructed(_newAddress)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

351    IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT.setImmutables(_newAddress, immutables)
```

		- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

355    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructedCodeHash(_newAddress, _bytecodeHash)
```

	Event emitted after the call(s):
	- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

234    emit ContractDeployed(_sender, _deployment.bytecodeHash, _deployment.newAddress)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L216-L235](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L216-L235)


- Reentrancy in 
```
File: code/system-contracts/contracts/L2EthToken.sol

74    function withdraw(address _l1Receiver) external payable override 
```
:
	External calls:
	- 
```
File: code/system-contracts/contracts/L2EthToken.sol

79    L1_MESSENGER_CONTRACT.sendToL1(message)
```

	Event emitted after the call(s):
	- 
```
File: code/system-contracts/contracts/L2EthToken.sol

81    emit Withdrawal(msg.sender, _l1Receiver, amount)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L74-L82](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L74-L82)


- Reentrancy in 
```
File: code/system-contracts/contracts/L2EthToken.sol

87    function withdrawWithMessage(address _l1Receiver, bytes memory _additionalData) external payable override 
```
:
	External calls:
	- 
```
File: code/system-contracts/contracts/L2EthToken.sol

92    L1_MESSENGER_CONTRACT.sendToL1(message)
```

	Event emitted after the call(s):
	- 
```
File: code/system-contracts/contracts/L2EthToken.sol

94    emit WithdrawalWithMessage(msg.sender, _l1Receiver, amount, _additionalData)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L87-L95](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L87-L95)


</details>

# 


## Variable names too similar
- Severity: Non-Critical
- Confidence: Medium

### Description
Detect variables with names that are too similar.

<details>

<summary>
There are 9 instances of this issue:

</summary>

###
- Variable 
```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

66    address payable _l1WethAddress
```
 is too similar to 
```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

85    address _l2WethAddress
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L66](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L66)


- Variable 
```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

46    address payable public immutable l1WethAddress
```
 is too similar to 
```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

58    address public l2WethAddress
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L46](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L46)


- Variable 
```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol

18    address _l1Receiver
```
 is too similar to 
```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol

11    address _l2Receiver
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol#L18](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol#L18)


- Variable 
```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol

10    address _l1WethAddress
```
 is too similar to 
```
File: code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol

11    address _l2WethAddress
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol#L10](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol#L10)


- Variable 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

230    bytes32 _systemContractUpgradeTxHash
```
 is too similar to 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

189    bytes32 systemContractsUpgradeTxHash = s.l2SystemContractsUpgradeTxHash
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L230](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L230)


- Variable 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

108    address _l1Receiver
```
 is too similar to 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

67    address _l2Receiver
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L108](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L108)


- Variable 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

66    address _l1Receiver
```
 is too similar to 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

92    address _l2Receiver
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L66](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L66)


- Variable 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

49    address _l1WethAddress
```
 is too similar to 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

50    address _l2WethAddress
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L49](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L49)


- Variable 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

31    address public l1WethAddress
```
 is too similar to 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

34    address public l2WethAddress
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L31](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L31)


</details>

# 


## Enforce Underscore Prefix for Non-External Variable and Function Names
- Severity: Non-Critical
- Confidence: High

### Description
Non external variables and functions play an internal role within the contract and are not intended to be accessed directly from outside the contract or by other contracts. By prefixing them with an underscore, it provides a visual cue to developers and auditors that these entities are meant for internal use only.

Using an underscore prefix for non-external entities promotes consistency and helps prevent accidental access or confusion between internal and external entities. This convention enhances code maintainability and reduces the risk of unintended behavior or vulnerabilities caused by incorrect access to these variables and functions.

<details>

<summary>
There are 124 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

34    IAllowList internal immutable allowList
```
 allowList should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L34](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L34)


- 
```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

37    IZkSync internal immutable zkSync
```
 zkSync should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L37](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L37)


- 
```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

45    mapping(address => mapping(address => mapping(bytes32 => uint256))) internal depositAmount
```
 depositAmount should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L45](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L45)


- 
```
File: code/contracts/ethereum/contracts/common/ReentrancyGuard.sol

31    uint256 private constant LOCK_FLAG_ADDRESS = 0x8e94fed44239eb2314ab7a406345e6c5a8f0ccedf3b600de3d004e672c33abf4
```
 LOCK_FLAG_ADDRESS should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L31](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L31)


- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

24    uint256 internal constant EXECUTED_PROPOSAL_TIMESTAMP = uint256(1)
```
 EXECUTED_PROPOSAL_TIMESTAMP should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L24)


- 
```
File: code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol

24    uint160 constant offset = uint160(0x1111000000000000000000000000000000001111)
```
 offset should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L24)


- 
```
File: code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol

30    function applyL1ToL2Alias(address l1Address) internal pure returns (address l2Address) 
```
 applyL1ToL2Alias(address) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L30-L34](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L30-L34)


- 
```
File: code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol

40    function undoL1ToL2Alias(address l2Address) internal pure returns (address l1Address) 
```
 undoL1ToL2Alias(address) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L40-L44](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L40-L44)


- 
```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

38    LibMap.Uint32Map internal committedBatchTimestamp
```
 committedBatchTimestamp should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L38](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L38)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol

17    AppStorage internal s
```
 s should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L17](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L17)


- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

31    bytes32 internal l2TokenProxyBytecodeHash
```
 l2TokenProxyBytecodeHash should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L31](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L31)


- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

24    ERC20Getters availableGetters
```
 availableGetters should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L24)


- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

29    uint8 private decimals_
```
 decimals_ should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L29](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L29)


- 
```
File: code/system-contracts/contracts/AccountCodeStorage.sol

25    bytes32 constant EMPTY_STRING_KECCAK = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470
```
 EMPTY_STRING_KECCAK should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L25](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L25)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

46    function encodeLegacyTransactionHash(Transaction calldata _transaction) internal view returns (bytes32 txHash) 
```
 encodeLegacyTransactionHash(Transaction) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L46-L136](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L46-L136)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

141    function encodeEIP2930TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) 
```
 encodeEIP2930TransactionHash(Transaction) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L141-L226](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L141-L226)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

231    function encodeEIP1559TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) 
```
 encodeEIP1559TransactionHash(Transaction) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L231-L321](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L231-L321)


- 
```
File: code/system-contracts/contracts/ContractDeployer.sol

28    mapping(address => AccountInfo) internal accountInfo
```
 accountInfo should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L28](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L28)


- 
```
File: code/system-contracts/contracts/ImmutableSimulator.sol

23    mapping(uint256 => mapping(uint256 => bytes32)) internal immutableDataStorage
```
 immutableDataStorage should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L23](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L23)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

38    bytes32 internal chainedLogsHash
```
 chainedLogsHash should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L38](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L38)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

42    uint256 internal numberOfLogsToProcess
```
 numberOfLogsToProcess should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L42](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L42)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

46    bytes32 internal chainedMessagesHash
```
 chainedMessagesHash should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L46](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L46)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

51    bytes32 internal chainedL1BytecodesRevealDataHash
```
 chainedL1BytecodesRevealDataHash should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L51](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L51)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

54    uint256 internal constant KECCAK_ROUND_GAS_COST = 40
```
 KECCAK_ROUND_GAS_COST should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L54](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L54)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

57    uint256 internal constant KECCAK_ROUND_NUMBER_OF_BYTES = 136
```
 KECCAK_ROUND_NUMBER_OF_BYTES should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L57](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L57)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

65    uint256 internal constant SHA256_ROUND_GAS_COST = 7
```
 SHA256_ROUND_GAS_COST should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L65](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L65)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

68    uint256 internal constant SHA256_ROUND_NUMBER_OF_BYTES = 64
```
 SHA256_ROUND_NUMBER_OF_BYTES should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L68](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L68)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

60    function keccakGasCost(uint256 _length) internal pure returns (uint256) 
```
 keccakGasCost(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L60-L62](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L60-L62)


- 
```
File: code/system-contracts/contracts/L1Messenger.sol

71    function sha256GasCost(uint256 _length) internal pure returns (uint256) 
```
 sha256GasCost(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L71-L73](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L71-L73)


- 
```
File: code/system-contracts/contracts/L2EthToken.sol

22    mapping(address => uint256) internal balance
```
 balance should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L22](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L22)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

30    uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128
```
 DEPLOY_NONCE_MULTIPLIER should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L30](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L30)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

33    uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32
```
 MAXIMAL_MIN_NONCE_INCREMENT should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L33](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L33)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

38    mapping(uint256 => uint256) internal rawNonces
```
 rawNonces should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L38](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L38)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

43    mapping(uint256 => mapping(uint256 => uint256)) internal nonceValues
```
 nonceValues should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L43](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L43)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

25    uint256 internal constant MINIBLOCK_HASHES_TO_STORE = 257
```
 MINIBLOCK_HASHES_TO_STORE should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L25](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L25)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

53    BlockInfo internal currentBatchInfo
```
 currentBatchInfo should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L53](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L53)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

57    mapping(uint256 => bytes32) internal batchHash
```
 batchHash should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L57](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L57)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

60    BlockInfo internal currentL2BlockInfo
```
 currentL2BlockInfo should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L60](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L60)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

63    bytes32 internal currentL2BlockTxsRollingHash
```
 currentL2BlockTxsRollingHash should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L63](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L63)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

72    bytes32[MINIBLOCK_HASHES_TO_STORE] internal l2BlockHash
```
 l2BlockHash should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L72](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L72)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

79    BlockInfo internal currentVirtualL2BlockInfo
```
 currentVirtualL2BlockInfo should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L79](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L79)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

82    VirtualBlockUpgradeInfo internal virtualBlockUpgradeInfo
```
 virtualBlockUpgradeInfo should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L82](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L82)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

38    function keccak(bytes calldata _data) internal view returns (bytes32) 
```
 keccak(bytes) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L38-L42](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L38-L42)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

47    function sha(bytes calldata _data) internal view returns (bytes32) 
```
 sha(bytes) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L47-L51](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L47-L51)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

60    function call(
61            uint256 _gas,
62            address _address,
63            uint256 _value,
64            bytes calldata _data,
65            bool _isSystem
66        ) internal returns (bytes memory returnData) 
```
 call(uint256,address,uint256,bytes,bool) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L60-L69](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L60-L69)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

76    function staticCall(
77            uint256 _gas,
78            address _address,
79            bytes calldata _data
80        ) internal view returns (bytes memory returnData) 
```
 staticCall(uint256,address,bytes) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L76-L83](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L76-L83)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

90    function delegateCall(
91            uint256 _gas,
92            address _address,
93            bytes calldata _data
94        ) internal returns (bytes memory returnData) 
```
 delegateCall(uint256,address,bytes) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L90-L97](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L90-L97)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

107    function mimicCall(
108            uint256 _gas,
109            address _address,
110            bytes calldata _data,
111            address _whoToMimic,
112            bool _isConstructor,
113            bool _isSystem
114        ) internal returns (bytes memory returnData) 
```
 mimicCall(uint256,address,bytes,address,bool,bool) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L107-L117](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L107-L117)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

126    function rawCall(
127            uint256 _gas,
128            address _address,
129            uint256 _value,
130            bytes calldata _data,
131            bool _isSystem
132        ) internal returns (bool success) 
```
 rawCall(uint256,address,uint256,bytes,bool) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L126-L154](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L126-L154)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

161    function rawStaticCall(uint256 _gas, address _address, bytes calldata _data) internal view returns (bool success) 
```
 rawStaticCall(uint256,address,bytes) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L161-L168](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L161-L168)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

175    function rawDelegateCall(uint256 _gas, address _address, bytes calldata _data) internal returns (bool success) 
```
 rawDelegateCall(uint256,address,bytes) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L175-L182](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L175-L182)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

193    function rawMimicCall(
194            uint256 _gas,
195            address _address,
196            bytes calldata _data,
197            address _whoToMimic,
198            bool _isConstructor,
199            bool _isSystem
200        ) internal returns (bool success) 
```
 rawMimicCall(uint256,address,bytes,address,bool,bool) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L193-L212](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L193-L212)


- 
```
File: code/system-contracts/contracts/libraries/EfficientCall.sol

234    function propagateRevert() internal pure 
```
 propagateRevert() should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L234-L240](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L234-L240)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

13    function encodeAddress(address _val) internal pure returns (bytes memory encoded) 
```
 encodeAddress(address) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L13-L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L13-L24)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

26    function encodeUint256(uint256 _val) internal pure returns (bytes memory encoded) 
```
 encodeUint256(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L26-L46](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L26-L46)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

51    function encodeNonSingleBytesLen(uint64 _len) internal pure returns (bytes memory) 
```
 encodeNonSingleBytesLen(uint64) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L51-L54](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L51-L54)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

58    function encodeListLen(uint64 _len) internal pure returns (bytes memory) 
```
 encodeListLen(uint64) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L58-L60](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L58-L60)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

77    function toL1(bool _isService, bytes32 _key, bytes32 _value) internal 
```
 toL1(bool,bytes32,bytes32) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L77-L86](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L77-L86)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

92    function getCodeAddress() internal view returns (address addr) 
```
 getCodeAddress() should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L92-L97](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L92-L97)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

103    function loadCalldataIntoActivePtr() internal view 
```
 loadCalldataIntoActivePtr() should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L103-L108](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L103-L108)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

114    function ptrPackIntoActivePtr(uint256 _farCallAbi) internal view 
```
 ptrPackIntoActivePtr(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L114-L119](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L114-L119)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

123    function ptrAddIntoActive(uint32 _value) internal view 
```
 ptrAddIntoActive(uint32) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L123-L131](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L123-L131)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

135    function ptrShrinkIntoActive(uint32 _shrink) internal view 
```
 ptrShrinkIntoActive(uint32) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L135-L143](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L135-L143)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

153    function packPrecompileParams(
154            uint32 _inputMemoryOffset,
155            uint32 _inputMemoryLength,
156            uint32 _outputMemoryOffset,
157            uint32 _outputMemoryLength,
158            uint64 _perPrecompileInterpreted
159        ) internal pure returns (uint256 rawParams) 
```
 packPrecompileParams(uint32,uint32,uint32,uint32,uint64) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L153-L165](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L153-L165)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

177    function unsafePrecompileCall(uint256 _rawParams, uint32 _gasToBurn) internal view returns (bool success) 
```
 unsafePrecompileCall(uint256,uint32) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L177-L186](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L177-L186)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

191    function setValueForNextFarCall(uint128 _value) internal returns (bool success) 
```
 setValueForNextFarCall(uint128) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L191-L199](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L191-L199)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

204    function eventInitialize(uint256 initializer, uint256 value1) internal 
```
 eventInitialize(uint256,uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L204-L209](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L204-L209)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

214    function eventWrite(uint256 value1, uint256 value2) internal 
```
 eventWrite(uint256,uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L214-L219](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L214-L219)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

225    function getZkSyncMetaBytes() internal view returns (uint256 meta) 
```
 getZkSyncMetaBytes() should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L225-L230](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L225-L230)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

237    function extractNumberFromMeta(uint256 meta, uint256 offset, uint256 size) internal pure returns (uint256 result) 
```
 extractNumberFromMeta(uint256,uint256,uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L237-L242](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L237-L242)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

248    function getGasPerPubdataByteFromMeta(uint256 meta) internal pure returns (uint32 gasPerPubdataByte) 
```
 getGasPerPubdataByteFromMeta(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L248-L250](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L248-L250)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

258    function getHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 heapSize) 
```
 getHeapSizeFromMeta(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L258-L260](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L258-L260)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

267    function getAuxHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 auxHeapSize) 
```
 getAuxHeapSizeFromMeta(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L267-L269](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L267-L269)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

275    function getShardIdFromMeta(uint256 meta) internal pure returns (uint8 shardId) 
```
 getShardIdFromMeta(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L275-L277](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L275-L277)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

284    function getCallerShardIdFromMeta(uint256 meta) internal pure returns (uint8 callerShardId) 
```
 getCallerShardIdFromMeta(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L284-L286](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L284-L286)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

293    function getCodeShardIdFromMeta(uint256 meta) internal pure returns (uint8 codeShardId) 
```
 getCodeShardIdFromMeta(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L293-L295](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L293-L295)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

299    function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) 
```
 getZkSyncMeta() should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L299-L305](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L299-L305)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

314    function getCallFlags() internal view returns (uint256 callFlags) 
```
 getCallFlags() should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L314-L319](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L314-L319)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

325    function getCalldataPtr() internal view returns (uint256 ptr) 
```
 getCalldataPtr() should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L325-L330](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L325-L330)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

336    function getExtraAbiData(uint256 index) internal view returns (uint256 extraAbiData) 
```
 getExtraAbiData(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L336-L343](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L336-L343)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

347    function isSystemCall() internal view returns (bool) 
```
 isSystemCall() should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L347-L351](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L347-L351)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

356    function isSystemContract(address _address) internal pure returns (bool) 
```
 isSystemContract(address) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L356-L358](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L356-L358)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractHelper.sol

362    function burnGas(uint32 _gasToPay) internal view 
```
 burnGas(uint32) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L362-L368](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L362-L368)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractsCaller.sol

78    function systemCall(uint32 gasLimit, address to, uint256 value, bytes memory data) internal returns (bool success) 
```
 systemCall(uint32,address,uint256,bytes) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L78-L115](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L78-L115)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractsCaller.sol

125    function systemCallWithReturndata(
126            uint32 gasLimit,
127            address to,
128            uint128 value,
129            bytes memory data
130        ) internal returns (bool success, bytes memory returnData) 
```
 systemCallWithReturndata(uint32,address,uint128,bytes) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L125-L142](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L125-L142)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractsCaller.sol

152    function systemCallWithPropagatedRevert(
153            uint32 gasLimit,
154            address to,
155            uint128 value,
156            bytes memory data
157        ) internal returns (bytes memory returnData) 
```
 systemCallWithPropagatedRevert(uint32,address,uint128,bytes) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L152-L167](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L152-L167)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractsCaller.sol

216    function getFarCallABI(
217            uint32 dataOffset,
218            uint32 memoryPage,
219            uint32 dataStart,
220            uint32 dataLength,
221            uint32 gasPassed,
222            uint8 shardId,
223            CalldataForwardingMode forwardingMode,
224            bool isConstructorCall,
225            bool isSystemCall
226        ) internal pure returns (uint256 farCallAbi) 
```
 getFarCallABI(uint32,uint32,uint32,uint32,uint32,uint8,CalldataForwardingMode,bool,bool) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L216-L240](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L216-L240)


- 
```
File: code/system-contracts/contracts/libraries/SystemContractsCaller.sol

252    function getFarCallABIWithEmptyFatPointer(
253            uint32 gasPassed,
254            uint8 shardId,
255            CalldataForwardingMode forwardingMode,
256            bool isConstructorCall,
257            bool isSystemCall
258        ) internal pure returns (uint256 farCallAbiWithEmptyFatPtr) 
```
 getFarCallABIWithEmptyFatPointer(uint32,uint8,CalldataForwardingMode,bool,bool) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L252-L268](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L252-L268)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

84    bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)")
```
 EIP712_DOMAIN_TYPEHASH should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L84](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L84)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

86    bytes32 constant EIP712_TRANSACTION_TYPE_HASH =
87            keccak256(
88                "Transaction(uint256 txType,uint256 from,uint256 to,uint256 gasLimit,uint256 gasPerPubdataByteLimit,uint256 maxFeePerGas,uint256 maxPriorityFeePerGas,uint256 paymaster,uint256 nonce,uint256 value,bytes data,bytes32[] factoryDeps,bytes paymasterInput)"
89            )
```
 EIP712_TRANSACTION_TYPE_HASH should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L86-L89](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L86-L89)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

96    function isEthToken(uint256 _addr) internal pure returns (bool) 
```
 isEthToken(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L96-L98](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L96-L98)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

102    function encodeHash(Transaction calldata _transaction) internal view returns (bytes32 resultHash) 
```
 encodeHash(Transaction) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L102-L116](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L102-L116)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

364    function processPaymasterInput(Transaction calldata _transaction) internal 
```
 processPaymasterInput(Transaction) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L364-L392](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L364-L392)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

397    function payToTheBootloader(Transaction calldata _transaction) internal returns (bool success) 
```
 payToTheBootloader(Transaction) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L397-L404](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L397-L404)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

407    function totalRequiredBalance(Transaction calldata _transaction) internal pure returns (uint256 requiredBalance) 
```
 totalRequiredBalance(Transaction) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L407-L415](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L407-L415)


- 
```
File: code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol

21    function readUint16(bytes calldata _bytes, uint256 _start) internal pure returns (uint16 result) 
```
 readUint16(bytes,uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L21-L26](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L21-L26)


- 
```
File: code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol

28    function readUint32(bytes calldata _bytes, uint256 _start) internal pure returns (uint32 result) 
```
 readUint32(bytes,uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L28-L33](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L28-L33)


- 
```
File: code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol

35    function readUint64(bytes calldata _bytes, uint256 _start) internal pure returns (uint64 result) 
```
 readUint64(bytes,uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L35-L40](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L35-L40)


- 
```
File: code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol

42    function readBytes32(bytes calldata _bytes, uint256 _start) internal pure returns (bytes32 result) 
```
 readBytes32(bytes,uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L42-L46](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L42-L46)


- 
```
File: code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol

48    function readUint256(bytes calldata _bytes, uint256 _start) internal pure returns (uint256 result) 
```
 readUint256(bytes,uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L48-L52](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L48-L52)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

15    bytes32 constant IS_CONSTRUCTOR_BYTECODE_HASH_BIT_MASK =
16            0x00ff000000000000000000000000000000000000000000000000000000000000
```
 IS_CONSTRUCTOR_BYTECODE_HASH_BIT_MASK should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L15-L16](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L15-L16)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

19    bytes32 constant SET_IS_CONSTRUCTOR_MARKER_BIT_MASK =
20            0x0001000000000000000000000000000000000000000000000000000000000000
```
 SET_IS_CONSTRUCTOR_MARKER_BIT_MASK should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L19-L20](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L19-L20)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

22    function safeCastToU128(uint256 _x) internal pure returns (uint128) 
```
 safeCastToU128(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L22-L26](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L22-L26)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

28    function safeCastToU32(uint256 _x) internal pure returns (uint32) 
```
 safeCastToU32(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L28-L32](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L28-L32)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

34    function safeCastToU24(uint256 _x) internal pure returns (uint24) 
```
 safeCastToU24(uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L34-L38](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L34-L38)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

41    function bytecodeLenInBytes(bytes32 _bytecodeHash) internal pure returns (uint256 codeLength) 
```
 bytecodeLenInBytes(bytes32) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L41-L43](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L41-L43)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

46    function bytecodeLenInWords(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) 
```
 bytecodeLenInWords(bytes32) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L46-L50](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L46-L50)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

53    function isContractConstructed(bytes32 _bytecodeHash) internal pure returns (bool) 
```
 isContractConstructed(bytes32) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L53-L55](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L53-L55)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

58    function isContractConstructing(bytes32 _bytecodeHash) internal pure returns (bool) 
```
 isContractConstructing(bytes32) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L58-L60](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L58-L60)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

65    function constructingBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) 
```
 constructingBytecodeHash(bytes32) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L65-L68](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L65-L68)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

73    function constructedBytecodeHash(bytes32 _bytecodeHash) internal pure returns (bytes32) 
```
 constructedBytecodeHash(bytes32) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L73-L75](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L73-L75)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

84    function hashL2Bytecode(bytes calldata _bytecode) internal view returns (bytes32 hashedBytecode) 
```
 hashL2Bytecode(bytes) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L84-L98](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L84-L98)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

38    function isContract(address account) internal view returns (bool) 
```
 isContract(address) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L38-L44](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L38-L44)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

62    function sendValue(address payable recipient, uint256 amount) internal 
```
 sendValue(address,uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L62-L73](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L62-L73)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

93    function functionCall(address target, bytes memory data)
94            internal
95            returns (bytes memory)
96        
```
 functionCall(address,bytes) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L93-L104](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L93-L104)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

112    function functionCall(
113            address target,
114            bytes memory data,
115            string memory errorMessage
116        ) internal returns (bytes memory) 
```
 functionCall(address,bytes,string) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L112-L118](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L112-L118)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

131    function functionCallWithValue(
132            address target,
133            bytes memory data,
134            uint256 value
135        ) internal returns (bytes memory) 
```
 functionCallWithValue(address,bytes,uint256) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L131-L143](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L131-L143)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

151    function functionCallWithValue(
152            address target,
153            bytes memory data,
154            uint256 value,
155            string memory errorMessage
156        ) internal returns (bytes memory) 
```
 functionCallWithValue(address,bytes,uint256,string) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L151-L171](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L151-L171)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

179    function functionStaticCall(address target, bytes memory data)
180            internal
181            view
182            returns (bytes memory)
183        
```
 functionStaticCall(address,bytes) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L179-L190](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L179-L190)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

198    function functionStaticCall(
199            address target,
200            bytes memory data,
201            string memory errorMessage
202        ) internal view returns (bytes memory) 
```
 functionStaticCall(address,bytes,string) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L198-L211](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L198-L211)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

219    function functionDelegateCall(address target, bytes memory data)
220            internal
221            returns (bytes memory)
222        
```
 functionDelegateCall(address,bytes) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L219-L229](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L219-L229)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

237    function functionDelegateCall(
238            address target,
239            bytes memory data,
240            string memory errorMessage
241        ) internal returns (bytes memory) 
```
 functionDelegateCall(address,bytes,string) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L237-L250](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L237-L250)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

258    function verifyCallResultFromTarget(
259            address target,
260            bool success,
261            bytes memory returndata,
262            string memory errorMessage
263        ) internal view returns (bytes memory) 
```
 verifyCallResultFromTarget(address,bool,bytes,string) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L258-L274](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L258-L274)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

282    function verifyCallResult(
283            bool success,
284            bytes memory returndata,
285            string memory errorMessage
286        ) internal pure returns (bytes memory) 
```
 verifyCallResult(bool,bytes,string) should start with '_' prefix.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L282-L292](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L282-L292)


</details>

# 


## Whitespace in Expressions
- Severity: Non-Critical
- Confidence: High

### Description
Detects when whitespace usage in expressions does not conform to the Solidity style guide.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

20    contract ExecutorFacet is Base, IExecutor 
```

```
// @audit: whitespace inside parenthesis
Line: 127                (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET);


// @audit: whitespace inside parenthesis
Line: 128                (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);


// @audit: whitespace inside parenthesis
Line: 129                (bytes32 logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);


```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L20-L473](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L20-L473)


- 
```
File: code/system-contracts/contracts/bridge/L2Weth.sol

23    contract L2Weth is ERC20PermitUpgradeable, IL2Weth, IL2StandardToken 
```

```
// @audit: whitespace inside parenthesis
Line: 83            (bool success, ) = msg.sender.call{value: _amount}("");


// @audit: whitespace inside parenthesis
Line: 108            (bool success, ) = _to.call{value: _amount}("");


```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L23-L116](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L23-L116)


- 
```
File: code/system-contracts/contracts/MsgValueSimulator.sol

21    contract MsgValueSimulator is ISystemContract 
```

```
// @audit: whitespace inside parenthesis
Line: 44                (bool success, ) = address(ETH_TOKEN_SYSTEM_CONTRACT).call(


```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L21-L61](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L21-L61)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

29    contract NonceHolder is INonceHolder, ISystemContract 
```

```
// @audit: whitespace inside parenthesis
Line: 129            (deploymentNonce, ) = _splitRawNonce(rawNonces[addressAsKey]);


// @audit: whitespace inside parenthesis
Line: 146            (prevDeploymentNonce, ) = _splitRawNonce(oldRawNonce);


```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L29-L178](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L29-L178)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

11    library Address 
```

```
// @audit: whitespace inside parenthesis
Line: 28            (bool success, ) = recipient.call{value: amount}("");


```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L11-L310](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L11-L310)


</details>

# 


## Variables with all caps names that are not constants or immutables
- Severity: Non-Critical
- Confidence: Medium

### Description
This detector flags variable declarations where the variable name is in all capital letters but the variable is not declared as a 'constant' or 'immutable'. In solidity, it is a convention to reserve variable names in all capital letters for constants and immutables. Variable names that need to be different based on the class they come from should be returned by a 'view' or 'pure' function instead.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: node_modules/@openzeppelin/contracts-upgradeable/token/ERC20/extensions/ERC20PermitUpgradeable.sol

40    bytes32 private _PERMIT_TYPEHASH_DEPRECATED_SLOT
```

</details>

# 


## Cast to bytes or bytes32 for clearer semantic meaning
- Severity: Non-Critical
- Confidence: High

### Description
Using a cast on a single argument, rather than abi.encodePacked() makes the intended operation more clear, leading to less reviewer confusion.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/bridge/L2WethBridge.sol

77    abi.encodePacked(_l1Receiver)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L77](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L77)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

207    abi.encodePacked(uint32(_blockNumber))
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L207](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L207)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

135    abi.encodePacked(_transaction.factoryDeps)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L135](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L135)


</details>

# 


## Token contract should have a blacklist function or modifier
- Severity: Non-Critical
- Confidence: Medium

### Description
NFT thefts have increased recently, so with the addition of stolen NFTs to the platform, NFTs can be converted into liquidity. To prevent this, we recommend adding a blacklist function.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

13    contract L2StandardERC20 is ERC20PermitUpgradeable, IL2StandardToken 
```
Token contract does not contain a blacklist. Consider adding one for increased security.
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L13-L152](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L13-L152)


- 
```
File: code/system-contracts/contracts/bridge/L2Weth.sol

23    contract L2Weth is ERC20PermitUpgradeable, IL2Weth, IL2StandardToken 
```
Token contract does not contain a blacklist. Consider adding one for increased security.
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L23-L116](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L23-L116)


</details>

# 


## Do not calculate constants
- Severity: Non-Critical
- Confidence: High

### Description
Due to how constant variables are implemented in Solidity (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas. While in most cases, the compiler will optimize these computations away, it is considered a best practice to write code that does not rely on the compiler optimization.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/NonceHolder.sol

30    uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L30](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L30)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

33    uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L33](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L33)


</details>

# 


## Empty function body
- Severity: Non-Critical
- Confidence: High

### Description
Functions with empty bodies should include comments describing their purpose.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/EmptyContract.sol

14    fallback() external payable 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L14](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L14)


- 
```
File: code/system-contracts/contracts/EmptyContract.sol

16    receive() external payable 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L16](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L16)


</details>

# 


## Override function arguments that are unused should have the variable name removed or commented out
- Severity: Non-Critical
- Confidence: High

### Description
Unused arguments in overridden functions can lead to compiler warnings andcan make the code less readable. It is a good practice to remove or comment outunused arguments in these cases.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/DefaultAccount.sol

129    Transaction calldata _transaction
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L129](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L129)


</details>

# 


## Too many digits
- Severity: Non-Critical
- Confidence: Medium

### Description

Literals with many digits are difficult to read and review.


<details>

<summary>
There are 5 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/SystemContext.sol

19    contract SystemContext is ISystemContext, ISystemContextDeprecated, ISystemContract 
```
 uses literals with too many digits:
	- 
```
File: code/system-contracts/contracts/SystemContext.sol

46    uint256 public difficulty = 2500000000000000
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L19-L486](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L19-L486)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

13    function encodeAddress(address _val) internal pure returns (bytes memory encoded) 
```
 uses literals with too many digits:
	- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

20    mstore(add(encoded, 0x20), 0x9400000000000000000000000000000000000000000000000000000000000000)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L13-L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L13-L24)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

84    function hashL2Bytecode(bytes calldata _bytecode) internal view returns (bytes32 hashedBytecode) 
```
 uses literals with too many digits:
	- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

91    hashedBytecode =
92                EfficientCall.sha(_bytecode) &
93                0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L84-L98](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L84-L98)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

13    library Utils 
```
 uses literals with too many digits:
	- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

15    bytes32 constant IS_CONSTRUCTOR_BYTECODE_HASH_BIT_MASK =
16            0x00ff000000000000000000000000000000000000000000000000000000000000
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L13-L99](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L13-L99)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

13    library Utils 
```
 uses literals with too many digits:
	- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

19    bytes32 constant SET_IS_CONSTRUCTOR_MARKER_BIT_MASK =
20            0x0001000000000000000000000000000000000000000000000000000000000000
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L13-L99](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L13-L99)


</details>

# 


## Unsafe use of pragma solidity >= without an upper bound
- Severity: Non-Critical
- Confidence: High

### Description
This detector checks for instances where pragma solidity is used with >= operator without specifying an upper bound. This could lead to incompatibilities with future versions of Solidity, potentially breaking the code. It's recommended to specify both a lower and an upper bound.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

1    pragma solidity >=0.8.0;
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L1)


</details>

# 


