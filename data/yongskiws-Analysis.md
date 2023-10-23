# Codebase analysis

# L1 CONTRACTS

# Executor

`ExecutorFacet` which provides a number of external functions that can be used for various tasks in the zkSync protocol as follows:

- External Functions
`commitBatches` is used to commit new batches. This includes several important steps Checking that batch commits are only performed after the previous batch has already been verified.
is there an active contract upgrade, if not, then run a batch commit without a contract upgrade, and if there is an upgrade, then run a batch commit with a contract upgrade and Add the number of successfully committed batches to the total committed batches.

- Batch Processing
`_commitBatchesWithoutSystemContractsUpgrade` part of the batch commit. used to execute batch commits without contract upgrade. This processes the entire batch contained in the given `CommitBatchInfo`, including validation, hash storage, and emits the `BlockCommit` event.

`_commitBatchesWithSystemContractsUpgrade`: used for batch commits with contract upgrades. This involves calculations for the first batch with the contract upgrade, and then another batch. It also validates, stores hashes, and emits `BlockCommit` events.

`_collectOperationsFromPriorityQueue`: to collect operations from the priority queue and generate a hash of the collected operations. This will be used to compare the hash priority of the aggregated operation with the expected hash in the batch.

`_executeOneBatch`: This serves to execute one batch of the batch execution order and check the validity of the priority operation hash, and save the root hash from the L2 to L1 log.
NOTE: Storing L2 Log Hash Root to L1, Batch also has L2 log hash root which must be stored in the contract and stores the log hash root in the contract for subsequent use or can be called a Merkle tree with L2 logs.

- Batch Verification

`executeBatches`: To execute confirmed batches. This involves processing pending operations and finalizing batches on the network. It also sets the total batches that have been executed.

`proveBatches`: Commit batches using zero-knowledge proof verification. This involves comparing batch hashes, calculating public input for ZKP proofs, and verifying ZKP proofs.

`revertBatches`: reverses batches that have not yet been executed. This only reduces the number of committed batches but does not delete existing data about those batches.

- Utility Functions

Several utility functions, such as `_maxU256` to return the largest of two numbers, `_createBatchCommitment` to create batch commits, and others.

- Internal Functions

Some internal functions, such as `_hashStoredBatchInfo` to calculate the hash of `StoredBatchInfo`, `_checkBit` to check whether the bit at a certain index in a number is 1, and `_setBit` to set the bit at a certain index to 1.



# ADMIN
a system that manages access rights and some important configurations. This provides a way to change governors, admins, validator status, zkPorter availability, and many other things, while also ensuring there is a mechanism to upgrade contracts and freeze contracts in emergency situations. All of this is important in maintaining system security and flexibility in the ZKSYNC protocol


