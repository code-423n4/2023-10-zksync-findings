## QA Summary:

- Low: 
  Issues: 15, Instances: 28
- NC: 
  Issues: 3, Instances: 12
- Total: 
  Issues: 18, Instances: 40

## QA Breakdown:

### Low 01 - In Executor.sol, `commitBatches()` has no check on the max size of dynamic array of `_newBatchesData()` passed, which may cause out-of-gas error. (Note: Instance not found in automated findings)

**Instances(3)**

(1):
In Executor.sol - `commitBatches()`, a dynamic array of `_newBatchesData` is passed and iterated through a for-loop in `_commitBatchesWithSystemContractsUpgrade()` or `_commitBatchesWithoutSystemContractsUpgrade()`. And inside the for-loop, each iteration will call `_commitOneBatch()` which invokes `_processL2Logs()` that contains a second for-loop iterating through `systemlogs`. 

Although `systemLogs` size is predictable and has checks to ensure its size, the first for-loop is unconstrained. This will possibly result in out-of-gas error when too many batches are passed. Especially given the fact the `_newBatchesData()`'s element is a struct `CommitBatchInfo` with undefined sizes, which increases the risk of out-of-gas error.

```solidity
    function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)
        external
        override
        nonReentrant
        onlyValidator
    {
...
|>          _commitBatchesWithoutSystemContractsUpgrade(_lastCommittedBatchData, _newBatchesData);
        } else {
|>          _commitBatchesWithSystemContractsUpgrade(
                _lastCommittedBatchData,
                _newBatchesData,
                systemContractsUpgradeTxHash
            );
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L190-L196)

```solidity
    function _commitBatchesWithoutSystemContractsUpgrade(
        StoredBatchInfo memory _lastCommittedBatchData,
        CommitBatchInfo[] calldata _newBatchesData
    ) internal {
...
        // @audit First for-loop
|>      for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
            _lastCommittedBatchData = _commitOneBatch(_lastCommittedBatchData, _newBatchesData[i], bytes32(0));
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L209)

```solidity
    function _commitOneBatch(
        StoredBatchInfo memory _previousBatch,
        CommitBatchInfo calldata _newBatch,
        bytes32 _expectedSystemContractUpgradeTxHash
    ) internal view returns (StoredBatchInfo memory) {
...
|>      ) = _processL2Logs(_newBatch, _expectedSystemContractUpgradeTxHash);
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L43)

```solidity
    function _processL2Logs(CommitBatchInfo calldata _newBatch, bytes32 _expectedSystemContractUpgradeTxHash)
...
        // @audit second for-loop  
|>      for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L123C1-L123C105)

(2)(3):
Note that there are similar instances found in `proveBatches()` and `executeBatches()` where unbounded dynamic array is iterated in a for-loop, although the operation inside for-loop is less intensive. And there is no nested for-loop like in the case of `commmitBatches()`.
```solidity
//proveBatches.sol
...
  for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L330)

```solidity
//executeBatches.sol
...
  for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L293)

Recommendation:
Consider enforcing a max size of the input dynamic array as a safeguard.


### Low 02 - In Executor.sol - `proveBatches()`, if validator pass `_proof`, the same proof verification will perform twice which is unecessary

**Instances(1)**

In `proveBatches()`, when a validator pass along non-empty `_proof` to verify, this will be done twice due to unnecessary `if` bypass.

```solidity
//code/contracts/ethereum/contracts/zksync/facets/Executor.sol
    function proveBatches(
        StoredBatchInfo calldata _prevBatch,
        StoredBatchInfo[] calldata _committedBatches,
        ProofInput calldata _proof
    ) external nonReentrant onlyValidator {
...

|>      if (_proof.serializedProof.length > 0) {
            bool successVerifyProof = s.verifier.verify(
                proofPublicInput,
                _proof.serializedProof,
                _proof.recursiveAggregationInput
            );
            require(successVerifyProof, "p"); // Proof verification fail
        }
|>      bool successVerifyProof = s.verifier.verify(
            proofPublicInput,
            _proof.serializedProof,
            _proof.recursiveAggregationInput
        );
        require(successVerifyProof, "p"); // Proof verification fail
...
``` 
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L363)

