# Gas Report

## Summary

Total **14 instances** over **2 issues**with **7200** gas saved:

|ID|Issue|Instances|Gas|
|:--:|:---|:--:|:--:|
| [[G&#x2011;01]](#g01-make-deprecated-states-privateinternal-to-save-gas) | Make deprecated states `private`/`internal` to save gas | 2 | 7200 |
| [[G&#x2011;02]](#g02-using-constants-instead-of-enum-can-save-gas) | Using `constant`s instead of `enum` can save gas | 12 | - |

## Gas Optimizations

### [G&#x2011;01] Make deprecated states `private`/`internal` to save gas

A public deprecated state variable can be marked as `private` or `internal`, which can save **3600 gas** on deployment because the compiler does not have to create non-payable getter functions for deployment calldata and does not add another entry to the method ID table. If the state data is needed, it can be obtained by reading the storage directly off the chain.

There are 2 instances:

- *L1ERC20Bridge.sol* ( [#L54](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L54), [#L56-L57](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L56-L57) ):

```solidity
54:     mapping(address => uint256) public __DEPRECATED_lastWithdrawalLimitReset;

56:     /// @dev A mapping L1 token address => the accumulated withdrawn amount during the withdrawal limit window
57:     mapping(address => uint256) public __DEPRECATED_withdrawnAmountInWindow;
```

### [G&#x2011;02] Using `constant`s instead of `enum` can save gas

`Enum` is expensive and it is [more efficient to use constants](https://www.codehawks.com/finding/clm84992q02j9w9ruebun36d9) instead. An illustrative example of this approach can be found in the [ReentrancyGuard.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/181d518609a9f006fcb97af63e6952e603cf100e/contracts/utils/ReentrancyGuard.sol#L34-L35).

There are 12 instances:

- *IAllowList.sol* ( [#L20-L24](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol#L20-L24) ):

```solidity
20:     enum AccessMode {
21:         Closed,
22:         SpecialAccessOnly,
23:         Public
24:     }
```

- *IGovernance.sol* ( [#L11-L16](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/IGovernance.sol#L11-L16) ):

```solidity
11:     enum OperationState {
12:         Unset,
13:         Waiting,
14:         Ready,
15:         Done
16:     }
```

- *Storage.sol* ( [#L13-L17](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Storage.sol#L13-L17) ):

```solidity
13: enum UpgradeState {
14:     None,
15:     Transparent,
16:     Shadow
17: }
```

- *IExecutor.sol* ( [#L8-L17](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L8-L17) ):

```solidity
8: enum SystemLogKey {
9:     L2_TO_L1_LOGS_TREE_ROOT_KEY,
10:     TOTAL_L2_TO_L1_PUBDATA_KEY,
11:     STATE_DIFF_HASH_KEY,
12:     PACKED_BATCH_AND_L2_BLOCK_TIMESTAMP_KEY,
13:     PREV_BATCH_HASH_KEY,
14:     CHAINED_PRIORITY_TXN_HASH_KEY,
15:     NUMBER_OF_LAYER_1_TXS_KEY,
16:     EXPECTED_SYSTEM_CONTRACT_UPGRADE_TX_HASH_KEY
17: }
```

- *IMailbox.sol* ( [#L11-L14](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L11-L14) ):

```solidity
11: enum TxStatus {
12:     Failure,
13:     Success
14: }
```

- *Diamond.sol* ( [#L79-L83](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L79-L83) ):

```solidity
79:     enum Action {
80:         Add,
81:         Replace,
82:         Remove
83:     }
```

- *Constants.sol* ( [#L89-L98](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Constants.sol#L89-L98) ):

```solidity
89: enum SystemLogKey {
90:     L2_TO_L1_LOGS_TREE_ROOT_KEY,
91:     TOTAL_L2_TO_L1_PUBDATA_KEY,
92:     STATE_DIFF_HASH_KEY,
93:     PACKED_BATCH_AND_L2_BLOCK_TIMESTAMP_KEY,
94:     PREV_BATCH_HASH_KEY,
95:     CHAINED_PRIORITY_TXN_HASH_KEY,
96:     NUMBER_OF_LAYER_1_TXS_KEY,
97:     EXPECTED_SYSTEM_CONTRACT_UPGRADE_TX_HASH_KEY
98: }
```

- *IContractDeployer.sol* ( [#L11-L14](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/interfaces/IContractDeployer.sol#L11-L14), [#L23-L26](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/interfaces/IContractDeployer.sol#L23-L26) ):

```solidity
11:     enum AccountAbstractionVersion {
12:         None,
13:         Version1
14:     }

23:     enum AccountNonceOrdering {
24:         Sequential,
25:         Arbitrary
26:     }
```

- *IPaymaster.sol* ( [#L7-L10](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/interfaces/IPaymaster.sol#L7-L10) ):

```solidity
7: enum ExecutionResult {
8:     Revert,
9:     Success
10: }
```

- *SystemContractHelper.sol* ( [#L51-L57](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L51-L57) ):

```solidity
51: enum Global {
52:     CalldataPtr,
53:     CallFlags,
54:     ExtraABIData1,
55:     ExtraABIData2,
56:     ReturndataPtr
57: }
```

- *SystemContractsCaller.sol* ( [#L56-L60](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L56-L60) ):

```solidity
56: enum CalldataForwardingMode {
57:     UseHeap,
58:     ForwardFatPointer,
59:     UseAuxHeap
60: }
```
