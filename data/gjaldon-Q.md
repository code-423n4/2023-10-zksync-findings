### [Low Risk](#low-risk-1)

| Total Low Risk Issues | 2 |
|:--:|:--:|

| Count | Title | Instances |
|:--:|:-------| :--: |
| [L-01](#l-01-does-not-check-that-facet-address-has-a-contract) | Does not check that facet address has a contract | 3 |
| [L-02](#l-02) | Unused prevBatchHash in SystemContext.publishTimestampDataToL1() | 1 |

### [Non-Critical](#non-critical-1)

| Total Non-Critical Issues | 1 |
|:--:|:--:|

| Count | Title | Instances |
|:--:|:-------| :--: |
| [NC-01](#nc-01-stale-documentation) | Stale Documentation | 2 |

## Low Risk

### [L-01] Does not check that facet address has a contract

The [reference implementation](https://github.com/mudgen/diamond-1-hardhat/blob/main/contracts/libraries/LibDiamond.sol#L196-L204) for a Diamond contract includes a check that the facet is a contract. The following
internal functions should have this check:

- [_addFunctions()](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L132)
- [_replaceFunctions()](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L156)
- [_removeFunctions()](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L176)

The check below could be added.

```solidity
require(_facet.code.length > 0, "not a contract");
```

### [L-02] Unused prevBatchHash in SystemContext.publishTimestampDataToL1()

This [line](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L385) can be removed in `SystemContext`.

```diff
// The structure of the "setNewBatch" implies that currentBatchNumber > 0, but we still double check it
require(currentBatchNumber > 0, "The current batch number must be greater than 0");

-bytes32 prevBatchHash = batchHash[currentBatchNumber - 1];
```

## Non-critical

### [NC-01] Stale Documentation

- Executor.sol ( [#L89](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L89)):

```solidity
    // @audit Typo - The documentation is incorrect. It should be "[batchTimestamp, lastL2BlockTimestamp]"
89: // All L2 blocks have timestamps within the range of [batchTimestamp, lastL2BatchTimestamp].
```

- extensions/call.md ( [#L45](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/VM%20Section/How%20compiler%20works/instructions/extensions/call.md?plain=1#L45)):

```
@audit the first parameter is 0 but must be in1 since it accepts the index as parameter.
| extra_abi_data | 0 | 0xFFE5 | - | 0 | 0xFFFF to prevent optimizing out by Yul | 0 | 0 |  | staticcall | ones passed in r3-r12 on far_call to the callee (saved in the very first instructions in the entry) |  |
```