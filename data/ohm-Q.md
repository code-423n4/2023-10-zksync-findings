# Input validations should be performed at the starting of the functions.

checking input validations at the starting of the function will helps to reduce the gas usage and to improve the code redability.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L327

The require statement in the `proveBatches` function can be changed to the starting of the function so that if the require statement fails then the function revert without executing all the initializations. And it also helps the code quality. 
