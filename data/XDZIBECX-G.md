## isssue N°1 :
in this line https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L187C7-L200C6
we have this 
```solidity
if (systemContractsUpgradeTxHash == bytes32(0) || s.l2SystemContractsUpgradeBatchNumber != 0) {
    _commitBatchesWithoutSystemContractsUpgrade(_lastCommittedBatchData, _newBatchesData);
} else {
    _commitBatchesWithSystemContractsUpgrade(
        _lastCommittedBatchData,
        _newBatchesData,
        systemContractsUpgradeTxHash
    );
}

s.totalBatchesCommitted = s.totalBatchesCommitted + _newBatchesData.length;
```
so  both _commitBatchesWithoutSystemContractsUpgrade and _commitBatchesWithSystemContractsUpgrade can loop over large arrays of _newBatchesData. and Depending on the size of these arrays, these functions lead to exceed the block gas limit, leading to transaction failures or denial of service.
as an example let's say :
    - Each batch processing consumes 5,000 gas.
    - The Ethereum block gas limit is 10,000,000 gas.
- Now, let's consider two transactions targeting the issue:
- Transaction 1:
    - Includes an array of 1,000 new batches.
    - Each batch processing consumes 5,000 gas.
- Transaction 2:
    - Includes an array of 2,000 new batches.
    - Each batch processing consumes 5,000 gas.
- Transaction 1 would consume 5,000 gas per batch * 1,000 batches = 5,000,000 gas.
- Transaction 2 would consume 5,000 gas per batch * 2,000 batches = 10,000,000 gas.
- Transaction 1 would be processed successfully within the block gas limit because it consumes 5,000,000 gas, which is below the limit of 10,000,000 gas.
- when Transaction 2 is processed, it would consume 10,000,000 gas, reaching the block gas limit.
- As a result:
    - Transaction 2 would fail to complete successfully due to exceeding the block gas limit.
    - Transaction 1 completed successfully or could be pending, but Transaction 2's gas consumption disrupts the normal contract operation.