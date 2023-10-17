G1. There is no need to check the second condition ``s.l2SystemContractsUpgradeBatchNumber != 0`` since it will be checked inside _commitBatchesWithSystemContractsUpgrade() anyway. 

[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L189](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L189)

G2. _processL2Logs() calls _checkBit() and then _setBit() to process each log. One can implement _checkAndsetBit() to combine both to save gas since we do not need to calculate mask twice and also use only one function call.

```javascipt
   function _checkAndSetBit(uint256 _bitMap, uint8 _index) internal pure returns (bool) {
        return (_bitMap & (1 << _index)) > 0;
    }

    /// @notice Sets the given bit in {_num} at index {_index} to 1.
    function _checkAndSetBit(uint256 _bitMap, uint8 _index) internal pure returns (uint256) {
        uint256 mask = 1 << _index;

        require(_bitMap & mask == 0, "bp");

        return _bitMap | mask; 
    }