# Input validations should be performed at the starting of the functions.

checking input validations at the starting of the function will helps to reduce the gas usage and to improve the code redability.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L327

The require statement in the `proveBatches` function can be changed to the starting of the function so that if the require statement fails then the function revert without executing all the initializations. And it also helps the code quality. 


# Improper variable names

It is recommended to use variable names that reflect the intended usage and meaning of the variables. Using variable names that lack meaning can make code difficult to  readability and understand.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L250
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L239

# All state changing methods Should emit events

It is always a best practice to emit events when the state changes.It helps to follow the current state.
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L177
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L291
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L311
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L398

