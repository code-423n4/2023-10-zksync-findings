0. Executor::proveBatches() - L348-L369: why is `verify()` appearing twice and can be called TWICE, when it doesnt make sense?

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L348-L369

Summary:

The verify() function appears twice in the code segment provided and can be called twice depending on the conditional logic. This approach introduces redundancy and potentially inefficient code execution, which may not align with the purpose indicated by the commented-out #else and #endif statements.

Details:

Within the code segment, the use of verify() is conditional and can be called twice based on the provided condition. However, the presence of two identical verify() calls raises questions about the necessity of such duplication. Interestingly, the comments with #else and #endif hints suggest that the initial intent was to implement conditional execution but have since been commented out. This might have resulted in unintentional redundancy.

```solidity
        // #if DUMMY_VERIFIER

        // Additional level of protection for the mainnet
        assert(block.chainid != 1);
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
        bool successVerifyProof = s.verifier.verify(
            proofPublicInput,
            _proof.serializedProof,
            _proof.recursiveAggregationInput
        );
        require(successVerifyProof, "p"); // Proof verification fail
        // #endif
```

Recommendation:

Given the presence of the commented-out #else and #endif statements, it appears that there was a previous intention to conditionally skip the zero-knowledge proof (ZKP) verification. Revisiting the initial intent of the #else and #endif statements and aligning the code accordingly can lead to improved code maintainability and execution efficiency.

=======
=======

1. QA/LOW: The `_maxU256` function's vulnerability emerges when `a` and `b` are equal, which may result in unintended outcomes in scenarios necessitating a clear distinction between the two, such as in resource allocation and various other value comparison scenarios. 

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L416-L419

Summary:

For how this `_maxU256` function was used in the `revertBatches()` function in DummyExecutor.sol contract, that's OK, there it doesn't matter because if `a` equals `b` the same correct value is assigned to `newTotalBatchesCommitted`. 
And `_maxU256` function is not currently used anywhere in the Executor.sol contract. If the intention is to leave it there available to use at some point in the future, it needs to be ensured that this function cannot/should not be used for cases where resource allocation or similar scenarios are implemented.

See PoC for clarity below.

Proof of Concept:

Suppose we have a hypothetical Ethereum smart contract for allocating a limited supply of tokens to two users. The contract relies on the `_maxU256` function, which consistently selects `b` when `a` and `b` are equal. 

```solidity
contract ResourceAllocation {
    uint256 public resourceSupply = 1000;

    function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) {
        return a < b ? b : a;
    }

    function allocateTokens(address recipientA, address recipientB, uint256 amount) external {
        uint256 maxRecipient = _maxU256(getBalance(recipientA), getBalance(recipientB));
        require(maxRecipient >= amount, "Not enough tokens for allocation");

        // Allocate 'amount' tokens to the recipient with the larger balance.
        if (maxRecipient == getBalance(recipientA)) {
            // Allocate to recipientA
            transferTokens(recipientA, amount);
        } else {
            // Allocate to recipientB
            transferTokens(recipientB, amount);
        }
    }

    function getBalance(address account) external view returns (uint256) {
        // Assume the balance is fetched from another part of the contract.
        // In this PoC, both recipientA and recipientB have a balance of 5.
        return 5;
    }

    function transferTokens(address recipient, uint256 amount) internal {
        // Simulate the transfer of tokens.
    }
}
```

In this PoC, `recipientA` and `recipientB` both have a balance of 5 tokens, and the `allocateTokens` function is called to allocate 10 tokens. The `_maxU256` function consistently selects `recipientB` when `recipientA` and `recipientB` have equal balances. 

Vulnerability Explanation:

In the `allocateTokens` function, when `allocateTokens(addressA, addressB, 10)` is called with both `recipientA` and `recipientB` having a balance of 5 tokens, `_maxU256(5, 5)` consistently selects `recipientB`. Consequently, the allocation function assigns tokens to `recipientB` due to the behavior of `_maxU256`, which consistently selects `b` when `a` and `b` are equal. This may lead to unintended outcomes in situations where a clear and consistent distinction is required when both variables are equal.

To address this potential vulnerability, the `_maxU256` function should be modified to consider equality as a scenario where either `a` or `b` can be selected, or provide a clear guideline on how to handle equality cases in specific contexts.

=======
=======

2. QA: might be useful to add events here to indicate why the return bool was `false`...as it could be due to no access allowed, or caller dont have special access.

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L42-L43

=======
=======

3. QA: Several contracts in the audited codebase use a floating pragma statement, not recommended, due to potential incompatibilities and other unknowns that could be introduced by future versions.

```solidity
pragma solidity ^0.8.13;
```

Recommendation: 

It is not recommended to use a floating pragma statement in smart contracts for several reasons:

Compatibility Concerns: A floating pragma, as demonstrated in the provided code snippet, specifies the minimum required compiler version with a caret (^) symbol followed by a version number. While this may work with the current compiler version, it can lead to compatibility issues with future compiler updates. New compiler versions may introduce breaking changes or modify the behavior of certain functions, which could affect the functionality of the smart contracts.

Lack of Deterministic Behavior: Smart contracts are intended to operate in a deterministic manner, meaning they should produce the same results when executed on different nodes of the network. Using a floating pragma increases the risk of non-deterministic behavior because future compiler versions might generate different bytecode, leading to inconsistent contract behavior across different nodes.

Security Vulnerabilities: Upgrading to a new compiler version without thorough testing may introduce vulnerabilities and expose the smart contract to security risks. It is crucial to assess the impact of compiler updates and thoroughly test the smart contracts on the target compiler version before deployment.

Conclusion: To ensure the long-term security and stability of smart contracts, it is advisable to use a fixed pragma statement with an explicitly specified compiler version. For example, you can use:

```solidity
pragma solidity 0.8.13;
```

This approach provides predictability and minimizes the risk of unexpected issues due to compiler updates.

=======
=======

4. QA: seems this could be changed to `external` modifier as I didnt see any internal function calls, as well as would be in line with the interface IMailbox.

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L170

Summary:

However, it is observed that this function does not make any internal function calls within the contract and aligns with the interface IMailbox. Given these considerations, it could be more appropriate and in better alignment with the interface to change the public modifier to external. This modification would signal that the function is intended to be used as part of the contract's external interface.


```solidity
) public pure returns (uint256) {
```

Recommendation:

It is recommended to consider changing the public modifier to external for the function in question. This change aligns the function's modifier with its intended external interface, as specified in the interface IMailbox.

=======
=======