`setPendingGovernor`: the current governor to initiate the transfer of governor privileges to another address. This will set `s.pendingGovernor`` with the new governor's address.

`acceptGovernor`: new governor to accept the offered governor rights. This will replace the current governor with a new governor.

`setPendingAdmin`: Similar to `setPendingGovernor`, ​​only applies to admin access.

`acceptAdmin`: Similar to `acceptGovernor`, ​​only applies to admin access.

`setValidator`: governor or admin to change the validator status (active or inactive) based on the validator address.

`setPorterAvailability`: governor to set zkPorter shard availability.

`setPriorityTxMaxGasLimit`: governor to set the maximum gas limit for L1 -> L2 transactions.

### Variables and Constants:

`s.pendingGovernor`: address of the new governor who is being considered to take over the role. If nothing is being considered, the value is address(0).

`s.governor`: address of the current governor.

`s.pendingAdmin`: address of the new admin who is being considered to take over the role. If nothing is being considered, the value is address(0).

`s.admin`: current admin address.

`s.validators`: mapping (list of key-value pairs) that stores validator status (active or inactive) based on validator address.

`s.zkPorterIsAvailable`: zkPorter shard availability status.

`s.priorityTxMaxGasLimit`: maximum gas limit for L1 -> L2 transactions.

### Upgrade Execution:

`executeUpgrade`: which allows the current governor to perform an upgrade to the contract using Diamond Standard. Diamond is a concept that allows upgrades without affecting the entire contract, only certain parts need to be changed.

`Contract Freezing`:
has a mechanism to freeze and unfreeze contract functionality. This is important in emergency situations or when major changes are needed.

`freezeDiamond`: governor to freeze contract functionality, meaning the contract will not accept new commands.

`unfreezeDiamond`: governor or admin to re-enable contract functionality after it has been frozen.

# MAILBOX

`MailboxFacet` is a system that facilitates interaction between L1 (Layer-1) and L2 (Layer-2) on the network. the main contract that provides an interface for various functions related to sending messages, withdrawing funds, and executing transactions and there are several imports in it such as OpenZeppelin related to priority queue management, and various other contracts relevant to zkSync.

### Handling Ethereum (ETH) Withdrawals from L2 to L1

takes care of withdrawing Ethereum (ETH) from Layer-2 (L2) to Layer-1 (L1). This is a necessary operation when a user wants to retrieve their ETH from the zkSync system and checks the proof `(_merkleProof)` to ensure that the requested ETH withdrawal has actually been initiated in L2. If the proof is valid, the ETH withdrawal is completed and the funds are sent to the recipient specified in L1.


```solidity
function finalizeEthWithdrawal(
    uint256 _l2BatchNumber,
    uint256 _l2MessageIndex,
    uint16 _l2TxNumberInBatch,
    bytes calldata _message,
    bytes32[] calldata _merkleProof
) external override nonReentrant senderCanCallFunction(s.allowList) {
     // @> Diff ....
}
```

### L2 Transaction Execution Request from L1

set the L2 transaction execution request from L1. This is an important part of the zkSync functionality that allows users to initiate transactions in L2 and process L2 transaction execution requests given by the user. This includes handling all the necessary details, including payment processing, transaction hash generation, validation, and setup in priority queues.


```solidity
 function requestL2Transaction(
    address _contractL2,
    uint256 _l2Value,
    bytes calldata _calldata,
    uint256 _l2GasLimit,
    uint256 _l2GasPerPubdataByteLimit,
    bytes[] calldata _factoryDeps,
    address _refundRecipient
) external payable nonReentrant senderCanCallFunction(s.allowList) returns (bytes32 canonicalTxHash) {
    //  @> Diff ....
}
```

NOTE: `Mailbox.sol` is the part that facilitates interaction between L1 and L2. This includes ETH withdrawals from L2 to L1 and L2 transaction execution requests from L1. This code ensures the security and integrity of zkSync operations and helps users interact with the system efficiently.


# GETTERS
Getters.sol, provides access to various information and status related to the Ethereum blockchain. This code is critical to various aspects of blockchain management and monitoring, as well as providing tools for developers and users to understand the state and operation of an Ethereum blockchain.

`getVerifier()`, `getGovernor()`, g`etPendingGovernor()` provides the addresses of the certifier, governor, and pending governor contracts.

`getTotalBatchesCommitted()`, `getTotalBatchesVerified()`, `getTotalBatchesExecuted()` gives the total number of batches that have been committed, verified, and executed on the blockchain.

`getTotalPriorityTxs()` returns the total priority transactions in the queue.

`getFirstUnprocessedPriorityTx()`: provides an index of the oldest unprocessed priority transaction.

`getPriorityQueueSize()`: gives the priority transaction queue size.

`priorityQueueFrontOperation()`: gives first priority transaction data in the queue.

`isValidator()`: used to check whether the given address is a validator or not.

`l2LogsRootHash():` provides the root hash of the Merkle tree used for L2 logs.

`storedBatchHash()`: provides the hash of the stored L2 batch.

`getL2BootloaderBytecodeHash()`, `getL2DefaultAccountBytecodeHash()`: gives the hash of the bootloader bytecode and the L2 default account.

`getVerifierParams()`: provides verifier parameters.

`getProtocolVersion()`: gives the current protocol version.

`getL2SystemContractsUpgradeTxHash()`: provides the L2 system contract transaction hash.

`getL2SystemContractsUpgradeBatchNumber()`: provides the batch number in which L2 system contract transactions are processed.

`isDiamondStorageFrozen()`: used to check whether Diamond storage is frozen or not.

`isFacetFreezable()`: checks whether a facet can be frozen by the governor or is always accessible.

`getPriorityTxMaxGasLimit()`: provides the maximum gas limit that a user can request for L1 -> L2 transactions.

`getAllowList()`: provides the address of the whitelist contract.

`isFunctionFreezable()`: checks whether a function can be frozen by the governor or is always accessible.

`isEthWithdrawalFinalized()`: used to check whether the Ether withdrawal has been finalized.

NOTE: consider `getTotalBlocksExecuted()`, `storedBlockHash()`, and `getL2SystemContractsUpgradeBlockNumber()` are all outdated methods and should be replaced with newer methods

# DIAMONDS
to add, replace, or delete facilitators (facets) and function selection. This is a key operation that allows Diamond contracts to change and evolve over time.

`getDiamondStorage()`:
Returns the DiamondStorage data structure used to store information related to facilitators and function selection in Diamond contracts.

`diamondCut(DiamondCutData memory _diamondCut)`:
to add, change, or remove facilitators and function selection in the Diamond contract.

`_addFunctions(address _facet, bytes4[] memory _selectors, bool _isFacetFreezable)`:
Added function selection to existing facilitator.

`_replaceFunctions(address _facet, bytes4[] memory _selectors, bool _isFacetFreezable)`:
selecting existing functions in the facilitator by selecting new functions.

`_removeFunctions(address _facet, bytes4[] memory _selectors)`:
to remove the function selection from the facilitator.

`_initializeDiamondCut(address _init, bytes memory _calldata)`:
to initialize changes to the Diamond contract, including changes to facilitators and function selection.

`_removeOneFunction(address _facet, bytes4 _selector)`:
used to delete one function selection associated with the facilitator.

`_removeFacet(address _facet)`:
removes a facilitator from the list of known facilitators if the facilitator does not have an associated function selection.


# MAILBOX TRANSACTION VALIDATOR
`TransactionValidator` is an important part of the zkSync library that is used to validate L1 to L2 transactions and upgrade transactions. By performing these checks, the zkSync network ensures that entered transactions comply with applicable rules and restrictions. This is one of the crucial components in maintaining the security and integrity of the zkSync network.

`validateL1ToL2Transaction` ensures that the gas cost required by an L2 transaction to execute the transaction body does not exceed the maximum gas limit set by `_priorityTxMaxGasLimit`.
Ensure that transactions do not generate more pubdata than the system can process.
Ensure that the minimum fees required to execute an L2 transaction are covered by the gas limit provided by the user.

`validateUpgradeTransaction`
to validate upgrade transactions on the zkSync network. These transactions differ from L1 to L2 transactions and only allow certain types of changes. This function performs a number of checks, including checking that transaction parameters meet certain requirements such as `to` and `from` constraints, as well as checking that the transaction value is zero and some other parameters comply with system requirements.

`getMinimalPriorityTransactionGasLimit` which is used to calculate the minimum gas required to run a priority transaction, and `getTransactionBodyGasLimit` which calculates the gas required for the actual transaction body.

`getOverheadForTransaction` is used to calculate transaction batch overhead, which includes the gas required for batch processing and pubdata.


# PriorityQueue

is designed for use in an L2 (Layer 2) context in blockchain networks and has the specific purpose of managing priority queues for L2 transactions.

functions that allow interaction with priority queues:
`getFirstUnprocessedPriorityTx`: Returns the index of the earliest unprocessed priority transaction.
`getTotalPriorityTxs`: Returns the total number of priority transactions that have been added to the `priority` queue, including those that have already been processed.
`getSize`: Returns the number of unprocessed priority transactions in the queue.
`isEmpty`: Checks whether the priority queue is empty.
`pushBack`: Adds a priority transaction to the end of the queue.
`front`: Returns unprocessed first priority transactions.
`popFront`: Removes and returns unprocessed first priority transactions from the queue.

# Mekrle
which is used to calculate the root hash of a Merkle tree using a given Merkle proof and this component is very important in various network protocols, especially in zksync era networks.

`function calculateRoot(...) internal pure returns (bytes32) { ... }`:
takes the `_path` array containing the Merkle proof, the index `_index` of the leaf node, and the hash `_itemHash` of the leaf node. then computes the root hash of the Merkle tree based on the given Merkle proof and returns it.

`function _efficientHash(...) private pure returns (bytes32 result) { ... }`
Auxiliary function used in Merkle root calculations. It takes two hash parameters and returns the hash result of their concatenation using bitwise operations and is declared private so it can only be accessed within this library.

((LibMap))
compresses multiple 32-bit unsigned integers into a single storage location. This can help reduce gas costs in contract development that uses many variables.

`function get(Uint32Map storage _map, uint256 _index) internal view returns (uint32 result) { ... }`:
used to retrieve uint32 values ​​from `Uint32Map` based on given index. performs bitwise operations to extract the appropriate uint32 value from the appropriate storage location.

`function set(Uint32Map storage _map, uint256 _index, uint32 _value) internal { ... }`:
changes the uint32 value at the index given in `Uint32Map` and performs bitwise and XOR operations to update the corresponding uint32 value in the appropriate storage location.


# Stroge

is part of a larger system that facilitates network operations, such as transaction execution, proof verification, and contract upgrades. some parts of the code have been marked as `deprecated`, meaning they are no longer used or are planned for removal in the future.

`enum UpgradeState`: an enumeration that provides three different values: None, Transparent, and Shadow. This enum appears to be used to indicate the types of upgrades that may occur on , such as transparent upgrades or shadow upgrades.

`struct UpgradeStorage`: an `UpgradeStorage` structure that contains various information about contract upgrades. This includes information such as `proposedUpgradeHash`, `state`, `securityCouncil`, etc. This structure is used to track upgrade status and approvals from related parties.

`struct L2Log`: `L2Log` structure that represents logs sent from L2 (layer two). This includes information such as `l2ShardId`, `isService`, `txNumberInBatch`, etc. part of the data to be processed by the contract.

`struct L2Message`: which represents the message sent from L2 . This includes information such as `txNumberInBatch`, `sender`, and `data`. a more general message that may contain any data sent from L2.

`struct VerifierParams`: data that contains parameters for the Zero-Knowledge Proof (ZKP) circuit configuration part of the configuration used by the contract to verify aggregate proofs.

`struct AppStorage`: This is a very important data structure definition. It contains various variables used by the contract, such as administrator address, contract verification, number of batches executed, etc. This is the primary storage used by the contract.

NOTE: consider hatching in SLOT trying to group variables with similar data types into the same slot to reduce unnecessary storage slot usage.


``` solidity
struct AppStorage {
    // Slot 0
    uint256[7] __DEPRECATED_diamondCutStorage;
    address governor;
    address pendingGovernor;
    
