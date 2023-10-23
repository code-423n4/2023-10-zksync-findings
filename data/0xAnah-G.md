#   zkSync Era GAS OPTIMIZATIONS

## INTRODUCTION
Highlighted below are optimizations exclusively targeting state-mutating functions and view/pure functions invoked by state-mutating functions. In the discussion that follows, only runtime gas is emphasized, given its inevitable dominance over deployment gas costs throughout the protocol's lifetime. 

Please be aware that some code snippets may be shortened to conserve space, and certain code snippets may include @audit tags in comments to facilitate issue explanations."




## [G-01] Refactor external/internal function to avoid unnecessary SLOAD
The functions below read storage slots that are previously read in the functions that invoke them. We can refactor the external/internal functions to pass cached storage variables as stack variables and avoid the extra storage reads that would otherwise take place in the internal functions.

### 1 Instance
1. #### Refactor `executeBatches()` and `_executeOneBatch()` to avoid 1 `SLOAD` per iteration
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L291
```solidity
file: contracts/ethereum/contracts/zksync/facets/Executor.sol

273:    function _executeOneBatch(StoredBatchInfo memory _storedBatch, uint256 _executedBatchIdx) internal {
274:        uint256 currentBatchNumber = _storedBatch.batchNumber;
275:        require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k"); // Execute batches in order
276:        require(
277:            _hashStoredBatchInfo(_storedBatch) == s.storedBatchHashes[currentBatchNumber],
278:            "exe10" // executing batch should be committed
279:        );
280:
281:        bytes32 priorityOperationsHash = _collectOperationsFromPriorityQueue(_storedBatch.numberOfLayer1Txs);
282:        require(priorityOperationsHash == _storedBatch.priorityOperationsHash, "x"); // priority operations hash does not match to expected
283:
284:        // Save root hash of L2 -> L1 logs tree
285:        s.l2LogsRootHashes[currentBatchNumber] = _storedBatch.l2LogsTreeRoot;
286:    }
.
.
.
291:    function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {
292:        uint256 nBatches = _batchesData.length;
293:        for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
294:            _executeOneBatch(_batchesData[i], i);
295:            emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
296:        }
297:
298:        uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
299:        s.totalBatchesExecuted = newTotalBatchesExecuted;
300:        require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.
301:
302:        uint256 batchWhenUpgradeHappened = s.l2SystemContractsUpgradeBatchNumber;
303:        if (batchWhenUpgradeHappened != 0 && batchWhenUpgradeHappened <= newTotalBatchesExecuted) {
304:            delete s.l2SystemContractsUpgradeTxHash;
305:            delete s.l2SystemContractsUpgradeBatchNumber;
306:        }
307:    }
```
The `executeBatches()` function above reads `s.totalBatchesExecuted` from state and also invokes function `_executeOneBatch()` in a loop which also reads the value of `s.totalBatchesExecuted` from state. This means that for every iteration the `_executeOneBatch()` function would read the value of `s.totalBatchesExecuted` from state, a value also read by the function that invokes it. We could refactor both `executeBatches()` and `_executeOneBatch()` such that the value of `s.totalBatchesExecuted` is read and cached in the `executeBatches()` function and passed as a parameter to the `_executeOneBatch()`. The diff below show the code should be refactored:
```diff
diff --git a/code/contracts/ethereum/contracts/zksync/facets/Executor.sol b/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
index 8fb6611..989fabb 100644
--- a/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
+++ b/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
@@ -270,9 +270,9 @@ contract ExecutorFacet is Base, IExecutor {
     /// @dev 1. Processes all pending operations (Complete priority requests)
     /// @dev 2. Finalizes batch on Ethereum
     /// @dev _executedBatchIdx is an index in the array of the batches that we want to execute together
-    function _executeOneBatch(StoredBatchInfo memory _storedBatch, uint256 _executedBatchIdx) internal {
+    function _executeOneBatch(StoredBatchInfo memory _storedBatch, uint256 _executedBatchIdx, uint256 _totalBatchesExecuted) internal {
         uint256 currentBatchNumber = _storedBatch.batchNumber;
-        require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k"); // Execute batches in order
+        require(currentBatchNumber == _totalBatchesExecuted + _executedBatchIdx + 1, "k"); // Execute batches in order
         require(
             _hashStoredBatchInfo(_storedBatch) == s.storedBatchHashes[currentBatchNumber],
             "exe10" // executing batch should be committed
@@ -290,12 +290,14 @@ contract ExecutorFacet is Base, IExecutor {
     /// @notice 2. Finalizes batch on Ethereum
     function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {
         uint256 nBatches = _batchesData.length;
+        uint256 totalBatchesExecuted = s.totalBatchesExecuted;
+
         for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
-            _executeOneBatch(_batchesData[i], i);
+            _executeOneBatch(_batchesData[i], i, totalBatchesExecuted);
             emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
         }

-        uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
+        uint256 newTotalBatchesExecuted = totalBatchesExecuted + nBatches;
         s.totalBatchesExecuted = newTotalBatchesExecuted;
         require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.
```




