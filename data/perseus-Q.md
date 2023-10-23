## [L-1] Incorrect implementation of Getters.getFirstUnprocessedPriorityTx()

Natspec comment for the function indicates that this function reverts when there are no unprocessed priority transactions. However, when there are no unprocessed priority transactions this function returns value equal to the queue tail, incorrectly indicating that an unprocessed transaction exists even when there is none.

**Mitigation:** update Natspec comment for Getters.getFirstUnprocessedPriorityTx() or change implementation of PriorityQueue.getFirstUnprocessedPriorityTx() to revert when there are no unprocessed transactions.

## [L-2] Incorrect event emitted in Admin.setPendingAdmin()

In the Admin.setPendingAdmin() implementation emits NewPendingGovernor() event instead of NewPendingAdmin() which is incorrect.

**Mitigation:** replace NewPendingGovernor() event emission with NewPendingAdmin() event emission.

## [L-3] Incorrect check in L1Messenger.publishPubdataAndClearState()

Check in the following code is inadequate since it will always be satisfied as the same variables are compared.

```
uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
```

**Mitigation:** replace incorrect check with the following

```
require(numberOfL2ToL1Logs <= numberOfLogsToProcess, "Too many L2->L1 logs");
```

## [N-1] Unnecessary code in SystemContext.publishTimestampDataToL1()

Following assignment in the SystemContext.publishTimestampDataToL1() does not have purpose since prevBatchHash variable is not used in subsequent code.

```
bytes32 prevBatchHash = batchHash[currentBatchNumber - 1];

```
**Mitigation:** consider removing above code.

## [N-2] Non indexed event (additional instance)

Consider adding indexed attribute to the most appropriate fields in NewPriorityRequest event defined within IMailbox.sol (e.g. txId).

## [N-3] Incorrect comment in L1Messenger.publishPubdataAndClearState()

Following comment indicates that "total len of compressed" is encoded using 2 bytes. However, it is encoded using 3 bytes by the implementation.
```
/// Check State Diffs
/// encoding is as follows:
/// header (1 byte version, 2 bytes total len of compressed, 1 byte enumeration index size, 2 bytes number of initial writes)
```