# GAS OPTIMIZATIONS
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
## [G-] Using calldata instead of memory for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to copy read each index of the calldata to memory. Each copy costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obviates the need for copies of words of the struct/array not being read. Note that even if an interface defines a function as having memory arguments, it's still valid for implementation contracts to use calldata arguments instead.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it's still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is public but can be marked as external since it's not called by the contract, and cases where a constructor is involved.

##

## [G-] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.





																																												## [G-] State variables should be cached in stack variables rather than re-reading them from storage	The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

##

## [G-] The result of function calls should be cached rather than re-calling the function	

The instances below point to the second+ call of the function within a single function																								
																										
																										
			/// @audit market.oracle() on line 918																							
			930:          (, info.oracleExchangeRate) = IOracle(market.oracle()).peek(																							
			/// @audit market.oracleData() on line 919																							
			931:              market.oracleData()																							
			/// @audit market.oracle() on line 918																							
			933:          info.spotExchangeRate = IOracle(market.oracle()).peekSpot(																							
			/// @audit market.oracleData() on line 919																							
			934:              market.oracleData()																							
									

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


##

## [G-] Remove or replace unused state variables

Saves a storage slot. If the variable is assigned a non-zero value, saves Gsset (20000 gas). If it's assigned a zero value, saves Gsreset (2900 gas). If the variable remains unassigned, there is no gas savings unless the variable is public, in which case the compiler-generated non-payable getter deployment cost is saved. If the state variable is overriding an interface's public function, mark the variable as constant or immutable so that it does not use a storage slot.



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




																								
																																																																												
																										
FALSE	Not using the named return variables anywhere in the function is confusing	Consider changing the variable to be an unnamed one, since the variable is never assigned, nor is it returned by name. If the optimizer is not turned on, leaving the code as it is will also waste gas for the stack variable.																								
																										
			/// @audit amount																							
			76        function getCollateralAmountForShare(																							
			77            IMarket market,																							
			78            uint256 share																							
			79:       ) public view returns (uint256 amount) {																							
			/// @audit collateralShares																							
			89        function getCollateralSharesForBorrowPart(																							
			90            IMarket market,																							
			91            uint256 borrowPart,																							
			92            uint256 liquidationMultiplierPrecision,																							
			93            uint256 exchangeRatePrecision																							
			94:       ) public view returns (uint256 collateralShares) {	

FALSE	String literals passed to abi.encode()/abi.encodePacked() should not be split by commas	String literals can be split into multiple parts and still be considered as a single string literal. Adding commas between each chunk makes it no longer a single string, and instead multiple strings. EACH new comma costs [21 gas](https://gist.github.com/IllIllI000/837d1b36c16c9bfe1010f9f775a09bbf) due to stack operations and separate MSTOREs																								
																										
			61:                   return string(abi.encodePacked("ERC1155", " (", asset.strategy.name(), ")"));																							
	https://github.com/code-423n4/2023-09-centrifuge/blob/main/bot-report.md																									

FALSE	Don't use _msgSender() if not supporting EIP-2771	Use msg.sender if the code does not implement [EIP-2771 trusted forwarder](https://eips.ethereum.org/EIPS/eip-2771) support	

FALSE	Use += for mappings	Using += for mappings saves [40 gas](https://gist.github.com/IllIllI000/4fc5f83a9edc6ed16677258bf58f32a5) due to not having to recalculate the mapping's value's hash																								
																										
	165:             balanceOf[to] = balanceOf[to] + value; // note: we don't need an overflow check here b/c balanceOf[to] <= totalSupply and there is an overflow check below	

Avoid emitting constants

A log topic (declared with indexed) has a gas cost of Glogtopic (375 gas). The Stake and Withdraw events’ second indexed parameter is a constant for a majority of events emitted (with the exception of the events emitted in the _stakeLP() and _withdrawLP() functions) and is unecessary to emit since the value will never change. Alternatively, you can avoid incurring the Glogtopic (375 gas) per call to any function that emits Stake/Withdraw (with the exception of _stakeLP() and _withdrawLP()) by creating separate events for each staking/withdraw function and opt out of using the current indexed asset topic in each event. This way you can still query the different staking/withdraw events and will save 375 gas for each staking/withdraw function (with the exception of _stakeLP() and _withdrawLP()).

Note that the events emitted in the _stakeLP() and withdrawLP() functions are not considered for this issue since the second indexed parameter is for the LP storage variable, which can be changed via the configureLP() function.

Is this possible to cache any external function calls inside the loop

Is this any possible to refatore the code and save gas ?

Is this possible to avoid internal functions?

if any return functions functions first return less gas value lastly return most gas values

Only cache memory or storage inside the condition checks if possible 





																																																																					
																																																																																																																			