## [G-02] Mappings used within a function more than once should be cached to save gas
Cache such mappings and perform operations on them, if operations include modifications to the mapping(s) then remember to equate the mapping to it's cached counterpart at the end.

### Instances
1. #### Cache `totalDepositedAmountPerUser[_l1Token][_depositor] + _amount`
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L347-#L348
```solidity
file: ontracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

340:    function _verifyDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal {
341:        IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token);
342:        if (!limitData.depositLimitation) return; // no deposit limitation is placed for this token
343:
344:        if (_claiming) {
345:            totalDepositedAmountPerUser[_l1Token][_depositor] -= _amount;
346:        } else {
347:            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
348:            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
349:        }
350:    }
```
In the `_verifyDepositLimit()` function above we could avoid one `SLOAD`(Gwarmaccess) `100` gas units and `ADD` `3` gas units in the else block in scenarios where the require statements results to true if we cache the `totalDepositedAmountPerUser[_l1Token][_depositor] + _amount` computation thereby saving `100` gas units. In other scenarios where the require statements results to false we would only incur extra `2` gas unit. The diff below shows how the code should be refactored:
```diff
diff --git a/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol b/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
index 9d134f8..e279ae6 100644
--- a/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
+++ b/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
@@ -344,8 +344,9 @@ contract L1ERC20Bridge is IL1Bridge, IL1BridgeLegacy, AllowListed, ReentrancyGua
         if (_claiming) {
             totalDepositedAmountPerUser[_l1Token][_depositor] -= _amount;
         } else {
-            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
-            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
+            uint totalAmount = totalDepositedAmountPerUser[_l1Token][_depositor] + _amount;
+            require(totalAmount <= limitData.depositCap, "d1");
+            totalDepositedAmountPerUser[_l1Token][_depositor] = totalAmount;
         }
     }
```

2. #### Cache `totalDepositedAmountPerUser[_l1Token][_depositor] + _amount`
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L279
```solidity
file: contracts/ethereum/contracts/zksync/facets/Mailbox.sol

275:    function _verifyDepositLimit(address _depositor, uint256 _amount) internal {
276:        IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH
277:        if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH
278:
279:        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
280:        s.totalDepositedAmountPerUser[_depositor] += _amount;
281:    }
```
In the `_verifyDepositLimit()` function above we could avoid one `SLOAD`(Gwarmaccess) `100` gas units and `ADD` `3` gas units in scenarios where the require statements results to true if we cache the `s.totalDepositedAmountPerUser[_depositor] + _amount ` computation thereby saving `100` gas units. In other scenarios where the require statements results to false we would only incur extra `2` gas unit. The diff below shows how the code should be refactored:
```diff
diff --git a/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol b/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
index 66e11ee..2f8d173 100644
--- a/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
+++ b/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
@@ -276,8 +276,9 @@ contract MailboxFacet is Base, IMailbox {
         IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH
         if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH

-        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
-        s.totalDepositedAmountPerUser[_depositor] += _amount;
+        uint256 totalAmount = s.totalDepositedAmountPerUser[_depositor] + _amount
+        require(totalAmount <= limitData.depositCap, "d2");
+        s.totalDepositedAmountPerUser[_depositor] = totalAmount;
     }
```




## [G-03] Multiple accesses of a mapping/ should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. 

