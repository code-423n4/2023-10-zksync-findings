## [G-01] - same computation is performed twice in the logic of proveBatches() in executor.sol 

in the logic of proveBatches() we have sucessfully verify proof twice by calling the `s.verifier.verify()` function twice, [(see here)](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354C1-L367C11)
This consumes lots of extra gas and is not neccessary to prove twice. Please take out the if statement code block and let it always call `s.verifier.verify()` no matter the length of `_proof.serializedProof.length`. 

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354C1-L367C11
```
        if (_proof.serializedProof.length > 0) {
            bool successVerifyProof = s.verifier.verify(
                proofPublicInput,
                _proof.serializedProof,
                _proof.recursiveAggregationInput
            );
            require(successVerifyProof, "p"); // Proof verification fail
        }
        // #else
        bool successVerifyProof = s.verifier.verify(
            proofPublicInput,
            _proof.serializedProof,
            _proof.recursiveAggregationInput
        );
```
