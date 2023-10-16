# [G-01] Using `private` rather than `public` for constants, saves gas
When you declare a constant variable as public, Solidity generates a getter function that allows anyone to read the value of the constant. This getter function can consume gas, especially if the constant is read frequently or the contract is called by multiple users.

By using private instead of public for constants, you can prevent the generation of the getter function and reduce the overall gas cost of your contract.
````solidity
code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
24:        string public constant override getName = "ValidatorTimelock";
````
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24)
````solidity
code/contracts/ethereum/contracts/zksync/facets/Admin.sol
15:        string public constant override getName = "AdminFacet";
````
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15)
````solidity
code/contracts/ethereum/contracts/zksync/facets/Executor.sol
22:    string public constant override getName = "ExecutorFacet";
````
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22)
````solidity
code/contracts/ethereum/contracts/zksync/facets/Getters.sol
19:    string public constant override getName = "GettersFacet";
````
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19)
````solidity
code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
41:        string public constant override getName = "MailboxFacet";
````
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbo