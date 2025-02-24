## A. Overdependency on Timestamp
[Link](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L74-L95)
The contract uses timestamps for various checks, such as ensuring that batches are processed in chronological order and verifying the validity of timestamps in L2 blocks. Timestamps are fundamental to these checks, but they can be manipulated in certain situations. For instance, miners can potentially influence timestamps to some extent, leading to inaccuracies in batch processing or verification.

Here is an example of timestamp dependency in the `_verifyBatchTimestamp` function:
```solidity
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

    // ...
}

```
### Impact:
Timestamp manipulation or inaccuracies can lead to incorrect batch processing and verification. If timestamps are not properly validated or if in any case they get manipulated by miners, it could potentially allow out-of-order or maliciously crafted batches to disrupt the contract's intended operation.
### Mitigation:
While using `block.number` as a mitigation strategy is a good approach to reduce timestamp-related vulnerabilities, it's important to strike a balance between using timestamps and block numbers based on the specific requirements of the contract. In some cases, a combination of both may be appropriate for comprehensive validation.

## B. Assumption of Non-Zero Address for `_facet` Parameter in `_addOneFunction`
[Link](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L206-L229)
```solidity
function _addOneFunction(
    address _facet,
    bytes4 _selector,
    bool _isSelectorFreezable
) private {
    DiamondStorage storage ds = getDiamondStorage();

    uint16 selectorPosition = (ds.facetToSelectors[_facet].selectors.length).toUint16();

    // If selectorPosition is nonzero, it means it is not a new facet
    // so the freezability of the first selector must be matched to _isSelectorFreezable
    // so all the selectors in a facet will have the same freezability
    if (selectorPosition != 0) {
        bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];
        require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
    }

    ds.selectorToFacet[_selector] = SelectorToFacet({
        facetAddress: _facet,
        selectorPosition: selectorPosition,
        isFreezable: _isSelectorFreezable
    });
    ds.facetToSelectors[_facet].selectors.push(_selector);
}
```
The vulnerability lies in the assumption that `_facet` is a non-zero Ethereum address. The code does not explicitly check whether `_facet` is a valid Ethereum address before proceeding. This assumption can lead to unexpected behavior if `_facet` is, in fact, a zero address.
### Impact:
The impact of this vulnerability is that if `_facet` is a zero address when calling `_addOneFunction`, it can result in unexpected behavior, erroneous state changes, or runtime errors in the contract. This could potentially disrupt the intended functionality of the diamond proxy and lead to contract failures.
### Mitigation:
To mitigate this vulnerability, it is essential to add a precondition check to ensure that `_facet` is a valid non-zero Ethereum address before calling the `_addOneFunction` function. Here's an example of how this check can be added:
```solidity
// Add a precondition check for valid _facet address
require(_facet != address(0), "Invalid _facet address");
// Proceed with the _addOneFunction operation
_addOneFunction(_facet, _selector, _isSelectorFreezable);
```
## C. Allowing Same Old Verifier Address as New Verifier Address in zkSync Protocol Upgrades
[Link](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L111-L123)
In the `BaseZkSyncUpgrade` contract, the `_setVerifier` function is responsible for changing the address of the verifier smart contract. The function does not explicitly prevent setting the new verifier address to the same value as the old verifier address. This allows for the possibility of accidentally or intentionally setting the old verifier address as the new verifier address.
## Impact:
The impact of this issue is primarily operational and related to the clarity and perceived significance of protocol upgrades. Allowing such a change effectively results in a "no-op" upgrade, where no meaningful changes are introduced to the protocol. This can lead to confusion among users and stakeholders regarding the purpose and significance of upgrades. It may impact the trust and perception of upgrade processes.
## Mitigation:
Consider revising the upgrade process to explicitly prevent such "no-op" changes, if they are not deemed meaningful or significant for the protocol.
## D. Ineffectual Parameter Changes in zkSync Protocol Upgrades
[Link](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L127-L139)
In the `BaseZkSyncUpgrade` contract, the `_setVerifierParams` function is responsible for updating verifier parameters during upgrades. However, the code does not explicitly prevent setting the new verifier parameters to match the old parameters, as shown in the following code snippet:
```solidity
function _setVerifierParams(VerifierParams calldata _newVerifierParams) private {
    if (
        _newVerifierParams.recursionNodeLevelVkHash == bytes32(0) ||
        _newVerifierParams.recursionLeafLevelVkHash == bytes32(0) ||
        _newVerifierParams.recursionCircuitsSetVksHash == bytes32(0)
    ) {
        return;
    }

    VerifierParams memory oldVerifierParams = s.verifierParams;
    s.verifierParams = _newVerifierParams;
    emit NewVerifierParams(oldVerifierParams, _newVerifierParams);
}
```
## Impact:
It may affect governance processes, as non-substantive upgrades could be perceived as a lack of meaningful change, impacting governance confidence.
## Mitigation:
Establish clear governance procedures that require upgrades to bring meaningful changes and document the rationale for parameter modifications.
## E. Gas Exhaustion in Batch Operations
[Link](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L58-L75)
When batch operations are performed on large arrays in the `AllowList` contract, the gas cost for processing these operations can exceed the gas limit per Ethereum block. This can result in the transaction being reverted, leaving the contract in an inconsistent state. Below is an example of a batch operation that can be vulnerable to gas exhaustion:
```solidity
function setBatchPermissionToCall(
    address[] calldata _callers,
    address[] calldata _targets,
    bytes4[] calldata _functionSigs,
    bool[] calldata _enables
) external onlyOwner {
    uint256 callersLength = _callers.length;

    require(callersLength == _targets.length, "yw");
    require(callersLength == _functionSigs.length, "yx");
    require(callersLength == _enables.length, "yy");

    for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
        _setPermissionToCall(_callers[i], _targets[i], _functionSigs[i], _enables[i]);
    }
}
```
In this function, the contract iterates through arrays of `_callers`, `_targets`, `_functionSigs`, and `_enables` to set permissions. If the arrays are large, the gas cost of this operation can be significant.
## Impact:
The impact of this vulnerability is that a large batch operation could cause a transaction to exceed the gas limit, leading to a revert. As a result, changes made in the batch operation may not take effect, and the contract's state may remain inconsistent.
## Mitigation:
Limit Batch Sizes: Implement a mechanism to limit the size of batch operations, preventing them from becoming too large and consuming excessive gas.
## F. Absence of a Check for `_l2BlockNumber` Greater Than Zero in `_setNewL2BlockData`
[Link](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L285-L294)
The `_setNewL2BlockData` function within the `SystemContext` contract lacks a crucial check to ensure that `_l2BlockNumber` is greater than zero. This omission is based on an implicit "expectation" that `_l2BlockNumber` should be non-zero, which can introduce security risks.
## Impact:
The impact of this issue is that the contract does not explicitly verify that `_l2BlockNumber` is a valid and positive number. If `_l2BlockNumber` were to unexpectedly be zero or negative due to a bug or external manipulation, it could lead to uninitialized or incorrect values being used, potentially causing unintended behavior. This may open the door to security risks, including unexpected contract behavior and potential exploits by malicious actors.
## Mitigation:
It is advisable to include an explicit check at the beginning of the `_setNewL2BlockData` function to verify that `_l2BlockNumber` is greater than zero. 
## G. Lack of Enforcement for Message Marker Parameter
[Link](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L119)
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L29)
The code includes a comment that describes the use of a "marker" in the context of sending variable-length messages from Layer 2 to Layer 1. The comment suggests that a "marker" parameter should be set to `true` to indicate a specific type of message. However, the code does not include any logic or variable to enforce or handle this "marker" parameter.
```solidity
/// @dev To send a variable length message we use this trick:
/// - This system contract accepts an arbitrary length message and sends a fixed length message with
/// parameters `senderAddress == this`, `marker == true`, `key == msg.sender`, `value == keccak256(message)`.
function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {
    // ...
}
```
The comment suggests that the "marker" should be set to `true`, but there is no code that explicitly sets or enforces the "marker" parameter. This lack of implementation might lead to ambiguity in how messages are processed and categorized.
## Impact:
The lack of enforcement for the "marker" parameter could potentially affect the protocol's intended functionality, especially if the "marker" is intended to categorize or differentiate messages. It may lead to misunderstandings in message processing and could impact interoperability with other parts of the protocol that expect the presence of a "marker."
## Mitigation:
Implement and enforce the "marker" parameter.
## H Lack of Reset Mechanisms for State Variables in L1Messenger Contract
[Link](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L36)
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L40)
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L44)
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L49) 
The L1Messenger contract has comments that suggest certain state variables should be reset at the end of a block. However, the contract code does not include the necessary logic to perform these resets. Let's examine each affected variable:

1. `chainedLogsHash` : This variable is meant to store a sequential hash of logs sent in the current block. The comment implies that it should be reset to zero at the end of each block. The code does not contain a mechanism to reset this variable, which means it will retain its value across multiple blocks.

2. `numberOfLogsToProcess` : This variable is used to keep track of the number of logs sent in the current block. Similar to `chainedLogsHash`, the comment indicates that it should be reset at the end of each block. However, there is no code in the contract to perform this reset.

3. `chainedMessagesHash` : This variable is used to store a sequential hash of hashes of messages sent in the current block. The comment suggests it should be reset at the end of a block, but there is no corresponding reset mechanism in the contract code.

4. `chainedL1BytecodesRevealDataHash` : This variable is meant to store a sequential hash of bytecode hashes that need to be published according to the current block's execution invariant. The comment indicates that it should be reset at the end of the block, but the contract does not include a reset mechanism.
## Impact:
The lack of reset mechanisms for these state variables can lead to unintended behavior in the contract. It may affect the proper functioning of the contract, especially in scenarios where these variables need to be reset at the end of each block. This issue could also impact the accuracy and reliability of the data being tracked by these variables.
## Mitigation:
Include a mechanism to reset the mentioned state variables at the end of each block, as per the comments in the code.