### Gas 01 - In Executor.sol, `proveBatches()`will most likely verify proof twice which is a waste of gas.

**Instances(1)**

In Executor.sol - `proveBatches()`, an `if` bypass is added in `proveBatches()` to skip verification if `_proof.serializedProof.length == 0` for `testnet environmment`. This will result in `s.verifier.verify()` to run twice. 

```solidity
// code/contracts/ethereum/contracts/zksync/facets/Executor.sol

    function proveBatches(
        StoredBatchInfo calldata _prevBatch,
        StoredBatchInfo[] calldata _committedBatches,
        ProofInput calldata _proof
    ) external nonReentrant onlyValidator {
...

        // We allow skipping the zkp verification for the test(net) environment
        // If the proof is not empty, verify it, otherwise, skip the verification

|>     if (_proof.serializedProof.length > 0) {
            bool successVerifyProof = s.verifier.verify(
                proofPublicInput,
                _proof.serializedProof,
                _proof.recursiveAggregationInput
            );
            require(successVerifyProof, "p"); // Proof verification fail
        }
|>      bool successVerifyProof = s.verifier.verify(
            proofPublicInput,
            _proof.serializedProof,
            _proof.recursiveAggregationInput
        );
        require(successVerifyProof, "p"); // Proof verification fail
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L368)

As seen above, `s.verifier.verify()` will run twice whenever `_proof.serializedProof.length > 0`. 

Recommendations:
Remove the `if` statement.

### Gas 02 - Unused and unnecessary interface functions should be removed to save deployment gas

**Instances(1)**

DiamondProxy facet contract inherits IBase.sol interface contract, and IBase.sol only defines a getter `getName()` method which is never implemented or invoked. In DiamondProxy facet contracts, `getName` is already a constant variable defined prior to facet contract deployment which makes it unnecessary to implement a separate getter. In addition, if the intention is to implement a separate getter, then no getter is actually implemented currently.

It's better to implement this unused `getName()` from IBase.sol, and since IBase.sol only defines `getName()`, the entire contract can be eliminated to save deployment gas.

```solidity
// code/contracts/ethereum/contracts/zksync/interfaces/IBase.sol
interface IBase {
    function getName() external view returns (string memory);
}
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/interfaces/IBase.sol#L4-L6)

Recommendations:
Consider eliminating this interface if no intention to implement `getName()`.



