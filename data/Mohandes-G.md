### G01 - Gas savings in Admin.setPendingGovernor

In `setPendingGovernor` function of `Admin` contract, you can save on gas by emitting the event before the state update. This way you can avoid storing the old value. Here is the [current code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L20):

```
        // Save previous value into the stack to put it into the event later
        address oldPendingGovernor = s.pendingGovernor;
        // Change pending governor
        s.pendingGovernor = _newPendingGovernor;
        emit NewPendingGovernor(oldPendingGovernor, _newPendingGovernor);
```

It can be changed to the following code:

```
        emit NewPendingGovernor(s.pendingGovernor, _newPendingGovernor);
        // Change pending governor
        s.pendingGovernor = _newPendingGovernor;
```

### G02 - Gas savings in Admin.acceptGovernor

In `acceptGovernor` function of `Admin` contract, you can save on gas by emitting the event before the state update. This way you can avoid storing the old value. Here is the [current code](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L29):

```
        address pendingGovernor = s.pendingGovernor;
        require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights

        address previousGovernor = s.governor;
        s.governor = pendingGovernor;
        delete s.pendingGovernor;

        emit NewPendingGovernor(pendingGovernor, address(0));
        emit NewGovernor(previousGovernor, pendingGovernor);
```

It can be changed to:

```
        address pendingGovernor = s.pendingGovernor;
        require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights

        emit NewPendingGovernor(pendingGovernor, address(0));
        emit NewGovernor(s.governor, pendingGovernor);

        s.governor = pendingGovernor;
        delete s.pendingGovernor;
```

### G03 - Gas savings in Admin.setPendingAdmin

In `setPendingAdmin` function of `Admin` contract, you can save on gas by emitting the event before the state update. This way you can avoid storing the old value. Here is the [current code](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L44):

```
        // Save previous value into the stack to put it into the event later
        address oldPendingAdmin = s.pendingAdmin;
        // Change pending admin
        s.pendingAdmin = _newPendingAdmin;
        emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);
```

It can be changed to the following code:

```
        emit NewPendingAdmin(s.pendingAdmin, _newPendingAdmin);
        // Change pending Admin
        s.pendingAdmin = _newPendingAdmin;
```

Please note that there is an error here, emitting `NewPendingGovernor` instead of `NewPendingAdmin` which I have reported separately as Medium Severity finding.

### G04 - Gas savings in Admin.acceptAdmin

In `acceptAdmin` function of `Admin` contract, you can save on gas by emitting the event before the state update. This way you can avoid storing the old value. Here is the [current code](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L53):

```
        address pendingAdmin = s.pendingAdmin;
        require(msg.sender == pendingAdmin, "n4"); // Only proposed by current admin address can claim the admin rights

        address previousAdmin = s.admin;
        s.admin = pendingAdmin;
        delete s.pendingAdmin;

        emit NewPendingAdmin(pendingAdmin, address(0));
        emit NewAdmin(previousAdmin, pendingAdmin);
```

It can be changed to:

```
        address pendingAdmin = s.pendingAdmin;
        require(msg.sender == pendingAdmin, "n4"); // Only proposed by current admin address can claim the admin rights

        emit NewPendingAdmin(pendingAdmin, address(0));
        emit NewAdmin(s.admin, pendingAdmin);

        s.admin = pendingAdmin;
        delete s.pendingAdmin;
```

### G05 - Gas savings in Admin.setPriorityTxMaxGasLimit

In `setPriorityTxMaxGasLimit` function of `Admin` contract, you can save on gas by emitting the event before the state update. This way you can avoid storing the old value. Here is the [current code](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L83):

```
        require(_newPriorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "n5");

        uint256 oldPriorityTxMaxGasLimit = s.priorityTxMaxGasLimit;
        s.priorityTxMaxGasLimit = _newPriorityTxMaxGasLimit;
        emit NewPriorityTxMaxGasLimit(oldPriorityTxMaxGasLimit, _newPriorityTxMaxGasLimit);
```

It can be changed to:

