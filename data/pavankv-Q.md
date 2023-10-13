## 1. Remove redundancy code :-
In below scenario comment says ` // If the proof is not empty, verify it, otherwise, skip the verification` but in reality even proof is zero it will verify because the code which there in if() statement to verify the same code is in outside the if() statement it will verify even the proof is zero

**Before**
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
        // #else //QA
        bool successVerifyProof = s.verifier.verify( //@audit redundancy code
            proofPublicInput,//@audit redundancy code
            _proof.serializedProof,//@audit redundancy code
            _proof.recursiveAggregationInput//@audit redundancy code
        );
        require(successVerifyProof, "p"); // Proof verification fail
        // #endif
```

**After**
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
        // #else //QA
       //@audit changed here . We removed the redundancy code to skip the verification if proof is empty .
        // #endif
```

Please see `//@audit changed here ` comment in above code snippet .

## 2 . Refactor the require statement :-

In below code snippet we can agree the first require statement that total committed batches should be greater than `newLastBatch` . In second require statement the comment says`// Already executed batches cannot be reverted` but the condition in second require statement anticipates even equal number to `s.totalBatchesExecuted` this may break the invariant it means already executed batches can also be reverted .

**Before**
```solidity
    function revertBatches(uint256 _newLastBatch) external nonReentrant onlyValidator {
        require(s.totalBatchesCommitted > _newLastBatch, "v1"); // The last committed batch is less than new last batch
        require(_newLastBatch >= s.totalBatchesExecuted, "v2"); //@audit // Already executed batches cannot be reverted
```


**After**
```solidity
function revertBatches(uint256 _newLastBatch) external nonReentrant onlyValidator {
        require(s.totalBatchesCommitted > _newLastBatch, "v1"); // The last committed batch is less than new last batch
        require(_newLastBatch > s.totalBatchesExecuted, "v2"); //@audit changed here // Already executed batches cannot be reverted

```

Please look into `//@audit changed here` comment in above comment .

## 3 . Constant name should be capitalized SNAKE_CASE

code snippet :-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22