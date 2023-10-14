# Variable s.totalBatchesExecuted can be queued to save on gas
```solidity
 function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {
    uint256 nBatches = _batchesData.length;
    for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
        _executeOneBatch(_batchesData[i], i);
        emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
    }

    s.totalBatchesExecuted = newTotalBatchesExecuted;

    ...
    rest of code
    }
}
```

```solidity
function _executeOneBatch(StoredBatchInfo memory _storedBatch, uint256 _executedBatchIdx) internal {
    uint256 currentBatchNumber = _storedBatch.batchNumber;
    require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k"); // Execute batches in order
    ....
}
```
s.totalBatchesExecuted can be queued and passed as a parameter to ```_executeOneBatch``` to avoid reading storage for every batch execution as the variable is only updated in storage after all batches have been executed.


# Unnecesary operation in ```SystemContext#setNewBatch```
Can save gas when setting the number parameter in the ```BlockInfo``` struct. Since we make sure that ```previousBatchNumber + 1 == _expectedNewNumber``` we can use ```_expectedNewNumber``` instead of ```previousBatchNumber + 1``` when setting the number parameter and save on gas.
```solidity
function setNewBatch(
        bytes32 _prevBatchHash,
        uint128 _newTimestamp,
        uint128 _expectedNewNumber,
        uint256 _baseFee
    ) external onlyCallFromBootloader {
        ...
        require(previousBatchNumber + 1 == _expectedNewNumber, "The provided block number is not correct");
        ...

        // Setting new block number and timestamp
        BlockInfo memory newBlockInfo = BlockInfo({number: previousBatchNumber + 1, timestamp: _newTimestamp});

        ...
    }

```
