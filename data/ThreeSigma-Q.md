## Low

[LOW-1] Wrong event is being emitted 
When setting a new `pendingAdmin` the event that is being emitted [here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L49) is `NewPendingGovernor(oldPendingAdmin, _newPendingAdmin)` (the wrong event) instead of the already defined [`NewPendingAdmin`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L45) event.



[LOW-2] Incorrect condition in `require`
In `L1Messenger.sol` in [line 206](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L206) there is a require that is comparing one variable to itself, therefore it will always be true and nothing is being verified. Thus, the condition of “Too many L2→L1 logs” of the function is not being properly checked.

## Non-Critical Issues

[N-1] Implement function to convert from `uint256` to `address`
Throughout the codebase, there are several instances of an `uint256` being converted to an `address` by using `address(uint160(uint256(x)))`, where `x` is the value being converted ([example 1](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L71), [example 2](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L106), [example 3](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L122), [example 4](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L134)). This conversion is used enough times to justify the implementation of a function that does this, thus mitigating the risk of a mistake by always having to write the conversion.

[N-2] Confusing function names in different files
There are function with very similar names in  [`TransactionHelper.sol`](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol) and [`BootloaderUtilies.sol`](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol). For example, in `BootloaderUtilies.sol` there is a function called [`encodeLegacyTransactionHash`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/BootloaderUtilities.sol#L44C14-L44C41) whereas in `TransactionHelper.sol` there is a function with the name [`_encodeHashLegacyTransaction`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#L147) - these functions also share very similar descriptions. It would cause less confusion for these names to be more distinct. 

[N-3] Unusual for a struct to be placed in the middle of a contract
Although not included in the Solidity Style Guide, it is unusual for structs to appear between functions, instead they should be declared in the beginning of the contract. This happens in the `ContractDeployer.sol` file, [here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L197-L209). 

[N-4] Consider adding events to state changing function
In the `SystemContext.sol` contract, events are not being emitted when setting a new transaction origin [here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L88) nor when setting a new gas price [here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L94). Consider adding events to these state changing functions.

[N-5] Unused variable
In `SystemContext.sol` a new variable `prevBatchHash` is created but is never used [here](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L385), so it should be deleted.