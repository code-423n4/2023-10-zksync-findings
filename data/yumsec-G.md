# [GAS-01] `Diamond.sol` function `_removeFacet` should delete `_facet` from `facetToSelectors`

## Lines of code

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L262

## Vulnerability details

### Impact

Function `_removeFacet` removes `_facet`, but doesn’t remove it from the mapping `facetToSelectors`, which will make the mapping lager over time, incurring higher gas cost for users. 

### Tools Used

Manual analysis.

### Recommended Mitigation Steps

Delete `_facet` from mapping `facetToSelectors`.

```
function _removeFacet(address _facet) private {
    ....
    // Remove last element from the facets array
    ds.facets.pop();
    delete ds.facetToSelectors[_facet]; // ADD THIS
}

```

# [GAS-02] `Diamond.sol` function `_replaceFunctions` should compare oldFacet and newFacet

## Lines of code

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L149

## Vulnerability details

### Impact

Function `_replaceFunctions` replaces `oldFacet` by `newFacet`. If `oldFacet == newFacet`, it could skip the replacement to save gas.

### Tools Used

Manual analysis.

### Recommended Mitigation Steps

Only when `oldFacet.facetAddress != _facet`, execute replacement process.

```solidity
function _replaceFunctions(
        address _facet,
        bytes4[] memory _selectors,
        bool _isFacetFreezable
) private {
    ....
    for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
        bytes4 selector = _selectors[i];
        SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
        require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address
        // ADD THIS
	if (oldFacet.facetAddress != _facet) {
             _removeOneFunction(oldFacet.facetAddress, selector);
             // Add facet to the list of facets if the facet address is a new one
             _saveFacetIfNew(_facet);
             _addOneFunction(_facet, selector, _isFacetFreezable);
         }
    }
}
```