    // Slot 1
    IVerifier verifier;
    uint256 totalBatchesExecuted;
    uint256 totalBatchesVerified;
    uint256 totalBatchesCommitted;
    
    // Slot 2
    mapping(address => bool) validators;
    bytes32 l2BootloaderBytecodeHash;
    bytes32 l2DefaultAccountBytecodeHash;
    bool zkPorterIsAvailable;
    
    // Slot 3
    bytes32[256] storedBatchHashes;
    
    // Slot 4
    bytes32[256] l2LogsRootHashes;
    
    // Slot 5
    PriorityQueue.Queue priorityQueue;
    IAllowList allowList;
    VerifierParams verifierParams;
    uint256 protocolVersion;
    
    // Slot 6
    bytes32 l2SystemContractsUpgradeTxHash;
    uint256 l2SystemContractsUpgradeBatchNumber;
    
    // Slot 7
    address admin;
    address pendingAdmin;
    uint256 __DEPRECATED_lastWithdrawalLimitReset;
    uint256 __DEPRECATED_withdrawnAmountInWindow;
    
    // Slot 8
    mapping(address => uint256) totalDepositedAmountPerUser;
}
```

# VALIDATOR TIME LOCK

ValidatorTimelock is a critical component in the zkSync network that ensures security and delays batch execution in a safe and reliable manner. With a careful structure and well-described functions, it takes an important role in maintaining a safe and reliable network ecosystem. The security of the blockchain ecosystem is a priority, and `ValidatorTimelock` is one of the tools used to achieve this goal.

`setValidator`: contract owner to set the authorized validator address.

`setExecutionDelay`: contract owner to set the batch execution delay.

`commitBatches`: used by validators to commit new batches, logging commit timestamps and requests to the zkSync contract.

`revertBatches`: validator for reversing batches, requests to the zkSync contract.

`proveBatches`: to prove committed batches. This requires inputting various batch and query information into the zkSync contract.

`executeBatches`: allows validators to execute committed batches. Before execution, check that the batch is committed at least after the predefined delay.


# BRIDGES

# L1ERC20Bridge

which is designed to allow users to make deposits from ERC20 tokens on Ethereum (Layer 1 or L1) to zkSync Era (Layer 2 or L2). acts as a bridge between two networks, allowing users to send tokens from L1 to L2 and vice versa.

provides a solution for transferring tokens between L1 and L2, allowing users to manage deposits and withdrawals, as well as resolve cases when transactions fail. Overall, it supports better integration between the two networks and allows users to access the zkSync ecosystem.

`deposit`:
to start the deposit process from L1 to L2. Users need to specify the recipient address on L2, the ERC20 token address on L1, the number of tokens to be sent, the gas limit for L2 transactions, and the fee for refund if the transaction fails on L2.

`_depositFunds`:
transfer tokens from sender to L1 contract.

`_getDepositL2Calldata`:
generate data that will be used to make L2 transaction calls.

`_getERC20Getters`:
to receive the name, symbol and decimal amount of the ERC20 token to be deposited.

`claimFailedDeposit`:
reclaim failed deposits if the transaction fails in L2 and Users must provide proof of successful L2 transactions and appropriate data.

`finalizeWithdrawal`:
allows users to complete withdrawals from L2 to L1. requires proof of successful L2 transaction and will transfer tokens to the recipient address in L1.

`_parseL2WithdrawalMessage`:
parses withdrawal messages from L2 to L1.

`_verifyDepositLimit`:
check whether the deposit limit has been reached for the user.

`l2TokenAddress`:
calculates the token address in L2 that will be issued when a particular ERC20 token deposit is made in L1.

# L1WethBridge 
which is designed to facilitate the transfer of WETH tokens between the L1 (Layer 1) and L2 (Layer 2) networks. which allows users to quickly and efficiently transfer WETH tokens between L1 and L2 networks. serves as a bridge between the two networks and organizes operations such as deposits, withdrawals, and initialization of appropriate L2 contracts. Layer 2 implementation that allows increased scalability and transaction efficiency

NOTE: Perhaps the primary goal of `L1WethBridge` is to reduce the number of transactions required, increase efficiency, and facilitate direct WETH transfers between L1 and L2 networks.

`deposit`:
allows users to initiate WETH deposits to the L2 network. This involves sending WETH to contract L1, followed by sending ETH to contract L2. This process requires a calculated gas amount and can provide a refund if the deposit fails.

`finalizeWithdrawal`:
used to complete the withdrawal from L2 to L1. This involves wrapping ETH into WETH and sending WETH to a recipient on the L1 network. This function checks whether the draw has been completed in L2 before executing.

# BridgeInitializationHelper

`requestDeployTransaction`:
create a Layer-2 transaction request that will deploy a Layer-2 contract from Layer-1. accepts several parameters, including zkSync contract address, deploy transaction cost, contract bytecode hash, constructor data, and a list of required additional bytecodes and generates the address of the newly deployed contract.

serves as a library that helps in the initialization of Layer-2 contracts in zkSync networks. This includes constant settings and functions that enable the use of Layer-2 tools over Layer-1 transactions.

developers can easily deploy and initialize their Layer-2 contracts on the zkSync Layer-2 network.


# GOVERNANCE

`Governance`. managing governance operations within the Ethereum ecosystem, including contract upgrade operations. The code is carefully designed to ensure certain actions can only be executed by authorized parties and within specified delays, critical in maintaining the reliability and security of the ecosystem.

### Variables and Data Structures

`securityCouncil`: The address used to identify the security council (multisig).

`minDelay`: The minimum delay value in seconds for the operation.

`timestamps`: Mapping that associates an operation ID with the time when the operation will be ready to execute.

``` solidity
address public securityCouncil;
uint256 public minDelay;
mapping(bytes32 => uint256) public timestamps;
```

### Important Functions

`isOperation`: Checks whether an operation is listed.

`isOperationPending`: Checks whether an operation is pending.

`isOperationReady`: Checks whether an operation is ready to execute.

`isOperationDone`: Checks whether an operation is complete.

`getOperationState`: Returns the operation state based on time.

### Operation Scheduling
#### There are two types of surgery that can be scheduled:

`scheduleTransparent`: transparent allowing the owner to execute with a delay or the security board to execute immediately.

`scheduleShadow`: shadow without open upgrade data.

### Operation Cancellation

`cancel`: Cancels an operation scheduled by the owner or security board.

### Operation Execution

`execute`: The owner or security board can execute an operation with a delay after the previous operation check is complete.
`executeInstant`: The security board can execute operations without delay.

### Help Function
#### There are several auxiliary functions, such as:

`hashOperation`: Calculates the hash of the operation.

`_schedule`: Sets the operation scheduling.

`_execute`: Executes an operation with multiple contract invocations.

`_checkPredecessorDone`: Checks whether the predecessor operation has completed.

# BaseZkSyncUpgrade

is the core of the zkSync system, is a solution for scalability on top of the Ethereum network.`BaseZkSyncUpgrade`, plays a role in managing the protocol upgrade process at the Layer-2 (L2) layer of the zkSync system an important part of the zkSync system and manages the protocol upgrade process to ensure the function of the platform Secure and efficient L2 on top of the Ethereum network.

`Struct ProposedUpgrade`: defines a `ProposedUpgrade` structure, which represents a protocol upgrade proposal. This proposal includes information such as L2 transactions for system upgrades, bootloader bytecode hash, default account bytecode hash, confirming address, confirming parameters,

`Event Log`: records a number of events via the event log. Events such as `NewProtocolVersion`, `NewL2BootloaderBytecodeHash`, and `NewL2DefaultAccountBytecodeHash` are used to track changes in the protocol version and bootloader code as well as the default account code at the L2 layer.

`upgrade`: applies a protocol upgrade. It checks whether the upgrade is ready to go by comparing the time specified in the upgrade proposal with the time when `upgrade` was called.

`Internal Functions`: This contract has a number of internal functions, such as `_setL2DefaultAccountBytecodeHash`, `_setL2BootloaderBytecodeHash`, `_setVerifier`, etc. to manage various aspects of the upgrade, including changes to the default account code hash, bootloader code hash, confirming address, and confirming parameters.

`_upgradeVerifier` & `_setBaseSystemContracts`: overrides the confirmer address and parameters, and manages the hash of the bootloader code and default account code.

`_setL2SystemContractUpgrade`: L2 system transactions for system upgrades and verifies transaction types to ensure that transaction hashes are unique.

`_verifyFactoryDeps`: verifies that the factory dependency list matches the expected bytecode hash. This ensures the consistency of the bytecode used in the system.

`_setNewProtocolVersion`: changes the protocol version. However, changes to the protocol version must be higher than the previous version and can only be done if the previous upgrade has been completed.

`_setAllowList`: overrides the allow list contract address.


# L2 contracts

# ContractDeployer

serves as a key part of the zkSync system, which is designed to support the implementation of smart contracts on the zkRollup network. serves to deploy and manage smart contracts on the zkRollup network, this component is critical in enabling the secure and efficient execution of smart contracts in the zkSync environment.

The main function

`getNewAddressCreate2`
calculates the contract address to be created using the `create2` method based on certain parameters, such as sender, bytecode hash, salt, and constructor data.

`getNewAddressCreate`
Similar to the previous function, but used for the regular create method.

`create2 and create`
deploy a smart contract using the usual `create2` and `create` methods, to generate a new contract address and ensure that the constructor is only executed once

`Nonce Management and Account Abstraction Versioning`
manages information about account abstraction nonce and version, which is important in the implementation of the contract in zkSync.

`Constructor and Bytecode Management`
manages contract placement by ensuring the constructor is only executed once and that the bytecode of the contract is known to the system.

`Value Management and Constructors`
has a mechanism to manage the values ​​sent to the contract during deployment, as well as running the contract constructor securely.


# BootloaderUtilities

an important part of the zkSync ecosystem that provides utility functions for calculating zkSync transaction hashes. helps understand how zkSync manages various types of transactions and can be used in smart contracts, wallets, and other zkSync-related tools. Although it is written in Solidity and provides the utility functions necessary for various zkSync operations, its logic is written in Solidity instead of yul for convenience and development flexibility

`getTransactionHashes`
accepts transaction parameters and returns two hashes: "txHash" and "signedTxHash

`encodeLegacyTransactionHash, encodeEIP2930TransactionHash, and encodeEIP1559TransactionHash`
computes hashes for zkSync-compliant transaction types. This involves coding the appropriate parameters, such as nonce, gasPrice, gasLimit, destination address, value, transaction data, access list, and signature. The result is a transaction hash according to its type.

`RLPEncoder`
some zkSync transaction parameters. This includes encoding nonce, gasPrice, gasLimit, destination address, value, transaction data, and signature. The results of this encoding are combined to calculate the transaction hash.

`Three Types of zkSync Transactions`
I see three types of zkSync transactions, namely transactions using `EIP-712`, legacy transactions, and transactions `EIP-1559`. Each transaction type has a different hash encoding, and appropriate logic is implemented in the code to calculate the hash according to the transaction type

# L1Messenger
to send messages of arbitrary length from L2 to L1. This allows sending messages that differ from the fixed-length messages that ZkSync sends by default in a secure and verified manner

### sendL2ToL1Log
sends logs from L2 to L1. These logs are used to monitor activity at both layers. This function can only be called by the system contract and creates a log entry with the given parameters.

### sendToL1
track gas expenditure used in sending messages.

### Validation Process

`publishPubdataAndClearState` received data is verified to ensure that logs, messages, bytecodes, and state diffs are as expected. This involves hash calculation, hash comparison, and message length verification, as well as ensuring that all data is received

### L2 to L1 Log Sending and Data Deletion
to submit public data and delete internal data used during the process and enable logs that inform L1 about data collected in previous batches.

# SystemContext
enables context management, including origin address settings, gas prices, block hash retrieval, batch hash retrieval, and addition of transaction hashes and reliability of ZKSync operations. helps maintain consistent ZKSync operations, reduces costs, and increases scalability at Layer-2 and provides an efficient and effective alternative in transaction processing

### Variables and Data Structures

contains various public and internal variables that store various types of data related to ZKSync, including information about blocks, transactions, and batches. Some important variables in the context of ZKSync include:

`chainId`: Identification number of the network being used

`origin`: The origin address of the transaction being executed

`gasPrice`: The price of gas in the current transaction

`blockGasLimit`: Current block gas limit

`coinbase`: Address receiving rewards from transactions

`difficulty`: Current block difficulty parameter

`baseFee`: The base fee of the transaction.

NOTE:There are also data structures such as `BlockInfo` and `VirtualBlockUpgradeInfo` that are used to store information about blocks and virtual block upgrades in ZKSync.

# SystemContractHelper.sol

access specific information required by system contracts in the ZkSync ecosystem, an integral part of the infrastructure that enables L2 (Layer 2) operations and leverages information and logic for reliable and efficient execution

`getZkSyncMetaBytes()`

aims to retrieve a compressed representation of ZkSyncMeta, which is a data structure that holds important information regarding system execution. This information includes gas per byte of pubdata, heap size, auxiliary heap size, shard ID etc.

`extractNumberFromMeta(uint256 meta, uint256 offset, uint256 size)`

a specified number of bits of the compressed ZkSyncMeta representation. This information may not always be contiguous in bits, so this function is useful for retrieving the necessary parts.

`getGasPerPubdataByteFromMeta(uint256 meta)`

gas value per pubdata byte of the ZkSyncMeta representation. This gas is the cost required to send one byte of data to L1 (Layer 1).

`getHeapSizeFromMeta(uint256 meta)`

current heap (memory) size in bytes. This is important because heap size can affect system execution costs and performance.

`getAuxHeapSizeFromMeta(uint256 meta)`

The auxilary heap is currently in bytes and this auxiliary Heap may be used for temporary storage of data required during system execution.

`getShardIdFromMeta(uint256 meta)`

retrieves the shard ID from the ZkSyncMeta representation. Shards are parts of a network that can have different execution rules and logic.

`getCallerShardIdFromMeta(uint256 meta)`

retrieves the shard ID of the message sender (msg.sender). This can help in understanding from which shard the message was received.

`getCodeShardIdFromMeta(uint256 meta)`

retrieves the shard ID of the currently executing code. This makes it possible to understand from which shard the contract code is running.

`getZkSyncMeta()`

one of several functions that combines information from multiple previous functions and returns a complete ZkSyncMeta data structure.

`getCallFlags()`

get call flags that contain information such as whether the call is a constructor or a system call.

`getCalldataPtr()`
retrieves a pointer to the current calldata data. However, NOTE: it is important to note that this pointer is just an integer and cannot be used to pass calldata to the next call.

`getExtraAbiData(uint256 index)`
I saw some code that allows only 10 registers to be accessed, and is used to retrieve data from these registers.

`isSystemCall()`
returns true if the current call is a system call.

`isSystemContract(address _address)`
almost similar to `isSystemCall` but if the address provided is the system contract address.

`burnGas(uint32 _gasToPay)`
to burn a specified amount of gas. This is an important part of the system for managing gas consumption.


# TransactionHelper

defines the various operations required to manage and process transactions in zkSync and is designed to perform specific tasks related to zkSync transactions, such as hash encoding, paymaster flow handling, and transaction fee calculations. All of this is an important part of the ecosystem that supports safe and efficient transaction execution

### Transaction Structure:

This includes transaction types such as `EIP_712_TX_TYPE` and `EIP_2930_TX_TYPE`. Additionally, a Transaction structure to represent zkSync transaction details.

### TransactionHelper Library:

assists in zkSync transaction processing, including transaction hash encoding, paymaster flow handling, and necessary balance calculations.

### Encode Transaction Hash:

encode transaction hashes according to different transaction types, such as `EIP-712`, `EIP-2930`, and `EIP-1559`.

### Paymaster Flow Process:

`processPaymasterInput` manages the paymaster flow, such as setting token permissions or passing necessary data to the paymaster.

### Payment to Bootloader:

`payToTheBootloader` pays transaction fees to the bootloader. Here, the fee paid is the maximum between maxFeePerGas and gasLimit.

### Calculation of Total Required Balance:

`totalRequiredBalance` calculates the total balance required to process the transaction. This includes transaction fees and the value transferred in the transaction.


# Bootloader.yul

organize various functions and logic that are important to process transactions efficiently and securely in a Layer 2 environment. organize logic to process transactions with correct gas prices, calculate base fees, and ensure transactions are executed correctly and provide transparency and control over Layer 2 operations and maintain the integrity of the ZKSYNC network.

#### Initialization and Setting of Gas Prices

Ethereum (L1_GAS_PRICE) received from the Ethereum Layer 1 (L1) blockchain and the fair Layer 2 gas price (FAIR_L2_GAS_PRICE). Operators play a role in providing gas prices. It also initializes some other variables such as PREV_BATCH_HASH, NEW_BATCH_TIMESTAMP, and NEW_BATCH_NUMBER.

```
let L1_GAS_PRICE := mload(128)
let FAIR_L2_GAS_PRICE := mload(160)
```

#### Base Fee Calculation

calculate the base fee based on fair Layer 1 gas prices and Layer 2 gas prices. This will be used to process transactions at Layer 2.

```
baseFee, GAS_PRICE_PER_PUBDATA := getBaseFee(L1_GAS_PRICE, FAIR_L2_GAS_PRICE)
```

#### Initialize New Batch

prepare a new batch that will be used to process transactions. This includes setting PREV_BATCH_HASH, NEW_BATCH_TIMESTAMP, NEW_BATCH_NUMBER, and EXPECTED_BASE_FEE

```
setNewBatch(PREV_BATCH_HASH, NEW_BATCH_TIMESTAMP, NEW_BATCH_NUMBER, EXPECTED_BASE_FEE)
```

#### Transaction Processing

This involves setting a pointer to the next transaction, checking whether the transaction should be executed, checking the transaction data structure, and then processing the transaction. During processing, the code monitors whether the transaction succeeded or failed.


```
processTx(txDataOffset, resultPtr, transactionIndex, isETHCall, GAS_PRICE_PER_PUBDATA)
```

#### Batch Closing

After all transactions are processed some final actions. This includes setting the pubdata price, resetting variables such as tx.origin and gasPrice, transferring the received ETH to the operator, and updating the data to be stored in L1 and some sending system logs to L1 and completing the batch.

There are several unique uses such as:

#### Use of VM Hook

to communicate with the L2 layer and perform certain actions. This allows further control over transaction processing and network operations

```setHook(VM_HOOK_ACCOUNT_VALIDATION_ENTERED())``` operator that transaction account validation must be initiated

```setHook(VM_HOOK_TX_HAS_ENDED())``` transaction execution has completed. It helps in managing the transaction index and related actions after the transaction.

```setHook(VM_HOOK_FINAL_L2_STATE_INFO())``` final information for the batch must be provided. This is an important part of batch completion in L2.

```setHook(VM_HOOK_DEBUG_LOG())``` is used to record debugging logs that can help in troubleshooting and monitoring system operations.

#### The unique of the bootloader

The main unique of the bootloader is managing gas prices, base fees, and transaction processing in an L2 environment. This allows L2 to operate in an efficient and independent manner, while remaining connected to the Layer 1 network. This unique lies in the code's ability to communicate with L1, determine reasonable gas prices, and execute transactions on L2 and is closely related to the security and integrity aspects L2 operations, including transaction validation of the infrastructure that allows Layer 2 to function efficiently and reliably on top of the Ethereum Layer 1 network.


# EcAdd.yul

The code is highly technical and complex and has a strict mathematical implementation and has several layers of validation to ensure that the operations performed conform to the rules of elliptic curves alt_bn128

- Constants and Supporting Functions
defines some constants used in subsequent operations on elliptic curves. For example, `MONTGOMERY_THREE()` is a constant for the value three in Montgomery form

- Support Functions
`precompileCall` which simulates calling the opcode `precompileCall` and other functions such as burnGas, which is used to burn gas in the case of a failed precompile call.

- Operations on Elliptic Curves
`getHighestHalfOfMultiplication` which returns the highest half of the multiplication result, submod which calculates the modular subtraction

- Functions in Montgomery Form
used to convert values ​​into Montgomery form and out of Montgomery form. such as `montgomeryAdd`, `montgomerySub`, `montgomeryMul`, and `montgomeryDiv`.

- Addition Logic on Elliptic Curves
i saw some addition logic on an elliptic curve `alt_bn128`, it implements adding points P1 and P2 to produce point P3 on the elliptic curve. This involves some very sophisticated mathematical operations, including calculating the slope between two points and using appropriate formulas to calculate the coordinates of the added points.

There are several unique characteristics of the `alt_bn128` elliptic curve that I like the most, namely:

- Special Weierstrass: This elliptic curve is described by the special Weierstrass equation,`y^2 = x^3 + 3`. Notably, the value of a in this equation is 3, which differentiates it from other elliptic curves.

- Montgomery: To optimize mathematical operations on these elliptic curves, the Montgomery format uses a special representation that allows modular multiplication and addition operations to be more efficient.

`montgomeryAdd` implements the add operation in Montgomery format the efficiency of the Montgomery representation. If the addition result exceeds the limit of the elliptic curve (P()), then a modular subtraction operation is performed to ensure the result remains within the curve. This is one unique example where the Montgomery format plays an important role in `alt_bn128` elliptic curve operations.


```
239:             function montgomeryAdd(augend, addend) -> ret {
240:                 ret := add(augend, addend)
241:                 if iszero(lt(ret, P())) {
242:                     ret := sub(ret, P())
243:                 }
244:             }
```

# Systemic risks

### Mailbox Security

Merkle Proof Validation:
`proveL2MessageInclusion` and `_proveL2LogInclusion` are used to verify the Merkle proof. This is an important step to ensure that operations such as withdrawing funds or executing L2 transactions are only carried out if the Merkle proof provided by the user is truly valid.
``` solidity
bool proofValid = proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);
require(proofValid, "pi");
```

Withdraw Limit Check:
`_verifyDepositLimit` checks that users have not exceeded their limits on withdrawing funds from the system. This is an important step to prevent unwanted expenses.
``` solidity
require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
```

Handling of Withdrawals and Fund Transfers (Withdrawal and Fund Transfer Handling):
consider ensuring that ETH withdrawals or fund transfers are done correctly. Using `finalizeEthWithdrawal` ensures that withdrawals are only made if all validations are successful.

``` solidity
require(!s.isEthWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "jj");
```

Priority Queue Management (Priority Queue Management):
The system uses a priority queue to manage transactions to be executed. This helps in processing important transactions first, which is especially relevant in the context of zkSync L2 systems.


``` solidity
s.priorityQueue.pushBack(PriorityOperation({
    canonicalTxHash: canonicalTxHash,
    expirationTimestamp: _priorityOpParams.expirationTimestamp,
    layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
}));
```

### Systemic risks Executor

`onlyValidator` In some functions, such as `commitBatches`, there is an `onlyValidator` modifier. This modifier ensures that only authorized parties (validators) can access these functions. an important step to maintain the security of the protocol, and similar modifiers are used throughout the code to regulate access.

`require` in the `_commitOneBatch` function, there is a check to ensure that `_newBatch.batchNumber` is greater than `_previousBatch.batchNumber` before the new batch can be committed. This ensures that batches can only be committed sequentially.


### Systemic risks Diamond 

`Delegatecall`:
Delegatecall can allow a Diamond contract to call functions from another contract. This potentially opens the door to malicious users if the selection of changed functions and facilitators is not properly monitored.

`Contract Initialization`:
Errors in contract initialization can result in serious security damage. Initialization must be done carefully to ensure the contract runs correctly.

`Exception Handling`:
Improper exception handling can cause damage to reentrancy attacks or similar attacks.

`Selection of Added or Replaced Functions`:
incorrectly set or improperly supervised operation of diamondCut may cause damage to undesirable changes in the Diamond contract.

Storage Usage and Storage Handling:
Improper use of storage or lack of security measures in data storage can be a potential damage.


### Systemic risks Bridge 


`Refund Management`:
Using `AddressAliasHelper` is sufficient to protect current refund addresses and consider refund addresses set correctly and securely.


``` solidity
File: f:\security\2023-10-zksync\code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol
176:     function deposit(
177:         address _l2Receiver,
178:         address _l1Token,
179:         uint256 _amount,
180:         uint256 _l2TxGasLimit,
181:         uint256 _l2TxGasPerPubdataByte,
182:         address _refundRecipient
183:     ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) {

// ...
192:         // Otherwise, the refund will be sent to the specified address.
193:         // If the recipient is a contract on L1, the address alias will be applied.
194:         address refundRecipient = _refundRecipient;
195:         if (_refundRecipient == address(0)) {
196:             refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
197:         }
// ...

212:     }
```

Input Data Validation: :
Consider Always validate input data very carefully to prevent `overflows` and `underflows` from occurring.

```solidity
176: function deposit(
177: address _l2Receiver,
178: address _l1Token,
179: uint256 _amount,
180: uint256 _l2TxGasLimit,
181: uint256 _l2TxGasPerPubdataByte,
182: address _refundRecipient
183: ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) {
184: require(_amount != 0, "2T"); // empty deposit amount
@>>> uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
@>>> require(amount == _amount, "1T"); // The token has non-standard transfer logic
// ...
212: }