As seen from above, when `_proof.serializedProof.length>0`, the proof will be verified twice performing unnecessary operations. 

Recommendations:
Consider removing the `if` statement, to always run `s.verifier.verify()` exactly once.

### Low 03 - In Executor.sol, there are incorrect comments in `proveBatches()`

**Instances(3)**

In `proveBatches()`, the code comment indicates in `test(net) environment If the proof is not empty, verify it, otherwise, skip the verification`, this is not implemented in current code logic. And subsequent comments suggest there is `#else` and `#endif`, but there is no else body statement follows.

```solidity
code/contracts/ethereum/contracts/zksync/facets/Executor.sol
    function proveBatches(
        StoredBatchInfo calldata _prevBatch,
        StoredBatchInfo[] calldata _committedBatches,
        ProofInput calldata _proof
    ) external nonReentrant onlyValidator {
...
        // We allow skipping the zkp verification for the test(net) environment
|>      // If the proof is not empty, verify it, otherwise, skip the verification

        if (_proof.serializedProof.length > 0) {
            bool successVerifyProof = s.verifier.verify(
                proofPublicInput,
                _proof.serializedProof,
                _proof.recursiveAggregationInput
            );
            require(successVerifyProof, "p"); // Proof verification fail
        }

|>      // #else
        bool successVerifyProof = s.verifier.verify(
            proofPublicInput,
            _proof.serializedProof,
            _proof.recursiveAggregationInput
        );
        require(successVerifyProof, "p"); // Proof verification fail
|>      // #endif

        emit BlocksVerification(s.totalBatchesVerified, currentTotalBatchesVerified);
        s.totalBatchesVerified = currentTotalBatchesVerified;
    }
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L352-L369)

Recommendation:
Remove comments that are not relevant or outdated, this increases readability and avoid unintended code implementation.

### Low 04 - In Mailbox.sol, there are incorrect comments in `_parseL2WithdrawalMessage()`

**Instances(1)**

In `_parseL2WithdrawalMessage()`, L2 withdraw message length is checked in the `require` statement. Although the `require` implementation is correct, the comment stated incorrect L2 message length.

```solidity
code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
    function _parseL2WithdrawalMessage(bytes memory _message)
        internal
        pure
        returns (address l1Receiver, uint256 amount)
    {
...
        // We check that the message is long enough to read the data.
        // Please note that there are two versions of the message:
        // 1. The message that is sent by `withdraw(address _l1Receiver)`
        // It should be equal to the length of the bytes4 function signature + address l1Receiver + uint256 amount = 4 + 20 + 32 = 56 (bytes).
        // 2. The message that is sent by `withdrawWithMessage(address _l1Receiver, bytes calldata _additionalData)`
        // It should be equal to the length of the following:
        // bytes4 function signature + address l1Receiver + uint256 amount + address l2Sender + bytes _additionalData =
|>      // = 4 + 20 + 32 + 32 + _additionalData.length >= 68 (bytes).

        // So the data is expected to be at least 56 bytes long.
        require(_message.length >= 56, "pm");
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L418)

As seen, the comment states there are (2) versions of the message, but the byte size for the second version in the comment is incorrect. In L2EthToken.sol, when `withdrawWithMessage()` is invoked, `_getExtendedWithdrawMessage()` will also concatenate `msg.sender` in the message. The correct message length should be `4 + 20 +32 + 20 + additionalData >= 76 (bytes)

```solidity
code/system-contracts/contracts/L2EthToken.sol
    function _getExtendedWithdrawMessage(
        address _to,
        uint256 _amount,
        address _sender,
        bytes memory _additionalData
    ) internal pure returns (bytes memory) {
        return abi.encodePacked(IMailbox.finalizeEthWithdrawal.selector, _to, _amount, _sender, _additionalData);
    }
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L2EthToken.sol#L117-L123)

