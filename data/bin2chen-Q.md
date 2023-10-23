## Findings Summary

| Label | Description |
| - | - |
| [L-01](#l-01-publishpubdataandclearstate-spelling-mistake) | publishPubdataAndClearState() spelling mistake|
| [L-02](#l-02-gettersstoredbatchhash-may-return-wrong-hash) | Getters.storedBatchHash() may return wrong hash|
| [L-03](#l-03-freezediamond-and-unfreezediamond-permissions-should-be-swapped) | FreezeDiamond() and unfreezeDiamond() permissions should be swapped.|



## [L-01] publishPubdataAndClearState() spelling mistake
in `publishPubdataAndClearState()`，need to check `numberOfL2ToL1Logs`<= L2_TO_L1_LOGS_MERKLE_TREE_LEAVES
However, the current use of `require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");`
Compare yourself

suggest:
```diff
    function publishPubdataAndClearState(
        bytes calldata _totalL2ToL1PubdataAndStateDiffs
    ) external onlyCallFromBootloader {
        uint256 calldataPtr = 0;

        /// Check logs
        uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
-       require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
+       require(numberOfL2ToL1Logs <= L2_TO_L1_LOGS_MERKLE_TREE_LEAVES, "Too many L2->L1 logs");
        calldataPtr += 4;
```

## [L-02] Getters.storedBatchHash() may return wrong hash
Executing `Executor.commitBatches()` sets `s.storedBatchHashes[_batchNumber]=hash`.
but `revertBatches()` does not clear the already set values.
The current implementation of Getters.storedBatchHash() is as follows.
```solidity
    function storedBatchHash(uint256 _batchNumber) external view returns (bytes32) {
        return s.storedBatchHashes[_batchNumber];
    }
```

This results in a situation where
Assuming current_batchNumber = 100 so `s.storedBatchHashes[100] = hash
which is then 99 due to `revertBatches(99)`.
At that point s.storedBatchHashes[100] was still there and not cleared, but the value should have been invalidated
However, `Getters.storedBatchHash(100) ` will still return the old invalid value.
Suggestion
```diff
    function storedBatchHash(uint256 _batchNumber) external view returns (bytes32) {
+     if (_batchNumber<s.totalBatchesCommitted) return;
        return s.storedBatchHashes[_batchNumber];
    }

```

## [L-03] FreezeDiamond() and unfreezeDiamond() permissions should be swapped.
Currently `freezeDiamond()` requires `onlyGovernor`, and `unfreezeDiamond()` requires `onlyGovernorOrAdmin`.

But normally, unfreeze should have higher permissions

Usually there is a security issue that requires freezing, such as `admin` being held hostage.
To unfreeze, you need to make sure that the system is already secure, which should require higher privileges to avoid `admin` being taken hostage and then unfreezing itself.

```diff
-   function freezeDiamond() external onlyGovernor {
+   function freezeDiamond() external onlyGovernorOrAdmin {
        Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();

        require(!diamondStorage.isFrozen, "a9"); // diamond proxy is frozen already
        diamondStorage.isFrozen = true;

        emit Freeze();
    }

-   function unfreezeDiamond() external onlyGovernorOrAdmin {
+   function unfreezeDiamond() external onlyGovernor {
        Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();

        require(diamondStorage.isFrozen, "a7"); // diamond proxy is not frozen
        diamondStorage.isFrozen = false;

        emit Unfreeze();
    }
```


