## FOR LOOPS GAS OPTIMIZATION

### Description

The following for loop structure is very common in the code base. 
I'll start with this [example](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L123):

~~~
for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
}
~~~

The loop starts by initializing the variable i to zero. However, if a variable is not initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address, etc.). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

Additionally, reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack. Caching the array length in the stack saves around 3 gas per iteration.


### Recomendation
Update the loop structure to the following:

~~~
uint256 emittedL2LogsLength = emittedL2Logs.length;
for (uint256 i; i < emittedL2LogsLength; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
}
~~~

Other instances:
https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L209

https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L241

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L395

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L100

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L85

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L116