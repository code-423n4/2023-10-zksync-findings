#### [Gas-01] `emit` could used differently to save gas

### [Gas-01-01] via updating storage while emiting events
Via doing so * 21gas * can be saved with each emit
```diff
function setValidator(address _newValidator) external onlyOwner {
-       address oldValidator = validator;
-       validator = _newValidator;
-       emit NewValidator(oldValidator, _newValidator);


+       emit NewValidator(validator, validator = _newValidator);
    }
```
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L55
```
### [Gas-01-02] via emiting `event` one step above so that we can save gas cost for one `memory` variable
Here `NewPendingGovernor(oldPendingGovernor, _newPendingGovernor)` could emited one step above so that we need not to create `address oldPendingGovernor` stack variable
```diff
    function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {
        // Save previous value into the stack to put it into the event later
-       address oldPendingGovernor = s.pendingGovernor;
+       emit NewPendingGovernor(s.pendingGovernor, _newPendingGovernor);
        // Change pending governor
        s.pendingGovernor = _newPendingGovernor;
-       emit NewPendingGovernor(oldPendingGovernor, _newPendingGovernor); 
    }
```
```
file: contracts/zksync/facets/Admin.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L25
```

```diff
function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {
        // Save previous value into the stack to put it into the event later
-       address oldPendingAdmin = s.pendingAdmin;
+       emit NewPendingGovernor(s.pendingAdmin, _newPendingAdmin);
        // Change pending admin
        s.pendingAdmin = _newPendingAdmin;
-       emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);   
    }
```
```
file: contracts/zksync/facets/Admin.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L49
```
```diff
-       address previousGovernor = s.governor;
+       emit NewGovernor(s.governor, pendingGovernor);
        s.governor = pendingGovernor;
        delete s.pendingGovernor;

        emit NewPendingGovernor(pendingGovernor, address(0));
-       emit NewGovernor(previousGovernor, pendingGovernor);
```
```
file: contracts/zksync/facets/Admin.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L38
```
```diff
function acceptAdmin() external {
        address pendingAdmin = s.pendingAdmin;
        require(msg.sender == pendingAdmin, "n4"); 

-       address previousAdmin = s.admin;
+       emit NewAdmin(s.admin, pendingAdmin); 
        s.admin = pendingAdmin;
        delete s.pendingAdmin;

        emit NewPendingAdmin(pendingAdmin, address(0));
-       emit NewAdmin(previousAdmin, pendingAdmin); 
```
```
file: contracts/zksync/facets/Admin.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L62
```
```diff
-       uint256 oldPriorityTxMaxGasLimit = s.priorityTxMaxGasLimit;
+       emit NewPriorityTxMaxGasLimit(s.priorityTxMaxGasLimit, _newPriorityTxMaxGasLimit);
        s.priorityTxMaxGasLimit = _newPriorityTxMaxGasLimit;
-       emit NewPriorityTxMaxGasLimit(oldPriorityTxMaxGasLimit, _newPriorityTxMaxGasLimit);
```
```
file: contracts/zksync/facets/Admin.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L88
```




#### [Gas-02] `memory` variable should created outside of loop, and get overriden with each iteration of loop, By doing so we save gas cost for memory variable creation in each iteration. 

```diff
        uint256 processedLogs;

        bytes32 providedL2ToL1PubdataHash = keccak256(_newBatch.totalL2ToL1Pubdata);

+       address logSender;
+       uint256 logKey;
+       bytes32 logValue;

        // linear traversal of the logs
        for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
            // Extract the values to be compared to/used such as the log sender, key, and value
-           (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET); 
-           (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);
-           (bytes32 logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);

+           (logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET); 
+           (logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);
+           (logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);
.....
.....
```
```
File:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L125-L127
```
```diff
+     bytes32 expectedUpgradeTxHash;
      for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
            // The upgrade transaction must only be included in the first batch.
-           bytes32 expectedUpgradeTxHash = i == 0 ? _systemContractUpgradeTxHash : bytes32(0);
+           expectedUpgradeTxHash = i == 0 ? _systemContractUpgradeTxHash : bytes32(0);
```
```
File:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L243
```
```diff
 +      bytes32 currentBatchCommitment;
        for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
            currentTotalBatchesVerified = currentTotalBatchesVerified.uncheckedInc();
            require(
                _hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],
                "o1"
            );

 -          bytes32 currentBatchCommitment = _committedBatches[i].commitment; 
 +          currentBatchCommitment = _committedBatches[i].commitment; 
