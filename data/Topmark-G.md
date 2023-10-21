### Report 1:
Using expected result of calculations from constant values would save gas instead of carrying out the calculation at every function call in the L1Messenger contract.
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L51-L60
```solidity
 /// The gas cost of processing one keccak256 round.
    uint256 internal constant KECCAK_ROUND_GAS_COST = 40;

    /// The number of bytes processed in one keccak256 round.
    uint256 internal constant KECCAK_ROUND_NUMBER_OF_BYTES = 136;

    /// The gas cost of calculation of keccak256 of bytes array of such length.
    function keccakGasCost(uint256 _length) internal pure returns (uint256) {
        return KECCAK_ROUND_GAS_COST * (_length / KECCAK_ROUND_NUMBER_OF_BYTES + 1);
    }
```
The code above shows how KeccakGasCost is calculated, the only variable that is not constant is the "_length" parameter
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L93
```solidity
 function sendL2ToL1Log(
        ...
    ) external onlyCallFromSystemContract returns (uint256 logIdInMerkleTree) {
        ...
---    uint256 gasToPay = keccakGasCost(L2_TO_L1_LOG_SERIALIZE_SIZE) + 3 * keccakGasCost(64);
---        SystemContractHelper.burnGas(Utils.safeCastToU32(gasToPay));

+++        SystemContractHelper.burnGas(Utils.safeCastToU32(160));
```
The code above shows how keccakGasCost(...) function is called to calculate "gasToPay" both calls are also using constant values, "L2_TO_L1_LOG_SERIALIZE_SIZE = 88" from [IL1Messenger Interface](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IL1Messenger.sol#L25) and "64" for the second call, this shows the expected output can be accurately calculated and the value used without the need to call the function over and over again using gas.
The value of uint256 gasTopay would equal 40 + 3 * 40 = 160.
160 can be easily used directly as corrected in the code above.
Note: A detailed comment description should be provided instead to show how "gasToPay" was calculated as 160.
###  Report 2:
Calling Math.max in comparison to variable that has not been assign any value from getOverheadForTransaction(...) function in the TransactionValidator library takes up unnecessary gas.
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L137-L146
```solidity
   function getOverheadForTransaction(
       ...
>>> ) internal pure returns (uint256 batchOverheadForTransaction) {
        uint256 batchOverheadGas = BATCH_OVERHEAD_L2_GAS + BATCH_OVERHEAD_PUBDATA * _gasPricePerPubdata;

        // The overhead from taking up the transaction's slot
        uint256 txSlotOverhead = Math.ceilDiv(batchOverheadGas, MAX_TRANSACTIONS_IN_BATCH);
        batchOverheadForTransaction = Math.max(batchOverheadForTransaction, txSlotOverhead);

        // The overhead for occupying the bootloader memory can be derived from encoded_len
        uint256 overheadForLength = Math.ceilDiv(_encodingLength * batchOverheadGas, BOOTLOADER_TX_ENCODING_SPACE);
---     batchOverheadForTransaction = Math.max(batchOverheadForTransaction, overheadForLength);
+++     batchOverheadForTransaction = overheadForLength;
        ...
```
As corrected in the code above since batchOverheadForTransaction will definitely be zero and max would always return equivalent of overheadForLength. Therefore simply assigning it directly would save up gas.
###  Report 3:
Repetition of same function call would use excess gas
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L335-L336
```solidity
     function setL2Block(
        ...
    ) external onlyCallFromBootloader {
        ...
        if (currentL2BlockNumber == 0 && currentL2BlockTimestamp == 0) {
            // Since currentL2BlockNumber and currentL2BlockTimestamp are zero it means that it is
            // the first ever batch with L2 blocks, so we need to initialize those.
            _upgradeL2Blocks(_l2BlockNumber, _expectedPrevL2BlockHash, _isFirstInBatch);

            _setNewL2BlockData(_l2BlockNumber, _l2BlockTimestamp, _expectedPrevL2BlockHash);
        ...
```
from the code above _upgradeL2Blocks(...) &  _setNewL2BlockData(...) is called when the if condition holds true
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L226
```solidity
    function _upgradeL2Blocks(uint128 _l2BlockNumber, bytes32 _expectedPrevL2BlockHash, bool _isFirstInBatch) internal {
        require(_isFirstInBatch, "Upgrade transaction must be first");

        // This is how it will be commonly done in practice, but it will simplify some logic later
        require(_l2BlockNumber > 0, "L2 block number is never expected to be zero");

        unchecked {
            bytes32 correctPrevBlockHash = _calculateLegacyL2BlockHash(uint128(_l2BlockNumber - 1));
            require(correctPrevBlockHash == _expectedPrevL2BlockHash, "The previous L2 block hash is incorrect");

            // Whenever we'll be queried about the hashes of the blocks before the upgrade,
            // we'll use batches' hashes, so we don't need to store 256 previous hashes.
            // However, we do need to store the last previous hash in order to be able to correctly calculate the
            // hash of the new L2 block.
---        _setL2BlockHash(uint128(_l2BlockNumber - 1), correctPrevBlockHash);// it is not needed here as it would be called again by _setNewL2BlockData(...)
        }
    }
```
As described in the comment above and below _setL2BlockHash(...) is called twice with same variables which would take up unnecessary gas.
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L290
```solidity
   function _setNewL2BlockData(uint128 _l2BlockNumber, uint128 _l2BlockTimestamp, bytes32 _prevL2BlockHash) internal {
        // In the unsafe version we do not check that the block data is correct
        currentL2BlockInfo = BlockInfo({number: _l2BlockNumber, timestamp: _l2BlockTimestamp});

        // It is always assumed in production that _l2BlockNumber > 0
>>>       _setL2BlockHash(_l2BlockNumber - 1, _prevL2BlockHash);// due to correctPrevBlockHash validation at _upgradeL2Blocks(...), it value would be same as _prevL2BlockHash

        // Reseting the rolling hash
        currentL2BlockTxsRollingHash = bytes32(0);
    }
```
Note: The repetition should be removed only in _upgradeL2Blocks(...) as it has only one use case in the library while _setNewL2BlockData(...) has two use cases in the library
