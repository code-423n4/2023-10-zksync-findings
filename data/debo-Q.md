## [L-01] LONG NUMBER LITERALS
Solidity supports multiple rational and integer literals, including decimal fractions and scientific notations. 
The use of very large numbers with too many digits was detected in the code that could have been optimised using a different notation also supported by Solidity.
The value 2500000000000000 was detected on line 44.
**Location**
**Bad**
```sol
// 2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L44-L44
uint256 public difficulty = 2500000000000000;
```
**Fix**
```sol
// 2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L44-L44
uint256 public difficulty = 25e14;
```