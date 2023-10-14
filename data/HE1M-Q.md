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

### Q4
This finding indicates that when `currentL2BlockNumber` is equal to 0 and `currentL2BlockTimestamp` is also 0, two functions, namely `_upgradeL2Blocks` and `_setNewL2BlockData`, invoke the `_setL2BlockHash` function.
https://github.com/code-423n4/2023-10-zksync/blob/24b4b0c1ea553106a194ef36ad4eb05b3b50275c/code/system-contracts/contracts/SystemContext.sol#L331
https://github.com/code-423n4/2023-10-zksync/blob/24b4b0c1ea553106a194ef36ad4eb05b3b50275c/code/system-contracts/contracts/SystemContext.sol#L226C13-L226C28
https://github.com/code-423n4/2023-10-zksync/blob/24b4b0c1ea553106a194ef36ad4eb05b3b50275c/code/system-contracts/contracts/SystemContext.sol#L290
This redundancy can be optimized by eliminating the `_setL2BlockHash` function call within the `_upgradeL2Blocks` function.
https://github.com/code-423n4/2023-10-zksync/blob/24b4b0c1ea553106a194ef36ad4eb05b3b50275c/code/system-contracts/contracts/SystemContext.sol#L226

### Q5
The comment concerning the `isETHCall` parameter is currently positioned below the `gasPerPubdata` section. To enhance clarity and avoid confusion, it is advisable to move line 550 to line 549.
https://github.com/code-423n4/2023-10-zksync/blob/24b4b0c1ea553106a194ef36ad4eb05b3b50275c/code/system-contracts/bootloader/bootloader.yul#L550

### Q6
To be more efficient, the following change is recommended, because the initial value of `ret` is zero.
```
ret := overheadForCircuits
```
https://github.com/code-423n4/2023-10-zksync/blob/7ed3944429f437a611c32e782a12b320f6a67c17/code/system-contracts/bootloader/bootloader.yul#L1841

### Q7
The comment needs correction. It should be revised to the following:
```
However, the default account **cannot** initiate a transaction ...
```
https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/DefaultAccount.sol#L124C52-L124C107

### Q8
The comment requires an update. It should be corrected to the following format, as the comment currently suggests that the length of the compressed data is stored in 2 bytes, whereas the code implementation actually uses 3 bytes.
```
**2** bytes total len of compressed
```
https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/L1Messenger.sol#L281C37-L281C68
https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/L1Messenger.sol#L291

### Q9
The modifier `onlySystemCall` is redundant as `msg.sender` is enforced to be `DEPLOYER_SYSTEM_CONTRACT`:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L135-L136

### Q10
Renaming from `Block` to `Batach` is recommended:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L131
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L83
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L373
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L455