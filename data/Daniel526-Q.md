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