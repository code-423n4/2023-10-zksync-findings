# Codebase Optimization Report

## Auditor's Disclaimer 

While we try our best to maintain readability in the provided code snippets, some functions have been truncated to highlight the affected portions.

It's important to note that during the implementation of these suggested changes, developers must exercise caution to avoid introducing vulnerabilities. Although the optimizations have been tested prior to these recommendations, it is the responsibility of the developers to conduct thorough testing again.

Code reviews and additional testing are strongly advised to minimize any potential risks associated with the refactoring process.

- [Codebase Optimization Report](#codebase-optimization-report)
  - [Auditor's Disclaimer](#auditors-disclaimer)
  - [Defer writing to state variables(Save 2 SSTORE - 4400 Gas)](#defer-writing-to-state-variablessave-2-sstore---4400-gas)
  - [Defer 1 SSTORE to later (Save 2200 Gas in case of reverts)](#defer-1-sstore-to-later-save-2200-gas-in-case-of-reverts)
  - [Avoid making an sstore if we might revert in the execution(Save 1 SSTORE: 2200 Gas if we revert)](#avoid-making-an-sstore-if-we-might-revert-in-the-executionsave-1-sstore-2200-gas-if-we-revert)
  - [Avoid an sstore in case of a revert(Save 2200 Gas)](#avoid-an-sstore-in-case-of-a-revertsave-2200-gas)
  - [Optimize function `_addFunctions`](#optimize-function-_addfunctions)
  - [Optimize function `deposit()`: Avoid making an external call in case of a revert](#optimize-function-deposit-avoid-making-an-external-call-in-case-of-a-revert)
  - [Function `deposit()` can be optimized by reordering the order of executions(avoid making external calls if we can make early reverts)](#function-deposit-can-be-optimized-by-reordering-the-order-of-executionsavoid-making-external-calls-if-we-can-make-early-reverts)
  - [Making use of the modifier in this function increases gas cost due to the external call that can be avoided in case we revert on parameter validation](#making-use-of-the-modifier-in-this-function-increases-gas-cost-due-to-the-external-call-that-can-be-avoided-in-case-we-revert-on-parameter-validation)
    - [Reorder the checks to have cheaper checks first(Save 1 SLOAD in case of a revert - 2100 Gas)](#reorder-the-checks-to-have-cheaper-checks-firstsave-1-sload-in-case-of-a-revert---2100-gas)
  - [Save an entire SLOAD by optimizing the order of execution(saves gas if we revert - 2100 Gas)](#save-an-entire-sload-by-optimizing-the-order-of-executionsaves-gas-if-we-revert---2100-gas)
  - [The fallback function Should validate `msg.data.length` first which could help avoid making a state load](#the-fallback-function-should-validate-msgdatalength-first-which-could-help-avoid-making-a-state-load)
  - [Avoid reading state variables earlier on if we might revert on a different cheaper check(Save 1 SLOAD in case of a revert - 2100 Gas)](#avoid-reading-state-variables-earlier-on-if-we-might-revert-on-a-different-cheaper-checksave-1-sload-in-case-of-a-revert---2100-gas)
  - [Cache `totalDepositedAmountPerUser[_l1Token][_depositor]` to a local variable](#cache-totaldepositedamountperuser_l1token_depositor-to-a-local-variable)
  - [The whole thing should be done in assembly](#the-whole-thing-should-be-done-in-assembly)
  - [Don't cache if using once](#dont-cache-if-using-once)
  - [No need to cache `deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit);`](#no-need-to-cache-derivel2gasprice_gasprice-_l2gasperpubdatabytelimit)
  - [Evaluate the simple arithmetic offline](#evaluate-the-simple-arithmetic-offline)
  - [Unnecessary casting](#unnecessary-casting)
  - [Caching calldata length increases gas](#caching-calldata-length-increases-gas)
  - [Conclusion](#conclusion)


## Defer writing to state variables(Save 2 SSTORE - 4400 Gas)

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L83-L131
```solidity
File: /code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
83:    function initialize(
84:        bytes[] calldata _factoryDeps,
85:        address _l2TokenBeacon,
86:        address _governor,
87:        uint256 _deployBridgeImplementationFee,
88:        uint256 _deployBridgeProxyFee
89:    ) external payable reentrancyGuardInitializer {
90:        require(_l2TokenBeacon != address(0), "nf");
91:        require(_governor != address(0), "nh");
92:        // We are expecting to see the exact three bytecodes that are needed to initialize the bridge
93:        require(_factoryDeps.length == 3, "mk");
94:        // The caller miscalculated deploy transactions fees
95:        require(msg.value == _deployBridgeImplementationFee + _deployBridgeProxyFee, "fee");
96:        l2TokenProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[2]);
97:        l2TokenBeacon = _l2TokenBeacon;

99:        bytes32 l2BridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
100:        bytes32 l2BridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);


111:        // Prepare the proxy constructor data
112:        bytes memory l2BridgeProxyConstructorData;
113:        {
114:            // Data to be used in delegate call to initialize the proxy
115:            bytes memory proxyInitializationParams = abi.encodeCall(
116:                IL2ERC20Bridge.initialize,
117:                (address(this), l2TokenProxyBytecodeHash, _governor)
118:            );
119:            l2BridgeProxyConstructorData = abi.encode(bridgeImplementationAddr, _governor, proxyInitializationParams);
120:        }
```

When the function `initialize()` is called, we are making some SSTOREs, we are more concerned with 2 SSTORES here , for `l2TokenProxyBytecodeHash` and `l2TokenBeacon` . 
Assigning `l2TokenBeacon` simply reads the function parameter while `l2TokenProxyBytecodeHash` requires a call to `L2ContractHelper.hashL2Bytecode()`.
Once we call the helper function, we make some checks and revert if the conditions are not met eg `_bytecode.length % 32 == 0`. 
Now, note we don't make use of the state variables we are assigning to immediately and only `l2TokenProxyBytecodeHash` is used(read) in the same function. Also, note we are making the call to the external helper 2 more times while storing the results to a local variable(cheaper as it's an mstore compared to the previous SSTORE). As the external helper could revert the while transaction, we should differ making any SSTORES until are sure no reverts would happen later on, which would save us 2 SSTORES in case of reverts


```diff
diff --git a/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol b/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
index 9d134f8..b25d57c 100644
--- a/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
+++ b/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
@@ -93,8 +93,8 @@ contract L1ERC20Bridge is IL1Bridge, IL1BridgeLegacy, AllowListed, ReentrancyGua
         require(_factoryDeps.length == 3, "mk");
         // The caller miscalculated deploy transactions fees
         require(msg.value == _deployBridgeImplementationFee + _deployBridgeProxyFee, "fee");
-        l2TokenProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[2]);
-        l2TokenBeacon = _l2TokenBeacon;
+
+

         bytes32 l2BridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
         bytes32 l2BridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);
@@ -107,7 +107,8 @@ contract L1ERC20Bridge is IL1Bridge, IL1BridgeLegacy, AllowL
isted, ReentrancyGua
             "", // Empty constructor data
             _factoryDeps // All factory deps are needed for L2 bridge
         );
-
+        l2TokenProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[2]);
+        l2TokenBeacon = _l2TokenBeacon;
         // Prepare the proxy constructor data
         bytes memory l2BridgeProxyConstructorData;
         {
```

## Defer 1 SSTORE to later (Save 2200 Gas in case of reverts)

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L96-L99
```solidity
File: /code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
96:        l2WethAddress = _l2WethAddress;

98:        bytes32 l2WethBridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
99:        bytes32 l2WethBridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);
```

```diff
--- a/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
+++ b/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
@@ -93,10 +93,10 @@ contract L1WethBridge is IL1Bridge, AllowListed, ReentrancyGuard {
             "Miscalculated deploy transactions fees"
         );

-        l2WethAddress = _l2WethAddress;
-
         bytes32 l2WethBridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
         bytes32 l2WethBridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);
+        l2WethAddress = _l2WethAddress;
+

         // Deploy L2 bridge implementation contract
         address wethBridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(
```

## Avoid making an sstore if we might revert in the execution(Save 1 SSTORE: 2200 Gas if we revert)

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L298-L300
```solidity
File: /code/contracts/ethereum/contracts/zksync/facets/Executor.sol
298:        uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
299:        s.totalBatchesExecuted = newTotalBatchesExecuted;
300:        require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.
```


```diff
         uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
-        s.totalBatchesExecuted = newTotalBatchesExecuted;
         require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.
+        s.totalBatchesExecuted = newTotalBatchesExecuted;
```

## Avoid an sstore in case of a revert(Save 2200 Gas)

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L291-L307
```solidity
File: /code/contracts/ethereum/contracts/zksync/facets/Executor.sol
291:    function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {

298:        uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
299:        s.totalBatchesExecuted = newTotalBatchesExecuted;
300:        require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); 
```
We can avoid an sstore in case the require statement is not satisfied 
```diff
diff --git a/code/contracts/ethereum/contracts/zksync/facets/Executor.sol b/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
index 8fb6611..8a222c4 100644
--- a/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
+++ b/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
@@ -296,8 +296,8 @@ contract ExecutorFacet is Base, IExecutor {
         }

         uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
-        s.totalBatchesExecuted = newTotalBatchesExecuted;
         require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.
+        s.totalBatchesExecuted = newTotalBatchesExecuted;

         uint256 batchWhenUpgradeHappened = s.l2SystemContractsUpgradeBatchNumber;
         if (batchWhenUpgradeHappened != 0 && batchWhenUpgradeHappened <= newTotalBatchesExecuted) {
```

## Optimize function `_addFunctions`

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L125-L145
```solidity
File: /code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
125:    function _addFunctions(
126:        address _facet,
127:        bytes4[] memory _selectors,
128:        bool _isFacetFreezable
129:    ) private {
130:        DiamondStorage storage ds = getDiamondStorage();

132:        require(_facet != address(0), "G"); // facet with zero address cannot be added

135:        _saveFacetIfNew(_facet);
```

On Line 135 we call a private function `_saveFacetIfNew()`  which has the following [implementation](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L190-L199)
```solidity
190:    function _saveFacetIfNew(address _facet) private {
191:        DiamondStorage storage ds = getDiamondStorage();

193:        uint256 selectorsLength = ds.facetToSelectors[_facet].selectors.length;

195:        if (selectorsLength == 0) {
196:            ds.facetToSelectors[_facet].facetPosition = ds.facets.length.toUint16();
197:            ds.facets.push(_facet);
198:        }
199:    }
```


In both this  function , we are making a call to `getDiamondStorage()` which involves making an SSTORE `diamondStorage.slot := position` 
We can avoid making this call twice by inlining our private function in the function `_addFunctions()` and utilizing only one call.
Note , the two calls also involved us storing the results of `getDiamondStorage()` in storage
`DiamondStorage storage ds = getDiamondStorage()`


```diff
diff --git a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
index 0b1fbdc..d3d1b6f 100644
--- a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
+++ b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
@@ -127,13 +127,17 @@ library Diamond {
         bytes4[] memory _selectors,
         bool _isFacetFreezable
     ) private {
-        DiamondStorage storage ds = getDiamondStorage();
-
         require(_facet != address(0), "G"); // facet with zero address cannot be added
+        DiamondStorage storage ds = getDiamondStorage();

         // Add facet to the list of facets if the facet address is new one
-        _saveFacetIfNew(_facet);
-
+        uint256 _selectorsLength = ds.facetToSelectors[_facet].selectors.length;
+        // If there are no selectors associated with facet then save facet as new one
+        if (_selectorsLength == 0) {
+            ds.facetToSelectors[_facet].facetPosition = ds.facets.length.toUint16();
+            ds.facets.push(_facet);
+        }
+
         uint256 selectorsLength = _selectors.length;
         for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
             bytes4 selector = _selectors[i];
```


## Optimize function `deposit()`: Avoid making an external call in case of a revert

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L176-L186
**Note: The bot suggested in lining the modifier but we go a step further, kindly follow the explanation**
```solidity
File: /code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
176:    function deposit(
177:        address _l2Receiver,
178:        address _l1Token,
179:        uint256 _amount,
180:        uint256 _l2TxGasLimit,
181:        uint256 _l2TxGasPerPubdataByte,
182:        address _refundRecipient
183:    ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) {
184:        require(_amount != 0, "2T"); // empty deposit amount
185:        uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
186:        require(amount == _amount, "1T"); // The token has non-standard transfer logic
```

The modifier has the following implementation
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowListed.sol#L10-L16
```solidity
    modifier senderCanCallFunction(IAllowList _allowList) {
        // Preventing the stack too deep error
        {
            require(_allowList.canCall(msg.sender, address(this), msg.sig), "nr");
        }
        _;
    }
```
Note, the modifier makes an external call, which is quite expensive. When used in a function, condition in the modifier is usually executed first before executing whatever implementation is in the function , as such, the external check will be performed first, then we would validate the function parameter. If the function parameter `amount` is `0` we would revert but only after making the expensive call found in the modifier. To minimize the gas spent, we should inline this modifier, then reorder the checks to have the cheap `parameter check` come first

```diff
diff --git a/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol b/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
index 9d134f8..5a95b8e 100644
--- a/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
+++ b/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
@@ -180,8 +180,9 @@ contract L1ERC20Bridge is IL1Bridge, IL1BridgeLegacy, AllowListed, ReentrancyGua
         uint256 _l2TxGasLimit,
         uint256 _l2TxGasPerPubdataByte,
         address _refundRecipient
-    ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) {
+    ) public payable nonReentrant returns (bytes32 l2TxHash) {
         require(_amount != 0, "2T"); // empty deposit amount
+        require(allowList.canCall(msg.sender, address(this), msg.sig), "nr");
         uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
         require(amount == _amount, "1T"); // The token has non-standard transfer logic
```

## Function `deposit()` can be optimized by reordering the order of executions(avoid making external calls if we can make early reverts)

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L159-L168
```solidity
File: /code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
159:    function deposit(
160:        address _l2Receiver,
161:        address _l1Token,
162:        uint256 _amount,
163:        uint256 _l2TxGasLimit,
164:        uint256 _l2TxGasPerPubdataByte,
165:        address _refundRecipient
166:    ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {
167:        require(_l1Token == l1WethAddress, "Invalid L1 token address");
168:        require(_amount != 0, "Amount cannot be zero");
```

```diff
diff --git a/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol b/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
index b53942d..f632ef9 100644
--- a/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
+++ b/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
@@ -163,9 +163,10 @@ contract L1WethBridge is IL1Bridge, AllowListed, ReentrancyGuard {
         uint256 _l2TxGasLimit,
         uint256 _l2TxGasPerPubdataByte,
         address _refundRecipient
-    ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {
+    ) external payable nonReentrant returns (bytes32 txHash) {
         require(_l1Token == l1WethAddress, "Invalid L1 token address");
         require(_amount != 0, "Amount cannot be zero");
+        require(allowList.canCall(msg.sender, address(this), msg.sig), "nr");

         // Deposit WETH tokens from the depositor address to the smart contract address
         IERC20(l1WethAddress).safeTransferFrom(msg.sender, address(this), _amount);
```

## Making use of the modifier in this function increases gas cost due to the external call that can be avoided in case we revert on parameter validation

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236-L257
```solidity
File: /code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
236:    function requestL2Transaction(
237:        address _contractL2,
238:        uint256 _l2Value,
239:        bytes calldata _calldata,
240:        uint256 _l2GasLimit,
241:        uint256 _l2GasPerPubdataByteLimit,
242:        bytes[] calldata _factoryDeps,
243:        address _refundRecipient
244:    ) external payable nonReentrant senderCanCallFunction(s.allowList) returns (bytes32 canonicalTxHash) {

247:        address sender = msg.sender;
248:        if (sender != tx.origin) {
249:            sender = AddressAliasHelper.applyL1ToL2Alias(msg.sender);
25:        }

257:        require(_l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp");
```

### Reorder the checks to have cheaper checks first(Save 1 SLOAD in case of a revert - 2100 Gas)

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L177-L185
```solidity
File: /code/contracts/ethereum/contracts/zksync/facets/Executor.sol
177:    function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)

183:        // Check that we commit batches after last committed batch
184:        require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data
185:        require(_newBatchesData.length > 0, "No batches to commit");
```
The first require check involves reading a state variable, while the second one only reads a function parameter
```diff
diff --git a/code/contracts/ethereum/contracts/zksync/facets/Executor.sol b/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
index 8fb6611..ab2a6ca 100644
--- a/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
+++ b/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
@@ -181,8 +181,8 @@ contract ExecutorFacet is Base, IExecutor {
         onlyValidator
     {
         // Check that we commit batches after last committed batch
-        require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data
         require(_newBatchesData.length > 0, "No batches to commit");
+        require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data
```

## Save an entire SLOAD by optimizing the order of execution(saves gas if we revert - 2100 Gas)

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L311-L327
```solidity
File: /code/contracts/ethereum/contracts/zksync/facets/Executor.sol
311:    function proveBatches(
312:        StoredBatchInfo calldata _prevBatch,
313:        StoredBatchInfo[] calldata _committedBatches,
314:        ProofInput calldata _proof
315:    ) external nonReentrant onlyValidator {
316:        // Save the variables into the stack to save gas on reading them later
317:        uint256 currentTotalBatchesVerified = s.totalBatchesVerified;
318:        uint256 committedBatchesLength = _committedBatches.length;

320:        // Save the variable from the storage to memory to save gas
321:        VerifierParams memory verifierParams = s.verifierParams;

323:        // Initialize the array, that will be used as public input to the ZKP
324:        uint256[] memory proofPublicInput = new uint256[](committedBatchesLength);

326:        // Check that the batch passed by the validator is indeed the first unverified batch
327:        require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");
```
If a require statement does not depend on other operations, it should be placed at the top. In this case, moving the require statement to be the second operation can help us revert early if the condition does not pass, which would save us an entire SLOAD
```diff
     ) external nonReentrant onlyValidator {
         // Save the variables into the stack to save gas on reading them later
         uint256 currentTotalBatchesVerified = s.totalBatchesVerified;
+        // Check that the batch passed by the validator is indeed the first unverified batch
+        require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");
+
         uint256 committedBatchesLength = _committedBatches.length;

         // Save the variable from the storage to memory to save gas
@@ -323,8 +326,6 @@ contract ExecutorFacet is Base, IExecutor {
         // Initialize the array, that will be used as public input to the ZKP
         uint256[] memory proofPublicInput = new uint256[](committedBatchesLength);

-        // Check that the batch passed by the validator is indeed the first unverified batch
-        require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");

         bytes32 prevBatchCommitment = _prevBatch.commitment;
         for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
```

## The fallback function Should validate `msg.data.length` first which could help avoid making a state load

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L20-L25
```solidity
File: /code/contracts/ethereum/contracts/zksync/DiamondProxy.sol
20:    fallback() external payable {
21:        Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();
22:        // Check whether the data contains a "full" selector or it is empty.
23:        // Required because Diamond proxy finds a facet by function signature,
24:        // which is not defined for data length in range [1, 3].
25:        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
```
Don't load storage if we might revert on another check, validate msg.data first before reading from storage
```diff
diff --git a/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol b/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol
index 1645915..27e6f81 100644
--- a/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol
+++ b/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol
@@ -18,11 +18,11 @@ contract DiamondProxy {
     /// @dev 1. Find the facet for the function that is called.
     /// @dev 2. Delegate the execution to the found facet via `delegatecall`.
     fallback() external payable {
-        Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();
         // Check whether the data contains a "full" selector or it is empty.
         // Required because Diamond proxy finds a facet by function signature,
         // which is not defined for data length in range [1, 3].
-        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
+        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");//@audit move up
+        Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();
         // Get facet from function selector
         Diamond.SelectorToFacet memory facet = diamondStorage.selectorToFacet[msg.sig];
         address facetAddress = facet.facetAddress;
```

## Avoid reading state variables earlier on if we might revert on a different cheaper check(Save 1 SLOAD in case of a revert - 2100 Gas)

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L123-L136
```solidity
File: /code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
123:    function _proveL2LogInclusion(
124:        uint256 _batchNumber,
125:        uint256 _index,
126:        L2Log memory _log,
127:        bytes32[] calldata _proof
128:    ) internal view returns (bool) {
129:        require(_batchNumber <= s.totalBatchesExecuted, "xx");

131:        bytes32 hashedLog = keccak256(
132:            abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBatch, _log.sender, _log.key, _log.value)
133:        );

136:        require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");
```

```diff
diff --git a/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol b/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
index 66e11ee..77133ce 100644
--- a/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
+++ b/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
@@ -126,7 +126,6 @@ contract MailboxFacet is Base, IMailbox {
         L2Log memory _log,
         bytes32[] calldata _proof
     ) internal view returns (bool) {
-        require(_batchNumber <= s.totalBatchesExecuted, "xx");

         bytes32 hashedLog = keccak256(
             abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBatch, _log.sender, _log.key, _log.value)
@@ -134,6 +133,8 @@ contract MailboxFacet is Base, IMailbox {
         // Check that hashed log is not the default one,
         // otherwise it means that the value is out of range of sent L2 -> L1 logs
         require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");
+
+        require(_batchNumber <= s.totalBatchesExecuted, "xx");

```

## Cache `totalDepositedAmountPerUser[_l1Token][_depositor]` to a local variable

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L340-L350
```solidity
File: /code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
340:    function _verifyDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal {
341:        IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token);
342:        if (!limitData.depositLimitation) return; // no deposit limitation is placed for this token

344:        if (_claiming) {
345:            totalDepositedAmountPerUser[_l1Token][_depositor] -= _amount;
346:        } else {
347:            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
348:            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
349:        }
350:    }
```


```diff
     function _verifyDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal {
         IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token);
         if (!limitData.depositLimitation) return; // no deposit limitation is placed for this token
-
+        uint256 _totalDepositedAmountPerUser = totalDepositedAmountPerUser[_l1Token][_depositor];
         if (_claiming) {
-            totalDepositedAmountPerUser[_l1Token][_depositor] -= _amount;
+            totalDepositedAmountPerUser[_l1Token][_depositor] = _totalDepositedAmountPerUser - _amount;//@audit - cache before the condition
         } else {
-            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
-            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
+            require(_totalDepositedAmountPerUser + _amount <= limitData.depositCap, "d1");
+            totalDepositedAmountPerUser[_l1Token][_depositor] = _totalDepositedAmountPerUser + _amount;
         }
     }
```

## The whole thing should be done in assembly

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L113-L120
```solidity
File: /code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
113:    function _withdrawFunds(address _to, uint256 _amount) internal {
114:        bool callSuccess;
115:        // Low-level assembly call, to avoid any memory copying (save gas)
116:        assembly {
117:            callSuccess := call(gas(), _to, _amount, 0, 0, 0, 0)
118:        }
119:        require(callSuccess, "pz");
120:    }
```

```diff
     function _withdrawFunds(address _to, uint256 _amount) internal {
-        bool callSuccess;
         // Low-level assembly call, to avoid any memory copying (save gas)
         assembly {
-            callSuccess := call(gas(), _to, _amount, 0, 0, 0, 0)
+            if iszero(call(gas(), _to, _amount, 0, 0, 0, 0)){
+                //signature for our error: 0x40678658
+                mstore(0x00,0x40678658)
+                revert(0x1c,0x04)
+            }
         }
-        require(callSuccess, "pz");
     }
```

## Don't cache if using once

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L116-L123
```solidity
File: /code/contracts/ethereum/contracts/common/AllowList.sol
116:    function _setPermissionToCall(address _caller, address _target, bytes4 _functionSig, bool _enable) internal {
117:        bool currentPermission = hasSpecialAccessToCall[_caller][_target][_functionSig];

119:        if (currentPermission != _enable) {
120:            hasSpecialAccessToCall[_caller][_target][_functionSig] = _enable;
121:            emit UpdateCallPermission(_caller, _target, _functionSig, _enable);
122:        }
123:    }
```

```diff
     function _setPermissionToCall(address _caller, address _target, bytes4 _functionSig, bool _enable) internal {
-        bool currentPermission = hasSpecialAccessToCall[_caller][_target][_functionSig];

-        if (currentPermission != _enable) {
+        if (hasSpecialAccessToCall[_caller][_target][_functionSig] != _enable) {
             hasSpecialAccessToCall[_caller][_target][_functionSig] = _enable;
             emit UpdateCallPermission(_caller, _target, _functionSig, _enable);
         }
```

## No need to cache `deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit);`

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L171-L172
```solidity
File: /code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
171:        uint256 l2GasPrice = _deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit);
172:        return l2GasPrice * _l2GasLimit;
```

```diff
@@ -168,8 +168,7 @@ contract MailboxFacet is Base, IMailbox {
         uint256 _l2GasLimit,
         uint256 _l2GasPerPubdataByteLimit
     ) public pure returns (uint256) {
-        uint256 l2GasPrice = _deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit);
-        return l2GasPrice * _l2GasLimit;
+        return _deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit) * _l2GasLimit;
     }
```

## Evaluate the simple arithmetic offline

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L87
```solidity
File: /code/contracts/ethereum/contracts/zksync/DiamondInit.sol
87:        assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);
```

```diff
-        assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);
+        assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 64);

```

Alternatively, we can use shift, as multiplying by 32 is equivalent to shifting by 5 to the left

## Unnecessary casting

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L45-L47
```solidity
File: /code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol
45:    function getSize(Queue storage _queue) internal view returns (uint256) {
46:        return uint256(_queue.tail - _queue.head);
47:    }
```
The two variables `_queue.tail and _queue.head` are of type `uint256` already, so no need to do casting when doing the subtraction

## Caching calldata length increases gas

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L83-L99
```solidity
File: /code/contracts/ethereum/contracts/common/AllowList.sol
83:    function setBatchPermissionToCall(
84:        address[] calldata _callers,
85:        address[] calldata _targets,
86:        bytes4[] calldata _functionSigs,
87:        bool[] calldata _enables
88:    ) external onlyOwner {
89:        uint256 callersLength = _callers.length;

91:        require(callersLength == _targets.length, "yw");
92:        require(callersLength == _functionSigs.length, "yx");
93:        require(callersLength == _enables.length, "yy");

95:        for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
96:            _setPermissionToCall(_callers[i], _targets[i], _functionSigs[i], _enables[i]);
97:        }
98:    }
```

This is because, since we pass calldata, the compiler already knows this values don't get mutated, as such when we read the length the first time, the compiler will keep the value and reuse it as it does not expect the length to change. If we do cache , we now force the compiler to do some mstores thus increasing the gas cost
```diff
diff --git a/code/contracts/ethereum/contracts/common/AllowList.sol b/code/contracts/ethereum/contracts/common/AllowList.sol
index e852b77..3380801 100644
--- a/code/contracts/ethereum/contracts/common/AllowList.sol
+++ b/code/contracts/ethereum/contracts/common/AllowList.sol
@@ -86,14 +86,13 @@ contract AllowList is IAllowList, Ownable2Step {
         bytes4[] calldata _functionSigs,
         bool[] calldata _enables
     ) external onlyOwner {
-        uint256 callersLength = _callers.length;

         // The size of arrays should be equal
-        require(callersLength == _targets.length, "yw");
-        require(callersLength == _functionSigs.length, "yx");
-        require(callersLength == _enables.length, "yy");
+        require(_callers.length == _targets.length, "yw");
+        require(_callers.length == _functionSigs.length, "yx");
+        require(_callers.length == _enables.length, "yy");

-        for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
+        for (uint256 i = 0; i < _callers.length; i = i.uncheckedInc()) {
             _setPermissionToCall(_callers[i], _targets[i], _functionSigs[i], _enables[i]);
         }
     }
```

## Conclusion

It is important to emphasize that the provided recommendations aim to enhance the efficiency of the code without compromising its readability. We understand the value of maintainable and easily understandable code to both developers and auditors.

As you proceed with implementing the suggested optimizations, please exercise caution and be diligent in conducting thorough testing. It is crucial to ensure that the changes are not introducing any new vulnerabilities and that the desired performance improvements are achieved. Review code changes, and perform thorough testing to validate the effectiveness and security of the refactored code.

Should you have any questions or need further assistance, please don't hesitate to reach out.