Recommendation:
Update the comment to reflect the correct byte size. 

### Low 05 - In TransactionValidator.sol, unnecessary math operation in `getOverheadForTransaction()`

**Instances(1)**

In `getOverheadForTransaction()`, `Math.max()` is used to compare a zero value with an uint256 value and then assigned to a local variable `batchOverheadForTransaction`. This results in unnecessary math operations.

```solidity
code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol
    function getOverheadForTransaction(
        uint256 _totalGasLimit,
        uint256 _gasPricePerPubdata,
        uint256 _encodingLength
    ) internal pure returns (uint256 batchOverheadForTransaction) {
        uint256 batchOverheadGas = BATCH_OVERHEAD_L2_GAS + BATCH_OVERHEAD_PUBDATA * _gasPricePerPubdata;
        // The overhead from taking up the transaction's slot
        uint256 txSlotOverhead = Math.ceilDiv(batchOverheadGas, MAX_TRANSACTIONS_IN_BATCH);

|>      batchOverheadForTransaction = Math.max(batchOverheadForTransaction, txSlotOverhead);
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L141-L142)

(1) As seen, `batchOverheadForTransaction` default zero value will be used in `Math.max()` method, which yields the same result as simply assigning `txSlotOverhead` to `batchOverheadForTransaction`;
(2) In addition since `txSlotOverhead` is only an intermediate variable for `Math.max()`, this variable can be directly eliminated.

Avoid unnecessary local variable declaration and avoid using math library when there is no need improves code readability and avoid mistakes.

Recommendations:
Change to `batchOverheadForTransaction = Math.ceilDiv(batchOverheadGas, MAX_TRANSACTIONS_IN_BATCH);`

### Low 06 - In TransactionValidator.sol, there is an incorrect range check for the system contract address.

**Instances(1)**

In `validateUpgradeTransaction()`, transaction `from` is checked to ensure it is a system contract. But this check is not exactly accurate.

```solidity
code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol
   function validateUpgradeTransaction(IMailbox.L2CanonicalTransaction memory _transaction) internal pure {
        // Restrict from to be within system contract range (0...2^16 - 1)
|>      require(_transaction.from <= type(uint16).max, "ua");
|>      require(_transaction.to <= type(uint160).max, "ub");
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L48-L51)

Since system contract address shouldn't include 2^16, require statement shouldn't include `type(uint16).max`. This allows 2^16 address to pass as system contract address.

Recommendations:
Consider changing `<=` into `<`.

### Low 07 - Diamond.sol is unable to completely remove an old facet address from accounting, the old facet address can still be queried from Getters.sol

**Instances(1)**

In Diamond.sol, an old facet address is designed to only be removed when its last function selector is removed. Any changes(add/replace/remove) to a facet and its selectors are intended to be done through `diamondCut()`, which depending on the passed `DiamondCutData` will operate existing facets directly on the selector/function level through `Action.Add`, `Action.Replace` and `Action.Remove`.

For example, when a new facet is to be added. `_addFunctions` will check whether the face address is new, and if so, add selectors to the new face address. Conversely, if an existing face needs to be removed. `_removeFunctions()` will be invoked and selectors passed through will be removed in a for-loop, if there is no selector left stored for the facet, `_removeFacet()` will be invoked automatically, which is the only way a facet is to be removed.

In Diamond proxy, a facet is stored both in mapping and an array. However, the issue is in `_removeFacet()` will only remove the array element stored but not the mapping. As a result, the old facet will be left in `faceToSelectors` mapping.

