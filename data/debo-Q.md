## [L-01] Massive number lengths
The SystemContext contract has a very large number in length.
The number is 2500000000000000 within line 44.
**Location**
**Bad**
```sol
// system-contracts/contracts/SystemContext.sol#L44-L44
uint256 public difficulty = 2500000000000000;
```
**Fix**
```sol
// system-contracts/contracts/SystemContext.sol#L44-L44
uint256 public difficulty = 25e14;
```
**Remediation**
Use the scientific notation value of 25e14 instead.

## [L-02] The loop contains msg.value
**Description**
The msg.value being utilised within a loop is incorrect programming logic.
The resulting vulnerability is the same ether utilised incrementally in a loop.
**Location**
```sol
// system-contracts/contracts/ContractDeployer.sol#L253-L255
 for (uint256 i = 0; i < deploymentsLength; ++i) {
            this.forceDeployOnAddress{value: _deployments[i].value}(_deployments[i], msg.sender);
        }
```
**Mitigation**
Make sure the msg.value in the loop is contained in this function only and it not linked to any other functions.