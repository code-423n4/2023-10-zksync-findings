# GAS OPTIMIZATIONS

In structs can be used structs for saving gas for same type variables 

																																																																																																																												##

## [G-] Structs can be packed into fewer storage slots	

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.

### ``protocolVersion`` can be ``uint96`` instead of ``uint256`` : Saves ``2000 GAS`` , ``1 SLOT``

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Storage.sol#L136

The ``protocolVersion`` value is increased in a sequential way, meaning that the next version number is always one greater than the current version number. This means that the ``protocolVersion`` value cannot exceed the maximum range of ``uint96`` unless the protocol is upgraded more than ``2^96 times``.

``2^96`` is a very large number, and it is unlikely that any protocol would ever need to be upgraded more than this many times. For example, the ``Ethereum protocol`` has only been upgraded a ``handful of times`` since it was launched in 2015.

Therefore, it is safe to change the ``protocolVersion`` variable to ``uint96`` without worrying about the value overflowing. This will save ``2000 gas`` and ``1 slot``, which can be significant for contracts that are frequently upgraded.


```diff
FILE: Breadcrumbs2023-10-zksync/code/contracts/ethereum/contracts/zksync/Storage.sol


struct AppStorage {
    /// @dev Storage of variables needed for deprecated diamond cut facet
    uint256[7] __DEPRECATED_diamondCutStorage;
    /// @notice Address which will exercise critical changes to the Diamond Proxy (upgrades, freezing & unfreezing)
    address governor;
    /// @notice Address that the governor proposed as one that will replace it
    address pendingGovernor;
    /// @notice List of permitted validators
    mapping(address => bool) validators;
    /// @dev Verifier contract. Used to verify aggregated proof for batches
    IVerifier verifier;
    /// @notice Total number of executed batches i.e. batches[totalBatchesExecuted] points at the latest executed batch
    /// (batch 0 is genesis)
    uint256 totalBatchesExecuted;
    /// @notice Total number of proved batches i.e. batches[totalBatchesProved] points at the latest proved batch
    uint256 totalBatchesVerified;
    /// @notice Total number of committed batches i.e. batches[totalBatchesCommitted] points at the latest committed
    /// batch
    uint256 totalBatchesCommitted;
    /// @dev Stored hashed StoredBatch for batch number
    mapping(uint256 => bytes32) storedBatchHashes;
    /// @dev Stored root hashes of L2 -> L1 logs
    mapping(uint256 => bytes32) l2LogsRootHashes;
    /// @dev Container that stores transactions requested from L1
    PriorityQueue.Queue priorityQueue;
    /// @dev The smart contract that manages the list with permission to call contract functions
    IAllowList allowList;
    /// @notice Part of the configuration parameters of ZKP circuits. Used as an input for the verifier smart contract
    VerifierParams verifierParams;
    /// @notice Bytecode hash of bootloader program.
    /// @dev Used as an input to zkp-circuit.
    bytes32 l2BootloaderBytecodeHash;
    /// @notice Bytecode hash of default account (bytecode for EOA).
    /// @dev Used as an input to zkp-circuit.
    bytes32 l2DefaultAccountBytecodeHash;
    /// @dev Indicates that the porter may be touched on L2 transactions.
    /// @dev Used as an input to zkp-circuit.
    bool zkPorterIsAvailable;
    /// @dev The maximum number of the L2 gas that a user can request for L1 -> L2 transactions
    /// @dev This is the maximum number of L2 gas that is available for the "body" of the transaction, i.e.
    /// without overhead for proving the batch.
    uint256 priorityTxMaxGasLimit;
    /// @dev Storage of variables needed for upgrade facet
    UpgradeStorage __DEPRECATED_upgrades;
    /// @dev A mapping L2 batch number => message number => flag.
    /// @dev The L2 -> L1 log is sent for every withdrawal, so this mapping is serving as
    /// a flag to indicate that the message was already processed.
    /// @dev Used to indicate that eth withdrawal was already processed
    mapping(uint256 => mapping(uint256 => bool)) isEthWithdrawalFinalized;
    /// @dev The most recent withdrawal time and amount reset
    uint256 __DEPRECATED_lastWithdrawalLimitReset;
    /// @dev The accumulated withdrawn amount during the withdrawal limit window
    uint256 __DEPRECATED_withdrawnAmountInWindow;
    /// @dev A mapping user address => the total deposited amount by the user
    mapping(address => uint256) totalDepositedAmountPerUser;
    /// @dev Stores the protocol version. Note, that the protocol version may not only encompass changes to the
    /// smart contracts, but also to the node behavior.
-    uint256 protocolVersion;
    /// @dev Hash of the system contract upgrade transaction. If 0, then no upgrade transaction needs to be done.
    bytes32 l2SystemContractsUpgradeTxHash;
    /// @dev Batch number where the upgrade transaction has happened. If 0, then no upgrade transaction has happened
    /// yet.
    uint256 l2SystemContractsUpgradeBatchNumber;
    /// @dev Address which will exercise non-critical changes to the Diamond Proxy (changing validator set & unfreezing)
    address admin;
    /// @notice Address that the governor or admin proposed as one that will replace admin role
    address pendingAdmin;
+    uint96 protocolVersion;
}

```

