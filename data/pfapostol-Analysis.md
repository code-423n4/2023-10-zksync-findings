## Introduction

This audit is conducted with a cognizant understanding of the limitations imposed by the available audit time, which proved to be insufficient for a comprehensive examination of the entire codebase, amounting to 31,029 Source Lines of Code (SLOC). To optimize the auditing process, a strategic decision was made to prioritize the assessment of L1 contracts and their intricate interactions with their corresponding L2 counterparts. This focused approach allows for a meticulous examination of critical components within the given constraints, ensuring a thorough evaluation of the specified smart contract system's key elements.

This analysis report aims to provide a comprehensive overview of the smart contract system, specifically focusing on the L1 contracts: `ExecutorFacet`, `MailboxFacet`, `AdminFacet`, `Diamond`, `TransactionValidator`, `ValidatorTimelock`, `L1ERC20Bridge`, and the communication patterns between `L1ERC20Bridge` and `L2ERC20Bridge`. 

## Approach taken in evaluating the codebase

Commencing the assessment, I initiated a thorough examination of the available documentation. Subsequently, I delineated the specific areas that warranted in-depth scrutiny during the audit, focusing particularly on L1 contracts and the intricacies of the L1 to L2 bridges.

Upon a comprehensive review of the code encompassing all facets and the diamond proxy architecture, I directed my attention towards a meticulous examination of the L1 ERC20 and L1 WETH bridges.

An integral aspect of this process involved the compilation of a comprehensive glossary, documenting terms that were either unfamiliar or not entirely elucidated. Subsequently, I embarked on an extensive study of these terms, utilizing both official documentation and external sources, including Google, to augment my understanding.

Following the attainment of a holistic understanding of L1 contracts, I proceeded to the identification of potential risks and the exploration of quality assurance (QA) and gas-related issues.

### Tools Employed
Throughout the audit, I refrained from utilizing specific programs. However, for the purpose of report compilation, I employed chatGPT with the prompt: "Rewrite the segments of the web3 audit report provided, imbuing them with a more professional style and adhering to grammatical precision." (Because my English writing skills are not as good as reading and listening.)

## Test Coverage

The prioritization of specific contracts during the audit was driven by a strategic focus on areas exhibiting lower test coverage percentages. This deliberate choice was guided by several considerations aimed at enhancing the overall robustness and reliability of the codebase. 

Certain contracts, such as `cache/solpp-generated-contracts/bridge/L1ERC20Bridge.sol` and `cache/solpp-generated-contracts/zksync/facets/Admin.sol`, exhibited lower coverage percentages. These contracts are integral components of the system, and addressing coverage gaps in these critical areas was essential to mitigate risks and ensure the robustness of the entire codebase.

