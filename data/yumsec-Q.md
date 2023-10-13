# `Admin.sol` function `setPendingAdmin` emits the wrong event `NewPendingGovernor`

## Lines of code

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L49

## **Vulnerability details**

**Impact**

In the function `setPendingAdmin` , the wrong event `NewPendingGovernor` is emitted.

```solidity
emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);
```

This could make it difficult for an off-chain indexer (such as TheGraph subgraphs) to differentiate between a `setPendingAdmin` vs `setPendingGovernor` function call since they emit the same event.

**Tools Used**

Manual analysis.

**Recommended Mitigation Steps**

The method should `emit NewPendingAdmin(oldPendingAdmin, _newPendingAdmin)`.


# `L1ERC20Bridge.sol` function `_verifyDepositLimit` integer overflow hides the expected error message “d1”

## Lines of code

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L347

## **Vulnerability details**

**Impact**

It is possible that `totalDepositedAmountPerUser[_l1Token][_depositor] + _amount > UINT256_MAX` so that the function reverts with `Reason: Arithmetic over/underflow` without carrying the supposed error message "d1", which makes the error harder to understand when devs troubleshoot the failed transaction.

```solidity
require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
```

**Proof Of Concept**

Imagine:

- limitData.depositCap = UINT256_MAX
- totalDepositedAmountPerUser[TokenA][Bob] = UINT256_MAX - 10000
- _amount = 10001

Instead of “d1”, the error will be “Reason: Arithmetic over/underflow”.

**Tools Used**

Manual analysis.

**Recommended Mitigation Steps**

Avoid math overflow by leveraging subtraction.
```
require(limitData.depositCap - totalDepositedAmountPerUser[_l1Token][_depositor] >= _amount, "d1");
```