

# Low Issue Findings

## Summary
|	|	Issue								| Instances	|
| ---	|	-----								|	-----	|
| [L-01]| Missing checks for address(0x0) when assigning values to address state variables	|	01	|
| [L-02]	| Missing checks for zero value when assigning values to address state variables									|	01	|
| [L-03]	| Avoid setting old verifier as new one	|	01	|






# [L-01] Missing checks for address(0) when assigning values to address state variables 
Zero-address check should be used, to avoid the risk of setting a storage variable as address(0) during important state change.

Link to the code:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L160

# Recommendation
The recommendation is made to place condition that ensures zero address is not passed in the parameter.
```
require(_l2Receiver != address(0) , address cannot be zero);
```


# [L-02] Missing checks for zero value when assigning values to address state variables 
Zero-valye check should be used, to avoid the risk of passing a storage variable as 0.

Link to the code:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L60
# Recommendation
The recommendation is made to place condition that ensures zero bytes are not passed in the function.
```
require(_executionDelay != 0, value cannot be zero);
```


# [L-03] Avoid setting old verifier as new one
Check must be placed to ensure ```oldVerifier``` cannot be set as ```_newVerifier```.

Link to the code:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L121

# Recommendation
The recommendation is made to place condition that ensure old address of verifier is not set as new verifier.
```
require(oldVerifier != _newVerifier, address cannot be same);
```
