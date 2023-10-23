## 1. There is no need to re-publish the preimage of the bytecode when deploying a same contract by L1 tx

code lines: https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L389-L396

Whenever you use an L1 transaction to deploy bytecode, the `mailbox._writePriorityOp` method always publishs the preimage of the `_factoryDeps` parameter.


```solidity
        emit NewPriorityRequest(
            _priorityOpParams.txId,
            canonicalTxHash,
            _priorityOpParams.expirationTimestamp,
            transaction,
            _factoryDeps
        );
```

However, this is unnecessary and can consume a significant amount of gas. Just ensure that the hash of each deployed bytecode is recorded, and redundant bytecode doesn't need to be re-published. 

## 2. A one-time priority gas limit on L1 tx will result in the operator overcharging gas for it

code lines: https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L180-L185

If a one-time Priority Gas is set when sending an L1 transaction (e.g., through Flashbots or some special validator fees), the gas price on L2 will also be set very high, even if the operator doesn't need to use such a high gas price to submit the pubdata. This constitutes an overcharge for gas.


```solidity
params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);

function _deriveL2GasPrice(uint256 _l1GasPrice, uint256 _gasPricePerPubdata) internal pure returns (uint256) {
    uint256 pubdataPriceETH = L1_GAS_PER_PUBDATA_BYTE * _l1GasPrice;
    uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;

    return Math.max(FAIR_L2_GAS_PRICE, minL2GasPriceETH);
}
```

## 3. The numberOfL2ToL1Logs verification condition is incorrect

codelines:  https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/L1Messenger.sol#L206


```solidity
        uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
        require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
```

The above check will always be True.

It should be `numberOfL2ToL1Logs <= L2_TO_L1_LOGS_MERKLE_TREE_LEAVES`

 

## 4. If the _newProtocolVersion parameter of the _setNewProtocolVersion function is set to MAX_UINT256, the DefaultUpgradecontract will no longer be available

codelines: https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L214-L219

There is a check for ` _newProtocolVersion` at the beginning of the `_newProtocolVersion` function:


```solidity
    function _setNewProtocolVersion(uint256 _newProtocolVersion) internal {
        uint256 previousProtocolVersion = s.protocolVersion;
        require(
            _newProtocolVersion > previousProtocolVersion,
            "New protocol version is not greater than the current one"
        );
```

So if the `_newProtocolVersion`  is set to `0xffff…ffff(MAX_UINT256)` in an upgrade, the `DefaultUpgradecontract` will no longer be available. Because the `DefaultUpgrade.upgrade` function calls the `_setNewProtocolVersion` function at the beginning. It can’t be skipped.