216: function _depositFunds(address _from, IERC20 _token, uint256 _amount) internal returns (uint256) {
217: uint256 balanceBefore = _token.balanceOf(address(this));
218: _token.safeTransferFrom(_from, address(this), _amount);
219: uint256 balanceAfter = _token.balanceOf(address(this));
220:
@>> return balanceAfter - balanceBefore;
222: }
```


### Security Features ContractDeployer

several unique features that differentiate it from common smart contracts in general and is a secure and efficient layer-2 network (zkRollup)

`Security System Devices and Upgrades`:
mechanisms for upgrading systems and contracts with high security. The `forceDeployOnAddresses` function allows the system to forcefully deploy contracts to specific addresses as part of the system upgrade process


```solidity
238:     function forceDeployOnAddresses(ForceDeployment[] calldata _deployments) external payable {
239:         require(
240:             msg.sender == FORCE_DEPLOYER || msg.sender == address(COMPLEX_UPGRADER_CONTRACT),
241:             "Can only be called by FORCE_DEPLOYER or COMPLEX_UPGRADER_CONTRACT"
242:         );
243: 
244:         uint256 deploymentsLength = _deployments.length;
245:         // We need to ensure that the `value` provided by the call is enough to provide `value`
246:         // for all of the deployments
247:         uint256 sumOfValues = 0;
248:         for (uint256 i = 0; i < deploymentsLength; ++i) {
249:             sumOfValues += _deployments[i].value;
250:         }
251:         require(msg.value == sumOfValues, "`value` provided is not equal to the combined `value`s of deployments");
252: 
253:         for (uint256 i = 0; i < deploymentsLength; ++i) {
254:             this.forceDeployOnAddress{value: _deployments[i].value}(_deployments[i], msg.sender);
255:         }
256:     }
```

`Account Abstraction Security and Version Control`:
has the ability to control the version of the account abstraction used. This version can be updated by the contract account owner and is used in the account abstraction model in zkSync.

# Gas Optimizations

### Mailbox

Gas Limitation Check:
consider gas limit checks, which require users to pay a sufficient amount of gas for the operations they perform. This is related to the estimated gas fees for L2 transactions.

``` solidity
uint256 l2GasPrice = _deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit);
return l2GasPrice * _l2GasLimit;
```

NOTE: when validating the Merkle proof, guard against unauthorized withdrawal of funds. Therefore, it is important to always ensure that Merkle proof validation and other validations are implemented correctly and safely.


### Executor

Bitwise Operation: in `_setBit` function to optimize gas usage. For example,setting of bits in integers is done efficiently.

Gas Usage Limit: statement that checks gas usage limits. For example, in the `_verifyBatchTimestamp` function `require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1");` there is a statement that checks that `New batch timestamp is too small` and `The last L2 block timestamp is too big.` This ensures that operations do not consume too much gas.

