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

### Low 10 - Unused Interface Functions

**Instances(1)**

Interface functions are declared but never implemented in deriving contracts. This is unnecessary code.
There are multiple instances of this issue in different contracts:

(1):
```solidity


```





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





 
