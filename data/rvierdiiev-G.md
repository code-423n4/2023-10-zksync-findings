## G-01. Remove unneeded line
## Description
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L141-L142
```solidity
uint256 txSlotOverhead = Math.ceilDiv(batchOverheadGas, MAX_TRANSACTIONS_IN_BATCH);
batchOverheadForTransaction = Math.max(batchOverheadForTransaction, txSlotOverhead);
```

This code can be simplified to `batchOverheadForTransaction = Math.ceilDiv(batchOverheadGas, MAX_TRANSACTIONS_IN_BATCH);`, because `batchOverheadForTransaction` is always 0 at that point, which means that `txSlotOverhead` will be always bigger or equal than it. Also `txSlotOverhead` is never used later.
So you can save gas by changing those 2 lines by 1 only.