### 5 Instances
1. #### Cache `tokenDeposit[_l1Token]` into a local storage variable
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L130-#L131
```solidity
file: contracts/ethereum/contracts/common/AllowList.sol

129:    function setDepositLimit(address _l1Token, bool _depositLimitation, uint256 _depositCap) external onlyOwner {
130:        tokenDeposit[_l1Token].depositLimitation = _depositLimitation; //@audit cache tokenDeposit[_l1Token] 
131:        tokenDeposit[_l1Token].depositCap = _depositCap;
132:    }
```
We could save up to `30` gas units if we cache `tokenDeposit[_l1Token]` into a local storage variable as this saves the EVM from having to re-calculate the key has for subsequent operations that involves `tokenDeposit[_l1Token]`. The diff below shows how the code could be refactored:
```diff
diff --git a/code/contracts/ethereum/contracts/common/AllowList.sol b/code/contracts/ethereum/contracts/common/AllowList.sol
index e852b77..edb2466 100644
--- a/code/contracts/ethereum/contracts/common/AllowList.sol
+++ b/code/contracts/ethereum/contracts/common/AllowList.sol
@@ -127,8 +127,9 @@ contract AllowList is IAllowList, Ownable2Step {
     /// @param _depositLimitation deposit limitation is active or not
     /// @param _depositCap The maximum amount that can be deposited.
     function setDepositLimit(address _l1Token, bool _depositLimitation, uint256 _depositCap) external onlyOwner {
-        tokenDeposit[_l1Token].depositLimitation = _depositLimitation;
-        tokenDeposit[_l1Token].depositCap = _depositCap;
+        Deposit storage _l1TokenDepost = tokenDeposit[_l1Token];
+        _l1TokenDepost.depositLimitation = _depositLimitation;
+        _l1TokenDepost.depositCap = _depositCap;
     }
```

2. #### Cache `ds.facetToSelectors[_facet]` into a local storage variable in the `_saveFacetIfNew` function
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L193&&#L196
```solidity
file: ontracts/ethereum/contracts/zksync/libraries/Diamond.sol

190:    function _saveFacetIfNew(address _facet) private {
191:        DiamondStorage storage ds = getDiamondStorage();
192:
193:        uint256 selectorsLength = ds.facetToSelectors[_facet].selectors.length; //@audit cache ds.facetToSelectors[_facet]
194:        // If there are no selectors associated with facet then save facet as new one
195:        if (selectorsLength == 0) {
196:            ds.facetToSelectors[_facet].facetPosition = ds.facets.length.toUint16();
197:            ds.facets.push(_facet);
198:        }
```
We could reduce the gas cost of the `_saveFacetIfNew()` function above if the value of the mapping `ds.facetToSelectors[_facet]` is cached in to a local storage so that in scenarios where subsequent reads would be needed the EVM would not have to re-calculate the hash to get the storage slot of the value. The diff below shows how the code should be refactored: 
```diff
diff --git a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
index 0b1fbdc..60cec1b 100644
--- a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
+++ b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
@@ -190,10 +190,11 @@ library Diamond {
     function _saveFacetIfNew(address _facet) private {
         DiamondStorage storage ds = getDiamondStorage();

-        uint256 selectorsLength = ds.facetToSelectors[_facet].selectors.length;
+        FacetToSelectors storage _facetToSelector = ds.facetToSelectors[_facet];
+        uint256 selectorsLength = _facetToSelector.selectors.length;
         // If there are no selectors associated with facet then save facet as new one
         if (selectorsLength == 0) {
-            ds.facetToSelectors[_facet].facetPosition = ds.facets.length.toUint16();
+            _facetToSelector.facetPosition = ds.facets.length.toUint16();
             ds.facets.push(_facet);
         }
     }
```

3. #### Cache `ds.facetToSelectors[_facet]` into a local storage variable in the `_addOneFunction` function
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L213-#L228
```solidity
file: contracts/ethereum/contracts/zksync/libraries/Diamond.sol

206:    function _addOneFunction(
207:        address _facet,
208:        bytes4 _selector,
209:        bool _isSelectorFreezable
210:    ) private {
211:        DiamondStorage storage ds = getDiamondStorage();
212:
213:        uint16 selectorPosition = (ds.facetToSelectors[_facet].selectors.length).toUint16(); //@audit ds.facetToSelectors[_facet]
214:
215:        // if selectorPosition is nonzero, it means it is not a new facet
216:        // so the freezability of the first selector must be matched to _isSelectorFreezable
217:        // so all the selectors in a facet will have the same freezability
218:        if (selectorPosition != 0) {
219:            bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];
220:            require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
221:        }
222:
223:        ds.selectorToFacet[_selector] = SelectorToFacet({
224:            facetAddress: _facet,
225:            selectorPosition: selectorPosition,
226:            isFreezable: _isSelectorFreezable
227:        });
228:        ds.facetToSelectors[_facet].selectors.push(_selector);
229:    }
```
We could reduce the gas cost of the `_addOneFunction()` function above if the value of the mapping `ds.facetToSelectors[_facet]` is cached in to a local storage so that in scenarios where subsequent reads would be needed the EVM would not have to re-calculate the hash to get the storage slot of the value. The diff below shows how the code should be refactored: 
```diff
diff --git a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
index 0b1fbdc..9ec1f48 100644
--- a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
+++ b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
@@ -210,13 +210,14 @@ library Diamond {
     ) private {
         DiamondStorage storage ds = getDiamondStorage();

-        uint16 selectorPosition = (ds.facetToSelectors[_facet].selectors.length).toUint16();
+        FacetToSelectors storage _facetToSelector = ds.facetToSelectors[_facet];
+        uint16 selectorPosition = (_facetToSelector.selectors.length).toUint16();

         // if selectorPosition is nonzero, it means it is not a new facet
         // so the freezability of the first selector must be matched to _isSelectorFreezable
         // so all the selectors in a facet will have the same freezability
         if (selectorPosition != 0) {
-            bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];
+            bytes4 selector0 = _facetToSelector.selectors[0];
             require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
         }

@@ -225,7 +226,7 @@ library Diamond {
             selectorPosition: selectorPosition,
             isFreezable: _isSelectorFreezable
         });
-        ds.facetToSelectors[_facet].selectors.push(_selector);
+        _facetToSelector.selectors.push(_selector);
     }

     /// @dev Remove one associated function with facet
```


