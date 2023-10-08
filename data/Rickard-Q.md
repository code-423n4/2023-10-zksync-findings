# [L-01] Floating pragma
## Lines of code
All 39 contracts.
## Impact
Contracts should be deployed with the same compiler version and flags that they have been tested thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.       
[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103).      
Floating Pragma example:
````solidity
code/contracts/ethereum/contracts/zksync/facets/Executor.sol
1  // SPDX-License-Identifier: MIT
2  
3: pragma solidity ^0.8.13;
4
````
## Tools Used
Manual Review
## Recommended Mitigation Steps
Lock the pragma version and also consider known bugs ([https://github.com/ethereum/solidity/releases](https://github.com/ethereum/solidity/releases)) for the compiler version that is chosen.