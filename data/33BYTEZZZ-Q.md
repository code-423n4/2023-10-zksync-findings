## L1 - Zero Address Check Missing

### Summary:
The constructor in Governance.sol file takes in _securityCouncil argument of type address. However, there is no check to ensure that this value is not a zero address.

### Vulnerable Code:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L41

### Impact:
The contract Governance.sol might get initialized with address of the _securityCouncil as the zero address. 

### Recommended Mitigation Steps:
Implement a check to ensure that _securityCouncil is not a 0 address.


