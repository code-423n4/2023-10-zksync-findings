Can remove some redundant code because In case _proof.serializedProof.length > 0 then the verification run twice this cost quite a lot of gas.  
[Executor](https://github.com/code-423n4/2023-10-zksync/blob/7ed3944429f437a611c32e782a12b320f6a67c17/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L368)
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
        require(successVerifyProof, "p"); // Proof verification fail
```

Change to 
```
    if (_proof.serializedProof.length > 0) {
            bool successVerifyProof = s.verifier.verify(
                proofPublicInput,
                _proof.serializedProof,
                _proof.recursiveAggregationInput
            );
            require(successVerifyProof, "p"); // Proof verification fail
        }
        
```