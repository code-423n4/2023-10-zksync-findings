### Q1
Better to rename `newBlockInfo` to `newBatchInfo`.
https://github.com/code-423n4/2023-10-zksync/blob/24b4b0c1ea553106a194ef36ad4eb05b3b50275c/code/system-contracts/contracts/SystemContext.sol#L431C26-L431C39

### Q2
The comment should change to `COMPRESSED_BYTECODES_BEGIN_SLOT + 32`:
https://github.com/code-423n4/2023-10-zksync/blob/24b4b0c1ea553106a194ef36ad4eb05b3b50275c/code/system-contracts/bootloader/bootloader.yul#L292

### Q3
The document is not reflecting the same structure defined in Bootloader:
https://github.com/code-423n4/2023-10-zksync/blob/24b4b0c1ea553106a194ef36ad4eb05b3b50275c/code/system-contracts/bootloader/bootloader.yul#L367
https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md#transactions-meta-descriptions