| File                                                                                      | % Lines           | % Statements      | % Branches       | % Funcs          |
|-------------------------------------------------------------------------------------------|-------------------|-------------------|------------------|------------------|
| cache/solpp-generated-contracts/bridge/L1ERC20Bridge.sol                                  | 33.33% (22/66)    | 38.20% (34/89)    | 9.38% (3/32)     | 45.45% (5/11)    |
| cache/solpp-generated-contracts/bridge/L1WethBridge.sol                                   | 75.00% (36/48)    | 72.41% (42/58)    | 67.86% (19/28)   | 85.71% (6/7)     |
| cache/solpp-generated-contracts/bridge/libraries/BridgeInitializationHelper.sol           | 0.00% (0/3)       | 0.00% (0/4)       | 100.00% (0/0)    | 0.00% (0/1)      |
| cache/solpp-generated-contracts/common/AllowList.sol                                      | 100.00% (26/26)   | 100.00% (31/31)   | 75.00% (9/12)    | 100.00% (9/9)    |
| cache/solpp-generated-contracts/common/ReentrancyGuard.sol                                | 0.00% (0/3)       | 0.00% (0/3)       | 0.00% (0/2)      | 0.00% (0/1)      |
| cache/solpp-generated-contracts/common/libraries/L2ContractHelper.sol                     | 50.00% (7/14)     | 42.11% (8/19)     | 30.00% (3/10)    | 25.00% (1/4)     |
| cache/solpp-generated-contracts/common/libraries/UncheckedMath.sol                        | 0.00% (0/2)       | 0.00% (0/4)       | 100.00% (0/0)    | 0.00% (0/2)      |
| cache/solpp-generated-contracts/common/libraries/UnsafeBytes.sol                          | 100.00% (8/8)     | 100.00% (8/8)     | 100.00% (0/0)    | 100.00% (4/4)    |
| cache/solpp-generated-contracts/zksync/DiamondInit.sol                                    | 0.00% (0/17)      | 0.00% (0/18)      | 0.00% (0/10)     | 0.00% (0/1)      |
| cache/solpp-generated-contracts/zksync/DiamondProxy.sol                                   | 100.00% (6/6)     | 100.00% (7/7)     | 50.00% (3/6)     | 100.00% (1/1)    |
| cache/solpp-generated-contracts/zksync/ValidatorTimelock.sol                              | 0.00% (0/18)      | 0.00% (0/25)      | 0.00% (0/2)      | 0.00% (0/8)      |
| cache/solpp-generated-contracts/zksync/Verifier.sol                                       | 0.00% (0/3)       | 0.00% (0/3)       | 100.00% (0/0)    | 0.00% (0/3)      |
| cache/solpp-generated-contracts/zksync/facets/Admin.sol                                   | 21.05% (8/38)     | 25.00% (10/40)    | 30.00% (3/10)    | 30.00% (3/10)    |
| cache/solpp-generated-contracts/zksync/facets/Executor.sol                                | 87.50% (119/136)  | 87.10% (162/186)  | 76.00% (76/100)  | 90.00% (18/20)   |
| cache/solpp-generated-contracts/zksync/facets/Getters.sol                                 | 22.00% (11/50)    | 21.54% (14/65)    | 25.00% (1/4)     | 20.59% (7/34)    |
| cache/solpp-generated-contracts/zksync/facets/Mailbox.sol                                 | 58.67% (44/75)    | 58.82% (60/102)   | 25.00% (7/28)    | 50.00% (8/16)    |
| cache/solpp-generated-contracts/zksync/libraries/Diamond.sol                              | 98.85% (86/87)    | 99.07% (106/107)  | 79.55% (35/44)   | 100.00% (10/10)  |
| cache/solpp-generated-contracts/zksync/libraries/LibMap.sol                               | 0.00% (0/9)       | 0.00% (0/14)      | 100.00% (0/0)    | 0.00% (0/2)      |
| cache/solpp-generated-contracts/zksync/libraries/Merkle.sol                               | 0.00% (0/10)      | 0.00% (0/12)      | 0.00% (0/6)      | 0.00% (0/2)      |
| cache/solpp-generated-contracts/zksync/libraries/PriorityQueue.sol                        | 0.00% (0/14)      | 0.00% (0/16)      | 0.00% (0/4)      | 0.00% (0/7)      |
| cache/solpp-generated-contracts/zksync/libraries/TransactionValidator.sol                 | 71.05% (27/38)    | 77.08% (37/48)    | 13.33% (4/30)    | 80.00% (4/5)     |

## Code Review

My primary emphasis during the analysis centered on the examination of L1 contracts and the L1->L2 bridges within the codebase. Understanding the intricacies of these components is crucial for ensuring the seamless operation and security of the entire system.

To illustrate the deposit transaction process from L1 to L2, the following diagram provides a visual representation:

**Note**: To view the image more closely, open it in a new tab as GitHub supports zooming.

