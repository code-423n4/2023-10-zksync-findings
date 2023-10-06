## `Admin.sol` function `setPendingAdmin` emits the wrong event `NewPendingGovernor`

In this line of code https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L49

```
emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);
```

The wrong event `NewPendingGovernor` is emitted. This could make it difficult for an offchain indexer (such as TheGraph subgraphs) to differentiate between a `setPendingAdmin` vs `setPendingGovernor` function call since they emit the same event.

### Recommendation

The method should `emit NewPendingAdmin(oldPendingAdmin, _newPendingAdmin)`.

## `L1ERC20Bridge.sol` function `_verifyDepositLimit` math overflow

In this line of code https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L347

```
require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
```

In theory, it is possible that `totalDepositedAmountPerUser[_l1Token][_depositor] + _amount > max u256` so that the function reverts early without carrying the supposed error message "d1", which makes the error harder to understand when devs troubleshoot the failed transaction.

## Recommendation

Avoid math overflow by leveraging subtraction.

```
require(limitData.depositCap - totalDepositedAmountPerUser[_l1Token][_depositor] >= _amount, "d1");
```