```solidity
// code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
    function _removeOneFunction(address _facet, bytes4 _selector) private {
        DiamondStorage storage ds = getDiamondStorage();
...
        uint256 lastSelectorPosition = ds.facetToSelectors[_facet].selectors.length - 1;
...
        // If there are no selectors for facet then remove the facet from the list of known facets
        if (lastSelectorPosition == 0) {
|>          _removeFacet(_facet);
        }
}
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L256)
```solidity
    function _removeFacet(address _facet) private {
        DiamondStorage storage ds = getDiamondStorage();
        // Get index of `DiamondStorage.facets` of the facet and last element of array
        uint256 facetPosition = ds.facetToSelectors[_facet].facetPosition;
        uint256 lastFacetPosition = ds.facets.length - 1;
        // If the facet is not at the end of the array then move the last element to the facet position
        if (facetPosition != lastFacetPosition) {
            address lastFacet = ds.facets[lastFacetPosition];
            ds.facets[facetPosition] = lastFacet;
            ds.facetToSelectors[lastFacet].facetPosition = facetPosition.toUint16();
        }
        // Remove last element from the facets array
|>      ds.facets.pop();
} 
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L278)
```solidity
    struct FacetToSelectors {
        bytes4[] selectors;
        uint16 facetPosition;
    }
...
    struct DiamondStorage {
        mapping(bytes4 => SelectorToFacet) selectorToFacet;
        mapping(address => FacetToSelectors) facetToSelectors;
        address[] facets;
        bool isFrozen;
    }
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L51)

As seen, `_removeFacet()` will pop the old facet from `DiamondStorage.facets` array, but `faceToSelectors` mapping will still contain the old facet's `FacetToSelectors.facetPosition` value in the array. Although `FacetToSelectors.selectors` length will be 0 due to all selectors are removed, but `facePosition` will not be zero. This will be confusing since an old facet's value is not entirely cleared in the mapping.

Recommendations:
In `_removeFacet()`, after removing the facet from `DiamondStorage.facets` array, also delete its mapping entry in `faceToSelectors`.

### Low 08 - In DiamondInit.sol, remove unnecessary code irrelevant to the production

**Instances(1)**

In DiamondInit.sol - `initialize()`, an `assert()` check on a constant value is performed which is not needed during production. According to the code comment, the check is only in place for testing purposes. 

```solidity
// code/contracts/ethereum/contracts/zksync/DiamondInit.sol
    function initialize(InitializeData calldata _initalizeData) external reentrancyGuardInitializer returns (bytes32) {
...
        // While this does not provide a protection in the production, it is needed for local testing
        // Length of the L2Log encoding should not be equal to the length of other L2Logs' tree nodes preimages
        assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L85-L87)

Note that `L2_TO_L1_LOG_SERIALIZE_SIZE` is a constant value fixed at 88 in Config.sol

```solidity
uint256 constant L2_TO_L1_LOG_SERIALIZE_SIZE = 88;
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Config.sol#L11)

It's clear the `assert` statement along with its comment is not relevant to production. 

Recommendation:
Remove `assert` and its comment. 

### Low 09 - DiamondProxy's `fallback()` cannot handle `msg.data.length==0` case against the design intention and will revert

**Instances(1)**

DiamondProxy.sol delegates call to its facet contracts through a payable `fallback()` function. When a caller needs to call a payable function on a facet contract, msg.value will be sent to DiamondProxy to be relayed depending on the facet function logic. In other cases, `fallback()` function also allows only msg.value to be sent to DiamondProxy.sol without msg.data, essentially allowing DiamondProxy.sol to receive ETH transfer. This case is explicitly allowed in a `require` statement.

```solidity
// code/contracts/ethereum/contracts/zksync/DiamondProxy.sol
    fallback() external payable {
        Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();
        // Check whether the data contains a "full" selector or it is empty.
        // Required because Diamond proxy finds a facet by function signature,
        // which is not defined for data length in range [1, 3].
|>      require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
        // Get facet from function selector
        Diamond.SelectorToFacet memory facet = diamondStorage.selectorToFacet[msg.sig];
        address facetAddress = facet.facetAddress;

|>      require(facetAddress != address(0), "F"); // Proxy has no facet for this selector
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L20-L30)