### ``newProtocolVersion`` can be ``uint96`` instead of ``uint256`` : Saves ``2000 GAS`` , ``1 SLOT``

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L31-L43

The ``newprotocolVersion`` value is increased in a sequential way, meaning that the next version number is always one greater than the current version number. This means that the ``newprotocolVersion`` value cannot exceed the maximum range of ``uint96`` unless the protocol is upgraded more than ``2^96 times``.


```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

struct ProposedUpgrade {
        IMailbox.L2CanonicalTransaction l2ProtocolUpgradeTx;
        bytes[] factoryDeps;
        bytes32 bootloaderHash;
        bytes32 defaultAccountHash;
        address verifier;
+         uint96 newProtocolVersion;
        VerifierParams verifierParams;
        bytes l1ContractsUpgradeCalldata;
        bytes postUpgradeCalldata;
        uint256 upgradeTimestamp;
-         uint256 newProtocolVersion;
        address newAllowList;
    }

```

### ``txType`` and ``nonce`` can be uint128 instead of uint256 : Saves ``2000 GAS`` , ``1 SLOT``

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L35-L62

The change to ``uint128`` for ``txType`` and ``nonce`` is safe because the maximum values of these fields are within the range of uint128.

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol

 struct L2CanonicalTransaction {
-        uint256 txType;
+        uint128 txType;
+        uint128 nonce;
        uint256 from;
        uint256 to;
        uint256 gasLimit;
        uint256 gasPerPubdataByteLimit;
        uint256 maxFeePerGas;
        uint256 maxPriorityFeePerGas;
        uint256 paymaster;
-        uint256 nonce;
        uint256 value;
        // In the future, we might want to add some
        // new fields to the struct. The `txData` struct
        // is to be passed to account and any changes to its structure
        // would mean a breaking change to these accounts. To prevent this,
        // we should keep some fields as "reserved".
        // It is also recommended that their length is fixed, since
        // it would allow easier proof integration (in case we will need
        // some special circuit for preprocessing transactions).
        uint256[4] reserved;
        bytes data;
        bytes signature;
        uint256[] factoryDeps;
        bytes paymasterInput;
        // Reserved dynamic type for the future use-case. Using it should be avoided,
        // But it is still here, just in case we want to enable some additional functionality.
        bytes reservedDynamic;
    }

```

##

## [G-] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```solidity
FILE: Breadcrumbs2023-10-zksync/code/contracts/ethereum/contracts/zksync/Storage.sol

 /// @dev Stored hashed StoredBatch for batch number
