### Report 1:
Wrong event function was used in setPendingAdmin(...) function in Admin.sol, it should be emit NewPendingAdmin(...) not emit NewPendingGovernor(...)
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L49
```solidity
  function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {
        // Save previous value into the stack to put it into the event later
        address oldPendingAdmin = s.pendingAdmin;
        // Change pending admin
        s.pendingAdmin = _newPendingAdmin;
---     emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);
+++     emit NewPendingAdmin(oldPendingAdmin, _newPendingAdmin);
    }
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L45
```solidity
    /// @notice pendingAdmin is changed
    /// @dev Also emitted when new admin is accepted and in this case, `newPendingAdmin` would be zero address
    event NewPendingAdmin(address indexed oldPendingAdmin, address indexed newPendingAdmin);
```
As noted in the code above this is the correct event meant for the emit purpose in setPendingAdmin(...) function as extracted from the IAdmin interface.
### Report 2:
## Title
Missing Validation for l2LogsTreeRoot before commitment in the Executor contract
## Impact
l2LogsTreeRoot is a critical variable important to the commitment and subsequent execution in the Executor contract, not having an important validation allows wrong data approval which opens the contract to errors and manipulations.
## Proof of Concept
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L41-L64
```solidity
function _commitOneBatch(
        ...
    ) internal view returns (StoredBatchInfo memory) {
    ...
   (
            uint256 expectedNumberOfLayer1Txs,
            bytes32 expectedPriorityOperationsHash,
            bytes32 previousBatchHash,
            bytes32 stateDiffHash,
>>>         bytes32 l2LogsTreeRoot,
            uint256 packedBatchAndL2BlockTimestamp
        ) = _processL2Logs(_newBatch, _expectedSystemContractUpgradeTxHash);

        require(_previousBatch.batchHash == previousBatchHash, "l");
        // Check that the priority operation hash in the L2 logs is as expected
        require(expectedPriorityOperationsHash == _newBatch.priorityOperationsHash, "t");
        // Check that the number of processed priority operations is as expected
        require(expectedNumberOfLayer1Txs == _newBatch.numberOfLayer1Txs, "ta");

        // Check the timestamp of the new batch
        _verifyBatchTimestamp(packedBatchAndL2BlockTimestamp, _newBatch.timestamp, _previousBatch.timestamp);

        // Create batch commitment for the proof verification
        bytes32 commitment = _createBatchCommitment(_newBatch, stateDiffHash);

        return
            StoredBatchInfo(
                _newBatch.batchNumber,
                _newBatch.newStateRoot,
                _newBatch.indexRepeatedStorageChanges,
                _newBatch.numberOfLayer1Txs,
                _newBatch.priorityOperationsHash,
>>>             l2LogsTreeRoot,
                _newBatch.timestamp,
                commitment
            );
    }
```
The _commitOneBatch(...) function above shows how l2 logs are processed by calling "_processL2Logs(...)" function, the problem is necessary validation was done for every variable results from the _processL2Logs(...) function except "l2LogsTreeRoot" before it storage into "StoredBatchInfo". It is important to know that every of this result variable are important as confirmed by sponsor.
`Sponsor: "This are critical checks, Please note, we are checking that logs that were submitted from L2 are the same as public input"`
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L136
```solidity
 function _processL2Logs(CommitBatchInfo calldata _newBatch, bytes32 _expectedSystemContractUpgradeTxHash)
        internal
        pure
        returns (
            uint256 numberOfLayer1Txs,
            bytes32 chainedPriorityTxsHash,
            bytes32 previousBatchHash,
            bytes32 stateDiffHash,
            bytes32 l2LogsTreeRoot,
            uint256 packedBatchAndL2BlockTimestamp
        )
{
      ...
             if (logKey == uint256(SystemLogKey.L2_TO_L1_LOGS_TREE_ROOT_KEY)) {
                require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lm");
 >>>            l2LogsTreeRoot = logValue;
      ...
```
As can also be confirmed from the code above inside the _processL2Logs(...) function, no validation to ensuer logValue assigned to "l2LogsTreeRoot"  is as expected
## Tools Used
Manual Review
## Recommended Mitigation Steps
Necessary validation should be done for l2LogsTreeRoot to ensure it is as expected from the log value before commitment.
### Report 3:
Repetition of validation as against comment Description
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L352-L369
```solidity
       // We allow skipping the zkp verification for the test(net) environment
        // If the proof is not empty, verify it, otherwise, skip the verification
        if (_proof.serializedProof.length > 0) {
            bool successVerifyProof = s.verifier.verify(
                proofPublicInput,
                _proof.serializedProof,
                _proof.recursiveAggregationInput
            );
            require(successVerifyProof, "p"); // Proof verification fail
        }
        // #else
---      bool successVerifyProof = s.verifier.verify(
---         proofPublicInput,
---         _proof.serializedProof,
---         _proof.recursiveAggregationInput
---     );
---     require(successVerifyProof, "p"); // Proof verification fail
        // #endif
```
The comment in the code above clearly state that "If the proof is not empty, verify it, otherwise, skip the verification", but verify(..) was called again outside the if condition, this will cause
 1. Overlapping declaration of bool successVerifyProof when the if condition holds true
 2. Excess usage of gas due to double function call
 3. Unnecessary verification when what is to be verified is empty i.e when _proof.serializedProof.length == 0.
The bool successVerifyProof outside the if condition should be removed.