4. #### Cache `ds.facetToSelectors[_facet]` into a local storage variable in the `_removeOneFunction` function
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L238-#L249
```solidity
file: contracts/ethereum/contracts/zksync/libraries/Diamond.sol

233:    function _removeOneFunction(address _facet, bytes4 _selector) private {
234:        DiamondStorage storage ds = getDiamondStorage();
235:
236:        // Get index of `FacetToSelectors.selectors` of the selector and last element of array
237:        uint256 selectorPosition = ds.selectorToFacet[_selector].selectorPosition;
238:        uint256 lastSelectorPosition = ds.facetToSelectors[_facet].selectors.length - 1; //@audit cache ds.facetToSelectors[_facet]
239:
240:        // If the selector is not at the end of the array then move the last element to the selector position
241:        if (selectorPosition != lastSelectorPosition) {
242:            bytes4 lastSelector = ds.facetToSelectors[_facet].selectors[lastSelectorPosition];
243:
244:            ds.facetToSelectors[_facet].selectors[selectorPosition] = lastSelector;
245:            ds.selectorToFacet[lastSelector].selectorPosition = selectorPosition.toUint16();
246:        }
247:
248:        // Remove last element from the selectors array
249:        ds.facetToSelectors[_facet].selectors.pop();
250:
251:        // Finally, clean up the association with facet
252:        delete ds.selectorToFacet[_selector];
253:
254:        // If there are no selectors for facet then remove the facet from the list of known facets
255:        if (lastSelectorPosition == 0) {
256:            _removeFacet(_facet);
257:        }
258:    }
```
We could reduce the gas cost of the `_removeOneFunction()` function above if the value of the mapping `ds.facetToSelectors[_facet]` is cached in to a local storage so that in scenarios where subsequent reads would be needed the EVM would not have to re-calculate the hash to get the storage slot of the value. The diff below shows how the code should be refactored:
```diff
diff --git a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
index 0b1fbdc..60fd31e 100644
--- a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
+++ b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
@@ -235,18 +235,20 @@ library Diamond {

         // Get index of `FacetToSelectors.selectors` of the selector and last element of array
         uint256 selectorPosition = ds.selectorToFacet[_selector].selectorPosition;
-        uint256 lastSelectorPosition = ds.facetToSelectors[_facet].selectors.length - 1;
+
+        FacetToSelectors storage _facetToSelector = ds.facetToSelectors[_facet];
+        uint256 lastSelectorPosition = _facetToSelector.selectors.length - 1;

         // If the selector is not at the end of the array then move the last element to the selector position
         if (selectorPosition != lastSelectorPosition) {
-            bytes4 lastSelector = ds.facetToSelectors[_facet].selectors[lastSelectorPosition];
+            bytes4 lastSelector = _facetToSelector.selectors[lastSelectorPosition];

-            ds.facetToSelectors[_facet].selectors[selectorPosition] = lastSelector;
+            _facetToSelector.selectors[selectorPosition] = lastSelector;
             ds.selectorToFacet[lastSelector].selectorPosition = selectorPosition.toUint16();
         }

         // Remove last element from the selectors array
-        ds.facetToSelectors[_facet].selectors.pop();
+        _facetToSelector.selectors.pop();

         // Finally, clean up the association with facet
         delete ds.selectorToFacet[_selector]
```


