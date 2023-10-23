# Low [1]

## Title
The value of `setPriorityTxMaxGasLimit` can be set very low

## Summary
In contract `zksync/facets/Admin.sol` in function `setPriorityTxMaxGasLimit()` and in contract `zksync/DiamondInit.sol` in function `initialize()` the new value of `s.priorityTxMaxGasLimit` is not checked if it is not set to 0 or other very low value.
The contracts include a check to ensure that `_newPriorityTxMaxGasLimit` in `Admin.sol` and `_initalizeData.priorityTxMaxGasLimit` in `DiamondInit.sol` are not greater than `L2_TX_MAX_GAS_LIMIT`, but there is no similar check for a minimum value. The minimal value would depend on the specific gas requirements of the transactions that are expected to be processed. In Ethereum, the gas cost for a simple transfer of Ether is 21,000 gas. However, transactions interacting with smart contracts or performing more complex operations can require significantly more gas.
The contracts do not specify a minimum value for `_newPriorityTxMaxGasLimit`, so technically it could be set to any value. But setting it too low could indeed prevent transactions from being processed. Therefore, to ensure that all transactions can be processed, `_newPriorityTxMaxGasLimit` should be set to a value that is higher than the maximum gas cost expected for the transactions.

File: `zksync/facets/Admin.sol`:

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L83-L89

```solidity
function setPriorityTxMaxGasLimit(uint256 _newPriorityTxMaxGasLimit) external onlyGovernor {

    require(_newPriorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "n5");

    uint256 oldPriorityTxMaxGasLimit = s.priorityTxMaxGasLimit;
@>  s.priorityTxMaxGasLimit = _newPriorityTxMaxGasLimit;
    emit NewPriorityTxMaxGasLimit(oldPriorityTxMaxGasLimit, _newPriorityTxMaxGasLimit);
}
```

File: `zksync/DiamondInit.sol`:

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L55-L90

```solidity
function initialize(InitializeData calldata _initalizeData) external reentrancyGuardInitializer returns (bytes32) {
    require(address(_initalizeData.verifier) != address(0), "vt");
    require(_initalizeData.governor != address(0), "vy");
    require(_initalizeData.admin != address(0), "hc");
    require(_initalizeData.priorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "vu");

    s.verifier = _initalizeData.verifier;
    s.governor = _initalizeData.governor;
    s.admin = _initalizeData.admin;

    // We need to initialize the state hash because it is used in the commitment of the next batch
    IExecutor.StoredBatchInfo memory storedBatchZero = IExecutor.StoredBatchInfo(
        0,
        _initalizeData.genesisBatchHash,
        _initalizeData.genesisIndexRepeatedStorageChanges,
        0,
        EMPTY_STRING_KECCAK,
        DEFAULT_L2_LOGS_TREE_ROOT_HASH,
        0,
        _initalizeData.genesisBatchCommitment
    );

    s.storedBatchHashes[0] = keccak256(abi.encode(storedBatchZero));
    s.allowList = _initalizeData.allowList;
    s.verifierParams = _initalizeData.verifierParams;
    s.zkPorterIsAvailable = _initalizeData.zkPorterIsAvailable;
    s.l2BootloaderBytecodeHash = _initalizeData.l2BootloaderBytecodeHash;
    s.l2DefaultAccountBytecodeHash = _initalizeData.l2DefaultAccountBytecodeHash;
@>  s.priorityTxMaxGasLimit = _initalizeData.priorityTxMaxGasLimit;

    // While this does not provide a protection in the production, it is needed for local testing
    // Length of the L2Log encoding should not be equal to the length of other L2Logs' tree nodes preimages
    assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

    return Diamond.DIAMOND_INIT_SUCCESS_RETURN_VALUE;
}
```

# Low [2]

## Title
Lack of time locks in `Admin.sol`

## Summary
The contract `zksync/facets/Admins.sol` lacks time locks for governance actions: `freezeDiamond()` and `unfreezeDiamond()` This means that changes can be made instantly, without giving users time to react.

File: `zksync/facets/Admins.sol`:

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L109-L127

```solidity

function freezeDiamond() external onlyGovernor {
    Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();

    require(!diamondStorage.isFrozen, "a9"); // diamond proxy is frozen already
    diamondStorage.isFrozen = true;

    emit Freeze();
}

function unfreezeDiamond() external onlyGovernorOrAdmin {
    Diamond.DiamondStorage storage diamondStorage = Diamond.getDiamondStorage();

    require(diamondStorage.isFrozen, "a7"); // diamond proxy is not frozen
    diamondStorage.isFrozen = false;

    emit Unfreeze();
}
```