```
```
File:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L337
```
```diff
+  uint256 commitBatchTimestamp;
   function executeBatches(StoredBatchInfo[] calldata _newBatchesData) external onlyValidator {
        uint256 delay = executionDelay; // uint32
        unchecked {
            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
-               uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);
+               commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);
```
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L117
```

#### [Gas-03] No need to cache state variable which only used once in corresponding called function {*These are missed by Bots*}
### `L2ContractHelper.hashL2Bytecode(_factoryDeps[0])` could used directly instead of caching it memory, as it only used once
```diff
l2TokenProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[2]);
        l2TokenBeacon = _l2TokenBeacon;

-       bytes32 l2BridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
        bytes32 l2BridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);

        // Deploy L2 bridge implementation contract
        address bridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeImplementationFee,
-           l2BridgeImplementationBytecodeHash, // @audit no need to cacghe
+           L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
            "", // Empty constructor data
            _factoryDeps // All factory deps are needed for L2 bridge
        );
```
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L99
```
### No need to cache `L2ContractHelper.hashL2Bytecode(_factoryDeps[2])`
```diff
        require(msg.value == _deployBridgeImplementationFee + _deployBridgeProxyFee, "fee"); 
-       l2TokenProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[2]);
        l2TokenBeacon = _l2TokenBeacon;

        bytes32 l2BridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
        bytes32 l2BridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);

        // Deploy L2 bridge implementation contract
        address bridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeImplementationFee,
            l2BridgeImplementationBytecodeHash, // @audit no need to cacghe
            "", // Empty constructor data
            _factoryDeps // All factory deps are needed for L2 bridge
        );

        // Prepare the proxy constructor data
        bytes memory l2BridgeProxyConstructorData;
        {
            // Data to be used in delegate call to initialize the proxy
            bytes memory proxyInitializationParams = abi.encodeCall(
                IL2ERC20Bridge.initialize,
 -              (address(this), l2TokenProxyBytecodeHash, _governor) // @audit direct use l2TokenProxyBytecodeHash
 +              (address(this), L2ContractHelper.hashL2Bytecode(_factoryDeps[2]), _governor) // @audit direct use l2TokenProxyBytecodeHash
            );
            l2BridgeProxyConstructorData = abi.encode(bridgeImplementationAddr, _governor, proxyInitializationParams);
        }
```
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L96
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L117
```

### No need to create `amount` variable instead use `_amount` through out function
```diff
function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        uint256 _l2TxGasLimit,
        uint256 _l2TxGasPerPubdataByte,
        address _refundRecipient
    ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) {
        require(_amount != 0, "2T"); // empty deposit amount
-       uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount); 
-       require(amount == _amount, "1T"); // The token has non-standard transfer logic
+       require(amount == _depositFunds(msg.sender, IERC20(_l1Token), _amount), "1T");
        // verify the deposit amount is allowed
        _verifyDepositLimit(_l1Token, msg.sender, _amount, false);

-       bytes memory l2TxCalldata = _getDepositL2Calldata(msg.sender, _l2Receiver, _l1Token, amount);
+       bytes memory l2TxCalldata = _getDepositL2Calldata(msg.sender, _l2Receiver, _l1Token, _amount);
        // If the refund recipient is not specified, the refund will be sent to the sender of the transaction.
        // Otherwise, the refund will be sent to the specified address.
        // If the recipient is a contract on L1, the address alias will be applied.
        address refundRecipient = _refundRecipient; // @audit no need to initialized here
        if (_refundRecipient == address(0)) {
            refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
        }
        l2TxHash = zkSync.requestL2Transaction{value: msg.value}(
            l2Bridge,
            0, // L2 msg.value
            l2TxCalldata,
            _l2TxGasLimit, // @audit absence of check against gas
            _l2TxGasPerPubdataByte,
            new bytes[](0),
            refundRecipient
        );

        // Save the deposited amount to claim funds on L1 if the deposit failed on L2
-       depositAmount[msg.sender][_l1Token][l2TxHash] = amount;
+       depositAmount[msg.sender][_l1Token][l2TxHash] = _amount;

-       emit DepositInitiated(l2TxHash, msg.sender, _l2Receiver, _l1Token, amount);
+       emit DepositInitiated(l2TxHash, msg.sender, _l2Receiver, _l1Token, _amount);
    }
```


