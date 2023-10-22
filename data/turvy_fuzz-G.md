## Iteration in setImmutables can be optimized to save gas

ImmutableSimulator:setImmutables():
```solidity
function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override {
        require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");
        unchecked {
            uint256 immutablesLength = _immutables.length;
            for (uint256 i = 0; i < immutablesLength; ++i) {
                uint256 index = _immutables[i].index;
                bytes32 value = _immutables[i].value;
                immutableDataStorage[uint256(uint160(_dest))][index] = value;
            }
        }
    }
```

Optimized:
```solidity
function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override {
        require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");
        unchecked {
            uint256 immutablesLength = _immutables.length;
            for (uint256 i = 0; i < immutablesLength; ++i) {
                immutableDataStorage[uint256(uint160(_dest))][_immutables[i].index] = _immutables[i].value;
            }
        }
    }
```
