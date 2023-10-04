## `setPendingAdmin` in `Admin.sol` emits the wrong event

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L49

In `Admin.sol`, function `setPendingAdmin` emits the wrong event `NewPendingGovernor`. This could make it difficult for an offchain indexer (such as TheGraph subgraphs) to differentiate between a `setPendingAdmin` vs `setPendingGovernor` function call since they emit the same event.

### Recommendation

The method should `emit NewPendingAdmin(oldPendingAdmin, _newPendingAdmin)`.
