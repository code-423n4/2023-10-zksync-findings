## [G-01] Multiplication or division by 2 should use bit shifting
**Description**
```2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L97-L97```
x * 2 is equal to x << 1 and x / 2 is equal to x >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas.
**Remediation**
**Bad**
```sol
// 2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L97-L97
vInt += 8 + block.chainid * 2;
```
**Fix**
```sol
// 2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L97-L97
vInt += 8 + block.chainid << 2;
```
It is recommended to use left and right shift instead of multiplying and dividing by 2 to save some gas.