## Title
No deleting of storage variable spends unneeded gas fee

## Vulnerability Detail
The `Diamond.sol#_removeFacet()` function is as follow.

```solidity
File: Diamond.sol
262:     function _removeFacet(address _facet) private {
263:         DiamondStorage storage ds = getDiamondStorage();
264: 
265:         // Get index of `DiamondStorage.facets` of the facet and last element of array
266:         uint256 facetPosition = ds.facetToSelectors[_facet].facetPosition;
267:         uint256 lastFacetPosition = ds.facets.length - 1;
268: 
269:         // If the facet is not at the end of the array then move the last element to the facet position
270:         if (facetPosition != lastFacetPosition) {
271:             address lastFacet = ds.facets[lastFacetPosition];
272: 
273:             ds.facets[facetPosition] = lastFacet;
274:             ds.facetToSelectors[lastFacet].facetPosition = facetPosition.toUint16();
275:         }
276: 
277:         // Remove last element from the facets array
278:         ds.facets.pop();
279:     }
```
On `#L278` of this function, the last element of `ds.facets` is removed but the corresponding element of `ds.facetToSelectors` is not removed.
As a result it leads to overspending of gas fee because `ds` is a storage variable.

## Recommended Mitigation Steps
The delete statement should be introduced in the foot of `Diamond.sol#_removeFacet()`.
```solidity
delete ds.facetToSelectors[_facet]
```