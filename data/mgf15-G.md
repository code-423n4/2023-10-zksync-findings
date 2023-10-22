|ID|Title|Category|Severity|Instances|
|-|:-|:-:|:-:|:-:|
| [[G-1](#g-1--Use-do-while-loops-instead-of-for-loops)] | Use do while loops instead of for loops | Gas Optimization | Informational | 10 |
| [[G-2](#g-2--dont-cache-length )] | don't cache .length  | Gas Optimization | Informational | 3 |
| [[G-3](#g-3--use-assembly-to-write-address-storage-values)] | use assembly to write address storage values  | Gas Optimization | Informational | 4 |
| [[G-4](#g-4--assembly-method-available)] | assembly method available  | Gas Optimization | Informational | 1 |

## **G-1** | Use do while loops instead of for loops 

A do while loop will cost less gas since the condition is not being checked for the first iteration. 

[Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L209-L217)
```solidity
        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
            _lastCommittedBatchData = _commitOneBatch(_lastCommittedBatchData, _newBatchesData[i], bytes32(0));

            s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData);
            emit BlockCommit(
                _lastCommittedBatchData.batchNumber,
                _lastCommittedBatchData.batchHash,
                _lastCommittedBatchData.commitment
            );
        }
```
```diff
diff --git a/Executor.sol b/aExecutor.sol
index 6e52004..d88033b 100644
--- a/Executor.sol
+++ b/aExecutor.sol
@@ -208,7 +208,9 @@ contract ExecutorFacet is Base, IExecutor {
         StoredBatchInfo memory _lastCommittedBatchData,
         CommitBatchInfo[] calldata _newBatchesData
     ) internal {
-        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
+        uint256 i;
+        do{
+        //for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
             _lastCommittedBatchData = _commitOneBatch(_lastCommittedBatchData, _newBatchesData[i], bytes32(0));
 
             s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData);
@@ -217,7 +219,10 @@ contract ExecutorFacet is Base, IExecutor {
                 _lastCommittedBatchData.batchHash,
                 _lastCommittedBatchData.commitment
             );
-        }
+            unchecked{
+                ++i;
+            }
+        }while(i<_newBatchesData.length);
     }
 
     /// @dev Commits new batches with a system contracts upgrade transaction.
@@ -239,8 +244,9 @@ contract ExecutorFacet is Base, IExecutor {
 
         // Save the batch number where the upgrade transaction was executed.
         s.l2SystemContractsUpgradeBatchNumber = _newBatchesData[0].batchNumber;
-
-        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
+        uint256 i;
+        do{
+        //for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
             // The upgrade transaction must only be included in the first batch.
             bytes32 expectedUpgradeTxHash = i == 0 ? _systemContractUpgradeTxHash : bytes32(0);
             _lastCommittedBatchData = _commitOneBatch(
@@ -255,17 +261,24 @@ contract ExecutorFacet is Base, IExecutor {
                 _lastCommittedBatchData.batchHash,
                 _lastCommittedBatchData.commitment
             );
-        }
+            unchecked{
+                ++i;
+            }
+        }while(i<_newBatchesData.length);
     }
 
     /// @dev Pops the priority operations from the priority queue and returns a rolling hash of operations
     function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {
         concatHash = EMPTY_STRING_KECCAK;
-
-        for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
+        uint256 i;
+        do{
+        //for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
             PriorityOperation memory priorityOp = s.priorityQueue.popFront();
             concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
-        }
+            unchecked{
+                ++i;
+            }
+        }while(i<_nPriorityOps);
     }
 
     /// @dev Executes one batch
@@ -292,10 +305,15 @@ contract ExecutorFacet is Base, IExecutor {
     /// @notice 2. Finalizes batch on Ethereum
     function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {
         uint256 nBatches = _batchesData.length;
-        for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
+        uint256 i;
+        do{
+        //for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
             _executeOneBatch(_batchesData[i], i);
             emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
-        }
+            unchecked{
+                ++i;
+            }
+        }while(i<nBatches);
 
         uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
         s.totalBatchesExecuted = newTotalBatchesExecuted;
@@ -329,7 +347,9 @@ contract ExecutorFacet is Base, IExecutor {
         require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");
 
         bytes32 prevBatchCommitment = _prevBatch.commitment;
-        for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
+        uint256 i;
+        do{
+        //for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
             currentTotalBatchesVerified = currentTotalBatchesVerified.uncheckedInc();
             require(
                 _hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],
@@ -344,7 +364,10 @@ contract ExecutorFacet is Base, IExecutor {
             );
 
             prevBatchCommitment = currentBatchCommitment;
-        }
+            unchecked{
+                ++i;
+            }
+        }while(i<committedBatchesLength);
         require(currentTotalBatchesVerified <= s.totalBatchesCommitted, "q");
 
 

```
gas report 

```diff
| cache/solpp-generated-contracts/zksync/facets/Executor.sol:ExecutorFacet contract |                 |       |        |       |         |
 |-----------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                   | Deployment Size |       |        |       |         |
-| 1509175                                                                           | 7570            |       |        |       |         |
+| 1521789                                                                           | 7633            |       |        |       |         |
 | Function Name                                                                     | min             | avg   | median | max   | # calls |
-| commitBatches                                                                     | 8378            | 37282 | 21734  | 73612 | 44      |
-| executeBatches                                                                    | 7957            | 19743 | 13597  | 39857 | 7       |
-| proveBatches                                                                      | 8065            | 28799 | 35104  | 45104 | 16      |
+| commitBatches                                                                     | 8378            | 37252 | 21709  | 73571 | 44      |
+| executeBatches                                                                    | 7957            | 13669 | 11967  | 23306 | 7       |
+| proveBatches                                                                      | 8065            | 28778 | 35080  | 45080 | 16      |
 | revertBatches                                                                     | 9790            | 16666 | 17983  | 19147 | 6       |

```

[Getters.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L182-L187)

```solidity
        for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
            address facetAddr = ds.facets[i];
            Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];


            result[i] = Facet(facetAddr, facetToSelectors.selectors);
        }
```
```diff
diff --git a/Getters.sol b/aGetters.sol
index 6480e91..fd2a9d9 100644
--- a/Getters.sol
+++ b/aGetters.sol
@@ -180,13 +180,17 @@ contract GettersFacet is Base, IGetters, ILegacyGetters {
 
         uint256 facetsLen = ds.facets.length;
         result = new Facet[](facetsLen);
-
-        for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
+        uint256 i;
+        do{
+        //for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
             address facetAddr = ds.facets[i];
             Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];
 
             result[i] = Facet(facetAddr, facetToSelectors.selectors);
-        }
+            unchecked{
+                ++i;
+            }
+        }while(i<facetsLen);
     }

```

```diff
 | cache/solpp-generated-contracts/zksync/facets/Getters.sol:GettersFacet contract |                 |      |        |      |         |
 |---------------------------------------------------------------------------------|-----------------|------|--------|------|---------|
 | Deployment Cost                                                                 | Deployment Size |      |        |      |         |
-| 678108                                                                          | 3419            |      |        |      |         |
+| 675108                                                                          | 3404            |      |        |      |         |
 | Function Name                                                                   | min             | avg  | median | max  | # calls |
 | facetAddress                                                                    | 625             | 625  | 625    | 625  | 29      |
 | getTotalBatchesCommitted                                                        | 370             | 370  | 370    | 370  | 1       |
```

[ValidatorTimelock.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L85-L87)

```solidity
            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
                committedBatchTimestamp.set(_newBatchesData[i].batchNumber, timestamp);
            }
```
```diff
diff --git a/ValidatorTimelock.sol b/aValidatorTimelock.sol
index 43921a6..13d4e85 100644
--- a/ValidatorTimelock.sol
+++ b/aValidatorTimelock.sol
@@ -80,14 +80,19 @@ contract ValidatorTimelock is IExecutor, Ownable2Step {
         StoredBatchInfo calldata,
         CommitBatchInfo[] calldata _newBatchesData
     ) external onlyValidator {
-        unchecked {
+        //unchecked {
             // This contract is only a temporary solution, that hopefully will be disabled until 2106 year, so...
             // It is safe to cast.
             uint32 timestamp = uint32(block.timestamp);
-            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
+            uint256 i;
+            do{
+            //for (uint256 i = 0; i < _newBatchesData.length; ++i) {
                 committedBatchTimestamp.set(_newBatchesData[i].batchNumber, timestamp);
-            }
-        }
+                unchecked{
+                    ++i;
+                }
+            }while(i<_newBatchesData.length);
+        //}
 
         _propagateToZkSync();
     }
@@ -114,8 +119,10 @@ contract ValidatorTimelock is IExecutor, Ownable2Step {
     /// make a call to the zkSync contract with the same calldata.
     function executeBatches(StoredBatchInfo[] calldata _newBatchesData) external onlyValidator {
         uint256 delay = executionDelay; // uint32
-        unchecked {
-            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
+        //unchecked {
+            uint256 i;
+            do{
+            //for (uint256 i = 0; i < _newBatchesData.length; ++i) {
                 uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);
 
                 // Note: if the `commitBatchTimestamp` is zero, that means either:
@@ -123,8 +130,11 @@ contract ValidatorTimelock is IExecutor, Ownable2Step {
                 // * The batch wasn't committed at all, so execution will fail in the zkSync contract.
                 // We allow executing such batches.
                 require(block.timestamp >= commitBatchTimestamp + delay, "5c"); // The delay is not passed
-            }
-        }
+                unchecked{
+                    ++i;
+                }
+            }while(i<_newBatchesData.length);
+        //}
 
         _propagateToZkSync();
     }

```
[Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L225-L233)
```solidity
        for (uint256 i = 0; i < _calls.length; ++i) {
            (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
            if (!success) {
                // Propage an error if the call fails.
                assembly {
                    revert(add(returnData, 0x20), mload(returnData))
                }
            }
        }
```
```diff
diff --git a/Governance.sol b/aGovernance.sol
index d3034a1..1562fc9 100644
--- a/Governance.sol
+++ b/aGovernance.sol
@@ -224,7 +224,9 @@ contract Governance is IGovernance, Ownable2Step {
     /// @dev Execute an operation's calls.
     /// @param _calls The array of calls to be executed.
     function _execute(Call[] calldata _calls) internal {
-        for (uint256 i = 0; i < _calls.length; ++i) {
+        uint256 i;
+        do{
+        //for (uint256 i = 0; i < _calls.length; ++i) {
             (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
             if (!success) {
                 // Propage an error if the call fails.
@@ -232,7 +234,10 @@ contract Governance is IGovernance, Ownable2Step {
                     revert(add(returnData, 0x20), mload(returnData))
                 }
             }
-        }
+            unchecked{
+                ++i;
+            }
+        }while(i<_calls.length);
     }

```
```diff
 | cache/solpp-generated-contracts/governance/Governance.sol:Governance contract |                 |       |        |       |         |
 |-------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                               | Deployment Size |       |        |       |         |
-| 1056727                                                                       | 5643            |       |        |       |         |
+| 1047113                                                                       | 5595            |       |        |       |         |
 | Function Name                                                                 | min             | avg   | median | max   | # calls |
 | cancel                                                                        | 1742            | 2620  | 1742   | 4915  | 7       |
-| execute                                                                       | 2330            | 8603  | 9978   | 27182 | 31      |
-| executeInstant                                                                | 2695            | 9031  | 9516   | 17150 | 9       |
+| execute                                                                       | 2330            | 8530  | 9871   | 27075 | 31      |
+| executeInstant                                                                | 2695            | 8939  | 9431   | 16936 | 9       |
 | getOperationState                                                             | 577             | 1084  | 602    | 2554  | 4       |
 | hashOperation                                                                 | 1568            | 1596  | 1568   | 1762  | 16      |
 | isOperation                                                                   | 689             | 1196  | 714    | 2666  | 4       |

```
[Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L395-L402)

```solidity
        for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
            bytes32 hashedBytecode = L2ContractHelper.hashL2Bytecode(_factoryDeps[i]);


            // Store the resulting hash sequentially in bytes.
            assembly {
                mstore(add(hashedFactoryDeps, mul(add(i, 1), 32)), hashedBytecode)
            }
        }
```
```diff
diff --git a/Mailbox.sol b/aMailbox.sol
index 8cabb8b..7990b4e 100644
--- a/Mailbox.sol
+++ b/aMailbox.sol
@@ -394,14 +394,19 @@ contract MailboxFacet is Base, IMailbox {
     {
         uint256 factoryDepsLen = _factoryDeps.length;
         hashedFactoryDeps = new uint256[](factoryDepsLen);
-        for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
+        uint256 i;
+        do{
+        //for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
             bytes32 hashedBytecode = L2ContractHelper.hashL2Bytecode(_factoryDeps[i]);
 
             // Store the resulting hash sequentially in bytes.
             assembly {
                 mstore(add(hashedFactoryDeps, mul(add(i, 1), 32)), hashedBytecode)
             }
-        }
+            unchecked{
+                ++i;
+            }
+        }while(i<factoryDepsLen);
     }
 
     /// @dev Decode the withdraw message that came from L2
@@ -429,3 +434,4 @@ contract MailboxFacet is Base, IMailbox {
         (amount, offset) = UnsafeBytes.readUint256(_message, offset);
     }
 }

```

```diff
 | cache/solpp-generated-contracts/zksync/facets/Mailbox.sol:MailboxFacet contract |                 |       |        |        |         |
 |---------------------------------------------------------------------------------|-----------------|-------|--------|--------|---------|
 | Deployment Cost                                                                 | Deployment Size |       |        |        |         |
-| 1673957                                                                         | 8393            |       |        |        |         |
+| 1670750                                                                         | 8377            |       |        |        |         |
 | Function Name                                                                   | min             | avg   | median | max    | # calls |
 | l2TransactionBaseCost                                                           | 912             | 912   | 912    | 912    | 1       |
-| requestL2Transaction                                                            | 74610           | 89501 | 91433  | 109208 | 41      |                                                              | 
+| requestL2Transaction                                                            | 7937            | 60437 | 103196 | 109150 | 11      |


```

## **G-2** | don't cache .length 
there is no need to cache .length and we can save some gas!

[Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L292)

```solditiy
        uint256 nBatches = _batchesData.length;
```

```diff
diff --git a/Executor.sol b/aExecutor.sol
index 6e52004..c2ea757 100644
--- a/Executor.sol
+++ b/aExecutor.sol
@@ -291,13 +291,13 @@ contract ExecutorFacet is Base, IExecutor {
     /// @notice 1. Processes all pending operations (Complete priority requests)
     /// @notice 2. Finalizes batch on Ethereum
     function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {
-        uint256 nBatches = _batchesData.length;
-        for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
+        //uint256 nBatches = _batchesData.length;
+        for (uint256 i = 0; i < _batchesData.length; i = i.uncheckedInc()) {
             _executeOneBatch(_batchesData[i], i);
             emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
         }
 
-        uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
+        uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + _batchesData.length;
         s.totalBatchesExecuted = newTotalBatchesExecuted;
         require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.
 
@@ -317,19 +317,19 @@ contract ExecutorFacet is Base, IExecutor {
     ) external nonReentrant onlyValidator {
         // Save the variables into the stack to save gas on reading them later
         uint256 currentTotalBatchesVerified = s.totalBatchesVerified;
-        uint256 committedBatchesLength = _committedBatches.length;
+        //uint256 committedBatchesLength = _committedBatches.length;
 
         // Save the variable from the storage to memory to save gas
         VerifierParams memory verifierParams = s.verifierParams;
 
         // Initialize the array, that will be used as public input to the ZKP
-        uint256[] memory proofPublicInput = new uint256[](committedBatchesLength);
+        uint256[] memory proofPublicInput = new uint256[]( _committedBatches.length);
 
         // Check that the batch passed by the validator is indeed the first unverified batch
         require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");
 
         bytes32 prevBatchCommitment = _prevBatch.commitment;
-        for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
+        for (uint256 i = 0; i <  _committedBatches.length; i = i.uncheckedInc()) {
             currentTotalBatchesVerified = currentTotalBatchesVerified.uncheckedInc();
             require(
                 _hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],

```
```diff 
 | cache/solpp-generated-contracts/zksync/facets/Executor.sol:ExecutorFacet contract |                 |       |        |       |         |
 |-----------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                                   | Deployment Size |       |        |       |         |
-| 1527796                                                                           | 7663            |       |        |       |         |
+| 1526196                                                                           | 7655            |       |        |       |         |
 | Function Name                                                                     | min             | avg   | median | max   | # calls |
 | commitBatches                                                                     | 8378            | 37282 | 21734  | 73612 | 44      |
-| executeBatches                                                                    | 7957            | 19808 | 13615  | 40064 | 7       |
-| proveBatches                                                                      | 8065            | 28813 | 35121  | 45121 | 16      |
+| executeBatches                                                                    | 7957            | 19805 | 13612  | 40059 | 7       |
+| proveBatches                                                                      | 8065            | 28799 | 35104  | 45104 | 16      |
 | revertBatches                                                                     | 9790            | 16666 | 17983  | 19147 | 6       |

```
[Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L393)
```solidity
        uint256 factoryDepsLen = _factoryDeps.length;
```
```diff
diff --git a/Mailbox.sol b/aMailbox.sol
index 8cabb8b..a1881b8 100644
--- a/Mailbox.sol
+++ b/aMailbox.sol
@@ -392,9 +392,9 @@ contract MailboxFacet is Base, IMailbox {
         pure
         returns (uint256[] memory hashedFactoryDeps)
     {
-        uint256 factoryDepsLen = _factoryDeps.length;
-        hashedFactoryDeps = new uint256[](factoryDepsLen);
-        for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
+        //uint256 factoryDepsLen = _factoryDeps.length;
+        hashedFactoryDeps = new uint256[](_factoryDeps.length);
+        for (uint256 i = 0; i < _factoryDeps.length; i = i.uncheckedInc()) {
             bytes32 hashedBytecode = L2ContractHelper.hashL2Bytecode(_factoryDeps[i]);

```
```diff
| cache/solpp-generated-contracts/zksync/facets/Mailbox.sol:MailboxFacet contract |                 |       |        |        |         |
 |---------------------------------------------------------------------------------|-----------------|-------|--------|--------|---------|
 | Deployment Cost                                                                 | Deployment Size |       |        |        |         |
-| 1673957                                                                         | 8393            |       |        |        |         |
+| 1673557                                                                         | 8391            |       |        |        |         |
 | Function Name                                                                   | min             | avg   | median | max    | # calls |
 | l2TransactionBaseCost                                                           | 912             | 912   | 912    | 912    | 1       |
-| requestL2Transaction                                                            | 74610           | 89501 | 91433  | 109208 | 41      |
+| requestL2Transaction                                                            | 74605           | 89496 | 91428  | 109203 | 41      |

```

## **G-3** | use assembly to write address storage values 

By using assembly, we can directly interact with the storage slot of the storedAddress variable, allowing us to efficiently write and read address values in storage.


[Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L46)

```solidity
        securityCouncil = _securityCouncil;
```
```diff
diff --git a/Governance.sol b/aGovernance.sol
index d3034a1..b6dc2ec 100644
--- a/Governance.sol
+++ b/aGovernance.sol
@@ -44,10 +44,14 @@ contract Governance is IGovernance, Ownable2Step {
         require(_admin != address(0), "Admin should be non zero address");
 
         _transferOwnership(_admin);
-
-        securityCouncil = _securityCouncil;
+        assembly{
+            sstore(securityCouncil.slot,_securityCouncil)
+        }
+        //securityCouncil = _securityCouncil;
         emit ChangeSecurityCouncil(address(0), _securityCouncil);
-
+        assembly{
+            sstore(minDelay.slot,_minDelay)
+        }
         minDelay = _minDelay;
         emit ChangeMinDelay(0, _minDelay);
     }
@@ -250,14 +254,20 @@ contract Governance is IGovernance, Ownable2Step {
     /// @param _newDelay The new minimum delay time (in seconds) for future operations.
     function updateDelay(uint256 _newDelay) external onlySelf {
         emit ChangeMinDelay(minDelay, _newDelay);
-        minDelay = _newDelay;
+        assembly{
+            sstore(minDelay.slot,_newDelay)
+        }
+        //minDelay = _newDelay;
     }
 
     /// @dev Updates the address of the security council.
     /// @param _newSecurityCouncil The address of the new security council.
     function updateSecurityCouncil(address _newSecurityCouncil) external onlySelf {
         emit ChangeSecurityCouncil(securityCouncil, _newSecurityCouncil);
-        securityCouncil = _newSecurityCouncil;
+        assembly{
+            sstore(securityCouncil.slot,_newSecurityCouncil)
+        }
+        //securityCouncil = _newSecurityCouncil;
     }
 

```
```diff
 | cache/solpp-generated-contracts/governance/Governance.sol:Governance contract |                 |       |        |       |         |
 |-------------------------------------------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                                                               | Deployment Size |       |        |       |         |
-| 1056727                                                                       | 5643            |       |        |       |         |
+| 1050878                                                                       | 5596            |       |        |       |         |
 | Function Name                                                                 | min             | avg   | median | max   | # calls |
 | cancel                                                                        | 1742            | 2620  | 1742   | 4915  | 7       |
-| execute                                                                       | 2330            | 8603  | 9978   | 27182 | 31      |
+| execute                                                                       | 2330            | 8598  | 9978   | 27182 | 31      |
 | executeInstant                                                                | 2695            | 9031  | 9516   | 17150 | 9       |
 | getOperationState                                                             | 577             | 1084  | 602    | 2554  | 4       |
 | hashOperation                                                                 | 1568            | 1596  | 1568   | 1762  | 16      |
@@ -126,7 +126,7 @@
 | scheduleTransparent                                                           | 2311            | 29450 | 33429  | 33977 | 35      |
 | securityCouncil                                                               | 383             | 1383  | 1383   | 2383  | 8       |
 | updateDelay                                                                   | 433             | 5756  | 433    | 21727 | 4       |
-| updateSecurityCouncil                                                         | 557             | 1653  | 557    | 4941  | 4       |
+| updateSecurityCouncil                                                         | 557             | 1611  | 557    | 4775  | 4       |

```

## **G-4** | assembly method available 

we can efficiently interact with storage slots and retrieve information such as address storage values and bytecode lengths. Assembly provides a low-level approach to optimize certain operations and gain more control over the contract's execution.

[Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L312)

```solditiy
        if (refundRecipient.code.length > 0) {
```
```diff
diff --git a/Mailbox.sol b/aMailbox.sol
index 8cabb8b..ca48f55 100644
--- a/Mailbox.sol
+++ b/aMailbox.sol
@@ -311,8 +311,15 @@ contract MailboxFacet is Base, IMailbox {
         // If the `_refundRecipient` is not provided, we use the `_sender` as the recipient.
         address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient;
         // If the `_refundRecipient` is a smart contract, we apply the L1 to L2 alias to prevent foot guns.
-        if (refundRecipient.code.length > 0) {
+        {
+            uint256 _codesize;
+            assembly{
+                _codesize := extcodesize(refundRecipient)
+            }
+
+        if (_codesize > 0) {
             refundRecipient = AddressAliasHelper.applyL1ToL2Alias(refundRecipient);
+            }
         }
 
         params.sender = _sender;
@@ -429,3 +436,4 @@ contract MailboxFacet is Base, IMailbox {
         (amount, offset) = UnsafeBytes.readUint256(_message, offset);
     }
 }
```
```diff
 | cache/solpp-generated-contracts/zksync/facets/Mailbox.sol:MailboxFacet contract |                 |       |        |        |         |
 |---------------------------------------------------------------------------------|-----------------|-------|--------|--------|---------|
 | Deployment Cost                                                                 | Deployment Size |       |        |        |         |
-| 1673957                                                                         | 8393            |       |        |        |         |
+| 1673157                                                                         | 8389            |       |        |        |         |
 | Function Name                                                                   | min             | avg   | median | max    | # calls |
 | l2TransactionBaseCost                                                           | 912             | 912   | 912    | 912    | 1       |
-| requestL2Transaction                                                            | 74610           | 89501 | 91433  | 109208 | 41      |
+| requestL2Transaction                                                            | 74605           | 89496 | 91420  | 109195 | 41      |
```