As seen above, it is allowed when msg.data is empty. And the first `require` ensures that the exact 4 or 0 bytes data can pass. However, when `msg.data.length==0`, `facetAddress` fetched from the `selectorToFacet` mapping will be address(0), which will cause the second `require` to revert.

Two `require` statements conflict with each other in allowing msg.data to pass through the function, preventing direct ETH transfer. This is conflicted logic within a function. 

Recommendations:
Either disallow ETH transfer and always only require `msg.data.length==4`. Or add an early return when `msg.data.length==0` to allow for ETH transfer.

### Low 10 - Unused Interface Functions (Note: Instances listed here not found in automated reports)

**Instances(5)**

Interface functions are declared but never implemented in deriving contracts. This is unnecessary code.
There are multiple instances of this issue in different contracts:

(1):
```solidity
// code/contracts/ethereum/contracts/zksync/interfaces/IBase.sol
interface IBase {
// @audit this getter function is not implemented anywhere in deriving contracts.
// @audit getName is currently implemented as a public constant variable for every facet contract. 
    function getName() external view returns (string memory);
}
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/interfaces/IBase.sol#L4-L6)

(2):
```solidity
// code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol

// @audit this getter function is not implemented anywhere in deriving contracts.
// @audit getAccessMode is currently implemented as a public variable. 
function getAccessMode(address _target) external view returns (AccessMode);
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol#L38C5-L38C80)

(3):
```solidity
// code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol

// @audit this hasSpecialAccessToCall function is not implemented anywhere in deriving contracts.
    function hasSpecialAccessToCall(
        address _caller,
        address _target,
        bytes4 _functionSig
    ) external view returns (bool);
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol#L40-L44)

(4):
```solidity
// code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

// @audit this isWithdrawalFinalized function is not implemented anywhere in deriving contracts.
function isWithdrawalFinalized(uint256 _l2BatchNumber, uint256 _l2MessageIndex) external view returns (bool);

```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L19)

(5):
```solidity
// code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

// @audit this l2Bridge function is not implemented anywhere in deriving contracts.
    function l2Bridge() external view returns (address);

```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L50)

Recommendations:
Implement unused interface functions above the deriving contracts.

### Low 11 - Missing getter functions for key internal `AppStorage` state variables. Some important state accounting cannot be queried directly.

**Instances(4)**

`AppStorage` is declared as an internal struct variable in Base.sol and its important struct member values need to be queried from Getters.sol.  

In Storage.sol, there are a few key `Appstorage` struct members that do not have any getter functions to query their states in Getters.sol or other facet contracts. This makes their values not accessible directly. 

Except for the (4) instances below, all other non-deprecated private variables have getter functions. For example, `governor` and `pending governor` have their getter functions but `admin` and `pending admin` do not have any getters. And user accounting like `totalDepositedAmountPerUser` needs to have a getter otherwise, since there is no event emitted for this variable value change either, as a result, the important accounting info cannot be queried.

(1):
```solidity
// code/contracts/ethereum/contracts/zksync/Storage.sol

    /// @dev A mapping user address => the total deposited amount by the user
    //@audit not implemented in Getters.sol
    mapping(address => uint256) totalDepositedAmountPerUser;
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Storage.sol#L133)

(2):
```solidity
// code/contracts/ethereum/contracts/zksync/Storage.sol
    /// @dev Address which will exercise non-critical changes to the Diamond Proxy (changing validator set & unfreezing)
    //@audit not implemented in Getters.sol
    address admin;
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Storage.sol#L143)

(3):
```solidity
// code/contracts/ethereum/contracts/zksync/Storage.sol
    /// @notice Address that the governor or admin proposed as one that will replace admin role
    //@audit not implemented in Getters.sol
    address pendingAdmin;
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Storage.sol#L145)

