# [L-01] Always passing `numberOfL2ToL1Logs` check
## Targets
https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/system-contracts/contracts/L1Messenger.sol#L206
## Impact
When the number of L2->L1 logs is greater than `L2_TO_L1_LOGS_MERKLE_TREE_LEAVES`, `publishPubdataAndClearState()` will revert with a confusing error: "Index out of bounds" will be returned instead of "Too many L2->L1 logs".
## Proof of Concept
When publishing L2->L1 logs, the number of logs is compared to itself ([L1Messenger.sol#L205-L206](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/system-contracts/contracts/L1Messenger.sol#L205-L206)):
```solidity
uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
```

Thus, the check will always pass. However, the number cannot be greater than `L2_TO_L1_LOGS_MERKLE_TREE_LEAVES` because this is the maximal size of the array storing the logs ([L1Messenger.sol#L209](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/system-contracts/contracts/L1Messenger.sol#L209)):
```solidity
bytes32[] memory l2ToL1LogsTreeArray = new bytes32[](L2_TO_L1_LOGS_MERKLE_TREE_LEAVES);
```

If more than `L2_TO_L1_LOGS_MERKLE_TREE_LEAVES` L2->L2 logs are passed by the operator, the length check will pass but the function will revert at writing to the array at an index grater than its length ([L1Messenger.sol#L216](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/system-contracts/contracts/L1Messenger.sol#L216)):
```solidity
for (uint256 i = 0; i < numberOfL2ToL1Logs; ++i) {
    ...
    l2ToL1LogsTreeArray[i] = hashedLog;
    ...
}
```
## Tools Used
Manual review
## Recommended Mitigation Steps
In the `L1Messenger.publishPubdataAndClearState()` function, consider checking if `numberOfL2ToL1Logs` is less than or equals to `L2_TO_L1_LOGS_MERKLE_TREE_LEAVES` and reverting with a meaningful error message when it is not so.



# [L-02] Gas limit overflow can potentially stop the priority queue and L1->L2 transactions processing
## Targets
https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L240
https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/system-contracts/bootloader/bootloader.yul#L3075-L3078
## Impact
Potentially, priority queue and L1->L2 transactions processing can be blocked after the gas limit restrictions for L1->L2 transactions were loosened (e.g. due to the improvement of the ZK proving algorithms). At the moment, the bug cannot be exploited.
## Proof of Concept
The [MailboxFacet.requestL2Transaction()](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236) takes the `_l2GasLimit` argument as a `uint256` number. In the bootloader, however, the value of the gas limit of a transaction must be a `uint32` value ([bootloader.yul#L3075-L3078](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/system-contracts/bootloader/bootloader.yul#L3075-L3078)):
```yul
let gasLimitValue := getGasLimit(innerTxDataOffset)
if iszero(validateUint32(gasLimitValue)) {
    assertionError("Encoding gasLimit")
}
```
If a too big value is set as the gas limit (which is possible due to the `uint256` type of the argument in `MailboxFacet.requestL2Transaction()`), the check will fail and the bootloader will revert the entire batch. Since the order of L1->L2 transactions is enforced and must be guaranteed (e.g. there's a check in the Executor: [Executor.sol#L281-L282](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L281-L282)), it wouldn't be possible to skip a transaction which gas limit overflows `uint32`. Also, the operator won't be able to modify the transaction and provide a proper value since the consistency of transactions is guaranteed via hashing ([bootloader.yul#L889](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/system-contracts/bootloader/bootloader.yul#L889)).

With the current L1 and L2 gas prices, the attack would cost ~2.15 ETH (the cost of a priority operation with L2 gas limit of 2**32 and the current fair L2 gas price), which seems like a cheap cost for blocking bridge transactions processing in an L2 network. However, due to the restrictions on the maximal transaction gas limit ([TransactionValidator.sol#L29-L32](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L29-L32)), the attack cannot be executed with the current settings.
## Tools Used
Manual review
## Recommended Mitigation Steps
In the `Mailbox.requestL2Transaction()` function, consider changing the type of the `_l2GasLimit` argument to `uint32`. Alternatively, consider keeping the `uint256` type and checking that its value is not grater than `type(uint256).max`.