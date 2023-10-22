# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Use uint256 constant instead for strings less than the length of 33| 5 |
| [GAS-2](#GAS-2) | Using `private` rather than `public` for constants, saves gas | 2 |
| [GAS-3](#GAS-3) | Use custom errors with bytes32 instead of revert when reverting errors | 86 |
| [GAS-4](#GAS-4) | Cache variables that are stored in either memory or storage you use more than once |14| 
| [GAS-5](#GAS-5) | Use assembly for shift operation | 4 |
| [GAS-6](#GAS-6) | cache length of array if used in a for loop | 5 |
| [GAS-7](#GAS-7) | Use named returns to save gas instead of unnamed returns | 56 |
| [GAS-8](#GAS-8) | Use assembly and shift operations for typecasting operations | 5 |
| [GAS-9](#GAS-9) | When comparing address typecast to the uint type first | 2 |
| [GAS-10](#GAS-10) | Use uint256 instead of bytes32 constant when the location is storage | 5 |
| [GAS-11](#GAS-11) | use solady instead of openzeppelin | 4 |
| [GAS-12](#GAS-12) | when the array is not mutated we should store it in the calladata instead of memory | 1 |
| [GAS-13](#GAS-13) | division or multiplication that involves the second digit as a power of two should be done either shift left or right | 5 |
| [GAS-14](#GAS-14) | possible offchain computation shoulf be computed offchain before being brought on chain | 1 |
| [GAS-15](#GAS-15) | when incrementing by one simply do a pre-increment | 1 |

### [GAS-1] Use uint256 constant instead for strings less than the length of 33
typecast strings that are less than the length of 33 to bytes32 then we then typecast this bytes32 to uint256 before storing them, this would be done offchain then when we simply want to use them, we can simply typecast it back to string using assembly or if possible it should be done offchain
This will save you 69000+ in gas per deployment cost for each instance
*Instances (5)*:
```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol
22: string public constant override getName = "ExecutorFacet";

File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
41: string public constant override getName = "MailboxFacet";

File: code/contracts/ethereum/contracts/zksync/facets/Getters.sol
19: string public constant override getName = "GettersFacet";

File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
24: string public constant override getName = "ValidatorTimelock";

File:
15: string public constant override getName = "AdminFacet";
```

GITHUB INSTANCES:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15C5-L15C60
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24C5-L24C67
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22C4-L22C63
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41

### [GAS-3] Use custom errors with bytes32 instead of revert when reverting errors
it's way better and more gas saving to use custom errors with bytes32(if a string is present) than to use require and string, using bytes32 and custom error more expensive us 8k+ in gas deployment cost per instance but this saves us 100+ in gas fees during runtime

*Instances (86)*:
```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol
45: require(_previousBatch.batchHash == previousBatchHash, "l");
47: require(expectedPriorityOperationsHash == _newBatch.priorityOperationsHash, "t");
49: require(expectedNumberOfLayer1Txs == _newBatch.numberOfLayer1Txs, "ta");
81: require(batchTimestamp == _expectedBatchTimestamp, "tb");
85: require(_previousBatchTimestamp < batchTimestamp, "h3");
93: require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1");
94: require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2");
130: require(!_checkBit(processedLogs, uint8(logKey)), "kp");
135: require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lm");
138: require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "ln");
139: require(providedL2ToL1PubdataHash == logValue, "wp");
141: require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lb");
144: require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sc");
147: require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sv");
150: require(logSender == L2_BOOTLOADER_ADDRESS, "bl");
153: require(logSender == L2_BOOTLOADER_ADDRESS, "bk");
156: require(logSender == L2_BOOTLOADER_ADDRESS, "bu");
157: require(_expectedSystemContractUpgradeTxHash == logValue, "ut");
167: require(processedLogs == 127, "b7");
169: require(processedLogs == 255, "b8");
184: require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i");
185: require(_newBatchesData.length > 0, "No batches to commit");
186: require(_newBatchesData.length > 0, "No batches to commit");
236: require(s.l2SystemContractsUpgradeBatchNumber == 0, "ik");
275: require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k");
276:    require(
            _hashStoredBatchInfo(_storedBatch) == s.storedBatchHashes[currentBatchNumber],
            "exe10" // executing batch should be committed
        );
282: require(priorityOperationsHash == _storedBatch.priorityOperationsHash, "x");
300: require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n");
327: require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");
328: require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");
332:  require(_hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],"o1");
333: require(currentTotalBatchesVerified <= s.totalBatchesCommitted, "q");
360: require(successVerifyProof, "p");
368: require(successVerifyProof, "p");
399: require(s.totalBatchesCommitted > _newLastBatch, "v1");
400: require(_newLastBatch >= s.totalBatchesExecuted, "v2");

File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
119: require(callSuccess, "pz");
129: require(_batchNumber <= s.totalBatchesExecuted, "xx");
136: require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");
210: require(proofValid, "pi"); 
257: require(_l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp");
279: require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
306: require(msg.value >= baseCost + _l2Value, "mv");
421: require(_message.length >= 56, "pm");
424: require(bytes4(functionSignature) == this.finalizeEthWithdrawal.selector, "is");

File: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
105: require(selectors.length > 0, "B");
132: require(_facet != address(0), "G");
141: require(oldFacet.facetAddress == address(0), "J");
156: require(_facet != address(0), "K");
162: require(oldFacet.facetAddress != address(0), "L");
176: require(_facet == address(0), "a1"); // facet address must be zero
182: require(oldFacet.facetAddress != address(0), "a2")
220: require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
285: require(_calldata.length == 0, "H");
302: require(data.length == 32, "lp");
303: require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");

File: code/contracts/ethereum/contracts/zksync/facets/Getters.sol
160: require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");

File: code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol
30: require(l2GasForTxBody <= _priorityTxMaxGasLimit, "ui");
32: require(l2GasForTxBody / _transaction.gasPerPubdataByteLimit <= PRIORITY_TX_MAX_PUBDATA, "uk");
36: require(
50: require(_transaction.from <= type(uint16).max, "ua");
51: require(_transaction.to <= type(uint160).max, "ub");
52: require(_transaction.paymaster == 0, "uc");
53: require(_transaction.value == 0, "ud");
54: require(_transaction.reserved[0] == 0, "ue");
55: require(_transaction.reserved[1] <= type(uint160).max, "uf");
56: require(_transaction.reserved[2] == 0, "ug");
57: require(_transaction.reserved[3] == 0, "uo");
58: require(_transaction.signature.length == 0, "uh");
59: require(_transaction.paymasterInput.length == 0, "ul");
60: require(_transaction.reservedDynamic.length == 0, "um");

File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
66: require(msg.sender == validator, "8h");
123: require(block.timestamp >= commitBatchTimestamp + delay, "5c");

File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol
31: require(msg.sender == pendingGovernor, "n4");
55: require(msg.sender == pendingAdmin, "n4");
84: require(_newPriorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "n5");
112: require(!diamondStorage.isFrozen, "a9");
123: require(diamondStorage.isFrozen, "a7");

File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol
56: require(address(_initalizeData.verifier) != address(0), "vt");
57: require(_initalizeData.governor != address(0), "vy");
58: require(_initalizeData.admin != address(0), "hc");
59: require(_initalizeData.priorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "vu");

File: code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol
65: require(!_queue.isEmpty(), "D"); // priority queue is empty
73: require(!_queue.isEmpty(), "s"); // priority queue is empty

File: code/contracts/ethereum/contracts/zksync/DiamondProxy.sol
14: require(_chainId == block.chainid, "pr");
25: require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
30: require(facetAddress != address(0), "F");
31: require(!diamondStorage.isFrozen || !facet.isFreezable, "q1");

File: code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
24: require(pathLength > 0, "xc");
25: require(pathLength < 256, "bt");
26: require(_index < (1 << pathLength), "px");

File: code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol
28: result = uint32(mapValue >> bitOffset);
56: uint32 oldValue = uint32(mapValue >> bitOffset);
63: _map.map[mapIndex] = (newValueXorOldValue << bitOffset) ^ mapValue;

File: code/contracts/ethereum/contracts/zksync/facets/Base.sol
19: require(msg.sender == s.governor, "1g");
25: require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");
31: require(s.validators[msg.sender], "1h");
```
GITHUB INSTANCES:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L31C9-L31C49
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L19C9-L19C49
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L25C1-L25C97
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L63C13-L63C80
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L56
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L28C13-L28C52
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L26
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L25C1-L25C1
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L24C1-L26C51
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L31
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L30
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L25C8-L25C69
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L14C9-L14C50
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L65C9-L66C1
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L59
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L58
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L57
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L56
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L123C9-L123C48
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L112C9-L112C49
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L84C9-L84C73
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L31
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L123C17-L123C80
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L66
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L60
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L59
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L58
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L53
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L52
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L51C61-L51C61
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L50C9-L60C65
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L36
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L32C9-L32C104
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L30
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L160C9-L160C81
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L303C1-L303C1
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L302C1-L303C94
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L285C13-L285C49
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L220C13-L220C94
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L182C13-L182C63
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L176C9-L176C75
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L162C13-L162C63
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L156C9-L156C44
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L141C13-L141C63
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L106C13-L106C48
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L424C9-L424C89
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L421C9-L422C1
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L210C9-L210C36
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L136C8-L137C1
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L136C8-L137C1
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L119
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L45
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L47
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L49
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L81
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L85C9-L85C65
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L93
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L94C9-L94C103
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L130
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L135C17-L135C85
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L138C16-L138C85
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L139C17-L139C70
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L141C17-L141C85
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L144C17-L144C84
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L147C17-L147C84
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L150
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L153
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L156C14-L157C81
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L157C16-L157C16
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L167
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L169C13-L169C49
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L184
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L185
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L236
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L275
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L276
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L282
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L300
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L327
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L332
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L346
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L360
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L368
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L399
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L400
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L453


### [GAS-4] Cache variables that are stored in either memory or storage you use more than once
when we read from the storage we invoke sload which cost 2100 in gas fees, if we were to cache it we spend 2103 in gas fees however in continuous usage in the function instead of use to keep spending 2100 in gas fees for each usage we simply spend 2 in gas fees, for memory each read cost us 3 gas using mload however if we were to cache it we save 1 in gas fees for continuous usage

*Instances (14)*:
```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol
32: require(_newBatch.batchNumber == _previousBatch.batchNumber + 1, "f");
52: _verifyBatchTimestamp(packedBatchAndL2BlockTimestamp, _newBatch.timestamp,_previousBatch.timestamp);
59:_newBatch.batchNumber
65:_newBatch.timestamp
62: _newBatch.numberOfLayer1Txs,
63: _newBatch.priorityOperationsHash,
47: require(expectedPriorityOperationsHash == _newBatch.priorityOperationsHash, "t");
49: require(expectedNumberOfLayer1Txs == _newBatch.numberOfLayer1Txs, "ta");
212: s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData);
214: _lastCommittedBatchData.batchNumber,
250:  s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData);
252: _lastCommittedBatchData.batchNumber,


File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
372: expirationTimestamp: _priorityOpParams.expirationTimestamp,
381: _priorityOpParams.expirationTimestamp,

```
GITHUB INSTANCES:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L32
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L52
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L59
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L65
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L62C17-L63C50
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L63
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L47
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L49
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L212
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L214
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L250
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L252

### [GAS-5] Use assembly for shift operations
using assembly for shift operations will save you 3+ in runtime gas cost
*Instances (4)*:
```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol
80:  uint256 batchTimestamp = _packedBatchAndL2BlockTimestamp >> 128;
473: return (_bitMap & (1 << _index)) > 0
478: return _bitMap | (1 << _index)

File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

File: code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
26: require(_index < (1 << pathLength), "px");


```
GITHUB INSTANCES:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L80C9-L80C73
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L473
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L478C9-L478C39

### [GAS-6] cache length of array if used in a for loop
depending on the length of the array caching the length is always good, less than 4 there's an increase in gas cost, more than 4 then we should definitely cache the length of the array this 4+ in runtime gas cost depending on the length of the array
*Instances (5)*:
```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol
241:  for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc())
330:  for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc())
209:  for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) 

File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
85: for (uint256 i = 0; i < _newBatchesData.length; ++i) {
116: for (uint256 i = 0; i < _newBatchesData.length; ++i) {
```
GITHUB INSTANCES:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L116C13-L116C67
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L85C13-L85C67
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L241
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L330C8-L330C78
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L209C9-L209C80

### [GAS-7] Use named returns to save gas instead of unnamed returns 
when we want to return a value in a function, it's way better to use named returns than unnamed returns
using named returns saves us 20+ in runtime gas cost compared to unnamed runtime cost
*Instances (56)*:
```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol
31:   ) internal view returns (StoredBatchInfo memory) {
380:  ) internal pure returns (uint256) {
417:  function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) {
425:  returns (bytes32)
434:  function _batchPassThroughData(CommitBatchInfo calldata _batch) internal pure returns (bytes memory) {
444:  function _batchMetaParameters() internal view returns (bytes memory) {
467:  function _hashStoredBatchInfo(StoredBatchInfo memory _storedBatchInfo) internal pure returns (bytes32) {
472:  function _checkBit(uint256 _bitMap, uint8 _index) internal pure returns (bool) {
477:  function _setBit(uint256 _bitMap, uint8 _index) internal pure returns (uint256) {

File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
54:   ) public view returns (bool) {
69:   ) external view returns (bool) {
89:   ) public view override returns (bool) {
128:  ) internal view returns (bool) {
149:  function _L2MessageToLog(L2Message memory _message) internal pure returns (L2Log memory) {
170:  ) public pure returns (uint256) {
179:  function _deriveL2GasPrice(uint256 _l1GasPrice, uint256 _gasPricePerPubdata) internal pure returns (uint256) {

File: code/contracts/ethereum/contracts/zksync/facets/Getters.sol
26: function getVerifier() external view returns (address) {
31: function getGovernor() external view returns (address) {
36: function getPendingGovernor() external view returns (address) {
41: function getTotalBatchesCommitted() external view returns (uint256) {
46: function getTotalBatchesVerified() external view returns (uint256) {
51: function getTotalBatchesExecuted() external view returns (uint256) {
56: function getTotalPriorityTxs() external view returns (uint256) {
63: function getFirstUnprocessedPriorityTx() external view returns (uint256) {
68: function getPriorityQueueSize() external view returns (uint256) {
73: function priorityQueueFrontOperation() external view returns (PriorityOperation memory) {
78: function isValidator(address _address) external view returns (bool) {
83: function l2LogsRootHash(uint256 _batchNumber) external view returns (bytes32) {
90: function storedBatchHash(uint256 _batchNumber) external view returns (bytes32) {
95: function getL2BootloaderBytecodeHash() external view returns (bytes32) {
100: function getL2DefaultAccountBytecodeHash() external view returns (bytes32) {
105: function getVerifierParams() external view returns (VerifierParams memory) {
110: function getProtocolVersion() external view returns (uint256) {
115: function getL2SystemContractsUpgradeTxHash() external view returns (bytes32) {
124: function getL2SystemContractsUpgradeBatchNumber() external view returns (uint256) {
129: function isDiamondStorageFrozen() external view returns (bool) {
148: function getPriorityTxMaxGasLimit() external view returns (uint256) {
153: function getAllowList() external view returns (address) {
158: function isFunctionFreezable(bytes4 _selector) external view returns (bool) {
167: function isEthWithdrawalFinalized(uint256 _l2BatchNumber, uint256 _l2MessageIndex) external view returns (bool) {
191: function facetFunctionSelectors(address _facet) external view returns (bytes4[] memory) {
197: function facetAddresses() external view returns (address[] memory) {
203: function facetAddress(bytes4 _selector) external view returns (address) {
214: function getTotalBlocksCommitted() external view returns (uint256) {
220: function getTotalBlocksVerified() external view returns (uint256) {
226: function getTotalBlocksExecuted() external view returns (uint256) {
234: function storedBlockHash(uint256 _batchNumber) external view returns (bytes32) {
244: function getL2SystemContractsUpgradeBlockNumber() external view returns (uint256) {

File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
function getCommittedBatchTimestamp(uint256 _l2BatchNumber) external view returns (uint256) {

File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol
55: function initialize(InitializeData calldata _initalizeData) external reentrancyGuardInitializer returns (bytes32) {

File: code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol
35: function getFirstUnprocessedPriorityTx(Queue storage _queue) internal view returns (uint256) {
40: function getTotalPriorityTxs(Queue storage _queue) internal view returns (uint256) {
45: function getSize(Queue storage _queue) internal view returns (uint256) {
50: function isEmpty(Queue storage _queue) internal view returns (bool) {
64: function front(Queue storage _queue) internal view returns (PriorityOperation memory) {

File:
22: ) internal pure returns (bytes32) {

```
GITHUB INSTANCES:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L22C5-L22C40
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L64C5-L64C92
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L50
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L45C5-L45C77
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L40C5-L40C89
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L35C5-L35C99
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L55C5-L55C5
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L71C5-L71C98
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L244C5-L244C88
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L234C5-L234C85
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L226C5-L226C72
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L220
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L214
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L197C5-L197C73
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L191C5-L191C94
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L167
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L158C5-L158C82
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L153C5-L153C62
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L148C5-L148C74
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L129C1-L130C1
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L124C4-L124C88
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L115C5-L115C83
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L110C5-L110C68
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L105C5-L105C81
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L100
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L95C5-L95C77
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L90
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L83C5-L83C84
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L78
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L73C5-L73C94
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L68
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L63
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L56
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L51
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L46C5-L46C73
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L41C5-L41C74
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L36C5-L36C68
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L31C5-L31C61
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L26
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L179C5-L179C115
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L170
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L149C5-L149C95
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L89C5-L89C44
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L69C5-L69C37
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L54C35-L54C35
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L477C5-L477C86
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L472
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L31
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L380C5-L380C40
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L417C3-L417C78
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L425C9-L425C26
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L434C1-L434C107
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L444C5-L444C75
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L128

### [GAS-8] | Use assembly and shift for typecasting operations
when we use assembly and shift operations for typecasting operations we tend to save 3+ in runtime gas than instead of using typecasting where we use 6+ in runtime gas per typecasting, we can simply do shift operation and arrive at our destination since all data types in solidity are bytes under the hood in solidity



*Instances (5)*:
```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
22: require(bytes4(functionSignature) == this.finalizeEthWithdrawal.selector, "is");
106: value: bytes32(uint256(_status))
346:  reserved: [_priorityOpParams.valueToMint, uint256(uint160(_priorityOpParams.refundRecipient)), 0, 0],
336: from: uint256(uint160(_priorityOpParams.sender)),
337: to: uint256(uint160(_priorityOpParams.contractAddressL2)),
       
```

GITHUB INSTANCES:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L337C13-L337C71
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L336
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L346
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L424
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L106

[GAS-9] When comparing address types typecast to the uint160 type first 
when comparing data types in solidity e.g `address(0) == address(0)` it's more gas saving to typecast to there uint256 type first

*Instances (5)*:
```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol


File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
248: if (sender != tx.origin) {

File: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
284: if (_init == address(0)) {

File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol
56: require(address(_initalizeData.verifier) != address(0), "vt");
57: require(_initalizeData.governor != address(0), "vy");
58: require(_initalizeData.admin != address(0), "hc");
```

GITHUB INSTANCES:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L58
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L57C9-L57C62
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L56
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L284
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L248


### [GAS-10] Use uint256 instead of bytes32 for bytes32 constant
typecast bytes32 to uint256 before storing them in storage just like how its done in solady, this saves us 7707 in deployment gas an exmaple can be found here in solady[ https://github.com/Vectorized/solady/blob/8c751db06e614e26e6acb9b385b463feef5e3a9f/src/tokens/ERC20.sol#L51], where uint256 was used to store bytes32 constant in storage, this saves us extra gas since solidity wont perform extra checks to determine if uint256 is va;id unlike bytes32

*Instances (5)*:
```solidity
File: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

17: bytes32 constant DIAMOND_INIT_SUCCESS_RETURN_VALUE =
    0x33774e659306e47509050e97cb651e731180a42d458212294d30751925c551a2; // 
    keccak256("diamond.zksync.init") - 1

21: bytes32 constant DIAMOND_STORAGE_POSITION = 
    0xc8fcad8db84d3cc18b4c41d551ea0ee66dd599cde068d998e57d5e09332c131b;

File: code/contracts/ethereum/contracts/zksync/Config.sol
6: bytes32 constant EMPTY_STRING_KECCAK = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470;
19: bytes32 constant L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH = 0x72abee45b59e344af8a6e520241c4744aff26ed411f4c4b00f8af09adada43ba;
36: bytes32 constant DEFAULT_L2_LOGS_TREE_ROOT_HASH = bytes32(0);

```

GITHUB INSTANCES:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Config.sol#L36
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Config.sol#L19
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Config.sol#L6
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L17
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L21

### [GAS-11] use solady instead of openzeppelin
instead of using openzeppelin we can use solady which is way cheaper and way gas efficient[https://github.com/Vectorized/solady]

*Instances (4)*:
```solidity
File: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

5:  import "@openzeppelin/contracts/utils/math/SafeCast.sol";

File: code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol

5: import "@openzeppelin/contracts/utils/math/Math.sol";

File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
5: import "@openzeppelin/contracts/access/Ownable2Step.sol";

File: code/contracts/ethereum/contracts/zksync/facets/Base.sol
9: import "@openzeppelin/contracts/access/Ownable.sol";

```

GITHUB INSTANCES:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L9
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L5
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L5
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L5

### [GAS-12] when the array is not mutated we should store it in the calladata instead of memory
store non mutated array in the calldata rather than memory

*Instances (1)*:
```solidity
File: code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol

48:  function validateUpgradeTransaction(IMailbox.L2CanonicalTransaction memory _transaction) internal pure {

```

GITHUB INSTANCES:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L48C5-L48C109

### [GAS-13] division or multiplication that involves the second digit as a power of two should be done either shift left or right 
the div and the mul opcode both cost 5 gas each, while the shl and shr opcode both cost 3 each when the second digit in a multiplication or division is a power of two we can simply or shr and shl hereby saving 2 in runtime gas fees

*Instances (5)*:
```solidity
File: code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol

21: uint256 mapValue = _map.map[_index / 8];
25: uint256 bitOffset = (_index & 7) * 32;
45: uint256 mapIndex = _index / 8;
50: uint256 bitOffset = (_index & 7) * 32;

File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol
87: assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

GITHUB INSTANCES:

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L87
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L50
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L45
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L21
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L25C13-L25C51
```

### [GAS-14]  possible offchain computation shoulf be computed offchain before being brought on chain
all possible offchain should be done offchain and not be brought onchain for computation
*Instances (1)*:
```solidity
File: code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol

File: code/contracts/ethereum/contracts/zksync/DiamondInit.sol
87: assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

GITHUB INSTANCES:

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L87
```

### [GAS-15] when incrementing by 1 simply do a pre-incremnt `++i`
A pre-increment is the cheapest form of increment it only cost 3 gas lass than all other type of increment
*Instances (2)*:
```solidity


File: code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol
80: _queue.head = head + 1
60: _queue.tail = tail + 1;

GITHUB INSTANCES:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L80
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L60C9-L60C32
```