(4):
```solidity
// code/contracts/ethereum/contracts/zksync/Storage.sol
    /// @dev Indicates that the porter may be touched on L2 transactions.
    /// @dev Used as an input to zkp-circuit.
    //@audit not implemented in Getters.sol
    bool zkPorterIsAvailable;
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Storage.sol#L116)

Recommendations: 
Add corresponding getter functions in Getters.sol for the instances above.

### Low 12 - Merkle.sol library cannot handle an edge case of a large Merkle tree and will revert.

**Instances(1)**

Although in the context of L1Messenger.sol, only fixed-size merkle tree will be used in this context. However, as a general library contract, Merkle.sol has a flawed `require` statement, which might prevent it from being used in other contexts or in future implementations. 

In Merkle.sol - `calculateRoot()`, `uint256 _index` representing leaf index in a tree is passed and the proof `_path` length is passed as a dynamic `bytes32` array. Since `_index` allows maximum total 2^256 number of leaves/nodes, the maximum tree height would be 256, which means the maximum array size is 256 not 255. 

We see that in the `require` statements `_path` length is checked to be within (0,256), where it should be (0,256]. This will cause reverts when a Merkle tree is 256 in height, which requires 256 elements of `_path` array to be passed. 

```solidity
// code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
    function calculateRoot(
        bytes32[] calldata _path,
        uint256 _index,
        bytes32 _itemHash
    ) internal pure returns (bytes32) {
        uint256 pathLength = _path.length;
        require(pathLength > 0, "xc");
|>      require(pathLength < 256, "bt");
        require(_index < (1 << pathLength), "px");
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L25)

Recommendations: 
Change to `require(pathLength <= 256, "bt");`

### Low 13 - DefaultUpgrade.sol `upgrade()` has no access control, which requires an inefficient and potentially risky flow of system upgrade

**Instances(1)**

DefaultUpgrade.sol defines public `upgrade()` with no access control, which is a basic mechanism method for system upgrade. Although the expected flow of default system upgrade is to deploy a DefaultUpgrade.sol, set it as a facet for DiamondProxy, and initialize it by calling `upgrade()` atomically, removing the need for access control for `upgrade()`, it's still inefficient and potentially risky to have an `upgrade()` accessible to anyone.

Multiple transactions have to be done atomically which itself is gas-intensive and this process can be simplified. In the current flow, DefaultUpgrade.sol needs to be deployed first - not necessarily atomically since there is no risk of malicious initialization. Next, governance needs to invoke `executeUpgrade()` from Admin.sol, which requires the following steps: (1) add DefaultUpgrade.sol as a facet, then (2) invoke custom `upgrade()` logic through initialization, and then (3) remove DefaultUpgrade.sol from DiamondProxy to prevent the public from calling `upgrade()`. 

If we add access control (e.g. only allow governance) to `upgrade()`, the (1) and (3) steps above can be eliminated. This way we save gas on update flow and also simplify the calldata for `executeUpgrade()`. This reduces the risk of mistakes, for example, if DefaultUpgrade.sol is not removed at `executeUpgrade()` due to negligence, a malicious actor can call `upgrade()` to update key addresses on L1 directly.

```solidity
// code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol
    // @audit no access control, and `super.upgrade()` only check timestamps not msg.sender.
    function upgrade(ProposedUpgrade calldata _proposedUpgrade) public override returns (bytes32) {
        super.upgrade(_proposedUpgrade);
        _setNewProtocolVersion(_proposedUpgrade.newProtocolVersion);
        _upgradeL1Contract(_proposedUpgrade.l1ContractsUpgradeCalldata);
        _upgradeVerifier(_proposedUpgrade.verifier, _proposedUpgrade.verifierParams);
        _setBaseSystemContracts(_proposedUpgrade.bootloaderHash, _proposedUpgrade.defaultAccountHash);
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L25)

```solidity
// code/contracts/ethereum/contracts/zksync/facets/Admin.sol
    function executeUpgrade(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {
        Diamond.diamondCut(_diamondCut);
        emit ExecuteUpgrade(_diamondCut);
    }
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L98-L100)

