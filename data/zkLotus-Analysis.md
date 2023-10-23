# ToC
- [ToC](#toc)
- [Testing the zkSync Era bootloader](#testing-the-zksync-era-bootloader)
  * [Remediations](#remediations)
    + [Possible test suite](#possible-test-suite)
    + [Logging capability](#logging-capability)
    + [Exploring bootloader memory](#exploring-bootloader-memory)
  * [Analyzing bootloader tooling](#analyzing-bootloader-tooling)
  * [Assessing testing criteria](#assessing-testing-criteria)
    + [Criteria for testing the bootloader](#criteria-for-testing-the-bootloader)
  * [Exploring tooling options](#exploring-tooling-options)
    + [Roadblocks: single transaction mode](#roadblocks--single-transaction-mode)
    + [Logging woes](#logging-woes)
    + [Results](#results)
- [Architectural risks](#architectural-risks)
  * [L2 batch timestamp manipulation](#l2-batch-timestamp-manipulation)
    + [Discussion](#discussion)
  * [Possible money steal using MsgValueSimulator](#possible-money-steal-using-msgvaluesimulator)
    + [Discussion](#discussion-1)
  * [Transaction gas price can be manipulated by user](#transaction-gas-price-can-be-manipulated-by-user)
    + [Discussion](#discussion-2)
  * [NonceHolder.isNonceUsed() doesn't work with Arbitrary nonce](#nonceholderisnonceused---doesn-t-work-with-arbitrary-nonce)
    + [Discussion](#discussion-3)
  * [Possible L1 bridge DoS through processing a batch that will OOG on L1](#possible-l1-bridge-dos-through-processing-a-batch-that-will-oog-on-l1)
    + [Discussion](#discussion-4)
- [Governance](#governance)
  * [Variables](#variables)
  * [Modifiers](#modifiers)
  * [Constructor](#constructor)
  * [Functions pertaining to operation states](#functions-pertaining-to-operation-states)
  * [Main Functions](#main-functions)
- [Executor Facet Exploration](#executor-facet-exploration)
  * [CommitBatches](#commitbatches)
  * [ExecuteBatches](#executebatches)
  * [ProveBatches](#provebatches)
  * [RevertBatches](#revertbatches)



This analysis report delves into various sections and modules of the zkSync Era codebase that the RED-LOTUS team audited, specific sections covered are listed within the table of contents above.

Our approach included a thorough examination and testing of the codebase, research on wider security implications, economic risks applicable to the protocol and expanded discussion of risks associated with the use of cross-chain messaging services by Tapioca DAO. 

A number of potential attack vectors were identified related to theoretical weaknesses of game theory and inherent systemic risks associated with specific functionality of the protocol. We supplied feedback on specific sections of protocol architecture and give other recommendations as relevant.

Throughout our analysis, we aimed to provide a comprehensive understanding of the codebase, suggesting areas for possible improvement. To validate our insights, we supplemented our explanations with wider industry research, demonstrating robust comprehension of zkSync Era's internal code functionality and interaction with system contracts.

Our ultimate goal is to provide a report that will give the project team wider perspective and value from our research conducted to strengthen the security posture, usability and efficiency of the protocol.

# Testing the zkSync Era bootloader 

> NOTE: This was performed during the the last week of the Code4rena contest. While the time constraints prevented us from fully completing the endeavor, we plan to follow up post-contest. 

Remediations are included at the start of this document for pragmatic readers, and what follows is an analysis of the tooling available for the bootloader to be tested during an audit. 

To motivate this exploration, testing the bootloader was done with the intent of embodying the persona of a new hire attempting to build a testing suite for a niche aspect of the zkSync system.   While the original intention was to build a fully functional test suite, the time allotted was too short to fully implement this vision.
## Remediations

At the end of all this time, several suggestions were considered for the Matter Labs team to address:
1. Provide an accessible, easy-to-update test suite, for the bootloader, to exercise both typical and edge-case transactions
2. Add easy-to-access logging capability for further development, and auditing, of the bootloader
3. Allow for accessible exploration of the bootloader memory throughout the execution of the bootloader

### Possible test suite 

For testing the bootloader, many kinds of scenarios can be formulated.  The general structure of the scenarios is the execution of batches.  In the the case that a batch contains a multitude of transactions, there is possibility of the state machine - that is implied by the bootloader's code graph and memory - could provide opportunities for the invariants of the system to be violated.  

It would be valuable to create tests that exercise the bootloader in manners that demonstrate the invariants hold. High-level descriptions of invariants to test against attacks would be:
- escalate transaction type to privileged upgrade
- premature batch closure
- skipped transactions
- update batch data after closing
- incorrect gas calculations
- wrong tx.origin, msg.sender generated
- priorityQueue reordering
- general overwrite transaction data within batch
  
### Logging capability

Put simpily, providing easily accessible logging to the system through the test node would make exploration, development, and refactoring of the bootloader code a smoother process.  
### Exploring bootloader memory

Being able to peek into the memory of a running bootloader would be a powerful device.  Possible methods could be - with no attempt to rank how difficult it would be to implement:
1. Binary memory dumps: dump memory to files during execution so that a hex editor can be used to explore the data. Possible options could be to dump before or after each transaction, after a timeout, at every state change, etc.
2. Provide a hook/file/endpoint for an external debugger to connect to: the canonical example is the Chrome webtools, which famously can connect to an external host to debug process on other devices.  Likely there could be challenges in the control of the test node, pausing and resuming operation
3. Include an internal debugger
## Analyzing bootloader tooling
## Assessing testing criteria 

A survey of the provided codebase was made for any files related to the operation of the bootloader internals.  No testing suites were found to satisfy that criteria.

While examining the code of the bootloader itself, it was clear that the structure of this subsystem was unsuited to run-of-the-mill testing. This typically involves interacting with explicit interfaces defined in the code, including externally visible functions and constructors.  What was found is that the bootloader operates on memory loaded by the zkSync Era EVM (which we'll call "zkEVM" at times throughout this document). Additionally, there were no externally-visible functions, and no constructor.  

> NOTE: In the scope of the zkSync Era codebase, it is clear that the bootloader was coded in Yul to take advantage of the increased control over the resulting compiled code's performance.  The consequence here is that, for Yul, the limited tooling in the wider EVM-based ecosystem leaves a lot to be desired in terms of developer experience, including the ability to test and benchmark Yul codebases.

At this point, there were two major and relatively obvious paths that could be taken:
1. Existing tools could be used to load and manipulate the bootloader memory and its environment
2. The bootloader could be modified to provide external interfaces and a constructor to be used in popular testing frameworks

It was decided that controlling the execution environment would be the best bet. The main factors were:
- controlling the memory to the granularity required by the bootloader would require brittle and difficult-to-maintain, parallel data structures between the zkEVM and test suite
- modifying the code changed the attack surface compared to the original implementation
- any modifications would likely change the memory outlay and performance characteristics of the original implementation
### Criteria for testing the bootloader

During this time, the bootloader code itself was being analyzed for possible attack vectors. As a result, a handful of criteria were surfaced that could help explore these attacks:
1. Many transactions would need to be available to load into memory to explore execution paths in the case memory segregation was compromised
2. Full control over each transaction was necessary to explore cases where invariants across the zkEVM and bootloader execution were violated 
3. Logging and exploring the memory space was key
## Exploring tooling options

Initially the in-scope, zkSync Era EVM code was explored to be used for loading the bootloader and constructing its memory.  The understanding was that the zkEVM already had data structures and interfaces available that would make loading arbitrary transactions into the bootloader's memory simple.  

While the code was available to explore, it was determined that the necessary "plumbing" to quickly get a proof-of-concept would take more time to understand and modify than was available.  After discussing possibilities with the C4 sponsors, it was discovered that there was in fact a "test node" in development (era_test_node) that could provide a minimal environment for running the bootloader against individual transactions. This was quickly determined to be useful, and simple entry points and data structures were identified for controlling the bootloader memory:

```rust
// matter-labs/era-test-node, src/node.rs
324:    if !transactions_to_replay.is_empty() {
325:        let _ = node.apply_txs(transactions_to_replay);
326:    }
```

```rust
// matter-labs/era-test-node, src/node.rs
1204: pub fn run_l2_tx_inner(
... 
1245:         let tx: Transaction = l2_tx.clone().into();
1246: 
1247:         vm.push_transaction(tx);
```

```rust
// matter-labs/zksync-era, core/lib/types/src/l2/mod.rs
133: pub struct L2Tx {
```

### Roadblocks: single transaction mode

During discussions with one of the sponsors, it was made clear that the zkEVM used with the test node was configured to only run the bootloader with a single transaction at a time.  This would be a roadblock.  Scanning the code, it was determined that the solution was to include an existing VM setting:

```rust
// matter-labs/zksync-era, core/lib/multivm/src/interface/types/inputs/execution_mode.rs
8:    vm::VmExecutionMode::Bootloader
```

### Logging woes 

During the implementation of a rough proof-of-concept, it was made clear that there was no easy way to read the logs from the bootloader through the test node.  The two suggestions from a sponsor were:
1. Use the `log0` - `log4` events in Yul so logs could be caught by the test node. 
2. Send transactions to the `Console` system contract. 

For the first option, the output would be a series of hex-encoded values according to the event parameters.  For the second option, the test node would output the logged strings using when run with the correct command-line setting.  This would be the preferred option.  

Unfortunately, neither seemed to be a great option, since the existing `debugLog` calls in the existing bootloader would not work. A third option was to be forking the `zksync-era` crate that was imported into the project and adding `println!` calls for when the `debugLog` hook was triggered.

### Results

While there wasn't time to fully implement the plan, the intended plan of attack became:

1. Add a command line setting through `clap` to support loading a JSON file with multiple transactions and their fields
2. Parse the JSON with `serde` and load the data into `L2Tx` instances
3. Fill `transactions_to_replay` with the data
4. Switch the vm execution mode to `Bootloader` while this new command line setting is triggered
5. Fork and update the `zksync-era` crate to output logs in the `debugLog` hook

---

# Architectural risks
During the course of the audit certain architectural risks were identified:
* Escalation or a transaction type to privileged a upgrade
* Premature batch closure
* Transactions skipped by setting `execute` flag to false by operator in bootloader
* Force update batch data after closing
* Incorrect gas calculations
* DoSing the bootloader
* Wrong tx.origin or msg.sender for a transaction
* Reordering priorityQueue
* Overriding transaction data within batch
* Unauthorized use of mimic call to impersonate a user
* `delegatecall` from bootloader address which would grant arbitrary execution in kernel space
* Verbatim opcodes with elevated privileges or forging L2 logs by bootloader operator

Apart from those, the team delved into the following possible attack vectors:

## L2 batch timestamp manipulation
Having reliable time of the actions happening on L2 is crucial to the functioning of the whole system. In `SystemContext.setNewBatch()` there is just check if the timestamp is bigger than [previous batch](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/system-contracts/contracts/SystemContext.sol#L416-L424).
```
    function setNewBatch(
        bytes32 _prevBatchHash,
        uint128 _newTimestamp,
        uint128 _expectedNewNumber,
        uint256 _baseFee
    ) external onlyCallFromBootloader {
        (uint128 previousBatchNumber, uint128 previousBatchTimestamp) = getBatchNumberAndTimestamp();
        require(_newTimestamp > previousBatchTimestamp, "Timestamps should be incremental");
        require(previousBatchNumber + 1 == _expectedNewNumber, "The provided block number is not correct");
```

Later in `ExecutorFacet._verifyBatchTimestamp()` the only safeguard is a check if it is not bigger than `block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA`, which is not set in the config and fallbacks to [365 days](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L94).

### Discussion
1. A malicious operator can easily put this to max value that would be problematic for honest operators, in case that multiple operators will run the L2. This would also break crucial invariant - L2 time is close to the real time measured offchain.
2. Each priority queue operation has expiration time. The concer here is: will setting batch timestamp long in the future invalidate a message on L1?


## Possible money steal using MsgValueSimulator 
There was an issue concerning MsgValueSimulator highlighted in the [previous contest](https://code4rena.com/reports/2023-03-zksync#h-01-the-call-to-msgvaluesimulator-with-non-zero-msgvalue-will-call-to-sender-itself-which-will-bypass-the-onlyself-check). It concerned a situation when `to` address is `MsgValueSimulator` itself.


This was resolved by enforcing that `to` cannot be `MsgValueSimulator`:
```
        require(to != address(this), "MsgValueSimulator calls itself");
```

However there is an additional case where this check could not protect the smart contract - by using delegatecall, `address(this)` is set to the calling contract while `msg.sender` is preserved. That means that even if we set `to` to `MsgValueSimulator`, `address(this)` will be different and the check will pass. If the call was originating from a system contract, we could recreate the attack found in previous implementation.

Additionally, in `L2EthToken.transferFromTo()` there is check who can call the contract:
```
    function transferFromTo(address _from, address _to, uint256 _amount) external override {
        require(
            msg.sender == MSG_VALUE_SYSTEM_CONTRACT || // @audit can I use MsgValueSimulator to call it using any arbitrary "from" address?
                msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
                msg.sender == BOOTLOADER_FORMAL_ADDRESS,
            "Only system contracts with special access can call this method"
        );
```


### Discussion
1. If we were able to force one of the contracts to execute this transaction with any `_from`, then we could steal anything from anyone. One specific possible attack vector that might be exploited could be a malicious transaction during which that bootloader can mimiccall or delegatecall to this contract. `ContractDeployer.createAccount()` can probably be utilized somehow in this attack - it is a system contract that can deploy any code and run it. 

## Transaction gas price can be manipulated by user
Gas to pay for executing crosschain transaction from L1 to L2 is calculated using [tx.gasprice](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L304). This is insecure, because this value can be manipulated and even set to 0 using MEV either by the user who posts the transaction or any other actor. In such case the gas to pay will fall back to [`FAIR_L2_GAS_PRICE`](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L183):

```
    function _deriveL2GasPrice(uint256 _l1GasPrice, uint256 _gasPricePerPubdata) internal pure returns (uint256) {
        uint256 pubdataPriceETH = L1_GAS_PER_PUBDATA_BYTE * _l1GasPrice;
        uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;

        return Math.max(FAIR_L2_GAS_PRICE, minL2GasPriceETH);
    }
```

In case of gas spikes or turbulent market conditions this could end up having grave consequences.

There is also an additional attack vector: in case that there is not enough gas to cover transaction cost, it is not reverted, but refund is [set to 0](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/system-contracts/bootloader/bootloader.yul#L983-L996):

```
                checkEnoughGas(gasForExecution)

                let gasBeforeExecution := gas()
                success := ZKSYNC_NEAR_CALL_executeL1Tx(
                    callAbi,
                    txDataOffset
                )
                notifyExecutionResult(success)
                let gasSpentOnExecution := sub(gasBeforeExecution, gas())

                potentialRefund := sub(gasForExecution, gasSpentOnExecution)

                if gt(gasSpentOnExecution, gasForExecution) {
                    potentialRefund := 0
                }
```
This means that operator may execute the transaction without getting full payment for doing it.

### Discussion
1. Gasprice on L2 is taken from the [transaction](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/system-contracts/bootloader/bootloader.yul#L870-L873). Equivalent of gas used is actually [minted on L2](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/system-contracts/bootloader/bootloader.yul#L927-L935). A concern is the worst case scenario for two parties:
* operator - not getting paid for the transaction
* user - not getting due refund.

2. Gas handling on L2 is peculiar. In case that there is not enough gas to cover the protocol will [accept the loss](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/system-contracts/bootloader/bootloader.yul#L908-L927).

The overarching idea is exploiting possible ways to monetize on free gas execution, for example:
* using gas tokens to sell them on market
* execute other users transactions without a need to pay for a gas
* gaining more refund than eligible for the user on L1

## NonceHolder.isNonceUsed() doesn't work with Arbitrary nonce
There are 2 possible ways of how nonces are assigned. Either they are incremented sequentially, or arbitrarily. There is however inconsistency on how it's set and handled. E.g. in function [`setValueUnderNonce()`](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/NonceHolder.sol#L147-L150) there are safeguards only for sequential nonce. An arbitrary nonce can by set to any value, even smaller than a current one. So technically, nonce can be, for example: 1, 5, 3. However, isNonceUsed() will return true for numbers 2 and 4 even though those weren't used:

```
    function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) { // @audit how can I set nonce as not used?
        uint256 addressAsKey = uint256(uint160(_address));
        return (_nonce < getMinNonce(_address) || nonceValues[addressAsKey][_nonce] > 0); // @audit `_nonce < getMinNonce(_address) ` doesn't work with arbitrary nonces
    }
```

### Discussion
We identified two possible solutions to the issue:
1. A nonce value lower than current one should be prohibited.
2. The check in `isNonceUsed()` should be changed to `nonceValues[addressAsKey][_nonce] > 0`.

## Possible L1 bridge DoS through processing a batch that will OOG on L1
Bootloader sends logs for a batch to L1 using [`L1Messenger.publishPubdataAndClearState()`](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/L1Messenger.sol#L206) function. It checks if the amount of logs is not too big in this require statement:
`require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs")`
It is however always true, because it checks `numberOfL2ToL1Logs` against itself:

```
    function publishPubdataAndClearState( // @audit bootloader does not use this function anywhere
        bytes calldata _totalL2ToL1PubdataAndStateDiffs
    ) external onlyCallFromBootloader {
        uint256 calldataPtr = 0;

        /// Check logs
        uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
        require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs"); // @issue it's tautology. Will always be true. Can operator then publish some huge amount of logs that will not be executed on L1?
...
```
That means that an operator is technically able to pass here huge amount of pubdata that will not be possible to process on L1 due to block gas limit constraints. That would mean that the transactions would not be able to be confirmed.

### Discussion
1. There is a limitation to that - bootloader memory is constrained, probably enforcing the max pubdata that can be sent. If an operator is able to somehow skip this check, the attack could be effective. The consequences are very severe - without possibility to settle transaction on L1, no more transactions can be sent to L2 form L1, permamently freezing the bridge.


For reference usage in bootloader - this function is used in bootloader disguised behind a msg.selector:

https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/bootloader/bootloader.yul#L2474

# Governance

This section of the analysis report encompasses the governance section of the codebase. 

The zkLotus team adopted a comprehensive and methodical approach to examine zkSync Era's governance contract in order to meet safe security standards - and ensure that no stone has been left unturned.  Upon first impression, it was deemed that the overall code structure of the `Governance.sol` contract is streamlined and does not contain any unnecessary or overly-complex code. Additionally, the comprehensive comments made it highly readable and understandable. The simplicity of the code is a huge benefit in terms of security, given the fact that it is simple and not purposefully bloated. Secondly, it meets expectations in respect of governance functionality and allows the team to fulfil all of their needs without any problems. 

With that being said, vulnerabilities in smart contracts are present due to a wide variety of reasons, with some being either irregular logic, or the code being too weak due to the fact that the developers were not able to take into consideration security aspects due to the complexity of the code. However, it seems like zkSync Era did not suffer from this dilemma within its governance contract, and reached their goal successfully.

The zkSync Era team also ensured that the governance aspect of the codebase is cross-chain compatible, allowing it to be future proof. The main governance contract is deployed on a layer 1 solution (Ethereum). This contracts embeds multiple functions which allow the contract itself, the owner of the contract and the "security council" (a multi-sig address) to make decisions in important situations as well as create and/or cancel operations and check the status of various operations based on the ID of the operation that is being queried. 

The following objectives were taken into consideration prior to the security analysis of the Governance contract:
- Ensuring the functionality of the `Governance.sol` contract is precise and correct
- The governance measures are fully reasonable and are not excessive in respect of the mission to implement full decentralisation in the future
- The contract is flawless to the extent where no vulnerabilities are present
## Variables

The main variables which are consistent throughout the codebase are the following:
1. `EXECUTED_PROPOSAL_TIMESTAMP` which is a constant that is used for the purpose of checking the timestamp of completed operations.
2. `securityCouncil` which is a multi-sig address that is responsible for governance related operations.
3. `timestamps` which is a mapping that allows for dictating when an operation is ready for execution, or is simply utilised for bookkeeping purposes.
4.  `minDelay` which is a `uint256` stored variable holding the number of seconds for operations to be ready for execution.

## Modifiers

The `Governance.sol` contract utilises 3 key modifiers which consist of:
1. `onlySelf` which is responsible for checking that `msg.sender == address(this)`.
2. `onlySecurityCouncil` which is responsible for checking that `msg.sender == securityCouncil`.
3. `onlyOwnerOrSecurityCouncil` which is responsible for checking that `msg.sender == owner() || msg.sender == securityCouncil`.

## Constructor

The `constructor` takes in the `_admin` variable which dictates who is the admin of the contract. The 2nd variable within the constructor is the `_securityCouncil` multi-sig address. Lastly, there is a 3rd parameter which is the `_minDelay`.

```solidity
constructor(address _admin, address _securityCouncil, uint256 _minDelay) {
    require(_admin != address(0), "Admin should be non zero address");

    _transferOwnership(_admin);

    securityCouncil = _securityCouncil;
    emit ChangeSecurityCouncil(address(0), _securityCouncil);

    minDelay = _minDelay;
    emit ChangeMinDelay(0, _minDelay);
}
```

The constructor enables a `0` address check to ensure that the admin cannot be set as the `0` address, and after that check is valid, it transfers the ownership to the address which has been passed through into the `_admin` variable. Surprisingly, it is interesting that there is no `0` address check for the `_securityCouncil` variable given the fact that they are just as important as the `admin` when it comes to the execution of certain functions. Nevertheless, the multi-sig address which was passed into `_securityCouncil` is assigned to `securityCouncil` allowing it to be used later down the line under the `securityCouncil` variable. The changes for the security council are emitted via the `ChangeSecurityCouncil` function, which means that the security council is changed from `address(0)` to `_securityCouncil`. The same logic applies to `_minDelay`.
## Functions pertaining to operation states

One of the first functions that we see within the contract is the `isOperation` function. This function works by taking a `bytes32` input under `_id` and returning a bool operation under the condition that the `_id` corresponds to a registered operation. This function works as expected, as entering an ID that does not exist returns a false via the boolean operation.

```solidity
function isOperation(bytes32 _id) public view returns (bool) {
    return getOperationState(_id) != OperationState.Unset;
}
```

The second function is the `isOperationPending` function which takes in the same input under the same conditions with the same result, however it is stated via comments that a "pending" operation may also be "ready". This seemed rather interesting upon first glance given the fact that there is a separate state for operations which is "ready", therefore there is a distinction between a "pending" state and a "ready" state. However, following the code logic here, it is as if the states are merged and receive the same output, which could be confusing. It is unknown as to why the ready state is being accounted for when the state is essentially separate, and also has its own function.

```solidity
function isOperationPending(bytes32 _id) public view returns (bool) {
    OperationState state = getOperationState(_id);
    return state == OperationState.Waiting || state == OperationState.Ready;
}

function isOperationReady(bytes32 _id) public view returns (bool) {
    return getOperationState(_id) == OperationState.Ready;
}
```

A comment prior to the `isOperationReady` function also states that a "ready" operation is also "pending". Again, the logic here in regards to the states is quite interesting when it comes to understanding these pieces of code, however there are no problems discovered post-analysis.

The caller also has the ability to check whether an operation has completed or not:

```solidity
function isOperationDone(bytes32 _id) public view returns (bool) {
    return getOperationState(_id) == OperationState.Done;
}
```

And lastly, there is the main function which does what all these previous 3 functions do all in one:

```solidity
    function getOperationState(bytes32 _id) public view returns (OperationState) {
        uint256 timestamp = timestamps[_id];
        if (timestamp == 0) {
            return OperationState.Unset;
        } else if (timestamp == EXECUTED_PROPOSAL_TIMESTAMP) {
            return OperationState.Done;
        } else if (timestamp > block.timestamp) {
            return OperationState.Waiting;
        } else {
            return OperationState.Ready;
        }
    }
```

It seems to be that the zkSync Era team decided to add more precise functionality within the code for the sake of higher user satisfaction, which is completely understandable. It can be argued however that because the `getOperationState` function exists, it explicitly undermines the functionality of the other functions and ultimately renders them ineffectual.
## Main Functions

The `scheduleTransparent` function takes in an `_operation` input via `Operation calldata _operation`, along with a `_delay` in `uint256` format. Essentially, only the owner can call this function (as the function has an `onlyOwner` modifier) meaning the owner has the most control in this scenario. The owner can call it simply with the delay timelock on its own, or with the security council. However, the security council cannot do this without the owner; it is simply not possible. The purpose of this function is to enable a completely transparent upgrade whilst also providing the upgrade data on-chain. 

  1. The `_operation` which is passed into the function is embedded into `hashOperation`, where the result is assigned to a `bytes32` `id`.
  2. This `id` is then scheduled via `_schedule` along with the `_delay` that was passed into the function prior to execution.
  3. The operation is emitted via `TransparentOperationScheduled`.

```solidity
function scheduleTransparent(Operation calldata _operation, uint256 _delay) external onlyOwner {
    bytes32 id = hashOperation(_operation);
    _schedule(id, _delay);
    emit TransparentOperationScheduled(id, _delay, _operation);
}
```

The `scheduleShadow` function is almost the same as the `scheduleTransparent` function, however the shadow function allows you to enable an upgrade without publishing the upgrade data on-chain. The reason as to why this function exists is unknown, however it does raise questions as to why zkSync would not like to publish the data on-chain in some events.
  1. The function executes `_schedule` with `_id` and `_delay`.
  2. The function emits `ShadowOperationScheduled(_id, _delay)`.

```solidity
function scheduleShadow(bytes32 _id, uint256 _delay) external onlyOwner {
    _schedule(_id, _delay);
    emit ShadowOperationScheduled(_id, _delay);
    }
```

The `cancel` function takes in `_id` via bytes32 format, permitting this function to be executed by the owner OR the security council. This function is responsible for simply cancelling operations.
  1. The `_id` is checked via the `isOperationPending` function, and if it is not pending, then it is rejected. In order to cancel an operation, it has to be pending.
  2. It deletes the `_id` from `timestamps` array.
  3. The function emits `OperationCancelled(_id)`.

```solidity
function cancel(bytes32 _id) external onlyOwnerOrSecurityCouncil {
    require(isOperationPending(_id), "Operation must be pending");
    delete timestamps[_id];
    emit OperationCancelled(_id);
}
```

The `execute` function is responsible for executing scheduled operations after the delay phase has been passed. This function enables the `onlyOwnerOrSecurityCouncil` modifier, also permitting this function to be executed by the owner OR the security council. The function takes in the exact same input (`_operation`) as in the `scheduleTransparent` function.
  1. The `_operation` which is passed into the function is embedded into `hashOperation` via `hashOperation(_operation)`, where it is then assigned to a `bytes32` `id`.
  2. It will check whether the predecessor operation has been completed by calling `_checkPredecessorDone`, accounting for `_operation.predecessor` within that call.
  3. A require check is done in order to ensure that the operation under the ID is ready to be executed.
  4. The operation is executed via an `_execute` call, where the `_operation.calls` is passed in.
  5. Surprisingly, and thankfully, this function performs a require check in order to prevent re-entrancy attacks so that certain operations cannot be replayed.
  6. The operation state is changed to done, by embedding the `id` into `timestamps` array, and emitting the change via `OperationExecuted` along with the `id` passed in.

```solidity
function execute(Operation calldata _operation) external onlyOwnerOrSecurityCouncil {
    bytes32 id = hashOperation(_operation);
    _checkPredecessorDone(_operation.predecessor);
    require(isOperationReady(id), "Operation must be ready before execution");
    _execute(_operation.calls);
    require(isOperationReady(id), "Operation must be ready after execution");
    timestamps[id] = EXECUTED_PROPOSAL_TIMESTAMP;
    emit OperationExecuted(id);
}
```

The `executeInstant` function is almost identical to the `execute` function, with the only difference being that the require check of the operation is changed from:
`require(isOperationReady(id)` to `require(isOperationPending(id)`.

The `hashOperation()` function is responsible for taking in the `_operation` (with the same premise as prior functions) and returning a keccak256 of the `_operation`. This can be executed by anybody.

```solidity
function hashOperation(Operation calldata _operation) public pure returns (bytes32) {
    return keccak256(abi.encode(_operation));
}
```

The `schedule` function is responsible for scheduling operations which should become valid after a specific delay has passed. The function takes in `_id` in `bytes32` format, along with `_delay` in `uint256` format. This function is available to internal calls only.
  1. The function checks whether the `_id` that is passed in is NOT an operation, otherwise it recognises that it already exists therefore the check fails.
  2. The function also checks whether the `_delay` is more than or equal to `minDelay`, otherwise it fails the check under the condition that the proposed delay is lower than the minimum delay.
  3. The `_id` is updated within the timestamps through `timestamps[_id] = block.timestamp + _delay`.

```solidity
function _schedule(bytes32 _id, uint256 _delay) internal {
    require(!isOperation(_id), "Operation with this proposal id already exists");
    require(_delay >= minDelay, "Proposed delay is less than minimum delay");

    timestamps[_id] = block.timestamp + _delay;
}
```

The `_execute` function is utilised for the execution of operation calls. Not just a singular one, but multiple, which did raise concerns of potential running out of gas issues, however the calculation for gas allowance seems to be `2^32 - 1` gas available for execution, therefore running out of gas does not seem plausible in this scenario. The function takes in `Call[] calldata _calls` as input, and the function can only be executed internally.
  1. The function implements a classical loop in the format of:
    i. `for (uint256 i = 0; i < _calls.length; ++i)`
  2. If the function is ruled as a success, it will `returnData` via bytes memory, which means that the `returnData` is bound to that specific contract. This did raise questions whether there would be any attack vectors regarding the usage of memory data when thinking of diamond implementations, however the functionality is perfectly normal and as intended following EIP-2535. To slightly unpack further, this success (via bool) is dependant on the actual `call` execution succeeding via:
    i. `_calls[i].target.call{value: _calls[i].value}(_calls[i].data);`
  3. If the call is not successful, then the `_execute` function does an assembly operation where it outputs an error via:
    i. `revert(add(returnData, 0x20), mload(returnData))`.

```
function _execute(Call[] calldata _calls) internal {
    for (uint256 i = 0; i < _calls.length; ++i) {
        (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
        if (!success) {
            assembly {
                revert(add(returnData, 0x20), mload(returnData))
            }
        }
    }
}
```

The `_checkPredecessorDone()` function takes in a `_predecessorId` in `bytes32` format, to verify whether the predecessor operation is completed or not. It is an internal view function.
  1. The function implements a require check where `_predecessorId` cannot equal 0 in bytes32 format, and checks (via logical OR) whether the operation is done or not.

The `updateDelay` function allows the contract (via the `onlySelf` modifier) to update the minimum timelock duration for future operations. Realistically, this should not mess with current operations according to the code logic. The function takes in a `_newDelay` in `uint256` format.
  1. Emits the change from `minDelay` to `_newDelay`.
  2. `minDelay` is set to `_newDelay`.

The `updateSecurityCouncil` function is responsible for simply updating the address of the multi-sig security council. This function has a `onlySelf` modifier therefore it has to be equal to `msg.sender`.
  1. Emits change via `ChangeSecurityCouncil` from `securityCouncil` to `_newSecurityCouncil`.
  2. `securityCouncil` is set to `_newSecurityCouncil`.

---

# Executor Facet Exploration

During the course of the audit, the Executor facet within the Diamond contracts was the subject of major focus.  To build a model for the rest of the team, the functions and their place in the greater system was considered and documented.

## CommitBatches
![](https://cdn.discordapp.com/attachments/1119257047058362399/1166098174822338680/Graphs8.png)

`commitBatches` takes in the last confirmed batch data along with new batch data. Depending on whether a system contract upgrade is present and the upgrade batch number, it invokes either `_commitBatchesWithoutSystemContractsUpgrade` or `_commitBatchesWithSystemContractsUpgrade` to commit the new batches.

Both `_commitBatchesWithoutSystemContractsUpgrade` and `_commitBatchesWithSystemContractsUpgrade` iterates through the new batches data. They process each batch individually by calling `_commitOneBatch` and then store the hash of the stored batch information.

The `_commitOneBatch` performs these steps:
- Checks if the new batch number is the next one after the last committed batch number.
- Processes the L2 logs in the new batch by invoking `_processL2Logs`.
- Verifies the timestamp of the new batch.
- Creates a batch commitment and returns a new `StoredBatchInfo`.

`_processL2Logs` iterates through each log in the new batch. It verifies the sender address, the log key, and the log value, and ensures each log is processed exactly once.
## ExecuteBatches 
![](https://cdn.discordapp.com/attachments/1119257047058362399/1166098174822338680/Graphs8.png)

`executeBatches` takes an array of batch data. It loops over each batch and calls `_executeOneBatch` to process each batch individually. After processing each batch, a `BlockExecution` event is emitted.

It updates the total number of executed batches. If this total exceeds the total number of verified batches, an error is thrown.

If a system contract upgrade has occurred and all batches up to and including the upgrade batch have been executed, the function clears the system contract upgrade transaction hash and batch number.

`_executeOneBatch` checks the batch is in the correct execution order and verifies it has been committed. It processes all pending operations by calling `_collectOperationsFromPriorityQueue` and verifies the hash of priority operations matches the expected hash. It then saves the root hash of the L2 to L1 logs tree.

`_collectOperationsFromPriorityQueue` removes priority operations from the priority queue and returns a rolling hash of operations.

## ProveBatches 
![](https://cdn.discordapp.com/attachments/1119257047058362399/1166100369311547453/Graphs9.png)

`proveBatches` is invoked with the previous batch data, an array of committed batches, and a proof.

- verifies that the hash of the previous batch matches the hash of the first unverified batch in the stored batch hashes.
- For each committed batch, it checks the hash of the batch against the corresponding hash in the stored batch hashes.
- generates a public input for the zk proof using the previous batch commitment, the current batch commitment, and the verifier parameters.
- After all batches have been processed, the function ensures that the total number of verified batches does not exceed the total number of committed batches.
- The zk proof is verified using the generated public inputs, the provided serialized proof, and the recursive aggregation input.
- If there is an issue with verification then an error is thrown and at last a `BlocksVerification` event is emitted with total number of verified batches (including both old and new), and the total number of verified batches is updated.
## RevertBatches 

The `revertBatches` reverts unexecuted batches. It accepts a batch number, representing the batch number after which batches should be reverted. The function does not delete the stored data about batches but only decreases the counters responsible for the number of batches. A `BlocksRevert` event is triggered after the reversal.


### Time spent:
100 hours