External Function Calls: Using the external keyword in function declarations is one way to regulate gas usage. These functions can be called from outside the contract, and this can affect gas costs because external calls are more expensive than internal calls.

Algorithm Optimization: processing L2 to L1 logs and other operations require algorithm optimization to reduce gas usage. This involves selecting efficient algorithms and using appropriate data.

Storage Optimization: Selection of the right storage type `(storage, memory, or calldata)` and efficient storage management are important factors in optimizing gas usage.

### Diamond

`Delegatecall`:
If Delegatecall usage is not optimized, it can result in excessive gas usage. Therefore, it is necessary to ensure that the use of Delegatecall is optimized.

`Data Storage`:
Using excessive data storage in contracts can take a lot of gas. Too many unnecessary storage variables can waste gas.

`Contract Initialization:`
Contract initialization that is too complex or lengthy can consume more gas than necessary.

`Exception Handling`:
Inefficient or excessive exception handling in contracts can waste additional gas.

`Selection of Added or Replaced Functions`:
If too many function selections are added or replaced in one `diamondCut` operation, this can result in high gas usage.

`Storage Usage and Storage Handling`:
Inefficient use of storage in contracts can result in higher gas.


### L1ERC20Bridge

Some functions perform I/O operations to L2 contracts, such as `zkSync.requestL2Transaction`. Such operations require significant gas usage and care must be taken not to exceed reasonable transaction cost limits.

