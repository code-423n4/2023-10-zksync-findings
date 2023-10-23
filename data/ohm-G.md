# Optimising gas in the for loops by local variables
 
loops can be optimized in several ways.lets take a look at the `executeBatches` function in the `Executor.sol` contract .
``` solidity
    function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {
        uint256 nBatches = _batchesData.length;
        for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
            _executeOneBatch(_batchesData[i], i);
            emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
        }

        uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
        s.totalBatchesExecuted = newTotalBatchesExecuted;
        //@audit --> checking the require statemnent before assigning the value can save gas if it reverts.
        require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.

        uint256 batchWhenUpgradeHappened = s.l2SystemContractsUpgradeBatchNumber;
        if (batchWhenUpgradeHappened != 0 && batchWhenUpgradeHappened <= newTotalBatchesExecuted) {
            delete s.l2SystemContractsUpgradeTxHash;
            delete s.l2SystemContractsUpgradeBatchNumber;
        }
    }
```
To optimize this loop and make it consume less gas, we can do the following things:
1. Save the `batchesData[i]` in a local variable instead of accessing it 4 times in every                iteration.This will save accessing the array's ith element 4 times in every iteration,which consumes an address calculation.
2. check the require statement before assigning the value can save gas. Because if it revert statement reverts there is no need to assign of value to the `s.totalBatchesExecuted` and it waste of gas.
3. There is no need to initialize i to its default value,it will be done automatically and it will consume more gas if it will done. There are many instances of  this default initialization.

So after applying all these changes the the loop will somethig like this:
```
    function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {
        uint256 nBatches = _batchesData.length;
	StoreBatchesInfo memory dBatch;
        for (uint256 i ; i < nBatches; i = i.uncheckedInc()) {
	    dBatches = _batchesData[i]
            _executeOneBatch(dBatch, i);
            emit BlockExecution(dBatch.batchNumber, dBatch.batchHash, dBatch.commitment);
        }

        uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
        require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches 	more than committed and proven currently.
        s.totalBatchesExecuted = newTotalBatchesExecuted;

        uint256 batchWhenUpgradeHappened = s.l2SystemContractsUpgradeBatchNumber;
        if (batchWhenUpgradeHappened != 0 && batchWhenUpgradeHappened <= newTotalBatchesExecuted) {
            delete s.l2SystemContractsUpgradeTxHash;
            delete s.l2SystemContractsUpgradeBatchNumber;
        }
    }
```
Code links: https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L291-L307
   