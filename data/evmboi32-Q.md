# Make sure there is enough ETH to cover the fees when executing governance proposals

```solidity
function _execute(Call[] calldata _calls) internal {
+    uint256 totalValue;
+    for (uint256 i = 0; i < _calls.length; ++i) {
+        totalValue += calls[i].value;
+    }
    
+    require(address(this).balance >= totalValue);

    for (uint256 i = 0; i < _calls.length; ++i) {
        (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
        if (!success) {
            // Propage an error if the call fails.
            assembly {
                revert(add(returnData, 0x20), mload(returnData))
            }
        }
    }
}
```

# There is no check that the Getters facet cannot be frozen
In the provided documentation https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/L1%20smart%20contracts.md#gettersfacet it states that the ```Getters``` facet ``` must never be frozen```. But when doing a diamondCut there is no check if the facet we are updating the selectors for is the ```Getters``` facet. When the first selector is added to a new facet the facet itself is pushed into the facet array and ```_isFacetFreezable``` is set. Make sure to check if the facet in question is the ```Getters``` facet and require that the ```_isFacetFreezable``` is set to ```false```.

```solidity
function diamondCut(DiamondCutData memory _diamondCut) internal {
    FacetCut[] memory facetCuts = _diamondCut.facetCuts;
    address initAddress = _diamondCut.initAddress;
    bytes memory initCalldata = _diamondCut.initCalldata;
    uint256 facetCutsLength = facetCuts.length;
    for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
        Action action = facetCuts[i].action;
        address facet = facetCuts[i].facet;
        bool isFacetFreezable = facetCuts[i].isFreezable;
        bytes4[] memory selectors = facetCuts[i].selectors;

        require(selectors.length > 0, "B"); // no functions for diamond cut

        if (action == Action.Add) {
            _addFunctions(facet, selectors, isFacetFreezable);
        } else if (action == Action.Replace) {
            _replaceFunctions(facet, selectors, isFacetFreezable);
        } else if (action == Action.Remove) {
            _removeFunctions(facet, selectors);
        } else {
            revert("C"); // undefined diamond cut action
        }
    }

    _initializeDiamondCut(initAddress, initCalldata);
    emit DiamondCut(facetCuts, initAddress, initCalldata);
}
```

# Wrong comment in ```L1Messenger#publishPubdataAndClearState```
Inside the header, it says that there are 2 bytes for the ```total len of compressed``` as opposed to the documentation https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Handling%20pubdata%20in%20Boojum/State%20diff%20compression%20v1%20spec.md where it says that the ```total_logs_len``` is 3 bytes.

```solidity
function publishPubdataAndClearState(
        bytes calldata _totalL2ToL1PubdataAndStateDiffs
    ) external onlyCallFromBootloader {
        ...
        /// Check State Diffs
        /// encoding is as follows:
        /// header (1 byte version, 2 bytes total len of compressed, 1 byte enumeration index size, 2 bytes number of initial writes)
        /// body (N bytes of initial writes [32 byte derived key || compressed value], M bytes repeated writes [enumeration index || compressed value])
        /// encoded state diffs: [20bytes address][32bytes key][32bytes derived key][8bytes enum index][32bytes initial value][32bytes final value]
        ...
}
```

We also use 3 bytes in the code 
```solidity
uint24 compressedStateDiffSize = uint24(bytes3(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 3]));

```audi

# Unused variable in ```SystemContext#publishTimestampDataToL1```
```solidity
function publishTimestampDataToL1() external onlyCallFromBootloader {
    (uint128 currentBatchNumber, uint128 currentBatchTimestamp) = getBatchNumberAndTimestamp();
    (, uint128 currentL2BlockTimestamp) = getL2BlockNumberAndTimestamp();

    // The structure of the "setNewBatch" implies that currentBatchNumber > 0, but we still double check it
    require(currentBatchNumber > 0, "The current batch number must be greater than 0");
    bytes32 prevBatchHash = batchHash[currentBatchNumber - 1];

    // In order to spend less pubdata, the packed version is published
    uint256 packedTimestamps = (uint256(currentBatchTimestamp) << 128) | currentL2BlockTimestamp;

    SystemContractHelper.toL1(
        false,
        bytes32(uint256(SystemLogKey.PACKED_BATCH_AND_L2_BLOCK_TIMESTAMP_KEY)),
        bytes32(packedTimestamps)
    );
}
```

The variable ```bytes32 prevBatchHash = batchHash[currentBatchNumber - 1];``` is unused.

# Wrong check in the ```L1Messenger#publishPubdataAndClearState```
The number of logs is compared to itself which is always true. Instead, it should be compared to ```L2_TO_L1_LOGS_MERKLE_TREE_LEAVES```.
```solidity
function publishPubdataAndClearState(
    bytes calldata _totalL2ToL1PubdataAndStateDiffs
) external onlyCallFromBootloader {
    uint256 calldataPtr = 0;

    /// Check logs
    uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
    require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
    calldataPtr += 4;
    
    ...
}
```
