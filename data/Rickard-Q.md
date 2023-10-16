# [L-01] Floating pragma
## Lines of code
All 39 contracts.
## Impact
Contracts should be deployed with the same compiler version and flags that they have been tested thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.       
[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103).      
Floating Pragma example:
````solidity
code/contracts/ethereum/contracts/zksync/facets/Executor.sol
1  // SPDX-License-Identifier: MIT
2  
3: pragma solidity ^0.8.13;
4
````
## Tools Used
Manual Review
## Recommended Mitigation Steps
Lock the pragma version and also consider known bugs ([https://github.com/ethereum/solidity/releases](https://github.com/ethereum/solidity/releases)) for the compiler version that is chosen.
# [L-02] Improved Function Parameter Naming
## Lines of code
[L1WethBridge.sol#L211-L224](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L211-L224)
## Impact
The lack of specific parameter names and detailed documentation in the `claimFailedDeposit` function could lead to potential misunderstandings, making the code less readable and maintainable.
## Proof of Concept
````solidity
211    /// @notice Withdraw funds from the initiated deposit, that failed when finalizing on L2.
212    /// Note: Refund is performed by sending an equivalent amount of ETH on L2 to the specified deposit refund
213    /// recipient address.
214    function claimFailedDeposit(
215        address, // _depositSender,
216        address, // _l1Token,
217        bytes32, // _l2TxHash
218        uint256, // _l2BatchNumber,
219        uint256, // _l2MessageIndex,
220        uint16, // _l2TxNumberInBatch,
221        bytes32[] calldata // _merkleProof
222    ) external pure {
223        revert("Method not supported. Failed deposit funds are sent to the L2 refund recipient address.");
224:   }
````
[L1WethBridge.sol#L211-L224](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L211-L224)
## Tools Used
Manual Review
## Recommended Mitigation Steps
To improve the clarity and maintainability of the code, it is strongly recommended to provide specific parameter names and detailed function documentation as shown in the proof of concept above. This will make it easier for developers to understand the function's intent and usage, reducing the likelihood of errors and facilitating smoother code maintenance in the future.
````solidity
/// @notice Withdraw funds from the initiated deposit, that failed when finalizing on L2.
/// @dev Refund is performed by sending an equivalent amount of ETH on L2 to the specified deposit refund recipient address.
/// @param _depositSender The address of the sender who initiated the deposit.
/// @param _l1Token The address of the token on Layer 1.
/// @param _l2TxHash The hash of the transaction on Layer 2.
/// @param _l2BatchNumber The batch number on Layer 2.
/// @param _l2MessageIndex The index of the message on Layer 2.
/// @param _l2TxNumberInBatch The transaction number within the batch on Layer 2.
/// @param _merkleProof The Merkle proof for the transaction inclusion.
function claimFailedDeposit(
    address _depositSender,
    address _l1Token,
    bytes32 _l2TxHash,
    uint256 _l2BatchNumber,
    uint256 _l2MessageIndex,
    uint16 _l2TxNumberInBatch,
    bytes32[] calldata _merkleProof
) external pure {
    revert("Method not supported. Failed deposit funds are sent to the L2 refund recipient address.");
}
````
# [L-03] Removal of deprecated functions
## Lines of code
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L212-L216](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L212-L216)
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L218-L222](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L218-L222)
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L224-L228](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L224-L228)
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L230-L236](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L230-L236)
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L238-L246](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L238-L246)
## Impact
These functions have been deprecated and replaced with updated ones:
- `getTotalBlocksCommitted` -> `getTotalBatchesCommitted`
- `getTotalBlocksVerified` -> `getTotalBatchesVerified`
- `getTotalBlocksExecuted` -> `getTotalBatchesExecuted`
- `storedBlockHash` -> `storedBatchHash`
- `getL2SystemContractsUpgradeBlockNumber` -> `getL2SystemContractsUpgradeBatchNumber`


However, the mentioned methods are public in in `Getters.sol` and no longer provide accurate outputs, this may potentially confuse users.
## Proof of Concept
````solidity
212        /// @return The total number of batches that were committed
213:       /// @dev It is a *deprecated* method, please use `getTotalBatchesCommitted` instead
214        function getTotalBlocksCommitted() external view returns (uint256) {
215            return s.totalBatchesCommitted;
216        }
217    
218        /// @return The total number of batches that were committed & verified
219:       /// @dev It is a *deprecated* method, please use `getTotalBatchesVerified` instead.
220        function getTotalBlocksVerified() external view returns (uint256) {
221            return s.totalBatchesVerified;
222        }
223    
224        /// @return The total number of batches that were committed & verified & executed
225:       /// @dev It is a *deprecated* method, please use `getTotalBatchesExecuted` instead.
226        function getTotalBlocksExecuted() external view returns (uint256) {
227            return s.totalBatchesExecuted;
228        }
229    
230        /// @notice For unfinalized (non executed) batches may change
231:       /// @dev It is a *deprecated* method, please use `storedBatchHash` instead.
232        /// @dev returns zero for non-committed batches
233        /// @return The hash of committed L2 batch.
234        function storedBlockHash(uint256 _batchNumber) external view returns (bytes32) {
235            return s.storedBatchHashes[_batchNumber];
236        }
237    
238        /// @return The L2 batch number in which the upgrade transaction was processed.
239:       /// @dev It is a *deprecated* method, please use `getL2SystemContractsUpgradeBatchNumber` instead.
240        /// @dev It is equal to 0 in the following two cases:
241        /// - No upgrade transaction has ever been processed.
242        /// - The upgrade transaction has been processed and the batch with such transaction has been
243        /// executed (i.e. finalized).
244        function getL2SystemContractsUpgradeBlockNumber() external view returns (uint256) {
245            return s.l2SystemContractsUpgradeBatchNumber;
246        }
````
## Tools Used
Manual Review
## Recommended Mitigation Steps
Consider removing the functions to prevent accidental misuse.
# [L-04] Redundant `if()` Conditional check
## Lines of code
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L368](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L368)
## Impact
This code contains a redundant conditional check that does not affect the behaviour of the program. The same verification operation is executed in both branches of the conditional, making the `if()` statement superfluous. 
## Proof of Concept
````solidity
354:        if (_proof.serializedProof.length > 0) {
355:            bool successVerifyProof = s.verifier.verify(
356:                proofPublicInput,
357:                _proof.serializedProof,
358:                _proof.recursiveAggregationInput
359:            );
360:            require(successVerifyProof, "p"); // Proof verification fail
361:        }
362:        // #else
363:        bool successVerifyProof = s.verifier.verify(
364:            proofPublicInput,
365:            _proof.serializedProof,
366:            _proof.recursiveAggregationInput
367:        );
368:        require(successVerifyProof, "p"); // Proof verification fail
````
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L368](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L368)
## Tools Used
Manual Review
## Recommended Mitigation Steps
Consider removing the `if()` statement.