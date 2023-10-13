# `Admin.sol` function `setPendingAdmin` emits the wrong event `NewPendingGovernor`

## Lines of code

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L49

## Vulnerability details

### Impact

In the function `setPendingAdmin` , the wrong event `NewPendingGovernor` is emitted.

```solidity
emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);
```

This could make it difficult for an off-chain indexer (such as TheGraph subgraphs) to differentiate between a `setPendingAdmin` vs `setPendingGovernor` function call since they emit the same event.

### Tools Used

Manual analysis.

### Recommended Mitigation Steps

The method should `emit NewPendingAdmin(oldPendingAdmin, _newPendingAdmin)`.



# `L1ERC20Bridge.sol` function `_verifyDepositLimit` integer overflow hides the expected error message “d1”

## Lines of code

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L347

## Vulnerability details

### Impact

It is possible that `totalDepositedAmountPerUser[_l1Token][_depositor] + _amount > UINT256_MAX` so that the function reverts with `Reason: Arithmetic over/underflow` without carrying the supposed error message "d1", which makes the error harder to understand when devs troubleshoot the failed transaction.

```solidity
function _verifyDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal {
        IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token);
        if (!limitData.depositLimitation) return; // no deposit limitation is placed for this token

        if (_claiming) {
            totalDepositedAmountPerUser[_l1Token][_depositor] -= _amount;
        } else {
            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
        }
}
```

### Proof Of Concept

Imagine:

- limitData.depositCap = UINT256_MAX
- totalDepositedAmountPerUser[TokenA][Bob] = UINT256_MAX - 10000
- _amount = 10001

Instead of “d1”, the error will be “Reason: Arithmetic over/underflow”.

### Tools Used

Manual analysis.

### Recommended Mitigation Steps

Avoid math overflow by leveraging subtraction.
```
require(limitData.depositCap - totalDepositedAmountPerUser[_l1Token][_depositor] >= _amount, "d1");
```


# `Mailbox.sol` function `_verifyDepositLimit` integer overflow hides the expected error message “d2”

## Lines of code

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L279

## Vulnerability details

### Impact

It is possible that `s.totalDepositedAmountPerUser[_depositor] + _amount > UINT256_MAX` so that the function reverts with `Reason: Arithmetic over/underflow` without carrying the supposed error message "d2", which makes the error harder to understand when devs troubleshoot the failed transaction.

```solidity
function _verifyDepositLimit(address _depositor, uint256 _amount) internal {
        IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH
        if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH

        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
        s.totalDepositedAmountPerUser[_depositor] += _amount;
}
```

### Proof Of Concept

Imagine:

- limitData.depositCap = UINT256_MAX
- totalDepositedAmountPerUser[Bob] = UINT256_MAX - 10000
- _amount = 10001

Instead of “d2”, the error will be “Reason: Arithmetic over/underflow”.

### Tools Used

Manual analysis.

### Recommended Mitigation Steps

Avoid math overflow by leveraging subtraction.
```
require(limitData.depositCap - s.totalDepositedAmountPerUser[_depositor] >= _amount, "d2");
```

# `Diamond.sol` function `_removeOneFunction` has integer underflow risk

## Lines of code

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L238

## Vulnerability details

### Impact

In the function `_removeOneFunction` , `ds.facetToSelectors[_facet].selectors.length - 1` will get underflow error in 2 cases:
1. `_facet` is ZERO address
2. `_facet` is NON-ZERO address, but it does not exist in mapping `ds.facetToSelectors`

In both scenarios, `ds.facetToSelectors[_facet].selectors.length == 0`, leading to error “Reason: Arithmetic over/underflow”.

Although in the current codebase, callers of `_removeOneFunction` has ensured above scenarios will not occur, it would be better to avoid underflow risk from the root.

```solidity
/// @dev Remove one associated function with facet
/// NOTE: It is expected but NOT enforced that `_facet` is NON-ZERO address
function _removeOneFunction(address _facet, bytes4 _selector) private {
    DiamondStorage storage ds = getDiamondStorage();

    // Get index of `FacetToSelectors.selectors` of the selector and last element of array
    uint256 selectorPosition = ds.selectorToFacet[_selector].selectorPosition;
    uint256 lastSelectorPosition = ds.facetToSelectors[_facet].selectors.length - 1;
    ....
}
```

### Tools Used

Manual analysis.

### Recommended Mitigation Steps
Add length check before calculating `lastSelectorPosition`.

```
require(ds.facetToSelectors[_facet].selectors.length > 0, "invalid facet");
uint256 lastSelectorPosition = ds.facetToSelectors[_facet].selectors.length - 1;
```