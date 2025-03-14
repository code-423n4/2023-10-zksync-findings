## [G-01] Multiplication or division by 2 should use bit shifting
**Description**
```system-contracts/contracts/BootloaderUtilities.sol#L97-L97```
x * 2 is equal to x << 1 and x / 2 is equal to x >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas.
**Remediation**
**Bad**
```sol
// system-contracts/contracts/BootloaderUtilities.sol#L97-L97
vInt += 8 + block.chainid * 2;
```
**Fix**
```sol
// system-contracts/contracts/BootloaderUtilities.sol#L97-L97
vInt += 8 + block.chainid << 2;
```
Use << instead of multiplication star and and use >> instead of division forward slash to save gas.

## [G-02] Either use a named returns value or return a value
**Description**
Either use a named returns value or return a value.
There is no need to do both as it waists gas.
**Location**
```txt
system-contracts/contracts/NonceHolder.sol#L125-L130
```
**Bad**
```sol
function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {
        uint256 addressAsKey = uint256(uint160(_address));
        (deploymentNonce, ) = _splitRawNonce(rawNonces[addressAsKey]);
        return deploymentNonce;
    }
```
**Fix**
```sol
function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {
        uint256 addressAsKey = uint256(uint160(_address));
        (deploymentNonce, ) = _splitRawNonce(rawNonces[addressAsKey]);
    }
```
## [G-03] Use bytes constants instead of strings as they use less gas
```txt
ethereum/contracts/zksync/ValidatorTimelock.sol#L24-L24
The getName string constant should instead utilise bytes32 constant to save gas.
```
```txt
ethereum/contracts/zksync/facets/Mailbox.sol#L41-L41
The getName string constant should instead utilise bytes32 constant to save gas.
```
```txt
ethereum/contracts/zksync/facets/Admin.sol#L15-L15
The getName string constant should instead utilise bytes32 constant to save gas.
```
```txt
ethereum/contracts/zksync/facets/Getters.sol#L19-L19
The getName string constant should instead utilise bytes32 constant to save gas.
```
```txt
ethereum/contracts/zksync/facets/Executor.sol#L22-L22
The getName string constant should instead utilise bytes32 constant to save gas.
```