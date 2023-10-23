# GAS OPTIMIZATIONS

## Findings

- [[G-1] State variables can be packed into fewer storage slots](#)
    - [[G-1.1] `_ENTERED`, `_*NOT_*ENTERED`can be packed same SLOT : Saves `2000 GAS,1 SLOT`](#)
- [[G-2]  Do not `cache` variables that are `only used once`](#)
- [[G-3] Using `immutable` directly is more `gas efficient` than caching](#)
- [[G-4] Use Gas optimized version of SafeCast library instead of openzeppelin library](#)
- [[G-5] Cache length of redundant variable in memory](#)
- [[G-6] Cache the `state` variables outside the loop](#)
- [[G-7] Variable Creation Should Be Outside Of Loop](#)
- [[G-8] Do not cache global variable](#)
- [[G-9] Emit `event` outside `for` loop](#)
- [[G-10] `Emit` storage value **instead of fetching a storage value](#)**
- [[G-11] Use a hardcoded address instead of `address(this)`](#)
- [[G-12] Use assembly for loops](#)

## [G-1] State variables can be packed into fewer storage slots

`saves 2000 gas ,1 slot`

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables can also be cheaper

### [G-1.1] `_ENTERED`, `_*NOT_*ENTERED`can be packed same SLOT : Saves `2000 GAS,1 SLOT`

`_ENTERED and _NOT_ENTERED` variable only stores the values `1` and `2` . So this can be declared as uint96 instead of uint256 to save one storage slot .

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L38C2-L39C43

```diff
File: code/contracts/ethereum/contracts/common/ReentrancyGuard.sol
36: // transaction's gas, it is best to keep them low in cases like this one, to
37:    // increase the likelihood of the full refund coming into effect.
-38:    uint256 private constant _NOT_ENTERED = 1;
+38:    uint96 private constant _NOT_ENTERED = 1;
-39:    uint256 private constant _ENTERED = 2;
+39:    uint96 private constant _ENTERED = 2;
```

## [G-02] Do not `cache` variables that are `only used once`

If the variable is only accessed once, it's cheaper to use the state variable directly that one time, and save the 3 gas the extra stack assignment would spend . Saves 35 GAS

**No need to cache the call**

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L181C8-L194C1

```solidity
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
194: address refundRecipient = _refundRecipient; //@audit no need to cache if use only once
```

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L181C8-L194C1

```solidity
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
181: address refundRecipient = _refundRecipient; //@audit No need to cache
```

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L312C4-L330C1

```diff
File: code/system-contracts/contracts/SystemContext.sol
320: if (_isFirstInBatch) {
-321:            uint128 currentBatchTimestamp = currentBatchInfo.timestamp; //@audit No need to cache bcz used only once
322:            require(
-323:                _l2BlockTimestamp >= currentBatchTimestamp,
+323:                _l2BlockTimestamp >= currentBatchInfo.timestamp,
324:                "The timestamp of the L2 block must be greater than or equal to the timestamp of the current batch"
325:            );
326:            require(_maxVirtualBlocksToCreate > 0, "There must be a virtual block created at the start of the batch");
327:        }
```

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L399C2-L406C1

```diff
File: code/system-contracts/contracts/SystemContext.sol
399: function _ensureBatchConsistentWithL2Block(uint128 _newTimestamp) internal view {
-400:        uint128 currentBlockTimestamp = currentL2BlockInfo.timestamp;//@audit No need to cache
401:        require(
-402:            _newTimestamp > currentBlockTimestamp,
+402:            _newTimestamp >  currentL2BlockInfo.timestamp,
403:            "The timestamp of the batch must be greater than the timestamp of the previous block"
404:        );
405:    }
```

## [G-03] Using `immutable` directly is more `gas efficient` than caching

Using immutable directly is more gas efficient than caching. This is because caching requires additional gas to store and retrieve the cached data. Saves `15 Gas` per instance

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L132C5-L155C2

```diff
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
132: function _propagateToZkSync() internal {
-133:        address contractAddress = zkSyncContract;
        assembly {
            // Copy function signature and arguments from calldata at zero position into memory at pointer position
            calldatacopy(0, 0, calldatasize())
            // Call method of the zkSync contract returns 0 on error
-            let result := call(gas(), contractAddress, 0, 0, calldatasize(), 0, 0)
+            let result := call(gas(), zkSyncContract, 0, 0, calldatasize(), 0, 0)
            // Get the size of the last return data
            let size := returndatasize()
            // Copy the size length of bytes from return data at zero position to pointer position
            returndatacopy(0, 0, size)
            // Depending on the result value
            switch result
            case 0 {
                // End execution and revert state changes
                revert(0, size)
            }
            default {
                // Return data with length of size at pointers position
                return(0, size)
            }
        }
    }
}
```

## [G-4] Use Gas optimized version of SafeCast library instead of openzeppelin library

Solady’s [SafeCast](https://github.com/Vectorized/solady/blob/main/src/utils/SafeCastLib.sol) implementation is more gas efficient than OZ's version. I recommend to switch to that library instead

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L5

```solidity
File: https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L5
5: import "@openzeppelin/contracts/utils/math/SafeCast.sol";
```

Solady’s [Ownable](https://github.com/Vectorized/solady/blob/main/src/utils/MerkleProofLib.sol) implementation is more gas efficient than OZ's version. I recommend to switch to that library instead

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L9

```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Base.sol
9: import "@openzeppelin/contracts/access/Ownable.sol"; //@audit solady library
```

## [G-05] Cache length of redundant variable in memory

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L54C4-L81C27

```diff
File: code/system-contracts/contracts/Compressor.sol
54: function publishCompressedBytecode(
55:        bytes calldata _bytecode,
56:        bytes calldata _rawCompressedData
57:    ) external payable onlyCallFromBootloader returns (bytes32 bytecodeHash) {
58:        unchecked {
59:            (bytes calldata dictionary, bytes calldata encodedData) = _decodeRawBytecode(_rawCompressedData);
60:
-61:            require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8"); //@audit cache length            require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");
+61:            require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8"); //@audit cache length            require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");
62:            require(
-63:                encodedData.length * 4 == _bytecode.length,
+63:                encodedData.length * 4 == _bytecode.length,
64:                "Encoded data length should be 4 times shorter than the original bytecode"
65:            );
66:
-67:            for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) { //@audit
+67:            for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {
68:                uint256 indexOfEncodedChunk = uint256(encodedData.readUint16(encodedDataPointer)) * 8;
-69:                require(indexOfEncodedChunk < dictionary.length, "Encoded chunk index is out of bounds");
+code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol69:                require(indexOfEncodedChunk < dictionary.length, "Encoded chunk index is out of bounds");
70:
70:
71:                uint64 encodedChunk = dictionary.readUint64(indexOfEncodedChunk);
72:                uint64 realChunk = _bytecode.readUint64(encodedDataPointer * 4);
73:
74:                require(encodedChunk == realChunk, "Encoded chunk does not match the original bytecode");
75:            }
76:        }
```

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L283C5-L304C10

```diff
File: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
+  uint256  data= data.length;
289: if (!success) {
                // If the returndata is too small, we still want to produce some meaningful error
-                if (data.length <= 4) { //@audit cache length
+                if (data <= 4) {
                    revert("I"); // delegatecall failed
                }

                assembly {
                    revert(add(data, 0x20), mload(data))
                }
            }

            // Check that called contract returns magic value to make sure that contract logic
            // supposed to be used as diamond cut initializer.
-            require(data.length == 32, "lp");
+            require(data == 32, "lp");
            require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
        }
```

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L177C1-L201C1

```diff
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol
177: function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)
        external
        override
        nonReentrant
        onlyValidator
    {
        // Check that we commit batches after last committed batch
+       uint256 nBatch = _newBatchesData.length;
        require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data
-        require(_newBatchesData.length > 0, "No batches to commit"); //@audit cache length  uint256 nBatches = _newBatchesData.length;
+        require(_newBatchesData.length > 0, "No batches to commit");

        bytes32 systemContractsUpgradeTxHash = s.l2SystemContractsUpgradeTxHash;
        // Upgrades are rarely done so we optimize a case with no active system contracts upgrade.
        if (systemContractsUpgradeTxHash == bytes32(0) || s.l2SystemContractsUpgradeBatchNumber != 0) {
            _commitBatchesWithoutSystemContractsUpgrade(_lastCommittedBatchData, _newBatchesData);
        } else {
            _commitBatchesWithSystemContractsUpgrade(
                _lastCommittedBatchData,
                _newBatchesData,
                systemContractsUpgradeTxHash
            );
        }

-        s.totalBatchesCommitted = s.totalBatchesCommitted + _newBatchesData.length;
+       s.totalBatchesCommitted = s.totalBatchesCommitted + _newBatchesData.length;
    }
    }
```

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L54C4-L76C14

```diff
File: code/system-contracts/contracts/Compressor.sol
54: function publishCompressedBytecode(
        bytes calldata _bytecode,
        bytes calldata _rawCompressedData
    ) external payable onlyCallFromBootloader returns (bytes32 bytecodeHash) {
        unchecked {
            (bytes calldata dictionary, bytes calldata encodedData) = _decodeRawBytecode(_rawCompressedData);
+            uint256 Dictionary = dictionary.length;
+            uint256 encodedD = encodeData.length;
-            require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8"); //@audit cache length
-            require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");
+            require(dictionary % 8 == 0, "Dictionary length should be a multiple of 8"); //@audit cache length
+            require(dictionary <= 2 ** 16 * 8, "Dictionary is too big");
            require(
-                encodedData.length * 4 == _bytecode.length,
+                encodedD * 4 == _bytecode.length,
                "Encoded data length should be 4 times shorter than the original bytecode"
            );

-            for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) { //@audit
+            for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedD; encodedDataPointer += 2) {
                uint256 indexOfEncodedChunk = uint256(encodedData.readUint16(encodedDataPointer)) * 8;
-                require(indexOfEncodedChunk < dictionary.length, "Encoded chunk index is out of bounds");
+                require(indexOfEncodedChunk < dictionary, "Encoded chunk index is out of bounds");

                uint64 encodedChunk = dictionary.readUint64(indexOfEncodedChunk);
                uint64 realChunk = _bytecode.readUint64(encodedDataPointer * 4);

                require(encodedChunk == realChunk, "Encoded chunk does not match the original bytecode");
            }
        }

        bytecodeHash = Utils.hashL2Bytecode(_bytecode);
        L1_MESSENGER_CONTRACT.sendToL1(_rawCompressedData);
        KNOWN_CODE_STORAGE_CONTRACT.markBytecodeAsPublished(bytecodeHash);
    }
```

## [G-06] Cache the state variables outside the loop

Saves `100 GAS` for each iterations

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L176

```solidity
File: Executor.sol
176: function facets() external view returns (Facet[] memory result) {
        Diamond.DiamondStorage storage ds = Diamond.getDiamondStorage();

        uint256 facetsLen = ds.facets.length;
        result = new Facet[](facetsLen);

        for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
            address facetAddr = ds.facets[i];
            Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr]; //@audit

            result[i] = Facet(facetAddr, facetToSelectors.selectors);
        }
    }
```

## [G-07] Variable Creation Should Be Outside Of Loop

When you declare a variable inside a loop, Solidity creates a new instance of the variable for each iteration of the loop. This can lead to unnecessary gas costs; especially if the loop is executed frequently or iterates over a large number of elements.

By declaring the variable outside of the loop, you can avoid the creation of multiple instances of the variable and reduce the gas cost of your contract.

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L117

```solidity
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
117: uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);
```

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L95

```solidity
File: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
95: function diamondCut(DiamondCutData memory _diamondCut) internal {
        FacetCut[] memory facetCuts = _diamondCut.facetCuts;
        address initAddress = _diamondCut.initAddress;
        bytes memory initCalldata = _diamondCut.initCalldata;
        uint256 facetCutsLength = facetCuts.length;
        for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
            Action action = facetCuts[i].action;
            address facet = facetCuts[i].facet; //@audit make variable outside loop
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
....
125: function _addFunctions(
        address _facet,
        bytes4[] memory _selectors,
        bool _isFacetFreezable
    ) private {
        DiamondStorage storage ds = getDiamondStorage();

        require(_facet != address(0), "G"); // facet with zero address cannot be added //@audit-info top of the function

        // Add facet to the list of facets if the facet address is new one
        _saveFacetIfNew(_facet);

        uint256 selectorsLength = _selectors.length;
        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
            bytes4 selector = _selectors[i]; //@audit
            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
            require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists  //@audit Use assembly for zero address check

            _addOneFunction(_facet, selector, _isFacetFreezable);
        }
    }
....
159: for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
            bytes4 selector = _selectors[i]; //@audit make variable outside of loop
            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
            require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address //@audit

            _removeOneFunction(oldFacet.facetAddress, selector);
            // Add facet to the list of facets if the facet address is a new one
            _saveFacetIfNew(_facet);
            _addOneFunction(_facet, selector, _isFacetFreezable);
        }
...
179: for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
            bytes4 selector = _selectors[i]; //@audit
            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
            require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet

            _removeOneFunction(oldFacet.facetAddress, selector);
        }
```

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L136

```solidity
File: code/system-contracts/contracts/Compressor.sol
136: uint64 enumIndex = stateDiff.readUint64(84); //@audit
168: uint64 enumIndex = stateDiff.readUint64(84); //@audit
```

## [G-08] Do not cache global variable

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L247

```diff
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
-247: address sender = msg.sender; //@audit Don't cache global variable
+247: address sender; 
-248:        if (sender != tx.origin) {
+248:        if (msg.sender != tx.origin) {
249:            sender = AddressAliasHelper.applyL1ToL2Alias(msg.sender);
250:        }
```

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L330

```solidity
File: code/system-contracts/contracts/ContractDeployer.sol
330: uint256 value = msg.value;
```

## [G-09] Emit event outside `for` loop

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L295

```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol
293: for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
294:            _executeOneBatch(_batchesData[i], i);
295:            emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment); //@audit
296:        }
```

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L213

```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol
213: emit BlockCommit(
214:                _lastCommittedBatchData.batchNumber, //@audit emit event outside for loop
215:                _lastCommittedBatchData.batchHash,
216:                _lastCommittedBatchData.commitment
217:            );
...
251: emit BlockCommit(
252:                _lastCommittedBatchData.batchNumber,
253:                _lastCommittedBatchData.batchHash,
254:                _lastCommittedBatchData.commitment
255:            );
```

## [G-10] Do not emit state variable save in memory before emit

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L80

```solidity
File: L2WethBridge.sol
80: emit WithdrawalInitiated(msg.sender, _l1Receiver, l2WethAddress, _amount); //@audit
...
106: emit FinalizeDeposit(_l1Sender, _l2Receiver, l2WethAddress, _amount); //@audit
```

## [G-11] Use a hardcoded address instead of address(this)

It can be more gas-efficient to use a hardcoded address instead of the address(this) expression, especially if you need to use the same address multiple times in your contract.

The reason for this is that using address(this) requires an additional EXTCODESIZE operation to retrieve the contract’s address from its bytecode, which can increase the gas cost of your contract. By pre-calculating and using a hardcoded address, you can avoid this additional operation and reduce the overall gas cost of your contract.

Here’s an example of how you can use a hardcoded address instead of address(this):

```
contract MyContract {
    address public myAddress = 0x1234567890123456789012345678901234567890;

    function doSomething() public {
        // Use myAddress instead of address(this)
        require(msg.sender == myAddress, "Caller is not authorized");

        // Do something
    }
}

```

In the above example, we have a contract, MyContract, with a public address variable myAddress. Instead of using address(this) to retrieve the contract’s address, we have pre-calculated and hardcoded the address in the variable. This can help to reduce the gas cost of our contract and make our code more efficient.

```solidity
File: code/contracts/ethereum/contracts/common/AllowList.sol
13: require(_allowList.canCall(msg.sender, address(this), msg.sig), "nr");
```

- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L136

```solidity
File: code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol
135: return
136:            L2ContractHelper.computeCreate2Address(address(this), salt, l2TokenProxyBytecodeHash, constructorInputHash);
```

## [G-12] Use assembly for loops

Assembly for loops can save gas in Solidity by avoiding the overhead of stack operations. When using a high-level language like Solidity, the compiler generates assembly code that includes stack operations to store and retrieve variables. This can lead to high gas costs, especially if the for loop is iterating over a large number of elements.

To avoid this overhead, you can use an assembly for loop instead. An assembly for loop allows you to explicitly manage the stack, which can lead to significant gas savings.

As per [sample tests](https://gist.github.com/sathishpic22/34b6972901f825a475753e44d39f0faf) in Remix IDE this saves around `350 Gas` per iterations. The gas savings may high based on the complexity

### There are 136 instances of this issue