```
        require(_newPriorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "n5");

        emit NewPriorityTxMaxGasLimit(s.priorityTxMaxGasLimit, _newPriorityTxMaxGasLimit);
        s.priorityTxMaxGasLimit = _newPriorityTxMaxGasLimit;
```

### G06 - The mapping address is calculated twice in Getters.isFunctionFreezable

In `isFunctionFreezable` function of `Getters` contract, you can save on gas by caching the value you can save gas on storage reads. Here is the [current code](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L158):

```
    function isFunctionFreezable(bytes4 _selector) external view returns (bool) {
        Diamond.DiamondStorage storage ds = Diamond.getDiamondStorage();
        require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
        return ds.selectorToFacet[_selector].isFreezable;
    }
```

This is suggested:

```
    function isFunctionFreezable(bytes4 _selector) external view returns (bool) {
        Diamond.DiamondStorage storage ds = Diamond.getDiamondStorage();
        SelectorToFacet memory selectorToFacet = ds.selectorToFacet[_selector];
        require(selectorToFacet.facetAddress != address(0), "g2");
        return selectorToFacet.isFreezable;
    }
```

The whole struct is just one slot, so reading all together will result in good gas saving.

### G07 - Gas savings for \_verifyDepositLimit function of Mailbox contract

In `_verifyDepositLimit` function of `Mailbox` contract, we can cache the calculated total deposit and reuse it.
Here is teh [current implementation](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L275):

```
        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
        s.totalDepositedAmountPerUser[_depositor] += _amount;
```

I suggest it to be updated to this:

```
        uint256 totalDepositedNew = s.totalDepositedAmountPerUser[_depositor] + _amount
        require(totalDepositedNew <= limitData.depositCap, "d2");
        s.totalDepositedAmountPerUser[_depositor] = totalDepositedNew;
```

### G08 - Gas savings in Merkle.calculateRoot

By applying some gas optimizations in `calculateRoot` function of `Merkle` library we can achieve a saving of `341 * pathLength` gas.
Here is the [current code](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L18):

```
        for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
            currentHash = (_index % 2 == 0)
                ? _efficientHash(currentHash, _path[i])
                : _efficientHash(_path[i], currentHash);
            _index /= 2;
        }
```

I suggest the code to be like this:

```
        for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
            unchecked {  //unchecked math is safe here
                currentHash = (_index & uint256(1) == 0) //checks if _index is even number
                    ? _efficientHash(currentHash, _path[i])
                    : _efficientHash(_path[i], currentHash);
                _index >>= 1;
            }
        }
```

Using unchecked math, which is safe here, will yield in gas saving of 2*153* pathLength which is significant.
Also by replacing `_index % 2 == 0` with `_index & uint256(1) == 0` there will be saving of 19 gas for each round
And by replacing `_index /= 2;` with `_index >>= 1;` there will be extra 16 gas saving for each round

So in total the savings will be `(2*153+16+19) * pathLength`.

### G09 - Gas savings in LibMap

The [LibMap Library](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol) can benefit from some gas optimizations.

**get function**

In get function , we can replace the division and multiplication with bit shift operation to save on gas.
This is the current implementation:

```
    uint256 mapValue = _map.map[_index / 8];

    uint256 bitOffset = (_index & 7) * 32;
```

And here is the suggestion:

```
    uint256 mapValue = _map.map[_index >> 3];

    uint256 bitOffset = (_index & 7) << 5;
```

**set function**

In set function as well, we can replace the division and multiplication with bit shift operation to save on gas.

```
    uint256 mapIndex = _index / 8;
    uint256 mapValue = _map.map[mapIndex];

    // First three bits of the original `_index` denotes the position of the uint32 in that slot.
    // So, '(_index & 7) * 32' is done to find the bit position of the uint32 in that storage slot.
    uint256 bitOffset = (_index & 7) * 32;
```

The suggested change is:

```
    uint256 mapIndex = _index >> 3;
    uint256 mapValue = _map.map[mapIndex];

    // First three bits of the original `_index` denotes the position of the uint32 in that slot.
    // So, '(_index & 7) * 32' is done to find the bit position of the uint32 in that storage slot.
    uint256 bitOffset = (_index & 7) << 5;
```
