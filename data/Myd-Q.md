## Impact

No events for critical operations like batch commits, executions, etc. Makes monitoring and debugging difficult.

## Proof of Concept

There are no events emitted for critical operations like batch commits and executions.

Some examples:

`commitBatches` function https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L177-L200

```solidity
    function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)
        external
        override
        nonReentrant
        onlyValidator
    {
// no events emitted  

```

Similarly, `executeBatches` on https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L291-L296

```solidity
 function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {
   // no events emitted
 } 
```
This makes it difficult to monitor key operations happening in the contract. 


Critical operations like batch commits and executions are opaque to off-chain monitoring.

## Recommended Mitigation Steps

I would recommend emitting events from these functions on batch commits and executions.

For example:

```solidity
event BatchCommitted(
  uint256 indexed batchNumber,
  bytes32 indexed batchHash
);

event BatchExecuted(
  uint256 indexed batchNumber, 
  bytes32 indexed batchHash
);

function commitBatches() {
  ...

  emit BatchCommitted(batchNumber, batchHash);
}

function executeBatches() {
  ...
  
  emit BatchExecuted(batchNumber, batchHash);
}
```