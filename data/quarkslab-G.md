# List of gas optimizations

## Use local storage when indexing

Solc does not cache memory and storage accesses in nested objects and arrays,
leading to duplicate `SLOAD` and `MLOAD`.
This is not caught by optimizer passes either.

To fix this, we need to explicitely cache a memory or storage location in a stack variable.

### `ethereum/contracts/zksync/libraries/Diamond.sol`

```diff
diff --git a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
index 0b1fbdc..8ba1e09 100644
--- a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
+++ b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
@@ -98,10 +98,11 @@ library Diamond {
         bytes memory initCalldata = _diamondCut.initCalldata;
         uint256 facetCutsLength = facetCuts.length;
         for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
-            Action action = facetCuts[i].action;
-            address facet = facetCuts[i].facet;
-            bool isFacetFreezable = facetCuts[i].isFreezable;
-            bytes4[] memory selectors = facetCuts[i].selectors;
+            FacetCut memory facetCut = facetCuts[i];
+            Action action = facetCut.action;
+            address facet = facetCut.facet;
+            bool isFacetFreezable = facetCut.isFreezable;
+            bytes4[] memory selectors = facetCut.selectors;

             require(selectors.length > 0, "B"); // no functions for diamond cut

@@ -210,13 +211,14 @@ library Diamond {
     ) private {
         DiamondStorage storage ds = getDiamondStorage();

-        uint16 selectorPosition = (ds.facetToSelectors[_facet].selectors.length).toUint16();
+        bytes4[] storage facetSelectors = ds.facetToSelectors[_facet].selectors;
+        uint16 selectorPosition = (facetSelectors.length).toUint16();

         // if selectorPosition is nonzero, it means it is not a new facet
         // so the freezability of the first selector must be matched to _isSelectorFreezable
         // so all the selectors in a facet will have the same freezability
         if (selectorPosition != 0) {
-            bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];
+            bytes4 selector0 = facetSelectors[0];
             require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
         }

@@ -225,7 +227,7 @@ library Diamond {
             selectorPosition: selectorPosition,
             isFreezable: _isSelectorFreezable
         });
-        ds.facetToSelectors[_facet].selectors.push(_selector);
+        facetSelectors.push(_selector);
     }

     /// @dev Remove one associated function with facet
@@ -233,20 +235,23 @@ library Diamond {
     function _removeOneFunction(address _facet, bytes4 _selector) private {
         DiamondStorage storage ds = getDiamondStorage();

+        // Cache `FacetToSelectors.selectors` storage location
+        bytes4[] storage facetSelectors = ds.facetToSelectors[_facet].selectors;
+
         // Get index of `FacetToSelectors.selectors` of the selector and last element of array
         uint256 selectorPosition = ds.selectorToFacet[_selector].selectorPosition;
-        uint256 lastSelectorPosition = ds.facetToSelectors[_facet].selectors.length - 1;
+        uint256 lastSelectorPosition = facetSelectors.length - 1;

         // If the selector is not at the end of the array then move the last element to the selector position
         if (selectorPosition != lastSelectorPosition) {
-            bytes4 lastSelector = ds.facetToSelectors[_facet].selectors[lastSelectorPosition];
+            bytes4 lastSelector = facetSelectors[lastSelectorPosition];

-            ds.facetToSelectors[_facet].selectors[selectorPosition] = lastSelector;
+            facetSelectors[selectorPosition] = lastSelector;
             ds.selectorToFacet[lastSelector].selectorPosition = selectorPosition.toUint16();
         }

         // Remove last element from the selectors array
-        ds.facetToSelectors[_facet].selectors.pop();
+        facetSelectors.pop();

         // Finally, clean up the association with facet
         delete ds.selectorToFacet[_selector];
```

## Use `uint256` for only variable in a slot

When a `uintN` variable occupies a slot by itself, prefer using `uint256`.
Smaller integer sizes need automatically-added masking instructions to remain safe to use which
occur overhead.
Additionally, using full-size `uint256` remove the needs for some calls to `.toUint16()`.

### `ethereum/contracts/zksync/libraries/Diamond.sol`

```diff
diff --git a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
index 0b1fbdc..ac052fe 100644
--- a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
+++ b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
@@ -37,7 +37,7 @@ library Diamond {
     /// @param facetPosition index in `DiamondStorage.facets` array, where is facet stored
     struct FacetToSelectors {
         bytes4[] selectors;
-        uint16 facetPosition;
+        uint256 facetPosition;
     }

     /// @notice The structure that holds all diamond proxy associated parameters
@@ -193,7 +193,7 @@ library Diamond {
         uint256 selectorsLength = ds.facetToSelectors[_facet].selectors.length;
         // If there are no selectors associated with facet then save facet as new one
         if (selectorsLength == 0) {
-            ds.facetToSelectors[_facet].facetPosition = ds.facets.length.toUint16();
+            ds.facetToSelectors[_facet].facetPosition = ds.facets.length;
             ds.facets.push(_facet);
         }
     }
@@ -271,7 +271,7 @@ library Diamond {
             address lastFacet = ds.facets[lastFacetPosition];

             ds.facets[facetPosition] = lastFacet;
-            ds.facetToSelectors[lastFacet].facetPosition = facetPosition.toUint16();
+            ds.facetToSelectors[lastFacet].facetPosition = facetPosition;
         }

         // Remove last element from the facets array
```
