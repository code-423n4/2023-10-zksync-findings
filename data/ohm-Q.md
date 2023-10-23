# Redundant conditional code in opcodes 
link:https://github.com/matter-labs/era-zkevm_circuits/blob/4fba537ccecc238e2da9c80844dc8c185e42466f/src/main_vm/opcodes/mod.rs#L162-L164
1. The code in the else block doesn't run anyways cause if condition is always going to be true since the variable is constant with 15 always.
```rust
            } else {
                let zero_num = Num::zero(cs);
                Num::enforce_equal(cs, &zero_num, &intermidiate_overflow.into_num());
            }
```
2. Use constants instead of plain numbers while dividing array into chunks in sha256 and keccak crates.

3. Should try to use variant Boolean checks for shift left instead of using Boolean opcode check.Since its return is true for any variant of SHL.


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

# Contracts files should define a locked compiler version and latest version of the compiler
Contract files should specify a locked compiler version and the latest available version of the compiler. Contracts should be deployed using the same compiler version and flags with which they have been thoroughly tested. It is advisable to use the most recent compiler versions to reduce gas fees and benefit from bug fixes. Locking the pragma version helps ensure that contracts are not inadvertently deployed with outdated compiler versions, which could introduce bugs and negatively impact the contract system.
```
pragma solidity ^0.8.13;
```