99:  mapping(uint256 => bytes32) storedBatchHashes;
100:    /// @dev Stored root hashes of L2 -> L1 logs
101: mapping(uint256 => bytes32) l2LogsRootHashes;


87:  mapping(address => bool) validators;
133: mapping(address => uint256) totalDepositedAmountPerUser;

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Storage.sol#L99-L101

##
																																												## [G-] State variables should be cached in stack variables rather than re-reading them from storage	

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

### ``totalDepositedAmountPerUser[_l1Token][_depositor]`` should be cached : Saves ``100 GAS`` , ``1 SLOD``

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L346-L348

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

346: } else {
+    uint256 totalDepositedAmountPerUserL1TokenDepositor = totalDepositedAmountPerUser[_l1Token][_depositor] ;
- 347:            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
+ 347:            require(totalDepositedAmountPerUserL1TokenDepositor + _amount <= limitData.depositCap, "d1");
- 348:            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
+ 348:            totalDepositedAmountPerUser[_l1Token][_depositor] = totalDepositedAmountPerUserL1TokenDepositor + _amount;
349:        }


```

##

## [G-] Combine events to save Glogtopic (375 gas)

We can combine the events into one singular event to save two Glogtopic (375 gas) that would otherwise be paid for the additional events.

### 

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets
/Admin.sol

- 37: emit NewPendingGovernor(pendingGovernor, address(0));
- 38: emit NewGovernor(previousGovernor, pendingGovernor);

+ PendingAndNewGovernor(pendingGovernor, address(0),previousGovernor,pendingGovernor);


- 61: emit NewPendingAdmin(pendingAdmin, address(0));
- 62: emit NewAdmin(previousAdmin, pendingAdmin);

+ PendingAndNewAdmin(pendingAdmin, address(0),previousAdmin,pendingAdmin);

```
																																																																																										##

## [G-] Optimize Gas Usage by Avoiding Variable Declarations Inside Loops

The variables ``action``, ``facet``, ``isFacetFreezable``, and ``selectors`` are all declared inside the loop. This means that a new instance of each variable will be created for each iteration of the loop. Saves 200-300 Gas per iteration

```solidity

for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
            action = facetCuts[i].action;
             facet = facetCuts[i].facet;
            isFacetFreezable = facetCuts[i].isFreezable;
             selectors = facetCuts[i].selectors;

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


```

```diff
diff --git a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
index 0b1fbdc..2f3cf9e 100644
--- a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
+++ b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
@@ -97,11 +97,15 @@ library Diamond {
         address initAddress = _diamondCut.initAddress;
         bytes memory initCalldata = _diamondCut.initCalldata;
         uint256 facetCutsLength = facetCuts.length;
+        Action action;
+        address facet;
+      bool isFacetFreezable;
+    bytes4[] memory selectors;
         for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
-            Action action = facetCuts[i].action;
+            action = facetCuts[i].action;
-            address facet = facetCuts[i].facet;
+             facet = facetCuts[i].facet;
-            bool isFacetFreezable = facetCuts[i].isFreezable;
+            isFacetFreezable = facetCuts[i].isFreezable;
-            bytes4[] memory selectors = facetCuts[i].selectors;
+            selectors = facetCuts[i].selectors;

```

###






##

## [G-] Place ``require() and ``if()`` checks in top of the functions to avoid unnecessary gas 

> FAIL CHEEPLY INSTEAD OF COSTLY

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

### First check the ``amount`` then call ``zkSync.proveL1ToL2TransactionStatus()`` costly external call : Saves ``2100 GAS``

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L255-L275

Can save gas by first checking the ``amount`` before calling the ``zkSync.proveL1ToL2TransactionStatus()`` function. This is because the ``zkSync.proveL1ToL2TransactionStatus()`` function is a costly external call, and it is only necessary to make the call if the ``amount`` is greater than zero.