#### [Gas-04] Some `require` statement should be at top to save excess gas cost during 
### `require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n")` should be at top of `executeBatches()`
```diff
    function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {
        uint256 nBatches = _batchesData.length;

+       uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
+       require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n");

        for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
            _executeOneBatch(_batchesData[i], i);
            emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
        }

-       uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
        s.totalBatchesExecuted = newTotalBatchesExecuted;
-       require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n");
```
```
File:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L300
```

#### [Gas-05] `memory` variable should used instead state variable while emiting events 
```diff
    function revertBatches(uint256 _newLastBatch) external nonReentrant onlyValidator {
        require(s.totalBatchesCommitted > _newLastBatch, "v1"); // The last committed batch is less than new last batch
        require(_newLastBatch >= s.totalBatchesExecuted, "v2"); // Already executed batches cannot be reverted

        if (_newLastBatch < s.totalBatchesVerified) {
            s.totalBatchesVerified = _newLastBatch;
        }
        s.totalBatchesCommitted = _newLastBatch;

        // Reset the batch number of the executed system contracts upgrade transaction if the batch
        // where the system contracts upgrade was committed is among the reverted batches.
        if (s.l2SystemContractsUpgradeBatchNumber > _newLastBatch) {
            delete s.l2SystemContractsUpgradeBatchNumber;
        }

-       emit BlocksRevert(s.totalBatchesCommitted, s.totalBatchesVerified, s.totalBatchesExecuted);
+       emit BlocksRevert(_newLastBatch, s.totalBatchesVerified, s.totalBatchesExecuted);
```
```
File:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L413
```
#### [Gas-06] `verificationKeyHash()` load all variables to memory via `_loadVerificationKey()` via assembly where it only use `VK_GATE_SETUP_0_X_SLOT` and `VK_RECURSIVE_FLAG_SLOT`. Its recommended to load only these 2 to memory and use them.
```solidity
    function verificationKeyHash() external pure returns (bytes32 vkHash) {
        _loadVerificationKey(); // @audit no need to update whole memory

        assembly {
            let start := VK_GATE_SETUP_0_X_SLOT
            let end := VK_RECURSIVE_FLAG_SLOT
            let length := add(sub(end, start), 0x20)

            vkHash := keccak256(start, length)
        }
    }
```
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Verifier.sol#L261
```

#### [Gas-07] struct can be packed more efficiently
### Via Re-ordering struct's variable struct can be packed more efficiently
`genesisIndexRepeatedStorageChanges` consumes 8bytes, so moving it up/down a extra storage slot can be saved
```diff
struct InitializeData {
        IVerifier verifier;
        address governor;
        address admin;
+       uint64 genesisIndexRepeatedStorageChanges;
        bytes32 genesisBatchHash;
-       uint64 genesisIndexRepeatedStorageChanges; // @audit reorder
        bytes32 genesisBatchCommitment;
        IAllowList allowList;
        VerifierParams verifierParams;
        bool zkPorterIsAvailable;
        bytes32 l2BootloaderBytecodeHash;
        bytes32 l2DefaultAccountBytecodeHash;
        uint256 priorityTxMaxGasLimit;
    }
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L39
#### [Gas-08]
```diff
   function _verifyDepositLimit(address _depositor, uint256 _amount) internal {
        IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0));
        if (!limitData.depositLimitation) return;
+       uint256 res = s.totalDepositedAmountPerUser[_depositor] + _amount;
-       require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
-       s.totalDepositedAmountPerUser[_depositor] += _amount;

+       require(res <= limitData.depositCap, "d2");
+       s.totalDepositedAmountPerUser[_depositor] = res;
```
```
file:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L279-L280
```
```diff
       } else {
+           uint256 res = totalDepositedAmountPerUser[_l1Token][_depositor] + _amount;
-           require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1"); 
-           totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;

+           require(res + _amount <= limitData.depositCap, "d1"); 
+           totalDepositedAmountPerUser[_l1Token][_depositor] = res;
        }
```
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L347-L348
```
#### [Gas-09] Extra storage slot can save in state variable case
### [Gas-09-01] Via Re-ordering state variables extra storage slot can be saved
### [Gas-09-02] `totalBatchesVerified` and `totalBatchesCommitted` could packed together to save save extra storage slot 
```diff
    address governor;
