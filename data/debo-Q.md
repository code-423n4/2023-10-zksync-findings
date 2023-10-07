## [L-01] Massive number lengths
The SystemContext contract has a very large number in length.
The number is 2500000000000000 within line 44.
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
**Remediation**
Use the scientific notation value of 25e14 instead.