In addition, if a custom upgrade needs to be performed (e.g. custom post-upgrade hook logic is required), a custom upgrade contract can be deployed when needed, in which case (1)&(3) steps cannot be eliminated. But in most cases, based on code doc in DefaultUpgrade.sol, (1)&(3) steps can be removed if we add access control to `upgrade()`.

Recommendations:
Add access control to DefaultUpgrade.sol to save gas and avoid risks of mistakes during `executeUpgrade()`. 

### Low 14 - Incorrect code comments in L1Messenger.sol

**Instances(1)**

In L1Messenger.sol - `publishPubdataAndClearState()`, there is an incorrect code comment on `compressedStateDiffSize`. `compressedStateDiffSize` is 3 bytes in call data, but in the code comments, it is mistakenly written `2 bytes total len of compressed`.

```solidity
// code/system-contracts/contracts/L1Messenger.sol

        /// Check State Diffs
        /// encoding is as follows:
        //@audit wrong comment header should include( 1 byte version, 3 bytes total len of compressed, ...)
|>      /// header (1 byte version, 2 bytes total len of compressed, 1 byte enumeration index size, 2 bytes number of initial writes)
        /// body (N bytes of initial writes [32 byte derived key || compressed value], M bytes repeated writes [enumeration index || compressed value])
        /// encoded state diffs: [20bytes address][32bytes key][32bytes derived key][8bytes enum index][32bytes initial value][32bytes final value]
...
        //@audit 3 bytes total len of compressed here
        uint24 compressedStateDiffSize = uint24(
            bytes3(
                _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 3]
            )
        );
        calldataPtr += 3;
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L281)

Recommendations:
Change the `len of compressed` from the comment into 3 bytes.

### Low 15 - Invalid validation - oversized number of logs can be passed to `publishPubdataAndClearState()`

**Instances(1)**

In L1Messenger.sol - `publishPubdataAndClearState()`, `numberOfL2ToL1Logs` is checked in `require` but this check is invalid, because `numberOfL2ToL1Logs <= numberOfL2ToL1Logs` will always evaluate to true.

```solidity
// code/system-contracts/contracts/L1Messenger.sol
    function publishPubdataAndClearState(
        bytes calldata _totalL2ToL1PubdataAndStateDiffs
    ) external onlyCallFromBootloader {
...
       //@audit this require will always return true since `numberOfL2ToL1Logs` will always equal itself.
|>      require(
            numberOfL2ToL1Logs <= numberOfL2ToL1Logs,
            "Too many L2->L1 logs"
        );
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L206)

Recommendations:
Consider changing it to `numberOfL2ToL1Logs <= numberOfLogsToProcess`.


### NC 01 - Consider adding comments for custom formula implementation, especially if it is optimized or modified from the original formula from the doc.

**Instances(1)**

In Mailbox.sol - `_deriveL2GasPrice()`, the price for L2 gas in ETH is derived from the formula `base_fee * gas_per_pubdata_limit = L1_gas_price * l1_gas_per_pubdata)` from the doc. The code implementation in `_deriveL2GasPrice()` differs from this exact formula and is optimized for rounding.

However, there is no code comment that directly refers to the original or derived formula used, neither does the comment mention that optimization is performed that deviates from the strict form of the original formula. 

