# zkSync Era Gas Optimization Report

This submission serves as a gas optimization report of the zkSync Era codebase. All optimizations relate to contracts meant to be deployed to the Ethereum mainnet per the contest guidelines. The ways the codebase can be optimized are as follows:

## `UnsafeBytes`

### UBS-01G: Significant Optimization of Overall Transaction Parsing

| Lines Affected | Gas Optimization |
| - | - |
| [L19-L24](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol#L19-L24), [L26-L31](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol#L26-L31), [L33-L38](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol#L33-L38), [L40-L45](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol#L40-L45) | Significant |

The `UnsafeBytes` library is utilized across `L1ERC20Bridge`, `L1WethBridge`, `Executor`, and the `Mailbox` contracts to parse significantly large payloads of data sequentially in an optimized way.

All utilization points of the library make use of a `memory` argument when a `calldata` argument could be passed in (`L1ERC20Bridge::_parseL2WithdrawalMessage`, `L1WethBridge::_parseL2EthWithdrawalMessage`, `Executor::_processL2Logs`, `Mailbox::_parseL2WithdrawalMessage`). If the library was updated to work on `calldata` arguments, its gas cost would be significantly reduced as it would utilize `calldataload`, would not require copying the full `calldata` payload to `memory`, and would not incur any hidden memory expansion gas costs.

## `L2ContractHelper`

### LCH-01G: Inefficient Evaluation of Modulo

| Lines Affected | Gas Optimization |
| - | - |
| [L27](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L27) | Minimal |

The referenced modulo operation (`%`) is inefficient as its purpose is to identify whether the last bit of the `bytecodeLenInWords` is `1`.

We advise an `AND` operation to be utilized instead, reducing the required gas cost from `5` to `3` per operation.

### LCH-02G: Inefficient Evaluation of Multiplication

| Lines Affected | Gas Optimization |
| - | - |
| [L50](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L50) | Minimal |

The referenced multiplication statement (`*`) is inefficient as its purpose is to "shift" the `_bytecodeHash[2]` value to the left, reconstructing the `uint16` variable representing the bytecode length in words.

We advise a bitwise shift operation to be utilized, reducing the gas cost required by the operation from `5` to `3` for each invocation.

## `AllowList`

### ALS-01G: Inefficient Mapping Lookups

| Lines Affected | Gas Optimization |
| - | - |
| [L117](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/common/AllowList.sol#L117), [L120](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/common/AllowList.sol#L120) | Observable |

The referenced statements will re-calculate the `keccak256` hash of the `hasSpecialAccessToCall` mapping slots twice without being optimized.

While the compiler does optimize `keccak256` calculations of the same value consecutively, the `hasSpecialAccessToCall` is accessed as a nested mapping causing three unique `keccak256` calculations to be calculated per access thus resulting in 6 `keccak256` operations that are redundant.

We advise the `hasSpecialAccessToCall[_caller][_target]` lookup to be stored in a local `mapping(bytes4 => bool) storage` variable, permitting its pre-calculated offset to be re-used when assigning the `_enable` flag within the ensuing `if` block.

## `Diamond`

### DDN-01G: Inefficient Memory Expansion

| Lines Affected | Gas Optimization |
| - | - |
| [L101-L104](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L101-L104) | Observable |

The referenced variables are declared locally within the `for` loop of `Diamond::diamondCut` whilst they are already present in `memory` as the function's argument.

We advise the code to instead utilize a single local variable within the `for` loop, a `FacetCut memory` variable whose members are accessed wherever the previous declarations were.

This optimization will result in a median decrease of `174` gas units **per iteration** with optimizations turned on for a `FacetCut` payload with `3` selectors. The optimizations will scale with the number of `selectors` present in a cut.

### DDN-02G: Inefficient Mapping Lookups

| Lines Affected | Gas Optimization |
| - | - |
| [L213](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L213), [L219](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L219), [L228](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L228), [L238](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L238), [L242](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L242), [L244](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L244), [L249](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L249) | Observable |

The referenced statements will re-calculate the `keccak256` hash of the `facetToSelectors` and `selectorToFacet` mapping slots multiple times without being optimized.

While the compiler does optimize `keccak256` claculations of the same value consecutively, the referenced statements are not performed in sequence and would benefit from an optimization that caches the `mapping` lookup in a local variable.

The total lookups that will be optimized by a local variable are `3`.

## `LibMap`

### LMP-01G: Sub-Optimal Fork of Solady

| Lines Affected | Gas Optimization |
| - | - |
| [L6](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L6) | Moderate |

The `LibMap` in use by the codebase is currently sub-optimal as an implementation is present in the official Solady repository that contains a greater degree of optimization. In detail, lookups are optimized by using bitwise operators instead of divisions and multiplications (result in a `2` gas cost reduction per operation) whilst sets are entirely performed in `assembly` blocks further benefitting from optimized gas costs.

We advise the latest implementation by Solady to be re-imported in the codebase, optimizing its gas cost across the board.

## `Merkle`

### MEL-01G: Inefficient Evaluation of Modulo

| Lines Affected | Gas Optimization |
| - | - |
| [L30](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L30) | Minimal |

The referenced modulo operation (`%`) is inefficient as its purpose is to identify whether the last bit of the `_index` is `1`.

We advise an `AND` operation to be utilized instead, reducing the required gas cost from `5` to `3` per operation.

### MEL-02G: Inefficient Evaluation of Division

| Lines Affected | Gas Optimization |
| - | - |
| [L33](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L33) | Minimal |

The referenced division operation (`/`) is inefficient as its purpose is to divide the `_index` by `2` on each iteration, effectively shifting its bits.

We advise a bitwise shift operation to be utilized instead, optimizing the gas cost of each iteration by `2` gas units.

## `Executor`

### ESR-01G: Inefficient Evaluation of Ternary Operator

| Lines Affected | Gas Optimization |
| - | - |
| [L243](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L243) | Observable |

The `Executor::_commitBatchesWithSystemContractsUpgrade` function will execute special logic for the first batch of the total batches being committed inefficiently within the `for` loop.

We advise the system upgrade batch to be executed outside the `for` loop and the `for` loop body to be identical to the `Executor::_commitBatchesWIthoutSystemContractsUpgrade` albeit starting the iterator at `1` instead of `0`.

## `Mailbox`

### MXO-01G: Inefficient Mapping Lookups

| Lines Affected | Gas Optimization |
| - | - |
| [L199](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L199), [L212](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L212) | Observable |

The referenced statements will re-calculate the `keccak256` hash of the `isEthWithdrawalFinalized` mapping slots two times redundantly in the same codeblock.

We advise the `s.isEthWithdrawalFinalized[_l2BatchNumber]` lookup to be cached to a local `mapping(uint256 => bool) storage` variable that is consequently utilized for the `true` assignment statement, optimizing the gas cost of the function by one less redundant `keccak256` operation.

## `L1ERC20Bridge`

### LER-01G: Inefficient Mapping Lookups

| Lines Affected | Gas Optimization |
| - | - |
| [L300](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L300), [L315](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L315), [L347](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L347), [L348](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L348) | Observable |

The referenced statements will re-calculate the `keccak256` hash of the `isWithdrawalFinalized` and `totalDepositedAmountPerUser` mapping slots two times redundantly in each codeblock.

We advise the `isWithdrawalFinalized[_l2BatchNumber]` and `totalDepositedAmountPerUser[_l1Token]` lookups to be cached to a local `mapping(uint256 => bool) storage` and `mapping(address => uint256) storage` variables respectively, optimizing the gas cost of each function by one less redundant `keccak256` operation.

## `L1WethBridge`

### LWB-01G: Inefficient Mapping Lookups

| Lines Affected | Gas Optimization |
| - | - |
| [L240](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L240), [L265](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L265) | Observable |

The referenced statements will re-calculate the `keccak256` hash of the `isWithdrawalFinalized` mapping slots two times redundantly in the same codeblock.

We advise the `isWithdrawalFinalized[_l2BatchNumber]` lookup to be cached to a local `mapping(uint256 => bool) storage` variable that is consequently utilized for the `true` assignment statement, optimizing the gas cost of the function by one less redundant `keccak256` operation.

## `Governance`

### GSE-01G: Inefficient Iterator Increment Statement

| Lines Affected | Gas Optimization |
| - | - |
| [L225](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/governance/Governance.sol#L225) | Minimal |

The referenced iterator increment statement, unlike the rest of the codebase does not make use of the `UncheckedMath` contract.

We advise it to be utilized and the increment statement to be performed unsafely, optimizing the code's gas cost.

## `BaseZkSyncUpgrade`

### BZS-01G: Inefficient Iterator Increment Statement

| Lines Affected | Gas Optimization |
| - | - |
| [L204](https://github.com/code-423n4/2023-10-zksync/tree/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204) | Minimal |

The referenced iterator increment statement, unlike the rest of the codebase does not make use of the `UncheckedMath` contract.

We advise it to be utilized and the increment statement to be performed unsafely, optimizing the code's gas cost.