![2023-10-zksync-Bridge ERC20](https://user-images.githubusercontent.com/50257230/277279823-3b1ef980-e282-4800-bf9b-9186bd200d32.jpg)

Then transaction is written in the Priority Queue; Understanding the mechanics of the Priority Queue is vital for comprehending the transaction processing workflow. The following diagram provides a concise representation:

![2023-10-zksync-PriorityQueue](https://user-images.githubusercontent.com/50257230/277280668-9e5712c1-49c1-4f43-a487-2fca9b0ecafa.jpg)

<details>
  <summary>Long details of reviewed functions</summary>

# AdminFacet

Public functions:

1. `setPendingGovernor`: - 
    1. State Diff:
        1. `s.pendingGovernor` update with input
    2. `onlyGovernor`
    3. set `s.pendingGovernor`
2. `acceptGovernor`
    1. State Diff:
        1. `s.governor` update with pending
        2. delete `s.pendingGovernor`
    2. Accept pending Governor
3. `setPendingAdmin`
    1. State Diff:
        1. `s.pendingAdmin` update with input
    2. `onlyGovernorOrAdmin`
    3. set `s.pendingAdmin`
4. `acceptAdmin`
    1. State Diff:
        1. `s.admin` update with pending
        2. delete `s.pendingAdmin`
5. `setValidator`
    1. State Diff:
        1. `s.validators[_validator]`
    2. `onlyGovernorOrAdmin`
6. `setPorterAvailability`
    1. State Diff:
        1. `s.zkPorterIsAvailable`
    2. `onlyGovernor`
7. `setPriorityTxMaxGasLimit`
    1. State Diff:
        1. `s.priorityTxMaxGasLimit`
    2. `onlyGovernor`
8. `executeUpgrade`: 
    1. `onlyGovernor`
    2. call `Diamond.diamondCut`
9. `freezeDiamond`:
    1. State Diff:
        1. `diamondStorage.isFrozen` true
    2. `onlyGovernor`
10. `unfreezeDiamond`:
    1. State Diff;
        1. `diamondStorage.isFrozen` false
    2. `onlyGovernorOrAdmin`


# ExecutorFacet

External functions:

1. `commitBatches`: - 
    1. State Diff:
        1. If upgrade is not planned:
            1. `s.storedBatchHashes[_lastCommittedBatchData.batchNumber]` updated with hash
        2. else:
            1. `s.l2SystemContractsUpgradeBatchNumber` updated with batch number
            2. `s.storedBatchHashes[_lastCommittedBatchData.batchNumber]` updated with hash
        3. `s.totalBatchesCommitted` incremented by number of new added batches
    2. `nonReentrant`
    3. `onlyValidator`
    4. Flow:
        1. Verify that `totalBatchesCommitted` point to the last data provided as input
        2. Verify new data is not empty
        3. If Upgrade is not need to be done and has not happened yet:
            1. call `_commitBatchesWithoutSystemContractsUpgrade`
        4. else:
            1. call `_commitBatchesWithSystemContractsUpgrade`
        5. Update `totalBatchesCommitted` with length of new batches
2. `executeBatches`: - 
    1. State Diff:
        1. `s.priorityQueue` poped for every executed batch
        2. `s.l2LogsRootHashes[currentBatchNumber]` updated with stored log tree hash
        3. `s.totalBatchesExecuted` incremented with amount executed
        4. If upgrade happened:
            1. delete `s.l2SystemContractsUpgradeTxHash`
            2. delete `s.l2SystemContractsUpgradeBatchNumber`
    2. `nonReentrant`
    3. `onlyValidator`
    4. Flow:
        1. For each batch:
            1. call `_executeOneBatch`
            2. emit `BlockExecution`
        2. set `totalBatchesExecuted` incremented by number of executed batches
        3. Verify that we execute no more that was *committed* and proven
        4. If update happened in this batch delete `l2SystemContractsUpgradeTxHash` and `l2SystemContractsUpgradeBatchNumber`
3. `proveBatches`: -
    1. State Diff:
        1. `s.totalBatchesVerified` incremented by number of newly verified batches
    2. `nonReentrant`
    3. `onlyValidator`
    4. Flow:
        1. Verify that `_prevBatch` indeed previous verified batch
        2. For each batch to commit:
            1. verify that it is next committed batch
            2. call `_getBatchProofPublicInput`
        3. Verify that we not proved more than committed
        4. IF testnet allow to skip proof
        5. Call `verifier.verify`
        6. Revert if not verified
        7. Emit `BlocksVerification`
        8. Update `totalBatchesVerified`
4. `revertBatches`: - 
    1. State Diff:
        1. If revert happened in verified batches:
            1. `s.totalBatchesVerified` update with batch to revert
        2. `s.totalBatchesCommitted` update with batch to revert
        3. If upgrade happened in batch after revert batch:
            1. delete `s.l2SystemContractsUpgradeBatchNumber`
    2. `nonReentrant`
    3. `onlyValidator`
    4. Flow:
        1. Verify that batch to start revert from is less than last committed, but more than last executed
        2. Update `totalBatchesVerified` if batch to revert is verified
        3. Update `totalBatchesCommitted`
        4. If upgrade happened after batch  to revert from delete `l2SystemContractsUpgradeBatchNumber`
        5. `BlocksRevert`

Internal function :

1. `_commitOneBatch`: -
    1. Verify that next batch has the next number
    2. call `_processL2Logs`
    3. Verify `previousBatchHash`
    4. Verify `expectedPriorityOperationsHash`
    5. Verify `expectedNumberOfLayer1Txs`
    6. call `_verifyBatchTimestamp`
    7. call `_createBatchCommitment`
    8. return `StoredBatchInfo` with created commitment
2. `_verifyBatchTimestamp`: - Verifies correctness of *the new batch and the new L2 block timestamps*
3. `_processL2Logs`: - 
    1. For each of `systemLogs`:
        1. Read `logSender`, `logKey`, `logValue`
        2. Verify, that `logKey` was not *processed* before
        3. Read log to specific variable or validate condition
        4. Verify that all logs was processed
4. `_commitBatchesWithoutSystemContractsUpgrade`: - 
    1. For each of new batch data:
        1. call `_commitOneBatch`
        2. Get `_hashStoredBatchInfo`
        3. Set `_hashStoredBatchInfo` to `storedBatchHashes`[`_lastCommittedBatchData.batchNumber`]
        4. emit `BlockCommit`
5. `_commitBatchesWithSystemContractsUpgrade`: -
    1. Set `l2SystemContractsUpgradeBatchNumber` - number of the first batch where upgrade happened 
    2. For each of new batch data:
        1. call `_commitOneBatch`
        2. Get `_hashStoredBatchInfo`
        3. Set `_hashStoredBatchInfo` to `storedBatchHashes`[`_lastCommittedBatchData.batchNumber`]
        4. emit `BlockCommit`
6. `_collectOperationsFromPriorityQueue`: 
    1. Remove `priorityOp` from queue
    2. Return hash of concat of empty string hash and `canonicalTxHash`
7. `_executeOneBatch`: 
    1. Verify order of execution
    2. Verify that hash is committed
    3. get `_collectOperationsFromPriorityQueue`
    4. Verify `priorityOperationsHash` match expected
    5. Set `l2LogsRootHashes` from batch to correspondent index
8. `_getBatchProofPublicInput`:
    1. Encode Packed: `_prevBatchCommitment`, `_currentBatchCommitment`, `recursionNodeLevelVkHash`, `recursionLeafLevelVkHash`
    2. get ***keccak256 hash of encoded data with trimmed first 4 bytes***
9. `_maxU256`
10. `_createBatchCommitment`: - 
    1. Get `_batchPassThroughData`
    2. Get `_batchMetaParameters`
    3. Get `_batchAuxiliaryOutput`
    4. Returns keccak256 of encoded a-c data
11. `_batchPassThroughData`: - Encode packed `indexRepeatedStorageChanges`,`newStateRoot`, 0 ,0
12. `_batchMetaParameters`: - Encode packed `zkPorterIsAvailable`,`l2BootloaderBytecodeHash`,`l2DefaultAccountBytecodeHash`
13. `_batchAuxiliaryOutput`: - 
    1. Get ***keccak256 hash of `systemLogs`***
    2. Encode `l2ToL1LogsHash`, `_stateDiffHash`,`bootloaderHeapInitialContentsHash`,`eventsQueueStateHash`
14. `_hashStoredBatchInfo`: - `keccak256` of encoded struct
15. `_checkBit`: - check if *bit at index is 1*
16. `_setBit`: - set bit at index to 1


# MailboxFacet

Public functions:

1. `proveL2MessageInclusion`: - 
    1. State Diff - None
    2. call `_L2MessageToLog`
    3. call `_proveL2LogInclusion`
2. `proveL2LogInclusion`: - 
    1. State Diff - None
    2. call `_proveL2LogInclusion`
3. `proveL1ToL2TransactionStatus`: 
    1. State Diff - None
    2. Build `L2Log` struct
    3. call `_proveL2LogInclusion`
4. `l2TransactionBaseCost`: -
    1. State Diff - None
    2. Get `_deriveL2GasPrice`
    3. return a. * `_l2GasLimit`
5. `finalizeEthWithdrawal`: - 
    1. State Diff:
        1. `s.isEthWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex]` set to `true`
        2. Native ETH transferred  from `this` to `_l1WithdrawReceiver`
    2. `nonReentrant` 
    3. `senderCanCallFunction(s.allowList)`
    4. Flow:
        1. Verify that Eth withdrawal is not finalized for this batch and message
        2. Build `L2Message` struct
        3. call `_parseL2WithdrawalMessage`
        4. call `proveL2MessageInclusion`
        5. Verify proof
        6. Set `isEthWithdrawalFinalized`
        7. call `_withdrawFunds`
        8. emit `EthWithdrawalFinalized`
6. `requestL2Transaction`: -
    1. State Diff:
        1. If `depositLimitation` is set to `true`:
            1. `s.totalDepositedAmountPerUser[_depositor]` incremented by amount
        2. `s.priorityQueue` - new operation added
    2. `nonReentrant` 
    3. `senderCanCallFunction(s.allowList)`
    4. Flow:
        1. If `msg.sender` ≠ `msg.origin` call `AddressAliasHelper.applyL1ToL2Alias` (apply offset)
        2. Call `_verifyDepositLimit`
        3. call `_requestL2Transaction`

Internal functions:

1. `_withdrawFunds`: - Low level call with amount
2. `_proveL2LogInclusion`: - 
    1. Verify that provided batch number was not executed
    2. Get hash of log - keccak256 of encode packed
    3. Verify that it is not default empty hash
    4. call `calculateRoot`
    5. verify that calculated root match actual one
3. `_L2MessageToLog`: - convert `L2Message` to `L2Log`
4. `_deriveL2GasPrice`: - calculate gas price using prepocessed constants and user input
5. `_verifyDepositLimit`: - 
    1. call `IAllowList(s.allowList).getTokenDepositLimitData(address(*0*))`
    2. if no deposit limitation return
    3. else Verify that deposit cap will not to be overflowed
    4. increment `totalDepositedAmountPerUser`
6. `_requestL2Transaction`: - 
    1. Verify that factory depth is lower than constant
    2. calculate timestamp of expiration
    3. Get total number of added transactions
    4. Check that `msg.value` cover gas cost and `l2value`
    5. If refund recipient is not provided use sender
    6. if code length call `AddressAliasHelper.applyL1ToL2Alias`
    7. Build `WritePriorityOpParams` and call `_writePriorityOp`
7. `_serializeL2Transaction`: - 
    1. Build `L2CanonicalTransaction` struct 
8. `_writePriorityOp`: - 
    1. call `_serializeL2Transaction`
    2. Encode transaction
    3. call `TransactionValidator.validateL1ToL2Transaction`
    4. push back to priority queue
    5. emit `NewPriorityRequest`
9. `_hashFactoryDeps`: -
    1. hash writing version in the zero byte and length in 2,3 bytes
10. `_parseL2WithdrawalMessage`: - 
    1. Verify that message is long enough
    2. Read `fsig`
    3. Verify that selector is `finalizeEthWithdrawal`
    4. Read `l1Receiver` and `amount`

# Diamond

1. `getDiamondStorage`: - storage ref
2. `diamondCut`: - main entry point
    1. For every facet cut:
        1. match action:
            1. Add - call `_addFunctions`
            2. Replace - call `_replaceFunctions`
            3. Remove - call `_removeFunctions`
    2. call `_initializeDiamondCut`
    3. emit `DiamondCut`
3. `_addFunctions`:
    1. Verify facet is not zero address
    2. call `_saveFacetIfNew`
    3. For each selector:
        1. Verify that selector not already added
        2. call `_addOneFunction`
4. `_replaceFunctions`:
    1. Verify that it is not zero address facet
    2. For each selector:
        1. Verify that old facet is existing one
        2. call `_removeOneFunction`
        3. call `_saveFacetIfNew`
        4. call `_addOneFunction`
5. `_removeFunctions`:
    1. for every selector call:
        1. `_removeOneFunction`
6. `_saveFacetIfNew`:
    1. If selectors length for this facet is zero
        1. update: `ds.facetToSelectors[_facet].facetPosition`
        2. push `ds.facets`
7. `_addOneFunction`:
    1. Get selector position
    2. if not zero:
        1. verify that *freezability* match others selectors
    3. Update `ds.selectorToFacet[_selector]`
    4. Push `ds.facetToSelectors[_facet].selectors`
8. `_removeOneFunction`:
    1. `ds.facetToSelectors[_facet].selectors` pop with reordering
    2. **delete** `ds.selectorToFacet[_selector]`
    3. if selectors is empty:
        1. call `_removeFacet`
9. `_removeFacet`:
    1. `ds.facets` pop with reordering
10. `_initializeDiamondCut`:
    1. if `init` address is zero:
        1. Verify that calldata is also zero
    2. else:
        1. Perform delegate call
        2. Verify success of call
        3. Verify that return is magic value for cut initializer

# L1ERC20Bridge

Public:

1. `initialize`: -
    1. State Diff:
        1. Update `l2TokenProxyBytecodeHash`
        2. Update `l2TokenBeacon`
    2. `reentrancyGuardInitializer`
    3. Verify that l2 token beacon and governor is not zero
    4. Verify that there are exactly 3 factory dependencies
    5. Verify that deploy gas sum up to `msg.value`
    6. Set `l2TokenProxyBytecodeHash` to `hashL2Bytecode` of `_factoryDeps[*2*]`
    7. Set `l2TokenBeacon`
    8. Get Bridge Hash and Bridge Proxy hash
    9. Request Deploy transaction
    10. 
2. `deposit(933999fb)`: - the same as  `e8b99b1b` but `_refundRecipient` is address zero
3. `deposit(e8b99b1b)`: -
    1. Data transformation to build deposit transaction:
    
    ![2023-10-zksync-Transaction.jpg](L1ERC20Bridge%20b7e8229092894b3ba2d03c7996f2900f/2023-10-zksync-Transaction.jpg)
    
    1. State Diff:
        1. transfer ERC20 tokens
        2. Change `totalDepositedAmountPerUser`
        3. `depositAmount[**msg.sender**][_l1Token][l2TxHash]`
    2. `nonReentrant` 
    3. `senderCanCallFunction(allowList)`
    4. Flow:
        1. Verify that amount is not zero
        2. call `_depositFunds`
        3. Verify that token has standard transfer logic
        4. call `_verifyDepositLimit`
        5. call `_getDepositL2Calldata`
        6. Normalize recipient
        7. call `zkSync.requestL2Transaction`
        8. Update `depositAmount[**msg.sender**][_l1Token][l2TxHash]`
4. `claimFailedDeposit`: -
    1. `nonReentrant`
    2. `senderCanCallFunction(allowList)`
    3. call `zkSync.proveL1ToL2TransactionStatus`
    4. TODO: Need to know a lot
5. `finalizeWithdrawal`:
    1. TODO:
6. `l2TokenAddress`: -
    1. Take l1 token address 
    2. call `L2ContractHelper.computeCreate2Address`

Internal:

1. `_depositFunds`: - 
    1. Transfer from sender to contract 
    2. return deference in contract balance
2. `_getDepositL2Calldata`: -
    1. call `_getERC20Getters`
    2. get tx calldata by encoding `IL2Bridge.finalizeDeposit` and parameters
3. `_getERC20Getters`: -
    1. get name, symbol and decimals
4. `_parseL2WithdrawalMessage`
5. `_verifyDepositLimit`: - 
    1. Check deposit limitation
    2. if claiming reduce from `totalDepositedAmountPerUser`
    3. else increase `totalDepositedAmountPerUser`

</details>

## Mechanism review

### Diamond Framework
The Diamond framework uses a smart contract design called EIP-2535 diamond proxy. This design is flexible and allows different parts of the system to be upgraded independently. It's like having multiple pieces that can be upgraded without affecting the whole system.

### Parts of the Diamond
- Administrative Facet: Manages important addresses and system settings.
- Message Relay (Mailbox) Facet: Handles communication between L1 and L2, making sure everything gets to where it needs to be.
- Executor Facet: Takes care of L2 batches, making sure they're valid and can be executed.

### Bridging Assets
Apart from the Diamond framework, there's a system for moving assets between L1 and L2. It's like a bridge connecting two islands.

#### ERC20 Bridge
- `L1ERC20Bridge`: Deals with regular ERC20 tokens, managing their movement between L1 and L2.
- `L2ERC20Bridge`: The L2 counterpart, handling the other side of ERC20 token movement.

#### WETH Bridge
- `L1WethBridge`: Specialized in handling WETH token transfers between L1 and L2 for a smoother experience.
- `L2WethBridge`: The L2 version, ensuring WETH can move seamlessly between the two layers.

### Others:

#### Validator Timelock
This acts as a time delay mechanism, giving validators a window to review and approve batch transactions before they're executed. It's like a safety lock to prevent hasty decisions.

### L1→L2 Transactions:

Two types of transactions can be initiated on L1:
- Priority Operations: These operations can be created by any user.
- Upgrade Transactions: These transactions are exclusive to system upgrades.

#### Initiation
To initiate a priority operation, the `requestL2Transaction` method on L1 is called. This method performs checks to ensure the transaction is processable and provides sufficient fees. The transaction is then appended to the priority queue.

#### Bootloader
When an operator identifies a priority operation, it can include the transaction in the batch. For L1→L2 transactions, there is no signature verification, so the bootloader maintains two variables: numberOfPriorityTransactions and priorityOperationsRollingHash. These variables help ensure that only valid transactions are included.

At the end of the batch, two L2→L1 log system logs are submitted with these values.

## Codebase quality analysis

Overall, the codebase is crafted with a high-level approach. However, the scrutiny has unearthed certain deficiencies, encompassing inaccurate comments, improper utilization of NatSpec tags, and various minor issues elucidated in the Quality Assurance (QA) report. These aspects warrant attention for a more precise and polished implementation.

#### Comments for the judge to contextualize your findings

I would like to reiterate that the Quality Assurance (QA) report exclusively covers L1 contracts. This focused scope is deliberate, intended to prevent the inundation of tasks with substantial complexity.

## Centralization risks

It's pertinent to note that conventional centralization concerns often associated with patterns like "onlyOwner" are not directly applicable to ZkSync. ZkSync stands out as a distinct Ethereum chain designed with a primary focus on decentralized principles and privacy enhancement through the strategic application of zero-knowledge proofs.

In the ZkSync ecosystem, validators play a crucial role by having the capability to approve transactions. Their involvement is pivotal for the system's operation. Additionally, the administration retains the authority to initiate upgrade transactions. However, it's essential to underscore that such upgrades are contingent on the consensus achieved through a multisignature process.

This multisignature requirement ensures that no single entity, even within the administration, can unilaterally implement upgrades. It acts as a robust safeguard against undue concentration of power, aligning with the overarching ethos of decentralization that ZkSync prioritizes. Therefore, from the standpoint of centralization, the implemented measures and governance structure contribute to the project's resilience and integrity.

## New insights and learning from this audit

Being relatively new to the realm of Layer 2 (L2) solutions, my introduction to ZkSync marked an exploration into uncharted territory. The intricacies of ZkSync's functionality, coupled with the novel concept of zero-knowledge proofs, presented a steep learning curve for me. My focus encompassed delving into the initiation of transactions within the Ethereum layer, understanding the role of validators in validating transaction legitimacy, and unraveling the process of aggregating transactions into batches on Layer 1 (L1). This learning journey provided valuable insights into the underlying mechanisms of ZkSync and broadened my understanding of transactional workflows in the Ethereum ecosystem.

### Time spent:
74 hours