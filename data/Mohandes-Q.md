Here are some Low severity findings and notes:

# Findings

## Low Severity Issues

### L01 `ERC20Getters` is missing from `BridgeInitialize` event of `L2StandardERC20`

The `BridgeInitialize` event ([L97](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L97)) in `L2StandardERC20` can benefit from having the information about ignored properties.
Ignoring properties like `name` and `symbol` might not be much problematic, but ignoring `decimals` is quite serious, as it might lead to miscalculations. Although, when ignored, the `decimals()` function reverts with error, but actually it uses zero underneath as the decimals.
So it is important for the monitoring systems to be aware of the error has happened that has led to ignoring these properties.

Here is the definition of `BridgeInitialize` event:

```
event BridgeInitialize(address indexed l1Token, string name, string symbol, uint8 decimals);
```

And here us where the even is emitted in

```
        try this.decodeUint8(decimalsBytes) returns (uint8 decimalsUint8) {
            // Set decoded value for decimals.
            decimals_ = decimalsUint8;
        } catch {
            getters.ignoreDecimals = true;
        }

        availableGetters = getters;
        emit BridgeInitialize(_l1Address, decodedName, decodedSymbol, decimals_);
```

The ERC20Getters struct includes the following properties that could be useful to be added to the event:

```
struct ERC20Getters {
  bool ignoreName;
  bool ignoreSymbol;
  bool ignoreDecimals;
}
```

### L02 - Getters.isFacetFreezable returns false for invalid facet

The `isFacetFreezable` function in `Getters` ([L135](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L135)) lacks validation for `_facet` input parameter and returns `false` if there is no such facet.
This can lead to confusions by implying that the facet is not freezable. Please consider checking the validity of `_facet` and revert with a proper error message if the `_facet` is not valid.

Here is the current implementation:

```
  uint256 selectorsArrayLen = ds.facetToSelectors[_facet].selectors.length;
  if (selectorsArrayLen != 0) {
      bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];
      isFreezable = ds.selectorToFacet[selector0].isFreezable;
  }
```

I suggest adding an else clause:

```
  else {
    revert("invalid facet");
  }
```

## Notes and Recommendations

### N01 - setValidator function in Admin contract does not check the previous value

The `setValidator` function of the `Admin` contract, does not check the previous value, and updates and emits the event even in the case of no change to the validator status.
Here is [the code](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L68) of the `setValidator` function:

```
    function setValidator(address _validator, bool _active) external onlyGovernorOrAdmin {
        s.validators[_validator] = _active;
        emit ValidatorStatusUpdate(_validator, _active);
    }
```

Please consider checking the previous value of the validator status, and refrain from updating and emitting the event, when there is no change to the value.

### N02 - Naming issues

Please consider changing the name of function in `Executor` contract ([L27](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L27))
`Executor._commitOneBatch` : The name implied that this function commits one batch, but surprisingly this is a view function. Please consider changing the name to something more obvious. As there is already another function called `_createBatchCommitment` and this function applies some more validations on top of it, you might be able to call it `_prepareBatchCommitment` or `_getNewBatchCommitment`

### N03 - Unused code

`Executor._MaxU256` :
[This function](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L417) is not used. Please consider removing it for better readability and consistency and also gas saving on deployment:

```
    /// @notice Returns larger of two values
    function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) {
        return a < b ? b : a;
    }
```