+   bool zkPorterIsAvailable;
    /// @notice Address that the governor proposed as one that will replace it
    address pendingGovernor;
    /// @notice List of permitted validators
    mapping(address => bool) validators;
    /// @dev Verifier contract. Used to verify aggregated proof for batches
    IVerifier verifier;
    /// @notice Total number of executed batches i.e. batches[totalBatchesExecuted] points at the latest executed batch
    /// (batch 0 is genesis)
-   uint256 totalBatchesExecuted;
    /// @notice Total number of proved batches i.e. batches[totalBatchesProved] points at the latest proved batch
-   uint256 totalBatchesVerified;

+   uint256 totalBatchesExecuted;
+   uint256 totalBatchesVerified; 
    /// @notice Total number of committed batches i.e. batches[totalBatchesCommitted] points at the latest committed
    /// batch
    uint256 totalBatchesCommitted;
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
-   bool zkPorterIsAvailable; // @audit re-order
    /// @dev The maximum number of the L2 gas that a user can request for L1 -> L2 transactions
    /// @dev This is the maximum number of L2 gas that is available for the "body" of the transaction, i.e.
    /// without overhead for proving the batch.
    uint256 priorityTxMaxGasLimit;
```
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L116

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L94
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L97
```
#### [Gas-10] No need to create `baseCost` variable, instead use expression directly inside `require()`
```diff
{
            params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);
-           uint256 baseCost = params.l2GasPrice * _l2GasLimit; 
-           require(msg.value >= baseCost + _l2Value, "mv");

+           require(msg.value >= params.l2GasPrice * _l2GasLimit + _l2Value, "mv");
```
```
file:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L305-L306
```

#### [Gas-11] `value + 1` should be replace with `value++`. In some places `uncheck{ value++ } will be more accurate.
```diff
-  require(_newBatch.batchNumber == _previousBatch.batchNumber + 1, "f");

+  require(_newBatch.batchNumber == _previousBatch.batchNumber++, "f");
```
*Instances()*
```
File: 
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L32
```
```diff
require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k");
```
```
File:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L275
```

#### [Gas-12] Validation check for `msg.sender` should done using `assembly`

```solidity
modifier onlyGovernor() {
        require(msg.sender == s.governor, "1g")
```
```
File: ethereum/contracts/zksync/facets/Base.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L19
```
```solidity
modifier onlyGovernorOrAdmin() {
        require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");
        _;
    }
```
```
File: ethereum/contracts/zksync/facets/Base.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L25
```
```solidity
modifier onlyValidator() {
        require(s.validators[msg.sender], "1h"); // validator is not active
        _;
    }
```
```
File: ethereum/contracts/zksync/facets/Base.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L30
```
```solidity
modifier onlyValidator() {
        require(msg.sender == validator, "8h"); 
        _;
    }
```
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L66
```
#### [Gas-13] Use of `assert`
```solidity
assert(block.chainid != 1);
```
```
File:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L337
```
```solidity
assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L87