```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

 function claimFailedDeposit(
        address _depositSender,
        address _l1Token,
        bytes32 _l2TxHash,
        uint256 _l2BatchNumber,
        uint256 _l2MessageIndex,
        uint16 _l2TxNumberInBatch,
        bytes32[] calldata _merkleProof
    ) external nonReentrant senderCanCallFunction(allowList) {

+        uint256 amount = depositAmount[_depositSender][_l1Token][_l2TxHash];
+        require(amount > 0, "y1");

        bool proofValid = zkSync.proveL1ToL2TransactionStatus(
            _l2TxHash,
            _l2BatchNumber,
            _l2MessageIndex,
            _l2TxNumberInBatch,
           _merkleProof,
            TxStatus.Failure
       );
        require(proofValid, "yn");

-        uint256 amount = depositAmount[_depositSender][_l1Token][_l2TxHash];
-        require(amount > 0, "y1");


```

### Caching ``diamondStorage`` before ``msg.data.length >= 4 || msg.data.length == 0`` check wastes the gas if any reverts in require check : Saves ``2100 GAS``

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L20-L25 

```diff
FILE : 2023-10-zksync/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

  fallback() external payable {
+        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
        Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();
        // Check whether the data contains a "full" selector or it is empty.
        // Required because Diamond proxy finds a facet by function signature,
        // which is not defined for data length in range [1, 3].
-        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");

```

### ``_newBatchesData.length > 0`` parameter should be checked before ``s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData)`` before state variable with function calling . Saves ``500 GAS``

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L183-L185

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

+ 185:        require(_newBatchesData.length > 0, "No batches to commit");
183: // Check that we commit batches after last committed batch
184:        require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data
- 185:        require(_newBatchesData.length > 0, "No batches to commit");

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L183-L185

### Check ``_l2GasPerPubdataByteLimit``  parameter before any other operations : Saves ``200 GAS``

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L257


```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

 function requestL2Transaction(
        address _contractL2,
        uint256 _l2Value,
        bytes calldata _calldata,
        uint256 _l2GasLimit,
        uint256 _l2GasPerPubdataByteLimit,
        bytes[] calldata _factoryDeps,
        address _refundRecipient
    ) external payable nonReentrant senderCanCallFunction(s.allowList) returns (bytes32 canonicalTxHash) {
        // Change the sender address if it is a smart contract to prevent address collision between L1 and L2.
+        require(_l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp");
        // Please note, currently zkSync address derivation is different from Ethereum one, but it may be changed in the future.
        address sender = msg.sender;
        if (sender != tx.origin) {
            sender = AddressAliasHelper.applyL1ToL2Alias(msg.sender);
        }

        // Enforcing that `_l2GasPerPubdataByteLimit` equals to a certain constant number. This is needed
        // to ensure that users do not get used to using "exotic" numbers for _l2GasPerPubdataByteLimit, e.g. 1-2, etc.
        // VERY IMPORTANT: nobody should rely on this constant to be fixed and every contract should give their users the ability to provide the
        // ability to provide `_l2GasPerPubdataByteLimit` for each independent transaction.
        // CHANGING THIS CONSTANT SHOULD BE A CLIENT-SIDE CHANGE.
-        require(_l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp");

```



##

## [G-] Optimize code to avoid to spend unnecessary GAS

### Any revert inside the for loop then caching ``initAddress`` ,``initCalldata `` is waste of the computation and gas . Saves ``300 GAS``. Values of ``initAddress`` and ``initCalldata `` is used after the for loop. So caching values before for loop is unnecessary.

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L97-L98

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

function diamondCut(DiamondCutData memory _diamondCut) internal {
        FacetCut[] memory facetCuts = _diamondCut.facetCuts;
-        address initAddress = _diamondCut.initAddress;
-        bytes memory initCalldata = _diamondCut.initCalldata;
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
+        address initAddress = _diamondCut.initAddress;
+        bytes memory initCalldata = _diamondCut.initCalldata;

        _initializeDiamondCut(initAddress, initCalldata);
        emit DiamondCut(facetCuts, initAddress, initCalldata);
    }

