# 1) Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol?plain=1#L303
```
        if (batchWhenUpgradeHappened != 0 && batchWhenUpgradeHappened <= newTotalBatchesExecuted) {
```


# Recommended Mitigation Steps

```
         if (batchWhenUpgradeHappened != 0) {
           if (batchWhenUpgradeHappened <= newTotalBatchesExecuted) {
               ...
           }
         } 
```



# 2) Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol?plain=1#L123
```
        for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
```

## Recommended Mitigation Steps
```solidity
uint256 len = emittedL2Logs.length;
for (uint256 i = 0; i < len; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE) {
...
}
```