```solidity
198:         l2TxHash = zkSync.requestL2Transaction{value: msg.value}(
199:             l2Bridge,
200:             0, // L2 msg.value
201:             l2TxCalldata,
202:             _l2TxGasLimit,
203:             _l2TxGasPerPubdataByte,
204:             new bytes[](0),
205:             refundRecipient
206:         );
```
Consider reducing unnecessary use of gas storage, such as the use of mapping. Use gas storage sparingly to reduce transaction costs.

```solidity
61:     mapping(address => mapping(address => uint256)) public totalDepositedAmountPerUser;
```

### BaseZkSyncUpgrade


`_verifyFactoryDeps`
calculations to the bytecode hash and comparison are carried out repeatedly. Optimizing these calculations can help reduce gas costs.


```
200:     function _verifyFactoryDeps(bytes[] calldata _factoryDeps, uint256[] calldata _expectedHashes) private pure {
201:         require(_factoryDeps.length == _expectedHashes.length, "Wrong number of factory deps");
202:         require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32");
203: 
204:         for (uint256 i = 0; i < _factoryDeps.length; ++i) {
205:             require(
206:                 L2ContractHelper.hashL2Bytecode(_factoryDeps[i]) == bytes32(_expectedHashes[i]),
207:                 "Wrong factory dep hash"
208:             );
209:         }
210:     }
```

