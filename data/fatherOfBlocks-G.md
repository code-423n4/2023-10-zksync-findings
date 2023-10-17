**ethereum/contracts/zksync/facets/Executor.sol**
- L13 - L2_KNOWN_CODE_STORAGE_SYSTEM_CONTRACT_ADDR is imported, but it is not necessary since it is not used on the contrary.

- L22 - The getName constant is created, but is never used.


**ethereum/contracts/zksync/facets/Base.sol**
- L9 - Ownable is imported but never used, therefore it should be removed.


**ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol**
- L61 - The UpgradeComplete event is created, but it is not necessary since it is not used on the contrary.


**system-contracts/contracts/Compressor.sol**
- L12/13/15/16/17/18/19/20 - INITIAL_WRITE_STARTING_POSITION, COMPRESSED_INITIAL_WRITE_SIZE, STATE_DIFF_ENUM_INDEX_OFFSET, STATE_DIFF_FINAL_VALUE_OFFSET, STATE_DIFF_DERIVED_KEY_OFFSET, DERIVED_KEY_LENGTH, VALUE_LENGTH, ENUM_INDEX_LENGTH are imported, but are not necessary as they are not used on the contrary.


**system-contracts/contracts/KnownCodesStorage.sol**
- L8 - SystemContractHelper is imported, but it is not necessary since it is not used on the contrary.