5. #### Cache `ds.facetToSelectors[_facet]` into a local storage variable in the `_removeOneFunction` function
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L160&&#L161
```solidity
file: contracts/ethereum/contracts/zksync/facets/Getters.sol

158:    function isFunctionFreezable(bytes4 _selector) external view returns (bool) {
159:        Diamond.DiamondStorage storage ds = Diamond.getDiamondStorage();
160:        require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");    //@audit ds.selectorToFacet[_selector]
161:        return ds.selectorToFacet[_selector].isFreezable;
162:    }
```
We could reduce the gas cost of the `isFunctionFreezable()` function above if the value of the mapping `ds.selectorToFacet[_selector]` is cached in to a local storage so that in scenarios where subsequent reads would be needed the EVM would not have to re-calculate the hash to get the storage slot of the value. The diff below shows how the code should be refactored:
```diff
diff --git a/code/contracts/ethereum/contracts/zksync/facets/Getters.sol b/code/contracts/ethereum/contracts/zksync/facets/Getters.sol
index 65a381d..8775b43 100644
--- a/code/contracts/ethereum/contracts/zksync/facets/Getters.sol
+++ b/code/contracts/ethereum/contracts/zksync/facets/Getters.sol
@@ -157,8 +157,9 @@ contract GettersFacet is Base, IGetters, ILegacyGetters {
     /// @return Whether the selector can be frozen by the governor or always accessible
     function isFunctionFreezable(bytes4 _selector) external view returns (bool) {
         Diamond.DiamondStorage storage ds = Diamond.getDiamondStorage();
-        require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
-        return ds.selectorToFacet[_selector].isFreezable;
+        SelectorToFace storage _selectorToFacet = ds.selectorToFacet[_selector];
+        require(_selectorToFacet.facetAddress != address(0), "g2");
+        return _selectorToFacet.isFreezable;
     }
```




## [G-04] Redundant state variable getters
Getters for public state variables are automatically generated by the solidity compiler so there is no need to code them manually as this increases deployment cost.

### 1 Instance
1. #### Make `tokenDeposit` mapping variable `private` or `internal` since a getter function was defined for it.
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L29
```solidity
file: contracts/ethereum/contracts/common/AllowList.sol

29:    mapping(address => Deposit) public tokenDeposit;
.
.
.
136:    function getTokenDepositLimitData(address _l1Token) external view returns (Deposit memory) {
137:        return tokenDeposit[_l1Token];
138:    }
```
The solidity compiler would automatically create a getter function for the `tokenDeposit` mapping above since it is declared as a `public` variable but a getter function `getTokenDepositLimitData()` was also declared in the contract for the same variable thereby making it two getter functions for the same variable in the contract. We could rectify this issue by making the `tokenDeposit` variable private or internal (if the variable is to be inherited). The diff below shows how the code could be refactored:
```diff
diff --git a/code/contracts/ethereum/contracts/common/AllowList.sol b/code/contracts/ethereum/contracts/common/AllowList.sol
index e852b77..43849fa 100644
--- a/code/contracts/ethereum/contracts/common/AllowList.sol
+++ b/code/contracts/ethereum/contracts/common/AllowList.sol
@@ -26,7 +26,7 @@ contract AllowList is IAllowList, Ownable2Step {
     mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall;

     /// @dev The mapping L1 token address => struct Deposit
-    mapping(address => Deposit) public tokenDeposit;
+    mapping(address => Deposit) internal tokenDeposit;

     constructor(address _initialOwner) {
         _transferOwnership(_initialOwner);

```




## [G-05] Sort Solidity operations using short-circuit mode
Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

//f(x) is a low gas cost operation \
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows \
f(x) || g(y) \
f(x) && g(y)