NOTE: bytecode hash calculations can be stored in temporary variables in case of long loops, to reduce gas usage.

### ContractDeployer

`getNewAddressCreate2` and `getNewAddressCreate` enable efficient calculation of contract addresses according to the `CREATE2` and `CREATE` models. This optimizes gas costs in the contract creation process

# Executor Diagram and Architecture

![ssszzz](https://github.com/yongsxyz/zksyncss/assets/39027215/aeab53b5-5fb1-43db-9408-cb847a4b64dd)

   - commitBatches
     - Parameters: _lastCommittedBatchData, _newBatchesData
     - Checks the commitment of new batches
     - Commits batches with or without system contracts upgrade
     - Updates total batches committed

   - _commitBatchesWithoutSystemContractsUpgrade
     - Commits new batches without system contracts upgrade
   
   - _commitBatchesWithSystemContractsUpgrade
     - Commits new batches with system contracts upgrade
     - Ensures the upgrade transaction is executed atomically
   
   - _collectOperationsFromPriorityQueue
     - Collects priority operations from a priority queue
     - Calculates a rolling hash of operations
   
   - _executeOneBatch
     - Executes one batch
     - Processes priority operations
     - Updates L2 logs root hash and total batches executed
   
   - executeBatches
     - Executes batches, completes priority operations, and processes withdrawals
     - Verifies the zk proof
   
   - proveBatches
     - Batch commitment verification
     - Verifies the zk proof for multiple batches
   
   - revertBatches
     - Reverts unexecuted batches
     - Updates total batches committed and verified
     - Handles system contracts upgrade
   
   - _maxU256
     - Returns the larger of two uint256 values
   
   - _createBatchCommitment
     - Creates a batch commitment from batch data and state diff hash
   
   - _batchPassThroughData
     - Returns encoded batch pass-through data
   
   - _batchMetaParameters
     - Returns encoded batch meta parameters
   
   - _batchAuxiliaryOutput
     - Returns encoded batch auxiliary output
   
   - _hashStoredBatchInfo
     - Calculates the keccak hash of a StoredBatchInfo structure
   
   - _checkBit
     - Checks if a specific bit in a number is set
   
   - _setBit
     - Sets a specific bit in a number to 1

   - BlockCommit
     - Emits information about a committed batch
   - BlockExecution
     - Emits information about an executed batch
   - BlocksVerification
     - Emits information about batch verification
   - BlocksRevert
     - Emits information about batch reversion

   - VerifierParams
    - Contains parameters used for verification of zk proofs

# Mailbox Diagram and Architecture

  ![zxcxzcxzcxz](https://github.com/yongsxyz/zksyncss/assets/39027215/a4f487d5-e269-4149-b4fa-871b21e42247)

- User initiates an transaction to request an L2 withdrawal.

- Request goes to the Mailbox.

- Performs an initial check.

- It then extracts information from the withdrawal message.

- Creates an L2 message and verifies it using a Merkle proof.

- Marks the withdrawal as complete.

- Sends the Ether to the specified receiver.

- Emits an "EthWithdrawalFinalized" event.

- User can also request L2 transactions.

- Checks if the user has enough Ether.

- Calculates the transaction cost and validates it.

- Adds the transaction to the priority queue.

- Emits a "NewPriorityRequest" event.

- Finally, the Bootloader or Operator processes the L2 transactions.


# Conclusion Codebase Quality Analysis
The ZKSYNC codebase quality practices that is easy to read, well written, and comes with self-explanatory documentation. Additionally, the code is neatly organized, with very clear use of variable and function names. However, there are recommendations to further optimize complex parts of the code, especially in the Rust language, to improve code readability.


### Time spent:
82 hours