```solidity
code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
    /// @notice Derives the price for L2 gas in ETH to be paid.
    /// @param _l1GasPrice The gas price on L1.
    /// @param _gasPricePerPubdata The price for each pubdata byte in L2 gas
    /// @return The price of L2 gas in ETH
    function _deriveL2GasPrice(uint256 _l1GasPrice, uint256 _gasPricePerPubdata) internal pure returns (uint256) {
        uint256 pubdataPriceETH = L1_GAS_PER_PUBDATA_BYTE * _l1GasPrice;
|>      uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;

        return Math.max(FAIR_L2_GAS_PRICE, minL2GasPriceETH);
    }
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L181)

Recommendations:
Adding relevant custom formulas in code comments increases code readability and increases efficiency for the review and auditing process. 

### NC 02 - Some long revert strings are not optimized and abbreviated as the rest of the code base (Note: Not submitted in automated findings)

**Instances(10)**

Throughout the code base, abbreviated revert strings are used for gas optimization (e.g. "pz", "xx", etc). But there are a few instances, long revert strings - complete sentences are reverted. Consider optimizing these long strings in the same manner as the rest of the code base.

Note this issue is different from [G57](https://raw.githubusercontent.com/code-423n4/2023-10-zksync/main/bot-report.md) which advices to convert all revert strings to errors.

```solidity
// code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

        require(_l2WethAddress != address(0), "L2 WETH address cannot be zero");
        require(_governor != address(0), "Governor address cannot be zero");
        require(_factoryDeps.length == 2, "Invalid factory deps length provided");
        require(
            msg.value == _deployBridgeImplementationFee + _deployBridgeProxyFee,
            "Miscalculated deploy transactions fees"
        );
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L90-L95)

```solidity
// code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

        revert("Method not supported. Failed deposit funds are sent to the L2 refund recipient address.");
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L223)

```solidity
// code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

        require(_message.length == 96, "Incorrect ETH message with additional data length");
...
        require(
            bytes4(functionSignature) == IMailbox.finalizeEthWithdrawal.selector,
            "Incorrect ETH message function selector"
        );
...
        require(l1EthReceiver == address(this), "Wrong L1 ETH withdraw receiver");
...
        require(l2Sender == l2Bridge, "The withdrawal was not initiated by L2 bridge");
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L279)
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L282-L284)
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L289)
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L295)

```solidity
// code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

        require(!isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "Withdrawal is already finalized");

```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L240)

### NC 03 - Weth deposit might still be submitted through L1ERC20Bridge.sol due to no explicit reverts, resulting in user waste of gas on L1.

**Instances(1)**

L1ERC20Bridge.sol cannot be used to bridge Weth, due to no inherent unwrap methods and handling of ETH deposit from L1-L2. 

However, there are no explicit reverts to prevent users from depositing Weth through L1ERC20Bridge.sol. As a result, Users can submit deposit of Weth through L1ERC20Bridge.sol -`deposit()`. And user's transaction will not be reverted unless Weth's deposit limit(`IAllowList.Deposit`) is explicitly set. If no deposit limit for Weth is set by the admin. The user's deposit transaction will go through and gas will be paid. But the user's request will not be honored on L2 due to not following the Weth-> eth ->Weth flow, then the user would have to go through `claimFailedDeposit()` to recover funds, which will pay L1 gas a second time.

```solidity
// code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

    function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        uint256 _l2TxGasLimit,
        uint256 _l2TxGasPerPubdataByte,
        address _refundRecipient
    ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) {
        require(_amount != 0, "2T"); // empty deposit amount
        uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
        require(amount == _amount, "1T"); // The token has non-standard transfer logic
        // verify the deposit amount is allowed
        _verifyDepositLimit(_l1Token, msg.sender, _amount, false);

        bytes memory l2TxCalldata = _getDepositL2Calldata(msg.sender, _l2Receiver, _l1Token, amount);
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L188)

```solidity
// code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

    /// @dev Verify the deposit limit is reached to its cap or not
    function _verifyDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal {
        IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token);
|>      if (!limitData.depositLimitation) return; // no deposit limitation is placed for this token
...
```
(https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L342)

Recommendations:
`IAllowList.Deposit` has to be set explicitly for Weth token as 0 to prevent accidental Weth deposit on L1ERC20Bridge.sol, this increases dependencies on admin and creates risks where the limit is not set for Weth. 

Since L1 Weth address is known, consider explicitly rejecting weth deposit in deposit() to cleanly divert the Weth deposit process, which removes dependencies on the admin for basic contract behaviors.

