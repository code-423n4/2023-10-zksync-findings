QA1. The constructor of ``AllowList`` fails to pass arguments to the base contract ``Ownable``.
Similarly, the constructor of ``ValidatorTimeLock``  fails to pass arguments to the base contract ``Ownable``.
Similarly, the constructor of ``Governance``  fails to pass arguments to the base contract ``Ownable``.


[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L31-L33](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L31-L33)

Mitigation:

```diff
- constructor(address _initialOwner) {
+ constructor(address _initialOwner) Ownable(_initialOwner) {

-        _transferOwnership(_initialOwner);
    }
```

QA2. When the argument ``_refundRecipient = address(0)``, _requestL2Transaction()._requestL2Transaction() will use ``_sender`` as the refundRecipient. The problem is that _requestL2Transaction() might apply AddressAliasHelper.AddressAliasHelperapplyL1ToL2Alias(_sender) again even though _sender is already the result of AddressAliasHelper.AddressAliasHelperapplyL1ToL2Alias(msg.sender) (see L249). 

The mitigation is that we do not need to apply L1ToL2Alisas translation when _refundRecipient == _sender.

```diff

        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
        s.totalDepositedAmountPerUser[_depositor] += _amount;
    }

    function _requestL2Transaction(
        address _sender,
        address _contractAddressL2,
        uint256 _l2Value,
        bytes calldata _calldata,
        uint256 _l2GasLimit,
        uint256 _l2GasPerPubdataByteLimit,
        bytes[] calldata _factoryDeps,
        bool _isFree,
        address _refundRecipient
    ) internal returns (bytes32 canonicalTxHash) {
        require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "uj");
        uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast
        uint256 txId = s.priorityQueue.getTotalPriorityTxs();

        // Here we manually assign fields for the struct to prevent "stack too deep" error
        WritePriorityOpParams memory params;

        // Checking that the user provided enough ether to pay for the transaction.
        // Using a new scope to prevent "stack too deep" error
        {
            params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);
            uint256 baseCost = params.l2GasPrice * _l2GasLimit;
            require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost
        }

        // If the `_refundRecipient` is not provided, we use the `_sender` as the recipient.
        address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient;
        // If the `_refundRecipient` is a smart contract, we apply the L1 to L2 alias to prevent foot guns.
-        if (refundRecipient.code.length > 0) {
+        if (_refundRecipient != _sender && refundRecipient.code.length > 0) {
            refundRecipient = AddressAliasHelper.applyL1ToL2Alias(refundRecipient);
        }

        params.sender = _sender;
        params.txId = txId;
        params.l2Value = _l2Value;
        params.contractAddressL2 = _contractAddressL2;
        params.expirationTimestamp = expirationTimestamp;
        params.l2GasLimit = _l2GasLimit;
        params.l2GasPricePerPubdata = _l2GasPerPubdataByteLimit;
        params.valueToMint = msg.value;
        params.refundRecipient = refundRecipient;

        canonicalTxHash = _writePriorityOp(params, _calldata, _factoryDeps);
    }
```

Q3. Mailbox._proveL2LogInclusion() fails to ensure that ``_batchNumber < s.totalBatchesExecuted``. This is important since the largest ``_batchNumber`` that can be verified is ``s.totalBatchesExecuted - 1``. 

Mitiation: 

```diff
function _proveL2LogInclusion(
        uint256 _batchNumber,
        uint256 _index,
        L2Log memory _log,
        bytes32[] calldata _proof
    ) internal view returns (bool) {
        console2.log("\n _proveL2LogInclusion() started....");

-        require(_batchNumber <= s.totalBatchesExecuted, "xx");
+        require(_batchNumber < s.totalBatchesExecuted, "xx");

        bytes32 hashedLog = keccak256(
            abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBatch, _log.sender, _log.key, _log.value)
        );
        // Check that hashed log is not the default one,
        // otherwise it means that the value is out of range of sent L2 -> L1 logs
        require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");

        // It is ok to not check length of `_proof` array, as length
        // of leaf preimage (which is `L2_TO_L1_LOG_SERIALIZE_SIZE`) is not
        // equal to the length of other nodes preimages (which are `2 * 32`)

        bytes32 calculatedRootHash = Merkle.calculateRoot(_proof, _index, hashedLog);
        bytes32 actualRootHash = s.l2LogsRootHashes[_batchNumber];

        console2.log("\n completed with %d", actualRootHash == calculatedRootHash");
        return actualRootHash == calculatedRootHash;
    }
```
