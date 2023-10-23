Contract 1: L1ERC20Bridge.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

1) Line 3: Contract uses a floating pragma, its better to lock compiler version 
2)Line 65: There no zero address check on _zkSync and _allowList

Contract 2: L1WethBridge.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

1) Line 3: Contract uses a floating pragma, its better to lock compiler version 
2) Line 64: There is no 0 address check on _l1WethAddress, _zkSync and _allowList

Contract 3: Executor.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

1) Line 3: Contract uses a floating pragma, its better to lock compiler version 


Contract 4: Governance.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol

1) line 41: No 0 address check for the `_securityCouncil` address variable
2) line 256: No 0 address check for `_newSecurityCouncil`


Contract 5: Diamond.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

1) line 3: floating pragma 


Contract 6: ValidatorTimelock.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

1) line 44: No 0 address checks on `_initialOwner`, `_zkSyncContract`, `_executionDelay`, and `_validator`
2) line 52: No 0 address checks on `_newValidator`


Contract 7: Admin.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol

1) line 3: floating pragma
2) line 20: No 0 address checks on setPendingGovernor 
3) line 44: No 0 address checks on setPendingAdmin

Contract 8: AllowList.sol
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol

1) line 31: No 0 address check in constructor
2) line 3: floating pragama


Note, most of the files have floating pragma
