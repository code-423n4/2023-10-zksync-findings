# Lines of code

https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/Compressor.sol#L54

https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/KnownCodesStorage.sol#L28

# Vulnerability details

## Impact
There are 2 ways to publish bytecode, compress it and then publish the compressed bytecode or punlish the raw bytecode on L1.

The zksync invariant states that 
```
Contract with bytecode that have already been published to L1 once is not needed to be published again . 
Whether its compressed or Raw, only 1 time is needed.
```

so we think that if we can compress & publish it then we don't really need the raw bytecode publishing option 

## Tools Used
manual review
## Recommended Mitigation Steps
reduce code complexity and remove the raw bytecode publishing option