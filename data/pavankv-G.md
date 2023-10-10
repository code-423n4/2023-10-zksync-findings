**The gas findings below have exact gas benchmark**

## 1. Use Do-while loop To save deployment GAs :-

Instance - 1 
**Before**
```solidity
      for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
            address facetAddr = ds.facets[i];
            Diamond.FacetToSelectors memory facetToSelectors = 
                ds.facetToSelectors[facetAddr];

            result[i] = Facet(facetAddr, facetToSelectors.selectors);
        }

```

**After**
```solidity
         uint256 i ;
         do{
            address facetAddr = ds.facets[i];
            Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];

            result[i] = Facet(facetAddr, facetToSelectors.selectors);
            i = i.uncheckedInc();
         }while(i < facetsLen);
```

**HardHat Gas BenchMark**
**Before**
| Deployments  |  % of limit   |
|---|---|
| GettersFacet  |  965891  |

**After**
| Deployments  |  % of limit   |
|---|---|
| GettersFacet  |  964583  |

Total saved gas :-
1308.

code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L182C1-L187C10