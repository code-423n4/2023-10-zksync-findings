# QA Report

## **Table of Contents**

|                                                                                                                                                                            | Issue                                                                                                                                                           |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [QA‑01](#qa-01-extendedaccountversion-should-not-return-accountabstractionversionversion1-for-addresses-in-the-kernel-space)                                               | `extendedAccountVersion()` should not return `AccountAbstractionVersion.Version1` for addresses in the kernel space                                             |
| [QA‑02](#qa-02-storebatchhash-should-in-no-instance-return-reverted-batches)                                                                                               | `storeBatchHash` should in no instance return reverted batches                                                                                                  |
| [QA‑03](#qa-03-the-additional-check-for-mainnet-in-provebatches-should-consider-that-after-a-hard-fork-assert-blockchaindid--1-would-no-longer-be-a-valid-protection)      | The additional check for mainnet in `proveBatches()` should consider that after a hard fork `assert block.chaindId != 1` would no longer be a valid protection. |
| [QA‑04](#qa-04-executeinstant-would-fail-for-operations-scheduled-in-the-future-due-to-checkpredecessordone)                                                               | `executeInstant()` would fail for operations scheduled in the future due to `_checkPredecessorDone()`                                                           |
| [QA&#x2011;05](#qa-05-setting-a-pending-admin-in-adminfacets-actually-emits-an-event-for-setting-a-governor-instead-which-leads-to-issues-for-any-one-listening-off-chain) | Setting a pending admin in AdminFacets actually emits an event for setting a governor instead which leads to issues for any one listening off-chain             |
| [QA&#x2011;06](#qa-06-forcedeployonaddressshould-be-modified-to-ensure-that-the-newaddress-always-receives-the-funds-allocated-to-it-regardles-of-callconstructors-value)  | `forceDeployOnAddress`should be modified to ensure that the newAddress always receives the funds allocated to it regardles of `callConstructor`'s value         |
| [QA&#x2011;07 ](#qa-07-allowlist-is-not-being-implemented-as-is-supposed-to-after-the-alpha-period)                                                                        | Allowlist is not being implemented as is supposed to after the Alpha period.                                                                                    |
| [QA&#x2011;08](#qa-08-forced-deployments-should-be-restricted)                                                                                                             | Forced deployments should be restricted                                                                                                                         |
| [QA&#x2011;09](#qa-09-processing-of-l2-to-l1-withdrawal-messages-with-additional-data-should-be-efficiently-done-and-correctly-documented)                                 | Processing of L2 to L1 withdrawal messages with additional Data should be efficiently done and correctly documented                                             |
| [QA&#x2011;10 ](#qa-10-make-the-fallback-a-payable-prefixed-function-to-ensure-that-calls-dont-fail-from-the-bootloader)                                                   | Make the `fallback` a `payable` prefixed function to ensure that calls don't fail from the `bootloader`                                                         |
| [QA&#x2011;11](#qa-11-add-transactionsignature-to-encodehasheip712transaction-to-ensure-compliance-with-eip712)                                                            | Add `transaction.signature` to `_encodeHashEIP712Transaction` to ensure compliance with EIP712                                                                  |
| [QA&#x2011;12](#qa-12-maxfeepergas-been-hardcoded-to-0-which-leads-to-not-providing-any-incentives-for-validators)                                                         | `maxFeePerGas` been hardcoded to 0 which leads to not providing any incentives for validators                                                                   |
| [QA&#x2011;13](#qa-13-interface-specifications-mismatch-with-actual-functions)                                                                                             | Interface specifications mismatch with actual functions                                                                                                         |
| [QA&#x2011;14](#qa-14-multiple-fixes-for-typosdocs--wrong-naming)                                                                                                          | Multiple fixes for typos/Docs & wrong naming                                                                                                                    |
| [QA&#x2011;15](#qa-15-deviation-from-solidity-best-styling-practices-in-scoped-contracts)                                                                                  | Deviation from Solidity best styling practices in scoped contracts                                                                                              |
| [QA&#x2011;16 ](#qa-16-l2ethtokentransferfromto-should-be-stopped-from-emmiting-dummy-events-and-also-protect-users-from-wasting-unnecessary-gas)                          | `L2EthToken::transferFromTo` should be stopped from emmiting dummy events and also protect users from wasting unnecessary gas                                   |
| [QA&#x2011;17](#qa-17-system-hang-if-validators-neglect-cross-chain-transactions)                                                                                          | System hang if validators neglect cross-chain transactions                                                                                                      |
| [QA&#x2011;18](#qa-18-multiple-issue-attached-with-isvalidsignature-does-not-follow-the-specification-in-the-eip)                                                          | Multiple issue attached with `_Isvalidsignature` does not follow the specification in the EIP                                                                   |
| [QA&#x2011;19](#qa-19-setters-should-always-have-equality-checkers)                                                                                                        | Setters should always have equality checkers                                                                                                                    |
| [QA&#x2011;20](#qa-20-bootloadermemoryfortxs-allocation-reduction-is-risky)                                                                                                | `BOOTLOADER_MEMORY_FOR_TXS` allocation reduction is risky                                                                                                       |
| [QA&#x2011;21](#qa-21-operationstatewaiting-should-be-considered-the-case-even-when-timestamp--blocktimestamp)                                                             | `OperationState.Waiting` should be considered the case even when `timestamp == block.timestamp`                                                                 |
| [QA&#x2011;22](#qa-22-compatibility-issues-could-arise-for-pure-erc20)                                                                                                     | Compatibility issues could arise for pure ERC20                                                                                                                 |
| [QA&#x2011;23](#qa-23-allowlist-could-use-a-blacklisting-mechanism)                                                                                                        | AllowList could use a blacklisting mechanism                                                                                                                    |
| [QA&#x2011;24](#qa-24-the-interface-ilegacygetters-only-provides-availability-of-deprecated-functions-instead-of-the-currently-accepted-ones)                              | The interface `ILegacyGetters` only provides availability of deprecated functions instead of the currently accepted ones                                        |
| [QA&#x2011;25](#qa-25-if-changes-are-made-to-l2contracthelperconfigsol-then-redeployments-of-facets-should-be-hastily-done)                                                | If Changes are made to `L2ContractHelper/Config.sol` then redeployments of facets should be hastily done                                                        |
| [QA&#x2011;26](#qa-26-presence-of-non-production-ready-code)                                                                                                               | Presence of non-production-ready code                                                                                                                           |
| [QA&#x2011;27](#qa-27-add-the-verifyingcontract-to-the-typehash-for-eip-712)                                                                                               | Add the `verifyingContract` to the TYPEHASH for EIP-712                                                                                                         |
| [QA&#x2011;28](#qa-28-redundant-v--27--v--28-check)                                                                                                                        | Redundant `v == 27 & v == 28` check                                                                                                                             |

## Borderline Issues

The issues listed below are ones I've categorized under "QA". However, I believe they teeter between "QA" and "med". To avoid spamming the main judging repository, I've included them in this QA report. But I would appreciate it if the judge could review these and consider if any of them merits an upgrade to "med".

|                                                                                                                                            | Issue                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------ |
| [QA‑02](#qa-02-storebatchhash-should-in-no-instance-return-reverted-batches)                                                               | `storeBatchHash` should in no instance return reverted batches                                                      |
| [QA‑04](#qa-04-executeinstant-would-fail-for-operations-scheduled-in-the-future-due-to-checkpredecessordone)                               | `executeInstant()` would fail for operations scheduled in the future due to `_checkPredecessorDone()`               |
| [QA&#x2011;09](#qa-09-processing-of-l2-to-l1-withdrawal-messages-with-additional-data-should-be-efficiently-done-and-correctly-documented) | Processing of L2 to L1 withdrawal messages with additional Data should be efficiently done and correctly documented |
| [QA&#x2011;10 ](#qa-10-make-the-fallback-a-payable-prefixed-function-to-ensure-that-calls-dont-fail-from-the-bootloader)                   | Make the `fallback` a `payable` prefixed function to ensure that calls don't fail from the `bootloader`             |
| [QA&#x2011;11](#qa-11-add-transactionsignature-to-encodehasheip712transaction-to-ensure-compliance-with-eip712)                            | Add `transaction.signature` to `_encodeHashEIP712Transaction` to ensure compliance with EIP712                      |
| [QA&#x2011;12](#qa-12-maxfeepergas-been-hardcoded-to-0-which-leads-to-not-providing-any-incentives-for-validators)                         | `maxFeePerGas` been hardcoded to 0 which leads to not providing any incentives for validators                       |
| [QA&#x2011;18](#qa-18-multiple-issue-attached-with-isvalidsignature-does-not-follow-the-specification-in-the-eip)                          | Multiple issue attached with `_Isvalidsignature` does not follow the specification in the EIP                       |
| [QA&#x2011;22](#qa-22-compatibility-issues-could-arise-for-pure-erc20)                                                                     | Compatibility issues could arise for pure ERC20                                                                     |



## QA-01 `extendedAccountVersion()` should not return `AccountAbstractionVersion.Version1` for addresses in the kernel space

### Impact

Heavy Low, since `extendedAccountVersion()` is only a _view_ function that's currently not being used anywhere asides tests.

### Proof of Concept

Take a look at `extendedAccountVersion()`

```solidity
    function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion) {
        AccountInfo memory info = accountInfo[_address];
        if (info.supportedAAVersion != AccountAbstractionVersion.None) {
            return info.supportedAAVersion;
        }

        // It is an EOA, it is still an account.
        if (ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getRawCodeHash(_address) == 0) {
            return AccountAbstractionVersion.Version1;
        }

        return AccountAbstractionVersion.None;
    }
```

As seen, the function returns the account abstraction version if `_address` is a deployed contract. when the `RawCodeHash `of an address is `0x0`, i.e `ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getRawCodeHash(_address) == 0 ` then code assumes it is an `EOA` then returns `AccountAbstractionVersion.Version1` but this would be wrong for kernel space addresses that have no deployed code, since the return value of the `extendedAccountVersion()` for these addresses would be `0x0` too.
If any reliance on this function is made to detect Account support version then this would be flawed for addresses in the kernel space.

### Recommended Mitigation Steps

First check if specified address is in kernel space then return `AccountAbstractionVersion.None` before the check for raw code hash value.

## QA-02 `storeBatchHash` should in no instance return reverted batches

### Impact

The `storedBatchHash` function might return the hash of a batch that is invalid since it's been reverted due to `Executor`'s `revertBlocks()` which is not intended behaviour.

### Proof of Concept

Note that `Executor` permits the validator to revert unexecuted batches using `revertBatches()`.

```solidity
    function revertBatches(uint256 _newLastBatch) external nonReentrant onlyValidator {
        require(s.totalBatchesCommitted > _newLastBatch, "v1"); // The last committed batch is less than new last batch
        require(_newLastBatch >= s.totalBatchesExecuted, "v2"); // Already executed batches cannot be reverted

        if (_newLastBatch < s.totalBatchesVerified) {
            s.totalBatchesVerified = _newLastBatch;
        }
        s.totalBatchesCommitted = _newLastBatch;

        // Reset the batch number of the executed system contracts upgrade transaction if the batch
        // where the system contracts upgrade was committed is among the reverted batches.
        if (s.l2SystemContractsUpgradeBatchNumber > _newLastBatch) {
            delete s.l2SystemContractsUpgradeBatchNumber;
        }

        emit BlocksRevert(s.totalBatchesCommitted, s.totalBatchesVerified, s.totalBatchesExecuted);
    }
```

This action can adjust both `s.totalBatchesVerified` and `s.totalBatchesCommitted` to a prior number. As a result, some already committed or verified batches would be disregarded.

But from `Getters.sol`:

```solidity
    function storedBatchHash(uint256 _batchNumber) external view returns (bytes32) {
        return s.storedBatchHashes[_batchNumber];
    }
```

This function is meant to return the hash of an unfinaliized L2 batch, issue is that this function could still produce such a `batchHash`, misleading the caller into thinking that it's a valid hash.

> ⚠️ As an attempt of not spamming the judging repo this was submitted as QA, but the implications of this flaw could vary depending on how this function is used, note that `Zk Scan` has a specific section for [batches](https://explorer.zksync.io/batches/) if a reverted batch is being chckedon and is used to determine the block's state then this could lead to massive confusion, otherwise since it's just a getter this could be just QA, leaving final decision to judge.

### Recommended Mitigation Steps

Incorporate a mechanism to first check if said batch has been reverted, if yes then 0 should be returned for it, otherwise the function should execute as usual.

## QA-03 The additional check for mainnet in `proveBatches()` should consider that after a hard fork `assert block.chaindId != 1` would no longer be a valid protection.

### Impact

Low, since this check was originally deployed as just an additional protection.

### Proof of Concept

Take a look at `proveBatches()`

```sol
    function proveBatches(
        StoredBatchInfo calldata _prevBatch,
        StoredBatchInfo[] calldata _committedBatches,
        ProofInput calldata _proof
    ) external nonReentrant onlyValidator {
    //...ommited for brevity


        // Additional level of protection for the mainnet
        assert(block.chainid != 1);
        //...ommited for brevity

        emit BlocksVerification(s.totalBatchesVerified, currentTotalBatchesVerified);
        s.totalBatchesVerified = currentTotalBatchesVerified;
    }
```

As seen the additional check deployed for mainnet's protection is the `assert(block.chainid != 1)` chedck, but this could go wrong, since it's not probable after a hardfork, also it's a popular note that _ETH has been forked in the past and can be forked in the future_

### Recommended Mitigation Steps

Implement an additional check that takes into account the fact that a hardfork could be done.

## QA-04 `executeInstant()` would fail for operations scheduled in the future due to `_checkPredecessorDone()`

### Impact

The current implementation of the `executeInstant()` function, which is intended for the security council to execute operations instantly, has a limitation due to the check for a predecessor operation's completion. If the operation and it's predecessor operation are scheduled for a future timestamp , then the `executeInstant()` function will revert. This essentially means that the security council's ability to perform instant executions is severely restricted, and they may not be able to respond quickly to necessary changes or operations.

### Proof of Concept

Take a look at both `execute()` & `executeInstant()`

```solidity

    function execute(Operation calldata _operation) external onlyOwnerOrSecurityCouncil {
        bytes32 id = hashOperation(_operation);
        // Check if the predecessor operation is completed.
        _checkPredecessorDone(_operation.predecessor);
        // Ensure that the operation is ready to proceed.
        require(isOperationReady(id), "Operation must be ready before execution");
        // Execute operation.
        _execute(_operation.calls);
        // Reconfirming that the operation is still ready after execution.
        // This is needed to avoid unexpected reentrancy attacks of re-executing the same operation.
        require(isOperationReady(id), "Operation must be ready after execution");
        // Set operation to be done
        timestamps[id] = EXECUTED_PROPOSAL_TIMESTAMP;
        emit OperationExecuted(id);
    }

    function executeInstant(Operation calldata _operation) external onlySecurityCouncil {
        bytes32 id = hashOperation(_operation);
        // Check if the predecessor operation is completed.
        //@audit
        _checkPredecessorDone(_operation.predecessor);
        // Ensure that the operation is in a pending state before proceeding.
        require(isOperationPending(id), "Operation must be pending before execution");
        // Execute operation.
        _execute(_operation.calls);
        // Reconfirming that the operation is still pending before execution.
        // This is needed to avoid unexpected reentrancy attacks of re-executing the same operation.
        require(isOperationPending(id), "Operation must be pending after execution");
        // Set operation to be done
        timestamps[id] = EXECUTED_PROPOSAL_TIMESTAMP;
        emit OperationExecuted(id);
    }
```

Both functions are used to execute _Scheduled operations_ with the major difference being that `executeInstant` does this without waiting till the delay passes, which is understandable as the function would most probably be used for cases like trying to move the execution of an operation closer or fixing a critical issue that was just announced to protocol, whereas for the latter this would probably not be an issue, for the former, this would never work see why below:

- Note the structure of an operation below:

```solidity
    struct Operation {
        Call[] calls;
        bytes32 predecessor;
        bytes32 salt;
    }
```

    - This means that in most cases any operation that's been scheduled in the future has a predecessor that's been attached to it.

- Additionally both `execute()` & `executeInstant()` make a query to `_checkPredecessorDone()`

```solidity
    function _checkPredecessorDone(bytes32 _predecessorId) internal view {
        require(_predecessorId == bytes32(0) || isOperationDone(_predecessorId), "Predecessor operation not completed");
    }
```

    - The `_checkPredecessorDone()` function only passes without reverting in two cases:
        - The `_predecessorId` is zero.
        - The predecessor operation is completed (has a timestamp in the past).

This all means that, given the common scenario where operations are scheduled for the future with their respective predecessors, the security council will not be able to instantly execute these operations due to this line in `executeInstant()`: `_checkPredecessorDone(_operation.predecessor);` since the predecessor wouldn't have naturally been executed via `execute()` since a check is done for the operation to be ready in this line: `require(isOperationPending(id), "Operation must be pending after execution");` _what `isOperationPending()` does is just to check that the timestamp has passed which would not be the case for future operations_.

All these essentially leads to the security council having to wait out for attached predecessor operations to first be processed before the availability to call `executeInstant()` on the needed operations

### Recommended Mitigation Steps

At one glance, recommendation would be to `executeInstant()` to:

```diff
    function executeInstant(Operation calldata _operation) external onlySecurityCouncil {
        bytes32 id = hashOperation(_operation);
        // Check if the predecessor operation is completed.
        //@audit
-       _checkPredecessorDone(_operation.predecessor);
+       _checkPredecessorDone(0);
        // Ensure that the operation is in a pending state before proceeding.
        require(isOperationPending(id), "Operation must be pending before execution");
        // Execute operation.
        _execute(_operation.calls);
        // Reconfirming that the operation is still pending before execution.
        // This is needed to avoid unexpected reentrancy attacks of re-executing the same operation.
        require(isOperationPending(id), "Operation must be pending after execution");
        // Set operation to be done
        timestamps[id] = EXECUTED_PROPOSAL_TIMESTAMP;
        emit OperationExecuted(id);
    }


```

With the fix above any operation that's passed to `executeInstant()` would instantly be processed.

#### Additional Note

This was submitted as QA, since one can argue that the original use of the function would be to fix a critical issue immediately and in that case, while creating the operation's struct admins can just specify `0` as the predecessor and work around this, but if there are any scenarios where operations that've been scheduled in the future would be moved closer then I assume argument for this as a _med_ could be made. Leaving in judge's hands to decide

## QA-05 Setting a pending admin in AdminFacets actually emits an event for setting a governor instead which leads to issues for any one listening off-chain

### Impact

Low, if a pending admin is waiting for the event off chain to know when they are going to be able to call `acceptAdmin()` then this would never work, alternatively it could lead the adin to think they've been given the governor role and try accepting the governor instead.

### Proof of Concept

```solidity
    function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {
        // Save previous value into the stack to put it into the event later
        address oldPendingAdmin = s.pendingAdmin;
        // Change pending admin
        s.pendingAdmin = _newPendingAdmin;
        //@audit
        emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);
    }
```

This could lead to multiple issues as an admin might think he's ben given the role of a governor but attempting to accept governor would revert in the function below since he's been set as a peding admin instead

```
    function acceptGovernor() external {
        address pendingGovernor = s.pendingGovernor;
        require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights

        address previousGovernor = s.governor;
        s.governor = pendingGovernor;
        delete s.pendingGovernor;

        emit NewPendingGovernor(pendingGovernor, address(0));
        emit NewGovernor(previousGovernor, pendingGovernor);
    }
```

### Recommended Mitigation Steps

Apply these changes to the `setPendingAdmin()` function

```diff
    function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {
        // Save previous value into the stack to put it into the event later
        address oldPendingAdmin = s.pendingAdmin;
        // Change pending admin
        s.pendingAdmin = _newPendingAdmin;
-        emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);
+        emit NewPendingAdmin(oldPendingAdmin, _newPendingAdmin);
    }
```

## QA-06 `forceDeployOnAddress`should be modified to ensure that the newAddress always receives the funds allocated to it regardles of `callConstructor`'s value

### Impact

Medium to Low, but If `callConstructor` is not invoked, the provided value will be ignored and may result in loss of the value.

### Proof of Concept

Take a look at `forceDeployOnAddress()`

```solidity
    function forceDeployOnAddress(ForceDeployment calldata _deployment, address _sender) external payable onlySelf {
        _ensureBytecodeIsKnown(_deployment.bytecodeHash);

        AccountInfo memory newAccountInfo;
        newAccountInfo.supportedAAVersion = AccountAbstractionVersion.None;
        // Accounts have sequential nonces by default.
        newAccountInfo.nonceOrdering = AccountNonceOrdering.Sequential;
        _storeAccountInfo(_deployment.newAddress, newAccountInfo);

        _constructContract(
            _sender,
            _deployment.newAddress,
            _deployment.bytecodeHash,
            _deployment.input,
            false,
            _deployment.callConstructor
        );

        emit ContractDeployed(_sender, _deployment.bytecodeHash, _deployment.newAddress);
    }
```

Note that the `FORCE_DEPLOYER` or `COMPLEX_UPGRADER_CONTRACT` can call the `forceDeployOnAddresses()` function which executes the `forceDeployOnAddress()` above and as seen a transfer of `_deployments.value` could be made.

Issue is that In the current design, if the `callConstructor` is required, the value is transferred to `newAddress` and `msg.value` is set for the constructor. However, if the `callConstructor` is not required, the value remains in the contract and is not utilized... _potentially lost_.

### Recommended Mitigation Steps

Modify the `forceDeployOnAddress` function to ensure that even if `callConstructor` is not invoked, the passed ETH value should still be transferred to the `newAddress`.

## QA-07 Allowlist is not being implemented as is supposed to after the Alpha period

### Impact

In `L1ETHBridge.sol` and `L1ERC20Bridge.sol`, the functions `deposit`, `claimFailedDeposit`, and `finalizeWithdrawal` play a crucial role in the user fund management. These functions utilize a specific modifier to check permissions.

A malicious allowlist owner has the capability to revoke access permissions, ensuring that no one can call these pivotal functions. By denying access to the `deposit` function, cross-chain requests are impeded. Furthermore, blocking both `claimFailedDeposit` and `finalizeWithdrawl` essentially traps user's funds within the contract, preventing any retrieval or movement.

### Proof of Concept

Idea of this was previously submitted [here](https://github.com/code-423n4/2022-10-zksync-findings), with the reply from the sponsors being that this is intended for the Alpha period, as seen [here](https://github.com/code-423n4/2022-10-zksync-findings/issues/168#issuecomment-1324200634)

> This is not an issue, and it is by design to prevent user's from any interaction with bridges if necessary in alpha version, in the later version this will change. So, invalid issue!

Now issue is that, the Alpha period based on information provided during the contest already ended in April, but this window has not been closed and attached issue from the previous contest still exists.

To understand this vulnerability, I'd advise checking out the attached issue, or by diving into both the `AllowListed.sol` and `AllowList.sol` contracts, one can see that am owner can exploit this by restricting the access of the for any of the aforementioned functions for a user.

A detailed breakdown can be found [here](https://github.com/code-423n4/2022-10-zksync-findings/issues/168).

### Recommended Mitigation Steps

Restrict this, since the Alpha period has passed.

## QA-08 Forced deployments should be restricted

### Impact

Low, since this looks like intended design, but current implementation of unrestricted deployment permissions can be perilous.

### Proof of Concept

In the `ContractDeployer` contract, typical deployment checks for:

1. The absence of pre-existing code on the address.
2. The address hasn't conducted transactions (rawNonce is zero).

However, for force deployments, these checks are bypassed. This poses risks as inadvertent deployments over active addresses could result in fund loss or data corruption. Even with the safeguard of `FORCE_DEPLOYER` & `COMPLEX_UPGRADER_CONTRACT` being activated from L1, there remains vulnerability from operator malfeasance or key leaks.

Moreover, the unrestricted deployment ability clashes with the aliasing feature. A malicious governor could deploy a popular contract on L2, luring users to interact. Then, with L1 access and corresponding private keys, the governor could manipulate L2 transactions and potentially drain contract balances.

### Recommended Mitigation Steps

Deployment should be restricted. Proposed limitations include ensuring target addresses have neither associated code nor previous activity.

## QA-09 Processing of L2 to L1 withdrawal messages with additional Data should be efficiently done and correctly documented

### Impact

The system's current design leads to the incomplete processing of withdrawal messages initiated from L2 to L1 when additional data is included using the `withdrawWithMessage()` function. Specifically, while the withdrawal itself is processed — the funds are transferred — the additional message data sent along with the withdrawal request is not parsed or utilized in the `_parseL2WithdrawalMessage` function. This oversight means that any extra information the user wishes to relay from L2 to L1 is ignored, potentially limiting the system's utility for cases where such data might be crucial for certain operations or record-keeping on L1.

Moreover, there's an inconsistency regarding the handling of the `l2Sender` address. The system expects a 32-byte segment for the `l2Sender` within the message structure, whereas standard Ethereum addresses are 20 bytes long. Though not the core issue, this inconsistency can create confusion and potential data misalignment if not documented or handled correctly.

### Proof of Concept

The `withdrawWithMessage` function in `L2EthToken.sol` allows users to initiate a withdrawal while sending additional data:

```solidity
    function withdrawWithMessage(address _l1Receiver, bytes memory _additionalData) external payable override {
        uint256 amount = _burnMsgValue();


        // Send the L2 log, a user could use it as proof of the withdrawal
        //@audit
        bytes memory message = _getExtendedWithdrawMessage(_l1Receiver, amount, msg.sender, _additionalData);
        L1_MESSENGER_CONTRACT.sendToL1(message);


        emit WithdrawalWithMessage(msg.sender, _l1Receiver, amount, _additionalData);
    }
```

However, when this message reaches L1, the `_parseL2WithdrawalMessage` function in `MailboxFacet.sol` processes it, the additional data included in the message is not decoded or utilized:

Here is `finalizeEthWithdrawal()` which calls `_parseL2WithdrawalMessage()` in MailboxFacet.sol:

```solidity
    function finalizeEthWithdrawal(
        uint256 _l2BatchNumber,
        uint256 _l2MessageIndex,
        uint16 _l2TxNumberInBatch,
        bytes calldata _message,
        bytes32[] calldata _merkleProof
    ) external override nonReentrant senderCanCallFunction(s.allowList) {
        require(!s.isEthWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "jj");
        L2Message memory l2ToL1Message = L2Message({
            txNumberInBatch: _l2TxNumberInBatch,
            sender: L2_ETH_TOKEN_SYSTEM_CONTRACT_ADDR,
            data: _message
        });
        //@audit
        (address _l1WithdrawReceiver, uint256 _amount) = _parseL2WithdrawalMessage(_message);
        bool proofValid = proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);
        require(proofValid, "pi"); // Failed to verify that withdrawal was actually initialized on L2
        s.isEthWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex] = true;
        _withdrawFunds(_l1WithdrawReceiver, _amount);
        emit EthWithdrawalFinalized(_l1WithdrawReceiver, _amount);
    }

    function _parseL2WithdrawalMessage(bytes memory _message)
        internal
        pure
        returns (address l1Receiver, uint256 amount)
    {
        require(_message.length >= 56, "pm");
        (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_message, 0);
        require(bytes4(functionSignature) == this.finalizeEthWithdrawal.selector, "is");
        (l1Receiver, offset) = UnsafeBytes.readAddress(_message, offset);
        (amount, offset) = UnsafeBytes.readUint256(_message, offset);
    }
```

As seen, only the `l1Receiver` and `amount` are parsed, and no further processing occurs on any additional data that might have been included in the message.

L2EthToken.sol:

```solidity

    function withdraw(address _l1Receiver) external payable override {
        uint256 amount = _burnMsgValue();


        // Send the L2 log, a user could use it as proof of the withdrawal
        bytes memory message = _getL1WithdrawMessage(_l1Receiver, amount);
        L1_MESSENGER_CONTRACT.sendToL1(message);


        emit Withdrawal(msg.sender, _l1Receiver, amount);
    }

    function withdrawWithMessage(address _l1Receiver, bytes memory _additionalData) external payable override {
        uint256 amount = _burnMsgValue();


        // Send the L2 log, a user could use it as proof of the withdrawal
        bytes memory message = _getExtendedWithdrawMessage(_l1Receiver, amount, msg.sender, _additionalData);
        L1_MESSENGER_CONTRACT.sendToL1(message);


        emit WithdrawalWithMessage(msg.sender, _l1Receiver, amount, _additionalData);
    }
    /// So the balance of `address(this)` is always bigger or equal to the `msg.value`!
    function _burnMsgValue() internal returns (uint256 amount) {
        amount = msg.value;


        // Silent burning of the ether
        unchecked {
            // This is safe, since this contract holds the ether balances, and if user
            // send a `msg.value` it will be added to the contract (`this`) balance.
            balance[address(this)] -= amount;
            totalSupply -= amount;
        }
    }
    function _getL1WithdrawMessage(address _to, uint256 _amount) internal pure returns (bytes memory) {
        return abi.encodePacked(IMailbox.finalizeEthWithdrawal.selector, _to, _amount);
    }
    function _getExtendedWithdrawMessage(
        address _to,
        uint256 _amount,
        address _sender,
        bytes memory _additionalData
    ) internal pure returns (bytes memory) {
        return abi.encodePacked(IMailbox.finalizeEthWithdrawal.selector, _to, _amount, _sender, _additionalData);
    }
```

From `withdrawWithMessage()`, it would never work to it's full capacity this is because in this function there is no decoding of the extra messages sent and as such the withdrawal would get finalized but the message would get missing in the process since there is no channel of directly parsing the information.

### Recommended Mitigation Steps

> NB: Submitting this as QA as it could be argued to be intended behaviour of the `MailBox.sol`, but if otherwise then I assume it's of med severity.
> If this is intended behaviour then it should be clearly documented, otherwise, update the `_parseL2WithdrawalMessage` function to include logic that decodes and appropriately handles the `_additionalData` included in messages sent by `withdrawWithMessage`. This update might involve emitting an event that logs the additional data or calling another function within the contract that can process or store this data.

Unrelated to the above, also consider standardization of the address formatting, i.e correct the expected byte length for the `l2Sender` and `l1Sender`.

## QA-10 Make the `fallback` a `payable` prefixed function to ensure that calls don't fail from the `bootloader`

### Impact

In the current implementation of `MockKnownCodesStorage`, the fallback function is used to ensure that no calls from the bootloader fail. However, the function lacks the `payable` keyword, meaning any call that attempts to send ether to the contract will revert.

> NB: This was only submitted as QA since the contract is a _test contract_, but it's been listed to be in scope for this contest as can be seen from the [readMe](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d#readme), here is [the referenced line indicating this is in-scope from scope.txt](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/scope.txt#L78), otherwise I assume this could be upgraded to a _med_ severity.

### Proof of Concept

Consider the short contract [MockKnownCodesStorage](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/test-contracts/MockKnownCodesStorage.sol#L4)

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

contract MockKnownCodesStorage {
    event MockBytecodePublished(bytes32 indexed bytecodeHash);

    function markBytecodeAsPublished(bytes32 _bytecodeHash) external {
        emit MockBytecodePublished(_bytecodeHash);
    }

    function getMarker(bytes32) public pure returns (uint256 marker) {
        return 1;
    }

    // To prevent failing during calls from the bootloader
    fallback() external {}
}
```

The function `fallback() external {}` is intended to catch any unexpected calls, particularly from the bootloader. Given the comment and intention, it's clear that the purpose is to avoid failures. However, the absence of the `payable` keyword means that any call that requires ether to be sent will cause the call to fail, contradicting the purpose of the function.

### Recommended Mitigation Steps

To resolve the issue and allow ether to be sent to the contract, update the fallback function as follows:

```solidity
fallback() external payable {}
```

This ensures that the contract can gracefully handle both ether and unexpected function calls, keeping in line with the stated intention of preventing failures from the bootloader.

## QA-11 Add `transaction.signature` to `_encodeHashEIP712Transaction` to ensure compliance with EIP712

### Impact

Medium to low, incomplete compliance with the EIP

### Proof of Concept

Take a look at `_encodeHashEIP712Transaction()`

```soldity

    /// @notice Encode hash of the zkSync native transaction type.
    /// @return keccak256 hash of the EIP-712 encoded representation of transaction
    function _encodeHashEIP712Transaction(Transaction calldata _transaction) private view returns (bytes32) {
        bytes32 structHash = keccak256(
            abi.encode(
                EIP712_TRANSACTION_TYPE_HASH,
                _transaction.txType,
                _transaction.from,
                _transaction.to,
                _transaction.gasLimit,
                _transaction.gasPerPubdataByteLimit,
                _transaction.maxFeePerGas,
                _transaction.maxPriorityFeePerGas,
                _transaction.paymaster,
                _transaction.nonce,
                _transaction.value,
                EfficientCall.keccak(_transaction.data),
                keccak256(abi.encodePacked(_transaction.factoryDeps)),
                EfficientCall.keccak(_transaction.paymasterInput)
            )
        );

        bytes32 domainSeparator = keccak256(
            abi.encode(EIP712_DOMAIN_TYPEHASH, keccak256("zkSync"), keccak256("2"), block.chainid)
        );

        return keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
    }
```

Do note that, as per EIP-712, the encoded EIP-712 hash of a struct must include every member field, but this implementation of the hash does not include `transaction.signature`

### Recommended Mitigation Steps

Add the `transaction.signature` to the struct.

## QA-12 `maxFeePerGas` been hardcoded to 0 which leads to not providing any incentives for validators

### Impact

Any where from med to QA this seems to mean a core functionality of making priority requests wouldn't work which is grounds for med on C4, but would let judge upgrade as they see fit

### Proof of Concept

Take a look at `_serializeL2Transaction()` and `_writePriorityOp()`

```
    function _serializeL2Transaction(
        WritePriorityOpParams memory _priorityOpParams,
        bytes calldata _calldata,
        bytes[] calldata _factoryDeps
    ) internal pure returns (L2CanonicalTransaction memory transaction) {
        transaction = L2CanonicalTransaction({
            txType: PRIORITY_OPERATION_L2_TX_TYPE,
            from: uint256(uint160(_priorityOpParams.sender)),
            to: uint256(uint160(_priorityOpParams.contractAddressL2)),
            gasLimit: _priorityOpParams.l2GasLimit,
            gasPerPubdataByteLimit: _priorityOpParams.l2GasPricePerPubdata,
            maxFeePerGas: uint256(_priorityOpParams.l2GasPrice),
            //@audit
            maxPriorityFeePerGas: uint256(0),
            paymaster: uint256(0),
            // Note, that the priority operation id is used as "nonce" for L1->L2 transactions
            nonce: uint256(_priorityOpParams.txId),
            value: _priorityOpParams.l2Value,
            reserved: [_priorityOpParams.valueToMint, uint256(uint160(_priorityOpParams.refundRecipient)), 0, 0],
            data: _calldata,
            signature: new bytes(0),
            factoryDeps: _hashFactoryDeps(_factoryDeps),
            paymasterInput: new bytes(0),
            reservedDynamic: new bytes(0)
        });
    }
    function _writePriorityOp(
        WritePriorityOpParams memory _priorityOpParams,
        bytes calldata _calldata,
        bytes[] calldata _factoryDeps
    ) internal returns (bytes32 canonicalTxHash) {
        L2CanonicalTransaction memory transaction = _serializeL2Transaction(_priorityOpParams, _calldata, _factoryDeps);

        bytes memory transactionEncoding = abi.encode(transaction);

        TransactionValidator.validateL1ToL2Transaction(transaction, transactionEncoding, s.priorityTxMaxGasLimit);

        canonicalTxHash = keccak256(transactionEncoding);

        s.priorityQueue.pushBack(
            PriorityOperation({
                canonicalTxHash: canonicalTxHash,
                expirationTimestamp: _priorityOpParams.expirationTimestamp,
                layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
            })
        );

        // Data that is needed for the operator to simulate priority queue offchain
        emit NewPriorityRequest(
            _priorityOpParams.txId,
            canonicalTxHash,
            _priorityOpParams.expirationTimestamp,
            transaction,
            _factoryDeps
        );
    }
```

Both functions are attached to the process of passing a priority op, but hardcoding `maxFeePerGas` to 0 means that there is currently no incentive applied to a priority transaction and validators might not want to take a part in this since the fees are hardcoded to 0, but the event been updated off-chain does not even pass information on this.

As can be seen from the factes' interface:

```
    /// @notice New priority request event. Emitted when a request is placed into the priority queue
    /// @param txId Serial number of the priority operation
    /// @param txHash keccak256 hash of encoded transaction representation
    /// @param expirationTimestamp Timestamp up to which priority request should be processed
    /// @param transaction The whole transaction structure that is requested to be executed on L2
    /// @param factoryDeps An array of bytecodes that were shown in the L1 public data. Will be marked as known bytecodes in L2
    event NewPriorityRequest(
        uint256 txId,
        bytes32 txHash,
        uint64 expirationTimestamp,
        L2CanonicalTransaction transaction,
        bytes[] factoryDeps
    );
```

Issue is that even the event does not seem to attach any info of gas that's being passsed which breaks the whole priority request logic since the idea that should be behind a `priority request` is to pass a higher fee to get the validators to hastily execute this.

### Recommended Mitigation Steps

For better system structure make it available for users to be able to pass in their own value
as the fee for any requested priority operation and also pass these values in the event that way a validator knows which to go with and what not.

## QA-13 Interface specifications mismatch with actual functions

### Impact

Confusing code, hardens attempt on understanding code and hints potential flaw in worse instances

### Proof of Concept

Take a look at `proveL2MessageInclusion`

```solidity
    function proveL2MessageInclusion(
        uint256 _l2BatchNumber,
        uint256 _index,
        L2Message calldata _message,
        bytes32[] calldata _proof
    ) external view returns (bool);
```

`_l2BatchNumber` is written as `BatchNumber` in the `MailBox.sol` as seen below

```solidity
    function proveL2MessageInclusion(
        uint256 _batchNumber,
        uint256 _index,
        L2Message memory _message,
        bytes32[] calldata _proof
    ) public view returns (bool) {
        return _proveL2LogInclusion(_batchNumber, _index, _L2MessageToLog(_message), _proof);
    }
```

> NB same issue is present for `proveL2LogInclusion()`

### Recommended Mitigation Steps

Make params name uniform across interfaces and contracts

## QA-14 Multiple fixes for typos/Docs & wrong naming

### Impact

Low, info on how to get a better code structure and pass the right informations.

> NB: This report contains some instances of the grammatical errors, or how the changelog isn't being followed

### Proof of Concept

- See this line: https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/DefaultAccount.sol#L160

Change `signaure` to `signature`

- Take a look at https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Handling%20L1%E2%86%92L2%20ops%20on%20zkSync.md#batch-commit

> Batch commit
> During block commit, the contract will remember those values, but not validate them in any way.
> Change the `During block commit,` to `During batch commit,`

- Another rampant one could be the ergsLimit, this was previously used to differentiate from L1 gas, but updated codebase now has it as L2Gaslimit, but not all instances have this set.

- Another one is the docs attached to `_parseL2WithdrawalMessage()`

```solidity

    /// @dev Decode the withdraw message that came from L2
    function _parseL2WithdrawalMessage(bytes memory _message)
        internal
        pure
        returns (address l1Receiver, uint256 amount)
    {
        // We check that the message is long enough to read the data.
        // Please note that there are two versions of the message:
        // 1. The message that is sent by `withdraw(address _l1Receiver)`
        // It should be equal to the length of the bytes4 function signature + address l1Receiver + uint256 amount = 4 + 20 + 32 = 56 (bytes).
        // 2. The message that is sent by `withdrawWithMessage(address _l1Receiver, bytes calldata _additionalData)`
        // It should be equal to the length of the following:
        // bytes4 function signature + address l1Receiver + uint256 amount + address l2Sender + bytes _additionalData =
        // = 4 + 20 + 32 + 32 + _additionalData.length >= 68 (bytes).
        //@audit

        // So the data is expected to be at least 56 bytes long.
        require(_message.length >= 56, "pm");

        (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_message, 0);
        require(bytes4(functionSignature) == this.finalizeEthWithdrawal.selector, "is");

        (l1Receiver, offset) = UnsafeBytes.readAddress(_message, offset);
        (amount, offset) = UnsafeBytes.readUint256(_message, offset);
    }
}
```

Change the line: `        // bytes4 function signature + address l1Receiver + uint256 amount + address l2Sender + bytes _additionalData = 4 + 20 + 32 + 32 + _additionalData.length >= 68 (bytes).`

To: `        // bytes4 function signature + address l1Receiver + uint256 amount + address l2Sender + bytes _additionalData = 4 + 20 + 32 + 32 + _additionalData.length >= 88 (bytes).`

- Also here:

> /// @dev The function reverts on invalid bytecode hash formam.

`format` not `formam`

- BlockInfo points to two different contexts which is wrong concept

Take a look at both `getBatchNumberAndTimestamp()` & `getL2BlockNumberAndTimestamp()` and some section of `SystemContext.sol`

```solidity
    BlockInfo internal currentBatchInfo;



    BlockInfo internal currentL2BlockInfo;


    //...omitted for brevity


    function getBatchNumberAndTimestamp() public view returns (uint128 batchNumber, uint128 batchTimestamp) {
        //@audit
        BlockInfo memory batchInfo = currentBatchInfo;
        batchNumber = batchInfo.number;
        batchTimestamp = batchInfo.timestamp;
    }

    function getL2BlockNumberAndTimestamp() public view returns (uint128 blockNumber, uint128 blockTimestamp) {
        BlockInfo memory blockInfo = currentL2BlockInfo;
        blockNumber = blockInfo.number;
        blockTimestamp = blockInfo.timestamp;
    }
```

As seen both functions are used to the current block's or batches number and timestamp, issue as highlighted with the `@audit` is that the instead of using `BlockInfo` is been queried for both which really goes against the idea, as `BlockInfo` should only represent the number and the timestamp of the current L2 block.

Make the change below to relevant sections to fix these

```diff
-    BlockInfo internal currentBatchInfo;
+    BatchInfo internal currentBatchInfo;



    BlockInfo internal currentL2BlockInfo;


    //...omitted for brevity


    function getBatchNumberAndTimestamp() public view returns (uint128 batchNumber, uint128 batchTimestamp) {
        //@audit
-        BlockInfo memory batchInfo = currentBatchInfo;
+        BlockInfo memory batchInfo = currentBatchInfo;
        batchNumber = batchInfo.number;
        batchTimestamp = batchInfo.timestamp;
    }

    function getL2BlockNumberAndTimestamp() public view returns (uint128 blockNumber, uint128 blockTimestamp) {
        BlockInfo memory blockInfo = currentL2BlockInfo;
        blockNumber = blockInfo.number;
        blockTimestamp = blockInfo.timestamp;
    }
```

### Recommended Mitigation Steps

Apply all recommended fixes and effectively follow the [Changelog](https://era.zksync.io/docs/reference/troubleshooting/changelog.html#how-to-update-your-project-6)

## QA-15 Deviation from Solidity best styling practices in scoped contracts

### Impact

Difficulties while reading through code

### Proof of Concept

Multiple instances of going against the best styling practices one of such can be seen in Executor.sol

This line:

```solidity
import {L2_BOOTLOADER_ADDRESS, L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, L2_KNOWN_CODE_STORAGE_SYSTEM_CONTRACT_ADDR} from "../../common/L2ContractAddresses.sol";
```

Covers the whole screen which should instead include a line break for better readability

### Recommended Mitigation Steps

Follow best styling practices

## QA-16 `L2EthToken::transferFromTo` should be stopped from emmiting dummy events and also protect users from wasting unnecessary gas

### Impact

Low. The `transferFromTo` function with a transfer amount of 0, the function will still execute and consume gas, even though no tokens are actually transferred to the recipient's address. This can result in unnecessary gas consumption and transaction fees for the sender.

### Proof of Concept

Take a look at `transferFromTo()`

```solidity
    function transferFromTo(address _from, address _to, uint256 _amount) external override {
        require(
            msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
                msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
                msg.sender == BOOTLOADER_FORMAL_ADDRESS,
            "Only system contracts with special access can call this method"
        );

        uint256 fromBalance = balance[_from];
        require(fromBalance >= _amount, "Transfer amount exceeds balance");
        unchecked {
            balance[_from] = fromBalance - _amount;
            // Overflow not possible: the sum of all balances is capped by totalSupply, and the sum is preserved by
            // decrementing then incrementing.
            balance[_to] += _amount;
        }

        emit Transfer(_from, _to, _amount);
    }
```

As seen, if we call `transferFromTo()` function with zero amount, the transferFromTo will be successful and no tokens are transferred to `receiver(_to)`. This leads to an emission of a dummy event.

Hence, the sender needs to pay gas and transaction cost. So the sender loses his tokens.

### Recommended Mitigation Steps

Add the change below

```diff
    function transferFromTo(address _from, address _to, uint256 _amount) external override {
        require(
            msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
                msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
                msg.sender == BOOTLOADER_FORMAL_ADDRESS,
            "Only system contracts with special access can call this method"
        );
+      require(_amount > 0, "No transfer amount specified");
        uint256 fromBalance = balance[_from];
        require(fromBalance >= _amount, "Transfer amount exceeds balance");
        unchecked {
            balance[_from] = fromBalance - _amount;
            // Overflow not possible: the sum of all balances is capped by totalSupply, and the sum is preserved by
            // decrementing then incrementing.
            balance[_to] += _amount;
        }

        emit Transfer(_from, _to, _amount);
    }
```

## QA-17 System hang if validators neglect cross-chain transactions

### Impact

Low, since this relies a but on trust model, ibut currently f the validator refuses to process a cross-chain tx, the user's fund is locked infinitely in the `L1ERC20Bridge.sol`& `L1ETHBridge.sol`

### Proof of Concept

This can be seen while committing or executing batches

```solidity
    function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)
        external
        override
        nonReentrant
        onlyValidator
    {
        // Check that we commit batches after last committed batch
        require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data
        require(_newBatchesData.length > 0, "No batches to commit");

        bytes32 systemContractsUpgradeTxHash = s.l2SystemContractsUpgradeTxHash;
        // Upgrades are rarely done so we optimize a case with no active system contracts upgrade.
        if (systemContractsUpgradeTxHash == bytes32(0) || s.l2SystemContractsUpgradeBatchNumber != 0) {
            _commitBatchesWithoutSystemContractsUpgrade(_lastCommittedBatchData, _newBatchesData);
        } else {
            _commitBatchesWithSystemContractsUpgrade(
                _lastCommittedBatchData,
                _newBatchesData,
                systemContractsUpgradeTxHash
            );
        }

        s.totalBatchesCommitted = s.totalBatchesCommitted + _newBatchesData.length;
    }

        function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {
        uint256 nBatches = _batchesData.length;
        for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
            _executeOneBatch(_batchesData[i], i);
            emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
        }

        uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
        s.totalBatchesExecuted = newTotalBatchesExecuted;
        require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.

        uint256 batchWhenUpgradeHappened = s.l2SystemContractsUpgradeBatchNumber;
        if (batchWhenUpgradeHappened != 0 && batchWhenUpgradeHappened <= newTotalBatchesExecuted) {
            delete s.l2SystemContractsUpgradeTxHash;
            delete s.l2SystemContractsUpgradeBatchNumber;
        }
    }
```

If for any reason the validator refuses to process cross-chain txs by calling the functions above then the transaction in the queue is effectively stuck

### Recommended Mitigation Steps

Measures should be taken to ensure validators would always timely execute and commit batches, this can easily be achieved by having a reward and punish system, i.e validators stake and earn rewards on what's at stake if they perform as needed, otherwise they risk losing their assets.

## QA-18 Multiple issue attached with `_Isvalidsignature` does not follow the specification in the EIP

### Impact

**Medium to low**

- Non compliance with the EIP, cause It's hinted to return an EIP 1271 return value which goes separately to how it's been specified in the EIP since it's to return a magic value instead, i.e `0x1626ba7e`.
- Using this to validate signatures also impacts the ability to validate compact signatures limiting the usability for users that provide these signatures. Additionally, not being able to sign messages from counterfactual contracts/accounts has always been a limitation of [ERC-1271](https://eips.ethereum.org/EIPS/eip-1271) another suggestion could be to integrate EIP 6492 instead since this assumes that the signing contract will normally be a contract wallet, but it could be any contract that implements [ERC-1271](https://eips.ethereum.org/EIPS/eip-1271) and is deployed counterfactually.

  - If the contract is deployed, produce a normal [ERC-1271](https://eips.ethereum.org/EIPS/eip-1271) signature
  - If the contract is not deployed yet, wrap the signature as follows: `concat(abi.encode((create2Factory, factoryCalldata, originalERC1271Signature), (address, bytes, bytes)), magicBytes)`

  > ⚠️ Report was submitted as QA, so as not to spam and making judging harder, nonetheless the second section of impact could be considered as a _refactoring_ case, but I assume the first part and a mixture of both could stand out as a _medium_ case in some instances and would leave final decision to judge.

### Proof of Concept

First take a look at [DefaultAccount.sol#L157-L191](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/DefaultAccount.sol#L157-L191)

```solidity
    /// @notice Validation that the ECDSA signature of the transaction is correct.
    /// @param _hash The hash of the transaction to be signed.
    /// @param _signature The signature of the transaction.
    /// @return EIP1271_SUCCESS_RETURN_VALUE if the signaure is correct. It reverts otherwise.
    function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) {
        require(_signature.length == 65, "Signature length is incorrect");
        uint8 v;
        bytes32 r;
        bytes32 s;
        // Signature loading code
        // we jump 32 (0x20) as the first slot of bytes contains the length
        // we jump 65 (0x41) per signature
        // for v we load 32 bytes ending with v (the first 31 come from s) then apply a mask
        assembly {
            r := mload(add(_signature, 0x20))
            s := mload(add(_signature, 0x40))
            v := and(mload(add(_signature, 0x41)), 0xff)
        }
        require(v == 27 || v == 28, "v is neither 27 nor 28");

        // EIP-2 still allows signature malleability for ecrecover(). Remove this possibility and make the signature
        // unique. Appendix F in the Ethereum Yellow paper (https://ethereum.github.io/yellowpaper/paper.pdf), defines
        // the valid range for s in (301): 0 < s < secp256k1n ÷ 2 + 1, and for v in (302): v ∈ {27, 28}. Most
        // signatures from current libraries generate a unique signature with an s-value in the lower half order.
        //
        // If your library generates malleable signatures, such as s-values in the upper range, calculate a new s-value
        // with 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141 - s1 and flip v from 27 to 28 or
        // vice versa. If your library also generates signatures with 0/1 for v instead 27/28, add 27 to v to accept
        // these malleable signatures as well.
        require(uint256(s) <= 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0, "Invalid s");

        address recoveredAddress = ecrecover(_hash, v, r, s);

        return recoveredAddress == address(this) && recoveredAddress != address(0);
    }
```

#### Issue 1

As commented, this function is meant to validate signatures and then return the `EIP1271_SUCCESS_RETURN_VALUE ` if the signature is valid, but that's not how it's been implemented in the EIP, note that the function is called while trying to validate the signature. [here](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/DefaultAccount.sol#L102)

Navigating to the EIP, [_EIP 1271_](https://eips.ethereum.org/EIPS/eip-1271) we can see that the `isValidSignature()` returns a magic value instead

```soldiity
  function isValidSignature(
    bytes32 _hash,
    bytes calldata _signature
  ) external override view returns (bytes4) {
    // Validate signatures
    if (recoverSigner(_hash, _signature) == owner) {
      return 0x1626ba7e;
    } else {
      return 0xffffffff;
    }
  }
```

#### Issue 2

- There is a check that the signature's length must be 65 as seen here
  `        require(_signature.length == 65, "Signature length is incorrect");`
  The above leads to an inability to validate compact signatures, since the signature length is 64 for these type of signatures.

- As we know, in ERC-4337, the account is not deployed until the first UserOp is mined. Before then, the account exists "counterfactually" — it has an address even though it's not really deployed. Usually, this is great since we can use the counterfactual address to receive assets without deploying the account first, but not being able to sign messages from counterfactual contracts/accounts has always been a limitation of [ERC-1271](https://eips.ethereum.org/EIPS/eip-1271) since one can't call the `_isValidSignature()` function on them.

Which makes the crux of these issues to be the fact that protocol is taking responsibility to check the validity of signatures, but partially failing to trigger signature validation signatures for a group of users/wallets

### Recommended Mitigation Steps

For _Issue 1_, correctly implement the EIP and instead return a magic value.

For _Issue 2_, a different method could be used to validate compact signatures.

Also related to the second section of _Issue 2_, [EIP-6492](https://eips.ethereum.org/EIPS/eip-6492) solves this. The author of the EIP already implemented ERC-6492 in a universal library which verifies 6492, 712, and 1271 sigs with this pull request.

## QA-19 Setters should always have equality checkers

### Impact

Low, info on how to have better code structure and ot going through unnecessary transactions.

### Proof of Concept

Take a look at `updateDelay()`

```solidity
    /// @dev Changes the minimum timelock duration for future operations.
    /// @param _newDelay The new minimum delay time (in seconds) for future operations.
    //@audit setters should have equalit checks in this case it should also include a zero check
    function updateDelay(uint256 _newDelay) external onlySelf {
        emit ChangeMinDelay(minDelay, _newDelay);
        minDelay = _newDelay;
    }

```

As seen, over here unlike in the `_setAccessMode()` function for example that has a check if the value is same, in `updateDelay` the value is just set without any checks which is not probable

### Recommended Mitigation Steps

Include equality checks in setter functions.

## QA-20 `BOOTLOADER_MEMORY_FOR_TXS` allocation reduction is risky

### Impact

The reduction in memory allocation for the bootloader can potentially lead to vulnerabilities in the contract system. If the memory reserved for transaction encoding exceeds the allocated size, it might cause unintended behavior or even transaction failures.

### Proof of Concept

The memory allocated for transaction encoding was changed from:

```json
"BOOTLOADER_MEMORY_FOR_TXS": 485225,
```

to:

```json
"BOOTLOADER_MEMORY_FOR_TXS": 273132,
```

This is a significant reduction, and after inspecting the bootloader contract, there doesn't seem to be significant reductions in functionality or optimizations that justify this change.

The README also notes that `BOOTLOADER_MEMORY_FOR_TXS` (abbreviated as _BM_) is the size of the bootloader memory that is used specifically for transaction encoding. This means that if a particular transaction encoding operation were to exceed the allocated size, it could lead to memory overflow or other unexpected behaviors.

For a system as critical as zkSync, such reductions without clear optimizations or justification pose a potential risk. If there are cases where the transaction encoding exceeds this limit, it could have broad implications for the protocol, leading to transaction failures or other security vulnerabilities.

### Recommended Mitigation Steps

Thoroughly evaluate the reasons for the reduction. If there were optimizations made elsewhere in the system that aren't immediately obvious in the bootloader, they should be documented and analyzed to ensure they justify the memory reduction.

## QA-21 `OperationState.Waiting` should be considered the case even when `timestamp == block.timestamp`

### Impact

Low, info on better code structure

### POC

Take a look at

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

As seen this function is used to get the operation states of ay operation, one thing to note is that the function is supposed to consider the operation pending only `after the timestamp has passed block.timestamp` but in the current implementation even when `timestamp == block.timestamp` the state would be `Ready` instead of `Waiting`

### Recommended Mitigation Steps

Make the `OperationState.Waiting` check inclusive.

## QA-22 Compatibility issues could arise for pure ERC20

Compatibility issues would arise for some ERC20 tokens in the bridge and might lead to deposits not completely working for tokens that purely follow the EIP 20

### Impact

Incompatibility for tokens that purely follow the EIP and do not support `name`/`symbol`/`decimals` addition.

Additionally, some tokens might also support these, but have them in different specification, i.e names can be encoded as `bytes32` instead of `string`, an example is the `MKR` token, which has both it's name and symbol as a `bytes32`, https://etherscan.io/address/0x9f8f72aa9304c8b593d555f12ef6589cc3a579a2#code#L473 & https://etherscan.io/address/0x9f8f72aa9304c8b593d555f12ef6589cc3a579a2#code#L406.

### Proof of Concept

Crux of the issue lies in how tokens that don't correctly support the `name`/`symbol`/`decimals` are being integrated, i.e leaving it in the L2 bridge to sort this out which in some instances could get the call stuck on the L1 bridge.

See `_getERC20Getters()`, which is called for every single deposits while trying to get the callData for the deposit tx on the L2
The code flow _(`deposit()` -> ` _getDepositL2Calldata()` -> ` _getERC20Getters()`)_ for the above claim can be see below:

<details>
  <summary>Click to see full code reference</summary>

```solidity
    function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        uint256 _l2TxGasLimit,
        uint256 _l2TxGasPerPubdataByte,
        address _refundRecipient
    ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) {
        require(_amount != 0, "2T"); // empty deposit amount
        uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
        require(amount == _amount, "1T"); // The token has non-standard transfer logic
        // verify the deposit amount is allowed
        _verifyDepositLimit(_l1Token, msg.sender, _amount, false);

        bytes memory l2TxCalldata = _getDepositL2Calldata(msg.sender, _l2Receiver, _l1Token, amount);
        // If the refund recipient is not specified, the refund will be sent to the sender of the transaction.
        // Otherwise, the refund will be sent to the specified address.
        // If the recipient is a contract on L1, the address alias will be applied.
        address refundRecipient = _refundRecipient;
        if (_refundRecipient == address(0)) {
            refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
        }
        l2TxHash = zkSync.requestL2Transaction{value: msg.value}(
            l2Bridge,
            0, // L2 msg.value
            l2TxCalldata,
            _l2TxGasLimit,
            _l2TxGasPerPubdataByte,
            new bytes[](0),
            refundRecipient
        );

        // Save the deposited amount to claim funds on L1 if the deposit failed on L2
        depositAmount[msg.sender][_l1Token][l2TxHash] = amount;

        emit DepositInitiated(l2TxHash, msg.sender, _l2Receiver, _l1Token, amount);
    }
    function _getDepositL2Calldata(
        address _l1Sender,
        address _l2Receiver,
        address _l1Token,
        uint256 _amount
    ) internal view returns (bytes memory txCalldata) {
        bytes memory gettersData = _getERC20Getters(_l1Token);

        txCalldata = abi.encodeCall(
            IL2Bridge.finalizeDeposit,
            (_l1Sender, _l2Receiver, _l1Token, _amount, gettersData)
        );
    }

    function _getERC20Getters(address _token) internal view returns (bytes memory data) {
        (, bytes memory data1) = _token.staticcall(abi.encodeCall(IERC20Metadata.name, ()));
        (, bytes memory data2) = _token.staticcall(abi.encodeCall(IERC20Metadata.symbol, ()));
        (, bytes memory data3) = _token.staticcall(abi.encodeCall(IERC20Metadata.decimals, ()));
        data = abi.encode(data1, data2, data3);
    }
```

</details>

As seen `_getERC20Getters` queries the token data's name, symbol and decimals, but the EIP 20 specification does not include this and this was added as an extension,.

Additionally this function can cause out of gas issues because of the static call. Static calls forward almost all gas when called, the best way to avoid an out of gas issue is to limit the gas consumption.

For tokens like `MKR` that have their types different from what's specified, are also not compatible and an attempt to try decoding would always revert.

### Recommended Mitigation Steps

Clearly specify that these tokens are not supported, and try not to use the `staticcall` to check for this in `_getERC20Getters`

## QA-23 AllowList could use a blacklisting mechanism

### Impact

Low; info on how to refactor code structure, but currently, if there are any use cases for an address to get blacklisted, say they engage in malicious activity, there is no functionality to do that.

### Proof of Concept

Currently, due to the Alpha period being over, the `allowList` is available to everyone from the public, but this could be changed in the future and instead allow only a specific set of addresses to have access to a function, which is a great use case.

The issue now is that if, instead of selecting a specific set of users to allow, a contract would like to disallow a specific set of users, this would not work. The protocol would need to go through all possible addresses they can think of and set them as allowed, while not setting the other addresses as allowed, which hints at a flaw in code structure and even leads to wasting a lot of gas.

### Recommended Mitigation Steps

Introduce a government-backed `disAllowList()` function.

## QA-24 The interface `ILegacyGetters` only provides availability of deprecated functions instead of the currently accepted ones

### Impact

Low

### Proof of Concept

Take a look at https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/ILegacyGetters.sol

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.13;

import "../libraries/PriorityQueue.sol";
import "./IBase.sol";

/// @author Matter Labs
/// @dev This interface contains getters for the zkSync contract that should not be used,
/// but still are keot for backward compatibility.
interface ILegacyGetters is IBase {
    function getTotalBlocksCommitted() external view returns (uint256);

    function getTotalBlocksVerified() external view returns (uint256);

    function getTotalBlocksExecuted() external view returns (uint256);

    function storedBlockHash(uint256 _batchNumber) external view returns (bytes32);

    function getL2SystemContractsUpgradeBlockNumber() external view returns (uint256);
}
```

But going through the `Getter.sol` contract we can see that all the functios abover are deprecated, this inclides both getter functions `getTotalBlocksVerified(), getTotalBlocksCommitted()` and even the other two functions... _[source](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L208-L246)_

```solidity

    /*//////////////////////////////////////////////////////////////
                        DEPRECATED METHODS
    //////////////////////////////////////////////////////////////*/


    /// @return The total number of batches that were committed
    /// @dev It is a *deprecated* method, please use `getTotalBatchesCommitted` instead
    function getTotalBlocksCommitted() external view returns (uint256) {
        return s.totalBatchesCommitted;
    }


    /// @return The total number of batches that were committed & verified
    /// @dev It is a *deprecated* method, please use `getTotalBatchesVerified` instead.
    function getTotalBlocksVerified() external view returns (uint256) {
        return s.totalBatchesVerified;
    }


    /// @return The total number of batches that were committed & verified & executed
    /// @dev It is a *deprecated* method, please use `getTotalBatchesExecuted` instead.
    function getTotalBlocksExecuted() external view returns (uint256) {
        return s.totalBatchesExecuted;
    }


    /// @notice For unfinalized (non executed) batches may change
    /// @dev It is a *deprecated* method, please use `storedBatchHash` instead.
    /// @dev returns zero for non-committed batches
    /// @return The hash of committed L2 batch.
    function storedBlockHash(uint256 _batchNumber) external view returns (bytes32) {
        return s.storedBatchHashes[_batchNumber];
    }


    /// @return The L2 batch number in which the upgrade transaction was processed.
    /// @dev It is a *deprecated* method, please use `getL2SystemContractsUpgradeBatchNumber` instead.
    /// @dev It is equal to 0 in the following two cases:
    /// - No upgrade transaction has ever been processed.
    /// - The upgrade transaction has been processed and the batch with such transaction has been
    /// executed (i.e. finalized).
    function getL2SystemContractsUpgradeBlockNumber() external view returns (uint256) {
        return s.l2SystemContractsUpgradeBatchNumber;
    }
```

### Recommended Mitigation Steps

Do not use deprecated methods and instead use their current counterparts instead

## QA-25 If Changes are made to `L2ContractHelper/Config.sol` then redeployments of facets should be hastily done

### Impact

Low

### Proof of Concept

There are functions/constants that are used in multiple contracts. Whenever the library function or constant require modification, it is mandatory the Governor updates all facets that employ said changes, otherwise, the behaviour of different facets are inconsistent and may result in unforseen major issues.

Using this search command: `https://github.com/search?q=repo%3Acode-423n4%2F2023-10-zksync+L2ContractHelper.hashL2Bytecode&type=code`

We can see that the function `hashL2Bytecode` is called in multiple contracts `BaseZkSyncUpgrade.sol` `Mailbox.sol`,`L1ERC20Bridge.sol`, and `L1EthBridge.sol` Thus, a change in `hashL2Bytecode` could requuire the redeployment in all four contracts.

### Recommended Mitigation Steps

This could be easily avoided by making L2ContractHelper and Config.sol a facet in the Diamond. Facets can access them through a cross-facet call, while other contracts can call them through the diamond. This way, if changes are required, only one facet needs redeployed and replaced.

## QA-26 Presence of non-production-ready code

### Impact

Low, info on how to get a better code structure in final production.

### Proof of Concept

Take a look at zkSync's contract initialization's `initialize()`

```solidity
    /// @notice zkSync contract initialization
    /// @return Magic 32 bytes, which indicates that the contract logic is expected to be used as a diamond proxy
    /// initializer
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
        s.priorityTxMaxGasLimit = _initalizeData.priorityTxMaxGasLimit;

        // While this does not provide a protection in the production, it is needed for local testing
        // Length of the L2Log encoding should not be equal to the length of other L2Logs' tree nodes preimages
        assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

        return Diamond.DIAMOND_INIT_SUCCESS_RETURN_VALUE;
    }
```

As seen, this: `assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);` is only needed for local testing and does not provide any additional protectionin in final production.

### Recommended Mitigation Steps

Remove this: `assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);`

## QA-27 Add the `verifyingContract` to the TYPEHASH for EIP-712

### Impact

Low, since this is not madatory as specified in the EIP, but having this brings no disadvantages and even makes the already tight nature of replays tighter

### Proof of Concept

Take a look at

```solidty
    /// @notice The EIP-712 typehash for the contract's domain
    bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");
```

As seen the struct lacks the `verifyingContract` section, which has been recommended by the [EIP](https://eips.ethereum.org/EIPS/eip-712), The beklow is stated under the _domain seperator_ section:

> address verifyingContract the address of the contract that will verify the signature. The user-agent may do contract specific phishing prevention.

### Recommended Mitigation Steps

Add _address verifyingContract_ to EIP712_DOMAIN_TYPEHASH, i.e change it to:

```solidity
bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId, address verifyingContract)");
```

Then the respective change should be ade to the domain seperator too.

## QA-28 Redundant `v == 27 || v == 28` check

### Impact

Low, info

### Proof of Concept

Take a look at `_isValidSignature()`

```solidity
    function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) {
        require(_signature.length == 65, "Signature length is incorrect");
        uint8 v;
        bytes32 r;
        bytes32 s;
        // Signature loading code
        // we jump 32 (0x20) as the first slot of bytes contains the length
        // we jump 65 (0x41) per signature
        // for v we load 32 bytes ending with v (the first 31 come from s) then apply a mask
        assembly {
            r := mload(add(_signature, 0x20))
            s := mload(add(_signature, 0x40))
            v := and(mload(add(_signature, 0x41)), 0xff)
        }
        //@audit
        require(v == 27 || v == 28, "v is neither 27 nor 28");

        // EIP-2 still allows signature malleability for ecrecover(). Remove this possibility and make the signature
        // unique. Appendix F in the Ethereum Yellow paper (https://ethereum.github.io/yellowpaper/paper.pdf), defines
        // the valid range for s in (301): 0 < s < secp256k1n ÷ 2 + 1, and for v in (302): v ∈ {27, 28}. Most
        // signatures from current libraries generate a unique signature with an s-value in the lower half order.
        //
        // If your library generates malleable signatures, such as s-values in the upper range, calculate a new s-value
        // with 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141 - s1 and flip v from 27 to 28 or
        // vice versa. If your library also generates signatures with 0/1 for v instead 27/28, add 27 to v to accept
        // these malleable signatures as well.
        require(uint256(s) <= 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0, "Invalid s");

        address recoveredAddress = ecrecover(_hash, v, r, s);

        return recoveredAddress == address(this) && recoveredAddress != address(0);
    }
```

As seen, this check `  require(v == 27 || v == 28, "v is neither 27 nor 28");` is employed but this is not needed since it's always true, for more info, refer to [this tweet](https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg)

### Recommended Mitigation Steps

Remove the check: `require(v == 27 || v == 28, "v is neither 27 nor 28");`
