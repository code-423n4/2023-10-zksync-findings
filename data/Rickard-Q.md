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