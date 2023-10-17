G1. There is no need to check the second condition ``s.l2SystemContractsUpgradeBatchNumber != 0`` since it will be checked inside _commitBatchesWithSystemContractsUpgrade() anyway. 

[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L189](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L189)

G2. _processL2Logs() calls _checkBit() and then _setBit() to process each log. One can implement _checkAndsetBit() to combine both to save gas since we do not need to calculate mask twice and also use only one function call.

```javascipt
   function _checkAndSetBit(uint256 _bitMap, uint8 _index) internal pure returns (bool) {
        return (_bitMap & (1 << _index)) > 0;
    }

    /// @notice Sets the given bit in {_num} at index {_index} to 1.
    function _checkAndSetBit(uint256 _bitMap, uint8 _index) internal pure returns (uint256) {
        uint256 mask = 1 << _index;

        require(_bitMap & mask == 0, "bp");

        return _bitMap | mask; 
    }

G3. exchanging L87 and L93 can save gas due to short-circuiting since lastL2BlockTimestamp might not need to be calculated when the require statement fails.

```diff
 function _verifyBatchTimestamp(
        uint256 _packedBatchAndL2BlockTimestamp,
        uint256 _expectedBatchTimestamp,
        uint256 _previousBatchTimestamp
    ) internal view {
        // Check that the timestamp that came from the system context is expected
        uint256 batchTimestamp = _packedBatchAndL2BlockTimestamp >> 128;
        require(batchTimestamp == _expectedBatchTimestamp, "tb");

        // While the fact that _previousBatchTimestamp < batchTimestamp is already checked on L2,
        // we double check it here for clarity
        require(_previousBatchTimestamp < batchTimestamp, "h3");

-        uint256 lastL2BlockTimestamp = _packedBatchAndL2BlockTimestamp & PACKED_L2_BLOCK_TIMESTAMP_MASK;

        // All L2 blocks have timestamps within the range of [batchTimestamp, lastL2BatchTimestamp].
        // So here we need to only double check that:
        // - The timestamp of the batch is not too small.
        // - The timestamp of the last L2 block is not too big.
        require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small

+        uint256 lastL2BlockTimestamp = _packedBatchAndL2BlockTimestamp & PACKED_L2_BLOCK_TIMESTAMP_MASK;


        require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big
    }

```

G4.  _commitBatchesWithSystemContractsUpgrade() requires the upgrade transaction must only be included in the first batch. As a result, the for loop always check to see whether it is the first batch. This is a waste of gas. We should separate this case out of the for loop to save gas.

```diff
 function _commitBatchesWithSystemContractsUpgrade(
        StoredBatchInfo memory _lastCommittedBatchData,
        CommitBatchInfo[] calldata _newBatchesData,
        bytes32 _systemContractUpgradeTxHash
    ) internal {
        // The system contract upgrade is designed to be executed atomically with the new bootloader, a default account,
        // ZKP verifier, and other system parameters. Hence, we ensure that the upgrade transaction is
        // carried out within the first batch committed after the upgrade.

        // While the logic of the contract ensures that the s.l2SystemContractsUpgradeBatchNumber is 0 when this function is called,
        // this check is added just in case. Since it is a hot read, it does not encure noticable gas cost.
        require(s.l2SystemContractsUpgradeBatchNumber == 0, "ik");

        // Save the batch number where the upgrade transaction was executed.
        s.l2SystemContractsUpgradeBatchNumber = _newBatchesData[0].batchNumber;
+       _lastCommittedBatchData = _commitOneBatch(
+                _lastCommittedBatchData,
+                _newBatchesData[i],
+                _systemContractUpgradeTxHash
+            );
+           s.storedBatchHashes[_lastCommittedBatchData.batchNumber] =              _hashStoredBatchInfo(_lastCommittedBatchData);
+       emit BlockCommit(
+                _lastCommittedBatchData.batchNumber,
+                _lastCommittedBatchData.batchHash,
+                _lastCommittedBatchData.commitment
+            );

-        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
+        for (uint256 i = 1; i < _newBatchesData.length; i = i.uncheckedInc()) {

            // The upgrade transaction must only be included in the first batch.
-            bytes32 expectedUpgradeTxHash = i == 0 ? _systemContractUpgradeTxHash : bytes32(0);
            _lastCommittedBatchData = _commitOneBatch(
                _lastCommittedBatchData,
                _newBatchesData[i],
-                expectedUpgradeTxHash
+                bytes32(0)
            );

            s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData);
            emit BlockCommit(
                _lastCommittedBatchData.batchNumber,
                _lastCommittedBatchData.batchHash,
                _lastCommittedBatchData.commitment
            );
        }
    }

    /// @dev Pop

```