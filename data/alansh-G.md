https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L142

Here `batchOverheadForTransaction` is never assigned before, so it can be simplified as `batchOverheadForTransaction = txSlotOverhead;`.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L367

Here `s.verifier.verify` is called twice, L363-L368 should be deleted.