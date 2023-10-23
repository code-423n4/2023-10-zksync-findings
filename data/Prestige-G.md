Gas Report

Contract 1
Executor.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

1) line 363: There is a redundant verify proof in the contract as there is no else block, so the same proof ends up being checked twice
2) isOperation function is only used once so you can inline it

3) line 234: !x is cheaper than a boolean comparison to 0 (==)
4) line 309: useing unstrict comparisons (=>) is cheaper than strict comparisons (>) can use s.totalBatches >= newLastbatch + 1 instead
5) line 241: You can save gas by caching the array length in memory instead of looking it up at every loop iteration
6) line 123: You can save gas by caching the array length in memory instead of looking it up at every loop iteration
7) line 22: You can make constants immutable
8) line 185: != is cheaper than > 

Contract 2:
L1WethBridge.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

1) line 279: use short message strings, each bytes32 can store up to 32 characters which this error string exceeds, by reducusing the error string length you can save gas

Contract 3:
BaseZKsyncupgrade.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

1) line 204: _factoryDeps.length can be cached into memory instead of looking up the length at every loop iterations
2) line 224: using !x instead of x == 0 is cheaper
3) line 235: using !x instead of x == 0 is cheaper

Contract 4: 
L1Messenger.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol

1) line 113: Pre increment (++i) is less expensive than post increment (i++)
2) line 292, 299, 306: (x = x + y) is cheaper than (x += y)
3) line 295: Pre increment (++i) is less expensive than post increment (i++)
4) line 52, 55, 63, 66: Can use immuatble instead of contstant 


Contract 5:
Mailbox.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

1) line 149: Redundant Return Statements: If a function has named return variables and also includes an explicit return statement, it can result in both redundancy and extra gas costs.



Contract 6:
Diamond.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

1) line 130, 154, 174: It is cheaper to do require checks before the expensive storage read incase the function reverts.


Contract 7: 
Getters.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol

1) line 19: constant can be made immutable


Contract 8: 
TransactionValidator.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol

1) lines 83, 86, 98:(x = x + y) is cheaper than (x += y)
2) line 92: No need to set a value to 0 as variables default to 0


Contract 9:
ValidatorTimelock.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

1) line 85: _newBatchesData.length can be cached into memory instead of looking up the length at every loop 
2) line 116: _newBatchesData.length can be cached into memory instead of looking up the length at every loop 

Contract 10
Admin.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol

1) line 20, 23, 26: can make constant values immuatable 

Contract 11
BridgeInitializationHelper.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol

1) line 18 and 21: Constant values could be made immutable 