### 1 Instance
1. #### Apply short-circuiting rule to `require(!diamondStorage.isFrozen || !facet.isFreezable, "q1")`
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L31
```solidity
file: contracts/ethereum/contracts/zksync/DiamondProxy.sol

20:    fallback() external payable {
21:        Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();
22:        // Check whether the data contains a "full" selector or it is empty.
23:        // Required because Diamond proxy finds a facet by function signature,
24:        // which is not defined for data length in range [1, 3].
25:        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
26:        // Get facet from function selector
27:        Diamond.SelectorToFacet memory facet = diamondStorage.selectorToFacet[msg.sig];
28:        address facetAddress = facet.facetAddress;
29:
30:        require(facetAddress != address(0), "F"); // Proxy has no facet for this selector
31:        require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen
.
.
.
55:    }
```
In the `fallback()` function of the `DiamondProxy` contract we can apply the short-circuiting rule to the require statement `require(!diamondStorage.isFrozen || !facet.isFreezable, "q1")` by placing the less gas costing computation `!facet.isFreezable` before the more gas costing computation `!diamondStorage.isFrozen` in the require statement. Therefore in scenarios where `!facet.isFreezable` condition results to `true` the EVM would not need to perform the gas costing operations of `!diamondStorage.isFrozen` (which involves reading from state). The code should be refactored as shown in the diff below:
```diff
diff --git a/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol b/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol
index 1645915..8e36feb 100644
--- a/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol
+++ b/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol
@@ -28,7 +28,7 @@ contract DiamondProxy {
         address facetAddress = facet.facetAddress;

         require(facetAddress != address(0), "F"); // Proxy has no facet for this selector
-        require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen
+        require(!facet.isFreezable || !diamondStorage.isFrozen, "q1"); // Facet is frozen
```




## [G-06]  Move lesser gas costing require checks to the top
Require() or revert() statements that check input arguments or cost lesser gas should be at the top of the function.
Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting alot of gas in a function that may ultimately revert in the unhappy case.

### Instances
1. #### Move `require(_newBatchesData.length > 0, "No batches to commit")` to the top of the function
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L184-#L185
```solidity
file: /contracts/ethereum/contracts/zksync/facets/Executor.sol

177:    function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)
178:        external
179:        override
180:        nonReentrant
181:        onlyValidator
182:    {
183:        // Check that we commit batches after last committed batch
184:        require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data
185:        require(_newBatchesData.length > 0, "No batches to commit"); //@audit move to top of function
186:
187:        bytes32 systemContractsUpgradeTxHash = s.l2SystemContractsUpgradeTxHash;
188:        // Upgrades are rarely done so we optimize a case with no active system contracts upgrade.
189:        if (systemContractsUpgradeTxHash == bytes32(0) || s.l2SystemContractsUpgradeBatchNumber != 0) {
200:            _commitBatchesWithoutSystemContractsUpgrade(_lastCommittedBatchData, _newBatchesData);
201:        } else {
202:            _commitBatchesWithSystemContractsUpgrade(
203:                _lastCommittedBatchData,
204:                _newBatchesData,
205:                systemContractsUpgradeTxHash
206:            );
207:        }
208:
209:        s.totalBatchesCommitted = s.totalBatchesCommitted + _newBatchesData.length;
210:    }
```
In the `commitBatches()` function above the require statement `require(_newBatchesData.length > 0, "No batches to commit")` is less gas costing compared to `require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i")` require statement therefore we should move `require(_newBatchesData.length > 0, "No batches to commit")` to the top of the function so that in scenarios where the `require(_newBatchesData.length > 0, "No batches to commit")` statement fails the EVM would not have to perform the gas consuming operations of `require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i")`. The diff below shows how the code should be refactored: 
```diff
diff --git a/code/contracts/ethereum/contracts/zksync/facets/Executor.sol b/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
index 8fb6611..fdf2ed3 100644
--- a/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
+++ b/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
@@ -181,8 +181,9 @@ contract ExecutorFacet is Base, IExecutor {
         onlyValidator
     {
         // Check that we commit batches after last committed batch
-        require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data
         require(_newBatchesData.length > 0, "No batches to commit");
+        require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data
+
```




## [G-07] Use do while loops instead of for loops
A do while loop will cost less gas since the condition is not being checked for the first iteration.

### 1 Instance
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L29
```solidity
file: contracts/zksync/libraries/Merkle.sol

18:    function calculateRoot(
19:        bytes32[] calldata _path,
20:        uint256 _index,
21:        bytes32 _itemHash
22:    ) internal pure returns (bytes32) {
23:        uint256 pathLength = _path.length;
24:        require(pathLength > 0, "xc");
25:        require(pathLength < 256, "bt");
26:        require(_index < (1 << pathLength), "px");
27:
28:        bytes32 currentHash = _itemHash;
29:        for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
30:            currentHash = (_index % 2 == 0)
31:                ? _efficientHash(currentHash, _path[i])
32:                : _efficientHash(_path[i], currentHash);
33:            _index /= 2;
34:        }
35:
36:        return currentHash;
37:    }
```
In the `calculateRoot()` function above the require statement `require(pathLength > 0, "xc")` ensures that the value of `pathLength` is greater than 0 therefore the condition of the first iteration of the for loop is redundant since it checks if `i` whose value is 0 is less than `pathLength`. We could avoid performing this first iteration check by using a do-while loop in place of the for loop thereby saving on gas that would have been used for the first iteration check computaion. The diff below shows how the code could be refactored:
```diff
diff --git a/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol b/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
index 6675c8b..9b719ac 100644
--- a/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
+++ b/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
@@ -26,12 +26,16 @@ library Merkle {
         require(_index < (1 << pathLength), "px");

         bytes32 currentHash = _itemHash;
-        for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
+
+        uint256 i;
+        do {
             currentHash = (_index % 2 == 0)
                 ? _efficientHash(currentHash, _path[i])
                 : _efficientHash(_path[i], currentHash);
-            _index /= 2;
-        }
+            _index /= 2;
+
+            i = i.uncheckedInc()
+        } while (i < pathLength);

         return currentHash;
     }
```




## [G-08] Multiple accesses of a array should use a local variable cache
The instances below point to the second+ access of a value inside an array, within a function. Caching an array's struct avoids re-calculating the array offsets into memory.

### Proof of concept 
```solidity
struct Person {
    string name;
    uint age;
    uint id;
}

contract NoCacheArrayElement {

    Person[] students;

    function createStudents() external  {
        Person[] memory arrayOfPersons = new Person[](3); 
        Person memory newPerson1 = Person("Emmanuel", 15,1);
        Person memory newPerson2 = Person("Faustina", 16,2);
        Person memory newPerson3 = Person("Emmanuela", 14,3);

        arrayOfPersons[0] = newPerson1;
        arrayOfPersons[1] = newPerson2;
        arrayOfPersons[2] = newPerson3;

        _addNewSet(arrayOfPersons);
    }

    function _addNewSet(Person[] memory _persons) internal {
        uint len = _persons.length;
        unchecked {
            for(uint i; i < len; ++i) {
                Person memory newStudent = Person(_persons[i].name, _persons[i].age, _persons[i].id);
                students.push(newStudent);
            }
        }

    }
}
```
```
test for test/NoCacheArrayElement.t.sol:NoCacheArrayElementTest
[PASS] test_createStudents() (gas: 230357)
```

```solidity

struct Person {
    string name;
    uint age;
    uint id;
}

contract CacheArrayElement {

    Person[] students;

    function createStudents() external  {
        Person[] memory arrayOfPersons = new Person[](3); 
        Person memory newPerson1 = Person("Emmanuel", 15,1);
        Person memory newPerson2 = Person("Faustina", 16,2);
        Person memory newPerson3 = Person("Emmanuela", 14,3);

        arrayOfPersons[0] = newPerson1;
        arrayOfPersons[1] = newPerson2;
        arrayOfPersons[2] = newPerson3;

        _addNewSet(arrayOfPersons);
    }

    function _addNewSet(Person[] memory _persons) internal {
        uint len = _persons.length;
        unchecked {
            for(uint i; i < len; ++i) {
                Person memory myPerson = _persons[i];
                Person memory newStudent = Person(myPerson.name, myPerson.age, myPerson.id);
                students.push(newStudent);
            }
        }

    }
}
```
```
test for test/Counter.t.sol:CacheArrayElementTest
[PASS] test_createStudents() (gas: 230096)
```

### 1 Instances
1. #### Cache `facetCuts[i]`
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L101-#L104
```solidity
file: contracts/ethereum/contracts/zksync/libraries/Diamond.sol

95:     function diamondCut(DiamondCutData memory _diamondCut) internal {
96:         FacetCut[] memory facetCuts = _diamondCut.facetCuts;
97:         address initAddress = _diamondCut.initAddress;
98:         bytes memory initCalldata = _diamondCut.initCalldata;
99:         uint256 facetCutsLength = facetCuts.length;
100:        for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
101:            Action action = facetCuts[i].action;    //@audit cache facetCuts[i]
102:            address facet = facetCuts[i].facet;
103:            bool isFacetFreezable = facetCuts[i].isFreezable;
104:            bytes4[] memory selectors = facetCuts[i].selectors;
105:
106:            require(selectors.length > 0, "B"); // no functions for diamond cut
107:
108:            if (action == Action.Add) {
109:                _addFunctions(facet, selectors, isFacetFreezable);
110:            } else if (action == Action.Replace) {
111:                _replaceFunctions(facet, selectors, isFacetFreezable);
112:            } else if (action == Action.Remove) {
113:                _removeFunctions(facet, selectors);
114:            } else {
115:                revert("C"); // undefined diamond cut action
116:            }
```