```


### Any revert in  ``block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp`` check then caching lastL2BlockTimestamp value unnecessary and waste of gas 

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L87


```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

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

-        uint256 lastL2BlockTimestamp = _packedBatchAndL2BlockTimestamp & PACKED_L2_BLOCK_TIMESTAMP_MASK;

        // All L2 blocks have timestamps within the range of [batchTimestamp, lastL2BatchTimestamp].
        // So here we need to only double check that:
        // - The timestamp of the batch is not too small.
        // - The timestamp of the last L2 block is not too big.
        require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small
+        uint256 lastL2BlockTimestamp = _packedBatchAndL2BlockTimestamp & PACKED_L2_BLOCK_TIMESTAMP_MASK;
        require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big
    }



123:  for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
            // Extract the values to be compared to/used such as the log sender, key, and value
-            (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET);
            (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);
-            (bytes32 logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);

            // Ensure that the log hasn't been processed already
            require(!_checkBit(processedLogs, uint8(logKey)), "kp");
            processedLogs = _setBit(processedLogs, uint8(logKey));
+            (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET);
+            (bytes32 logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);

```



##

## [G-] Not initializing variables to their default values incurs extra gas, So leaving them with their default values is more gas-efficient

This is because the default value of 0 is already assigned to value automatically. By not initializing it, we avoid the cost of the unnecessary assignment.

```solidity
FILE: Breadcrumbs2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

123: for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

209: for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

241:  for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

263: for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

