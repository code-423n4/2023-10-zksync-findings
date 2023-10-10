## [L-01] No check for  if parameter arrays lengths are equal in setBatchPermissionToCall() of AllowList contract. 

if array lenghths are not of equal lenghts and are used in a for loop it will cause a revert because the code will try to read from an inexistent index in the shorter/shortest array. check that all arrrays have same length before using in the for loop. 

```
    function setBatchPermissionToCall(
        address[] calldata _callers,
        address[] calldata _targets,
        bytes4[] calldata _functionSigs,
        bool[] calldata _enables
    ) external onlyOwner {
        uint256 callersLength = _callers.length;

        // The size of arrays should be equal
        require(callersLength == _targets.length, "yw");
        require(callersLength == _functionSigs.length, "yx");
        require(callersLength == _enables.length, "yy");

        for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
            _setPermissionToCall(_callers[i], _targets[i], _functionSigs[i], _enables[i]);
        }
    }
```