```diff
diff --git a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
index 0b1fbdc..30d648e 100644
--- a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
+++ b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
@@ -98,10 +98,11 @@ library Diamond {
         bytes memory initCalldata = _diamondCut.initCalldata;
         uint256 facetCutsLength = facetCuts.length;
         for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
-            Action action = facetCuts[i].action;
-            address facet = facetCuts[i].facet;
-            bool isFacetFreezable = facetCuts[i].isFreezable;
-            bytes4[] memory selectors = facetCuts[i].selectors;
+            FacetCut memory facetCut = facetCuts[i];
+            Action action = facetCut.action;
+            address facet = facetCut.facet;
+            bool isFacetFreezable = facetCut.isFreezable;
+            bytes4[] memory selectors = facetCut.selectors;
```




## [G-09] Remove Lines of code that are not needed in production.
Lines of code that are not required in production should be removed from the codebase as they would increase the contract deployment cost and would also increase execution cost of the functions they are in. 

### 1 Instance
1. #### Remove `assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32)` statement since it is only needed for local testing 
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L87
```solidity
file: 


85:        // While this does not provide a protection in the production, it is needed for local testing
86:        // Length of the L2Log encoding should not be equal to the length of other L2Logs' tree nodes preimages
87:        assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);
```
The comment above states that the line of code `assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32)` is only needed for local testing purposes and provides no protection during production.




## [G-10] Use custom error instead of assert
In using the assert() function, when the conditions results to false all the changes made to the contract would be reverted all the remaining gas of the transaction would be consumed.
Meanwhile, in using an if revert custom error, when the condition in the if-statement results to true would revert all the changes made to the contract and does a refund all the remaining gas fees of the transaction.

### 1 Instance
1. #### Refactor `assert(amount <= balance)` to `if (amount > balance) revert InsufficientAmount()`.
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L351
```solidity
file: contracts/ethereum/contracts/zksync/facets/Executor.sol

311:    function proveBatches(
312:        StoredBatchInfo calldata _prevBatch,
313:        StoredBatchInfo[] calldata _committedBatches,
314:        ProofInput calldata _proof
315:    ) external nonReentrant onlyValidator {
316:        // Save the variables into the stack to save gas on reading them later
317:        uint256 currentTotalBatchesVerified = s.totalBatchesVerified;
.
.
.
350:        // Additional level of protection for the mainnet
351:        assert(block.chainid != 1); //@audit use custom error instead
.
.
.        
373:    }
```

```diff
diff --git a/code/contracts/ethereum/contracts/zksync/facets/Executor.sol b/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
index 8fb6611..b507fe4 100644
--- a/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
+++ b/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
@@ -19,6 +19,9 @@ contract ExecutorFacet is Base, IExecutor {
     using UncheckedMath for uint256;
     using PriorityQueue for PriorityQueue.Queue;

+    /** ### ERRORS ### */
+    error WrongChainId();
+
     string public constant override getName = "ExecutorFacet";

     /// @dev Process one batch commit using the previous batch StoredBatchInfo
@@ -347,8 +350,9 @@ contract ExecutorFacet is Base, IExecutor {

         // #if DUMMY_VERIFIER

-        // Additional level of protection for the mainnet
-        assert(block.chainid != 1);
+        if (block.chainid == 1)
+            revert WrongChainId();
+
```

## [G-11] Stack variable used as a cheaper cache for a state variable is only used once
If the variable is only accessed once, it's cheaper to use the state variable directly that one time, and save the 3 gas the extra stack assignment would spend.
#### Please note the below instances were not included in the bot reports

### Instances
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L193
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L140
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L22
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L33
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L46
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L57
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L86
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L85
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L120
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L136
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L117


## [G-12] Declaring Unnecessary variables
Some varibles were defined even though they are used once. Not defining variables can reduce gas cost and contract size.

### Instances
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L455
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L171
- https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L231








## CONCLUSION
As you embark on incorporating the recommended optimizations, we want to emphasize the utmost importance of proceeding with vigilance and dedicating thorough efforts to comprehensive testing. It is of paramount significance to ensure that the proposed alterations do not inadvertently introduce fresh vulnerabilities, while also successfully achieving the anticipated enhancements in performance.

We strongly advise conducting a meticulous and exhaustive evaluation of the modifications made to the codebase. This rigorous scrutiny and exhaustive assessment will play a pivotal role in affirming both the security and efficacy of the refactored code. Your careful attention to detail, coupled with the implementation of a robust testing framework, will provide the necessary assurance that the refined code aligns with your security objectives and effectively fulfills the intended performance optimizations.