293: for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {

330: for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L123

##

## [G-] Don't cache immutable variable incurs extra Gas

 
```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

 function _propagateToZkSync() internal {
-        address contractAddress = zkSyncContract;
        assembly {
            // Copy function signature and arguments from calldata at zero position into memory at pointer position
            calldatacopy(0, 0, calldatasize())
            // Call method of the zkSync contract returns 0 on error
-            let result := call(gas(), contractAddress, 0, 0, calldatasize(), 0, 0)
+            let result := call(gas(), zkSyncContract, 0, 0, calldatasize(), 0, 0)

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L133

##

## [G-] Bytes constants are more efficient than string constants

bytes constants are more gas efficient than string constants when the string length is fixed. This is because the Ethereum Virtual Machine (EVM) stores bytes constants in a more compact way than string constants.

As per remix [gas tests](https://gist.github.com/sathishpic22/244dd5b1a18d1dd3aab64bf6304c1cd4) saves ``31630 GAS`` per instance

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

- 22: string public constant override getName = "ExecutorFacet";
+ 22: bytes32 public constant override getName = "ExecutorFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

- 41:  string public constant override getName = "MailboxFacet";
+ 41:  bytes32 public constant override getName = "MailboxFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Getters.sol

- 19: string public constant override getName = "GettersFacet";
+ 19: bytes32 public constant override getName = "GettersFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

- 24:  string public constant override getName = "ValidatorTimelock";
+ 24:  bytes32 public constant override getName = "ValidatorTimelock";

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Admin.sol

- 15: string public constant override getName = "AdminFacet";
+ 15: bytes32 public constant override getName = "AdminFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15

##

## [G-] Don't cache variable only used once 

If the variable is only accessed once, it's cheaper to use the state variable directly that one time, and save the 3 gas the extra stack assignment would spend.

### ``hasSpecialAccessToCall[_caller][_target][_functionSig]`` can be used directly instead of local cache

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L117

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/common/AllowList.sol

- 117: bool currentPermission = hasSpecialAccessToCall[_caller][_target][_functionSig];

-        if (currentPermission != _enable) {
+        if (hasSpecialAccessToCall[_caller][_target][_functionSig] != _enable) {
            hasSpecialAccessToCall[_caller][_target][_functionSig] = _enable;
            emit UpdateCallPermission(_caller, _target, _functionSig, _enable);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L117

### Using a local variable to cache the result of ``s.priorityQueue.popFront()`` is redundant and costs ``250 GAS`` per iteration. Instead, use the result of the function call directly.

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L263-L266

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

263: for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
- 264:            PriorityOperation memory priorityOp = s.priorityQueue.popFront();
- 265:            concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
+ 265:            concatHash = keccak256(abi.encode(concatHash, s.priorityQueue.popFront().canonicalTxHash));
266:        }

```

```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

-  uint256 lastL2BlockTimestamp = _packedBatchAndL2BlockTimestamp & PACKED_L2_BLOCK_TIMESTAMP_MASK;

        // All L2 blocks have timestamps within the range of [batchTimestamp, lastL2BatchTimestamp].
        // So here we need to only double check that:
        // - The timestamp of the batch is not too small.
        // - The timestamp of the last L2 block is not too big.
        require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small
-        require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big
+        require(_packedBatchAndL2BlockTimestamp & PACKED_L2_BLOCK_TIMESTAMP_MASK <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L87-L94

###


```diff
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol


```

##

## [G-] Use uint256(1)/uint256(2) instead of true/false to save gas for changes

#### Note this instances are not found by bot 

Avoids a Gsset (20000 gas) when changing from false to true, after having been true in the past. Since most of the bools aren't changed twice in one transaction, I've counted the amount of gas as half of the full amount, for each variable.

```solidity
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/Storage.sol

32:  bool approvedBySecurityCouncil;
 
49: bool isService;

87: mapping(address => bool) validators;

116: bool zkPorterIsAvailable;

127: mapping(uint256 => mapping(uint256 => bool)) isEthWithdrawalFinalized;

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Storage.sol#L87

```solidity
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

32: bool isFreezable;

53: bool isFrozen;

64: bool isFreezable;

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L32
																							
##																																																																																																																																																## [G-] Using private rather than public for constants, saves gas	

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a [tuple of the values](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table																								
																										
			File: tapioca-bar-audit/contracts/Penrose.sol																							
			45:       uint256 public immutable tapAssetId;																							
			53:       uint256 public immutable wethAssetId;																							
																										
																										
			41:       uint256 public constant INITIAL_SUPPLY = 46_686_595 * 1e18; // Everything minus DSO																							
			49:       uint256 public constant WEEK = 604800;																							
			53:       uint256 public immutable emissionsStartTime;	


FALSE	Don't compare boolean expressions to boolean literals	if (<x> == true) => if (<x>), if (<x> == false) => if (!<x>)																								
			175:              isSingularityMasterContractRegistered[mc] == true,																							
			183:              isBigBangMasterContractRegistered[mc] == true,																							
			322:              isSingularityMasterContractRegistered[mcAddress] == false,																							
			344:              isBigBangMasterContractRegistered[mcAddress] == false,																							
																										
FALSE	Multiple if-statements with mutually-exclusive conditions should be changed to if-else statements	If two conditions are the same, their blocks should be combined																								
			264           if (borrowPartDecimals > 18) {																							
			265               borrowPartScaled = borrowPart / (10 ** (borrowPartDecimals - 18));																							
			266           }																							
			267           if (borrowPartDecimals < 18) {																							
			268               borrowPartScaled = borrowPart * (10 ** (18 - borrowPartDecimals));																							
			269:          }																							
			272           if (collateralPartDecimals > 18) {																							
			273               collateralPartInAssetScaled =																							
			274                   collateralPartInAsset /																							
			275                   (10 ** (collateralPartDecimals - 18));																							
			276           }	




																								
																																																																												
																										
	


																							



Is this possible to cache any external function calls inside the loop

Is this possible to avoid internal functions?

if any return functions functions first return less gas value lastly return most gas values

Only cache memory or storage inside the condition checks if possible 


struct names can be aligned with order same like chainlink findings to target alex 


																																																																					
																																																																																																																			