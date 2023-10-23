#### [Gas-01] `emit` could used differently to save gas

### [Gas-01-01] via updating storage while emiting events
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

#### [Gas-03] No need to cache state variable which only used once in corresponding called function {*These are missed by Bots*}
### `` could used directly instead of caching it memory, as it only used once

#### [Gas-04] Some state variable and calculation's results could cached to save gas [Missed by Bot Report]
### [Gas-04-01] Those state variable could be cached
### [Gas-04-02] Those calculations result could be cached
## `_newBatchesData.length` could be cached in `commitBatches()` function as it used twice
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L209
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L185


#### [Gas-05] Some `require` statement should be at top to save excess gas cost during 
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

#### [Gas-06] `memory` variable should used instead state variable while emiting events 
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
#### [Gas-07] No need to assigning value during stack variable creation

#### [Gas-08] struct can be packed more efficiently
#### [Gas-0]
```diff
   function _verifyDepositLimit(address _depositor, uint256 _amount) internal {
        IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0));
        if (!limitData.depositLimitation) return;
+       uint256 res = s.totalDepositedAmountPerUser[_depositor] + _amount;
-       require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
-       s.totalDepositedAmountPerUser[_depositor] += _amount;

-       require(res <= limitData.depositCap, "d2");
-       s.totalDepositedAmountPerUser[_depositor] = res;
```
```
file:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L279-L280
```
#### [Gas-0] No need to create `baseCost` variable, instead use expression directly inside `require()`
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

#### [Gas-0] `value + 1` should be replace with `value++`. In some places `uncheck{ value++ } will be more accurate.
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

#### [Gas-0] Validation check for `msg.sender` should done using `assembly`

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
#### [Gas-0] Use of `assert`
```solidity
assert(block.chainid != 1);
```
```
File:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L337
```