# Analysis - zkSync Era

### Summary
| List |Head |Details|
|:--|:----------------|:------|
|1|Overview of zkSync Era| Description overview of zkSync Era Protocol|
|2 |Code review approach and methodology | Understand the purpose of the code |
|3 |Analysis of the code base | To evaluate the quality, design, and implementation of the code  |
|4 |Mechanism Review | Thorough examination, assessment, or evaluation of a mechanism  |
|5 |Architecture Overview of Zksync Era| Project's architecture and features |
|6 |Systemic & Centralization Risks | Ensure that the system remains robust against systemic and centralization risks |
|7|Competition analysis | Other Competitors for Zksyc  |
|8 |Other Recommendations | General Recommendations  |
|9 |New insights and learning from this audit | Learnings from this audit |
|10|Time spent | The total time spend for this analysis |

## Overview of zkSync Era

zkSync Era is a robust ``Layer-2 scaling`` solution designed to enhance the ``scalability`` and ``efficiency`` of Ethereum. It achieves this by ``amalgamating`` smart contracts deployed on the ``Ethereum mainnet`` with ``zkEVM``, allowing for ``Ethereum Virtual Machine`` (EVM)-compatible smart contract execution. 

This technical overview delves into the core components and functionalities of zkSync Era

### zkSync Smart Contracts

The ``zkSync`` smart contracts deployed on ``Ethereum's mainnet`` form the foundational layer of the ``zkSync`` Era protocol. These contracts facilitate various crucial operations and interactions, such as ``deposits``, ``withdrawals``, and executing smart contracts in a ``Layer-2 environment``. They maintain the ``security`` and ``integrity`` of the Layer-2 network while ensuring ``compatibility`` with ``Ethereum's mainnet``.

### zkEVM

``zkEVM`` is the technological ``heart`` of ``zkSync Era``, providing ``EVM-compatible`` smart contract execution within the ``Layer-2 environment``. It leverages ``Zero-Knowledge Proofs`` to securely validate transactions and contract interactions, offering ``high throughput`` and substantially ``lower gas fees`` compared to ``Ethereum's mainnet``. Developers can deploy and run smart contracts on ``zkEVM`` with minimal changes to their code.

### zk-Circuits

``zkSync`` Era employs ``zk-Circuits`` to handle ``transaction privacy``, ``validate proofs``, and maintain the consistency and correctness of the ``Layer-2 state``. These circuits ensure that all operations within ``zkSync`` are transparently executed and meet security standards. zk-Circuits play a ``pivotal role`` in providing ``zkSync`` with its ``scalability``, ``privacy``, and ``security features``.

## Code review approach and methodology

In the initial phase of our code review, conducted a thorough ``assessment`` of the project's scope. This assessment provided us with a clear understanding of the project's ``objectives``, ``architecture``, and the specific components that needed to be reviewed.

### Important Scope Contracts

#### L1 Contracts

   - [Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol)
   ``ExecutorFacet `` contract is the core contract in zkSync, responsible for ``processing`` and ``validating batches`` of transactions. It ensures ``correct order`` and ``timing of batch execution``, handles ``priority operations``, and ``validates Zero-Knowledge Proofs``.
   
   - [Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol)
    ``MailboxFacet`` is a contract used in the zkSync Layer-2 scaling solution for Ethereum. It acts as a ``communication channel`` between the ``Layer-1 (Ethereum)`` and ``Layer-2 (zkSync)`` chains, facilitating the transfer of assets and information between them.
    
   - [TransactionValidator.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol)
      ``TransactionValidator`` contract is a Solidity library for ``validating`` ``Layer-1 to Layer-2 ``(L1 to L2) transactions. It's designed for use in ``zkSync``, a ``Layer-2 scaling solution``
    
   - [Admin.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol)  
      The ``AdminFacet`` contract is responsible for managing access ``rights`` within a ``system``. It allows for the ``transfer`` of ``governor`` and ``admin roles``.
    
   - [L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol) 
       The ``L1ERC20Bridge`` contract is used for bridging ERC20 tokens from ``Ethereum Layer`` 1 (L1) to ``zkSync Layer`` 2 (L2). You can deposit tokens from L1 to L2 using the ``deposit`` function. If a deposit fails, you can claim the tokens back using ``claimFailedDeposit``. Additionally, the contract ``finalizes withdrawals`` from ``L2`` to ``L1``
     
   - [L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol)
      The ``L1WethBridge`` is a Solidity smart contract designed to facilitate the seamless transfer of ``Wrapped Ether`` ``(WETH)`` tokens between ``Layer 1 (L1)`` and ``Layer 2 (L2)`` Ethereum networks
    
   - [Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)
      The ``Governance`` smart contract, inspired by OpenZeppelin's ``TimelockController`` and Matter Labs  ``Diamond Proxy`` upgrade mechanism, facilitates governance tasks and upgrades within zkSync Era governed contracts. It enables the ``scheduling``, ``execution``, and ``cancellation`` of operations with different ``permissions`` and ``delays``
    
   - [BaseZkSyncUpgrade.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol)
      The ``BaseZkSyncUpgrade`` is an abstract smart contract used as a foundation for implementing upgrades in the ``zkSync protocol``. It defines a set of functions and events for managing protocol upgrades, such as ``changing the protocol version``, updating ``bytecode hashes`` for ``L2 components``, and transitioning to a new verifier contract. It also provides a structure for ``representing upgrade proposals``
   
   - [AllowList.sol]()   
    The ``AllowList`` contract that stores the ``permissions`` to call the function on ``different contracts``.The contract is fully controlled by the ``owner``, that can grant and revoke ``any permissions`` at any time.
    
#### L2 Contracts

 - [L1Messenger.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)
    ``L1Messenger`` contract for sending arbitrary length messages to L1 by default ZkSync can send fixed length messages on L1. A fixed length message has 4 parameters ``senderAddress``, ``isService``, ``key``, ``value``
    
 - [ContractDeployer.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)
      ``ContractDeployer`` is system smart contract that is responsible for deploying ``other smart contracts`` on ``zkSync``
   
 - [Compressor.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)
      ``Compressor`` Contract with code pertaining to ``compression`` for ``zkEVM``. At the moment this is used for ``bytecode compression`` and state diff ``compression validation``
   
 - [DefaultAccount.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)
     The default implementation of account. The bytecode of the contract is set by default for all addresses for which no other bytecodes are deployed.
     
 - [NonceHolder.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol)
    ``NonceHolder`` contract used for managing ``nonces`` for accounts. Together with ``bootloader``,this contract ensures that the pair ``(sender, nonce)`` is always ``unique``, ensuring ``unique`` transaction hashes.
    
 - [L2EthToken.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol)
     This contract is used by the ``bootloader`` and ``MsgValueSimulator`/`ContractDeployer`` system contracts to perform the balance changes while simulating the ``msg.value`` Ethereum behavior.
    
 - [MsgValueSimulator.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol)
     The contract responsible for simulating transactions with ``msg.value`` inside ``zkEVM``
    
 - [L2ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol)
     The ``default bridge`` implementation for the ERC20 tokens
    
 - [L2StandardERC20.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol)
      The ERC20 token implementation, that is used in the "default" ERC20 bridge
 - [L2WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol)
      This contract works in conjunction with the ``L1WethBridge`` to streamline the process of bridging ``WETH`` tokens between ``L1`` and ``L2``
    
 - [L2Weth.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol)
     The canonical implementation of the WETH token
     
 - [SystemContractsCaller.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/SystemContractsCaller.sol)
     ``SystemContractsCaller`` contractcontains the functions to make system calls
     
   #### YUL Files
   
  - [EventWriter.yul](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EventWriter.yul)
     The ``EventWriter`` is a contract or object within a smart contract system that handles the ``decoding`` and ``writing of events`` using ``low-level`` instructions in ``Ethereum``
     
  - [EcAdd.yul](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/precompiles/EcAdd.yul)
     The ``EcAdd`` object within this smart contract appears to be responsible for implementing the ``addition operation`` for ``elliptic curve`` points on the ``alt_bn128`` curve
     
  - [EcMul.yul](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/precompiles/EcMul.yul)
     The provided code appears to be a Solidity smart contract named ``EcMul_deployed`` implementing elliptic curve point ``multiplication`` using the ``Montgomery`` form for a specific ``elliptic curve`` known as ``alt_bn128``
     
  - [Ecrecover.yul](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/precompiles/Ecrecover.yul)
     Contract called ``Ecrecover_deployed``, which emulates the behavior of the Ethereum Virtual Machine's (EVM) ``ecrecover precompile``. It uses the ``precompileCall`` function to call zkEVM's built-in precompiles for ``cryptographic signature recovery``
     
  - [Keccak256.yul](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/precompiles/Keccak256.yul)
     Contract, named ``Keccak256_deployed,`` emulates the behavior of Ethereum's ``keccak256 opcode``
     
 - [SHA256.yul](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/precompiles/SHA256.yul)
     Contract, ``SHA256_deployed,`` emulates Ethereum's ``sha256`` precompile functionality
     
## Analysis of the code base

I've presented a detailed technical explanation of the real-time operation of key processes in the ZKSync era, including their flow, illustrated through flow diagrams

###  Flow of Commit New Batches

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

```
![Commiting New Batches](https://user-images.githubusercontent.com/58845085/277153555-67c0afa8-37d3-4253-9f28-06404e414707.png)

### Verifying the Transmission of an Arbitrary-Length Message within a Designated L2 Batch

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
![Prove that a specific arbitrary-length message was sent in a specific L2 batch number- MailBox](https://user-images.githubusercontent.com/58845085/277153782-e2071b0d-4b05-4fc5-b92e-19e6f0d5660b.png)

### Execution flow of L2 transaction from L1

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
        // Change the sender address if it is a smart contract to prevent address collision between L1 and L2.
        // Please note, currently zkSync address derivation is different from Ethereum one, but it may be changed in the future.
        address sender = msg.sender;
        if (sender != tx.origin) {
            sender = AddressAliasHelper.applyL1ToL2Alias(msg.sender);
        }

        // Enforcing that `_l2GasPerPubdataByteLimit` equals to a certain constant number. This is needed
        // to ensure that users do not get used to using "exotic" numbers for _l2GasPerPubdataByteLimit, e.g. 1-2, etc.
        // VERY IMPORTANT: nobody should rely on this constant to be fixed and every contract should give their users the ability to provide the
        // ability to provide `_l2GasPerPubdataByteLimit` for each independent transaction.
        // CHANGING THIS CONSTANT SHOULD BE A CLIENT-SIDE CHANGE.
        require(_l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp");

        // The L1 -> L2 transaction may be failed and funds will be sent to the `_refundRecipient`,
        // so we use `msg.value` instead of `_l2Value` as the bridged amount.
        _verifyDepositLimit(msg.sender, msg.value);
        canonicalTxHash = _requestL2Transaction(
            sender,
            _contractL2,
            _l2Value,
            _calldata,
            _l2GasLimit,
            _l2GasPerPubdataByteLimit,
            _factoryDeps,
            false,
            _refundRecipient
        );
    }

```

![execution flow of L2 transaction from L1- MailBox](https://user-images.githubusercontent.com/58845085/277153926-e2056e64-b557-4bc7-ab42-d5e06f4126c1.png)

### Validation key properties of an L1->L2 transaction

```solidity
 function validateL1ToL2Transaction(
        IMailbox.L2CanonicalTransaction memory _transaction,
        bytes memory _encoded,
        uint256 _priorityTxMaxGasLimit
    ) internal pure {
        uint256 l2GasForTxBody = getTransactionBodyGasLimit(
            _transaction.gasLimit,
            _transaction.gasPerPubdataByteLimit,
            _encoded.length
        );

        // Ensuring that the transaction is provable
        require(l2GasForTxBody <= _priorityTxMaxGasLimit, "ui");
        // Ensuring that the transaction cannot output more pubdata than is processable
        require(l2GasForTxBody / _transaction.gasPerPubdataByteLimit <= PRIORITY_TX_MAX_PUBDATA, "uk");

        // Ensuring that the transaction covers the minimal costs for its processing:
        // hashing its content, publishing the factory dependencies, etc.
        require(
            getMinimalPriorityTransactionGasLimit(
                _encoded.length,
                _transaction.factoryDeps.length,
                _transaction.gasPerPubdataByteLimit
            ) <= _transaction.gasLimit,
            "up"
        );
    }

```
![validate key properties of an L1-L2 transaction](https://user-images.githubusercontent.com/58845085/277154171-c1bfd2a6-7a8d-45ae-aebd-a77adc9e2285.png)

### FinalizeWithdrawal from L2 to L1

```solidity
function finalizeWithdrawal(
        uint256 _l2BatchNumber,
        uint256 _l2MessageIndex,
        uint16 _l2TxNumberInBatch,
        bytes calldata _message,
        bytes32[] calldata _merkleProof
    ) external nonReentrant senderCanCallFunction(allowList) {
        require(!isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "pw"); 

        L2Message memory l2ToL1Message = L2Message({
            txNumberInBatch: _l2TxNumberInBatch,
            sender: l2Bridge,
            data: _message
        });

        (address l1Receiver, address l1Token, uint256 amount) = _parseL2WithdrawalMessage(l2ToL1Message.data);
        // Preventing the stack too deep error
        {
            bool success = zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);
            require(success, "nq");
        }

        isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex] = true; 
        // Withdraw funds
        IERC20(l1Token).safeTransfer(l1Receiver, amount);

        emit WithdrawalFinalized(l1Receiver, l1Token, amount);
    }

```
![FinalizeWithdrawalfromL2to L1](https://user-images.githubusercontent.com/58845085/277154261-c69181bf-67f9-439a-a0d4-e3ca7521f446.png)

### Upgrade Process

```solidity
function upgrade(ProposedUpgrade calldata _proposedUpgrade) public override returns (bytes32) {
        super.upgrade(_proposedUpgrade);

        _setNewProtocolVersion(_proposedUpgrade.newProtocolVersion);
        _upgradeL1Contract(_proposedUpgrade.l1ContractsUpgradeCalldata);
        _upgradeVerifier(_proposedUpgrade.verifier, _proposedUpgrade.verifierParams);
        _setBaseSystemContracts(_proposedUpgrade.bootloaderHash, _proposedUpgrade.defaultAccountHash);

        bytes32 txHash;
        txHash = _setL2SystemContractUpgrade(
            _proposedUpgrade.l2ProtocolUpgradeTx,
            _proposedUpgrade.factoryDeps,
            _proposedUpgrade.newProtocolVersion
        );

        _setAllowList(IAllowList(_proposedUpgrade.newAllowList));
        _postUpgrade(_proposedUpgrade.postUpgradeCalldata);

        emit UpgradeComplete(_proposedUpgrade.newProtocolVersion, txHash, _proposedUpgrade);

        return Diamond.DIAMOND_INIT_SUCCESS_RETURN_VALUE;
    }

```
![Upgrade-Process](https://user-images.githubusercontent.com/58845085/277154348-025fd3ee-9808-4dab-9049-b93d5315a05e.png)

### Message Sending Process Form L2 to L1

```solidity

function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {
        uint256 gasBeforeMessageHashing = gasleft();
        hash = EfficientCall.keccak(_message);
        uint256 gasSpentOnMessageHashing = gasBeforeMessageHashing - gasleft();

        /// Store message record
        chainedMessagesHash = keccak256(abi.encode(chainedMessagesHash, hash));

        /// Store log record
        L2ToL1Log memory l2ToL1Log = L2ToL1Log({
            l2ShardId: 0,
            isService: true,
            txNumberInBlock: SYSTEM_CONTEXT_CONTRACT.txNumberInBlock(),
            sender: address(this),
            key: bytes32(uint256(uint160(msg.sender))),
            value: hash
        });
        _processL2ToL1Log(l2ToL1Log);

        // Get cost of one byte pubdata in gas from context.
        uint256 meta = SystemContractHelper.getZkSyncMetaBytes();
        uint32 gasPerPubdataBytes = SystemContractHelper.getGasPerPubdataByteFromMeta(meta);

        uint256 pubdataLen;
        unchecked {
            // 4 bytes used to encode the length of the message (see `publishPubdataAndClearState`)
            // L2_TO_L1_LOG_SERIALIZE_SIZE bytes used to encode L2ToL1Log
            pubdataLen = 4 + _message.length + L2_TO_L1_LOG_SERIALIZE_SIZE;
        }

        // We need to charge cost of hashing, as it will be used in `publishPubdataAndClearState`:
        // - keccakGasCost(L2_TO_L1_LOG_SERIALIZE_SIZE) and keccakGasCost(64) when reconstructing L2ToL1Log
        // - keccakGasCost(64) and gasSpentOnMessageHashing when reconstructing Messages
        // - at most 2 times keccakGasCost(64) (as merkle tree can contain ~2*N leaves)
        uint256 gasToPay = pubdataLen *
            gasPerPubdataBytes +
            keccakGasCost(L2_TO_L1_LOG_SERIALIZE_SIZE) +
            4 *
            keccakGasCost(64) +
            gasSpentOnMessageHashing;
        SystemContractHelper.burnGas(Utils.safeCastToU32(gasToPay));

        emit L1MessageSent(msg.sender, hash, _message);
    }

```
![send messages to L1 FromL2- L1messenger](https://user-images.githubusercontent.com/58845085/277154530-32bd9156-341d-4495-8ef9-da6e2dd5a7b0.png)

### The Deposit Flow 

```solidity
 function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        uint256 _l2TxGasLimit,
        uint256 _l2TxGasPerPubdataByte
    ) external payable returns (bytes32 l2TxHash) {
        l2TxHash = deposit(_l2Receiver, _l1Token, _amount, _l2TxGasLimit, _l2TxGasPerPubdataByte, address(0));
    }
```
![L1toL2Depositflow](https://user-images.githubusercontent.com/58845085/277155020-c51f0007-6b27-46a7-bc54-cc5bb45429cb.png)


## Mechanism Review

### Zksync Distinctive Patterns 

### Hyperscaling
   Hyperchains are parallel zkEVM instances interconnected through Hyperbridges, akin to how the web uses hyperlinks for site navigation.    They share a common settlement layer on the Ethereum L1 mainnet

### ZK rollups 
   - ZK rollups, short for ``Zero-Knowledge Rollups``, are a technology used in the Ethereum ecosystem to enhance the ``scalability`` and     ``efficiency`` of the network.
   - In a ``ZK rollup``, a batch of transactions is verified for correctness off-chain (outside the Ethereum network), utilizing zero-   knowledge proofs. Once this batch is confirmed to be valid through the ``zero-knowledge proof``, only a single succinct proof is submitted to the Ethereum network.

### Zero-knowledge proof (ZKP)
 - ``ZKPs`` are a cryptographic technique that allows ``one party`` to prove to ``another party`` that they know a certain piece of information without ``revealing the information`` itself .

### zkSync specific opcodes
These opcodes are allowed only for contracts in kernel space (i.e. system contracts). If executed in other places they result in revert(0,0)

 - ``mimic_call`` - Same as a normal call, but it can alter the msg.sender field of the transaction 
 - ``to_l1`` - Sends a system L2→L1 log to Ethereum
 - ``event`` - Emits an L2 log to zkSync
 -  ``precompile_call`` - This is an opcode that accepts two parameters: the uint256 representing the packed parameters for it as well as the ergs to burn. 
   - If it is the address of the ``ecrecove``r system contract, it performs the ecrecover operation
   - If it is the address of the ``sha256/keccak256`` system contracts, it performs the corresponding hashing operation.
 - ``setValueForNextFarCall`` - Sets ``msg.value`` for the next ``call/mimic_call ``
 - ``increment_tx_counter`` - Increments the counter of the transactions within the VM
 
Opcodes that can be generally accessed by any contract

  - ``near_call`` - ``framed`` jump to some location of the code of your contract
  - ``getMeta`` - Returns an ``u256`` packed value of ``ZkSyncMeta struct``
  - ``getCodeAddress`` - Receives the address of the executed code

### Flags for calls

  - ``r1``-  The pointer to the calldata
  - ``r2`` - This is a mask, where each bit is set only if certain flags have been set to the call
   Two flags are supported
     - ``0-th bit``: ``isConstructor`` flag
     - ``1-st bit``: ``isSystem`` flag
   - r3 - r12 registers are non-empty only if the isSystem flag is set

### Existing Patterns Used

#### ``Access Control`` 
   - On-chain access controls
    The zkSync protocol defines a number of on-chain roles that control access to different parts of the system. For example, the ``OnlyGovernor`` role and ``onlyGovernorOrAdmin`` is responsible for managing the system's parameters.
    
  ```solidity
  function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {
  
  function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {
  
  ```
  - Off-chain access controls
   zkSync uses a permissioned key management system (KMS) to store the private keys that are used to sign transactions.
   
#### ``Token Standards``
  zkSync supports deposits of ETH and any ERC-20 token . Users can deposit tokens from the Ethereum mainnet to zkSync by using the zkSync bridge
 
## Architecture Overview of Zksync Era

### Hyperscaling 

<img width="3780" alt="hyperbridges-2fe1f60d" src="https://user-images.githubusercontent.com/58845085/277155352-d30444b2-0c3d-49d4-9260-5c3b61456aba.png">

#### Why Hyperscaling ? 
Hyperchains address the pressing need for scalability, interoperability, and security in the blockchain space. They provide a framework for a more scalable, interconnected, and secure blockchain ecosystem while promoting innovation and flexibility through a permissionless and modular approach.

#### How Hyperbridges work

<img width="3385" alt="hyperscalingBridgingFull-80a704a4" src="https://user-images.githubusercontent.com/58845085/277155595-d89c5316-e09b-4f0f-a9a7-de111ef90d66.png">


- Sending Hyperchain settles its proof on L1.
- This updates the Transaction Root, committing to Hyperbridge transactions.
- Receiving Hyperchain imports the Transaction Root.
- A relayer sends the transaction with a Merkle Proof.
- The proof is verified, and if correct, the transaction is executed.
- The receiving Hyperchain settles its proof, verifying the imported Transaction Root.

### Architecture Risk in Hyperchain architecture 

#### Architecture Complexity
The ``Hyperchains architecture`` is ``complex``, and it may be difficult for ``developers`` to understand and ``use``. 

#### The lack of a clear governance model
The governance of Hyperchains is unclear. This could lead to disputes between Hyperchains that could be difficult to resolve.

#### The use of Hyperbridges
Hyperbridges are used to connect ``Hyperchains`` together. However, ``Hyperbridges`` could be used to create ``malicious Hyperchains`` that could harm the ``Hyperchains ecosystem``.

#### The reliance on a single zkEVM engine
If a vulnerability is found in the ``zkEVM engine``, all ``Hyperchains`` could be affected. This is because ``all Hyperchains`` are required to use the same ``zkEVM ``engine in order to be interoperable.

### Blocks in zkSync Era

In zkSync there are two notions of blocks: ``L2 block`` and ``L1 rollup block``

``L2 blocks`` - L2 blocks, also known simply as "blocks," are created on the L2 network (zkSync Era)
``L1 rollup blocks`` - L1 rollup blocks are ``batches`` of consecutive`` L2 blocks`` that contain all the ``transactions`` in the same order, from the ``first block`` to the ``last block`` in the batch

#### Block Properties

``Hash``: Block's unique identifier (null if pending).
``ParentHash``: Hash of the parent block in L2.
``Number``: Block's sequence number (null if pending).
``Timestamp``: Time when the block was created.
``Nonce``: Latest transaction count for an account (null if pending).
``Difficulty``: Fixed at 2.5 quadrillion (zkSync uses a different consensus).
``GasLimit``: Maximum gas allowed in hexadecimal, always 2^32-1.
``GasUsed``: Actual gas consumed in the block.
``Transactions``: Array of transaction objects.
``BaseFeePerGas``: EIP1559-like baseFee for the block.


### Transaction data on zkSync Era
Transactions on zkSync Era are similar to Ethereum transactions so you can use the same wallet as you use on Ethereum. There are some minor differences, mostly with regards to setting the fees.

```
Whether it originated on Layer 1 (L1): ``is_l1_originated`` (boolean).
Transaction status: ``status`` (Pending, Included, Verified, or Failed).
Transaction fee: ``fee`` (U256, related to the fee mechanism).
Initiator's address: ``initiator_address``.
Receipt timestamp: ``received_at`` (in Utc format).
Ethereum commitment transaction hash: ``eth_commit_tx_hash`` (optional).
Ethereum proof transaction hash: ``eth_prove_tx_hash`` (optional).
Ethereum execution transaction hash: ``eth_execute_tx_hash`` (optional).
```

### Fee mechanism
zkSync Era's fee model is similar to Ethereum’s where gas is charged for computational cost, cost of publishing data on-chain and storage effects. However, zkSync Era includes additional costs for publishing to L1 and for proof generation.

``gasPrice``: the price, in gwei, of a unit of gas.
``gasPerPubdata``: the amount of gas for publishing one byte of data on Ethereum.

#### Risks of Unpredictable transaction fees

The ``L1 gas price`` is ``volatile``, which means that the amount of gas required to publish data to ``L1`` can vary. This can make it difficult for ``users`` to predict the ``cost of transactions``.

#### Risk of Reduced throughput

The ``proof generation`` process can be ``computationally expensiv``e. This can limit the ``throughput`` of the ``zkSync Era network``.

### Bridges
Bridge is implemented by having two contracts ``L1ERC20Bridge`` , ``L2ERC20Bridge``  (one deployed to L1, and the second deployed to L2) communicating with each other using L1 <-> L2 interoperability.

### System contracts
- ContractDeployer
- L1Messenger
- NonceHolder
- Bootloader

### L1 Smart contracts
  - Diamond
  - DiamondProxy
  - DiamondInit
  - DiamondCutFacet
  - GettersFacet
  - GovernanceFacet
  - MailboxFacet
  - ExecutorFacet

### Smart contract development

zkSync Era allows developers to build projects using the same programming languages and tools used to build on Ethereum

#### Languages Support

- Solidity support
- Vyper support

### Compilers

- zksolc
- zkvyper

#### Benefits of zkSync Era

 - ``High scalability``: zkSync Era can process thousands of transactions per second, making it a highly scalable solution for Ethereum.
 - ``Low fees``: zkSync Era transactions are much cheaper than Ethereum mainchain transactions.
 - ``Fast settlemen``t: zkSync Era transactions are settled within minutes, compared to hours or days on the Ethereum mainchain.
 - ``EVM compatibility``: zkSync Era is compatible with the Ethereum Virtual Machine (EVM), which means that existing Ethereum applications can be deployed on zkSync without any modifications.

## Systemic & Centralization Risks

### Genaral Systemic Risks

#### Reliance on the sequencer
The ``sequencer`` is a critical component of ``zkSync Era``. The sequencer is responsible for ordering transactions and generating blocks. If the ``sequencer`` is ``compromised``, it could result in a number of problems, such as the loss of funds or the ``double-spending`` of tokens.

#### Complexity of the zk-proof generation process
The zk-proof generation process is a complex and computationally expensive process. If there is a bug in the zk-proof generation process, it could result in the generation of invalid zk-proofs. This could lead to the rejection of valid transactions or the acceptance of invalid transactions

#### Centralization of the Hyperchains architecture
The Hyperchains architecture is a centralized architecture. This means that a small number of actors have a large amount of control over the network

#### Collision attacks
A collision attack is an attack in which an attacker is able to generate ``two different transactions`` that have the ``same hash``. This could allow the ``attacker to ``double-spend tokens`` or to manipulate the state of the network.

#### Execution delay
zkSync Era uses a technique called ``execution delay`` to ensure the security of the network. Execution delay means that transactions are not executed immediately on L2. Instead, they are first batched together and then executed on L1. This can lead to a delay of several minutes between the time a transaction is submitted and the time it is executed.

#### Gas cost
The gas cost of using zkSync Era may consume more gas. This is because zkSync Era needs to generate ``zk-proofs ``for every transaction. The zk-proof generation process is computationally expensive, and this cost is passed on to users in the form of higher gas fees.

#### Limited support
zkSync Era is a relatively ``new technology``, and it is not yet supported by ``all wallets`` and ``exchanges``. This means that users may have difficulty using ``zkSync Era`` if they do not have a ``wallet`` or ``exchange`` that supports it.

#### Competition 
zkSync Era is not the only ``scaling solution`` for Ethereum. There are a number of other scaling solutions being developed, such as ``Optimistic Rollups`` and ``Plasma``. These other scaling solutions have their ``own advantages`` and ``disadvantages``, and it is not clear which solution will ultimately be the most successful.

#### Smart contract risk
As with any smart contract, there is always the risk of ``bugs`` or ``vulnerabilities`` in the zkSync code. If a bug is found, it could result in the ``loss of funds`` or the ``manipulation`` of the state of the network.

#### User error
zkSync is a ``complex system``, and there is always the risk of ``user error``. For example, a user could accidentally send funds to the wrong address or lose their ``private keys``. This could result in the loss of funds.

### Here's an analysis of potential systemic and centralization risks in the provided contracts

#### Executor :

``Systemic Risk``:
- There's a risk of incorrect contract replacements or failed upgrades
- Gas costs are not explicitly checked in the code. If the gas consumption exceeds block gas limits, transactions could fail
- The correct finalization of L2 to L1 logs is essential for data integrity. If logs are not processed correctly, it can lead to incorrect data being committed to the Ethereum mainnet

``Cenralization Risks`` :
Centralization risks associated with these functions, primarily due to the ``onlyValidator`` modifier. The functions ``proveBatches()``, ``revertBatches()``, ``commitBatches()``, and ``executeBatches()`` are only accessible to an entity or a group of validators.

### Mailbox

``Systemic Risk``:
- The correctness of the ``proveL2MessageInclusion`` function is crucial. If it fails to validate the inclusion of L2 logs, it could lead to improper withdrawal or transaction processing, potentially causing financial losses or inconsistencies.
- The contract enforces a maximum gas limit for priority transactions ``(s.priorityTxMaxGasLimit)``. Inadequate estimation of gas limits could lead to transaction failures or reordering issues.

### TransactionValidator

``Systemic Risk``:
 - External dependencies like ``OpenZeppelin``. Systemic risks can emerge if vulnerable versions are used
 -  In the ``validateL1ToL2Transaction`` function, validate the input data, particularly the ``_encoded`` and ``_transaction`` parameters. Verify that the ``_encoded`` data is not excessively large or potentially malformed, which could lead to ``out-of-gas`` or other vulnerabilities
 -  Verify the logic in the ``getOverheadForTransaction`` function, especially the calculations related to overhead, ``tx`` slot overhead, and overhead for gas.

### Admin

``Cenralization Risks`` :

```Solidity

 function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {
        // Save previous value into the stack to put it into the event later
        address oldPendingGovernor = s.pendingGovernor;
        // Change pending governor
        s.pendingGovernor = _newPendingGovernor;
        emit NewPendingGovernor(oldPendingGovernor, _newPendingGovernor);
    }

    /// @notice Accepts transfer of governor rights. Only pending governor can accept the role.
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
The provided code shows a mechanism for transferring governance rights, but it lacks additional safeguards to mitigate certain risks

#### Unrestricted Governor Power
The current implementation allows the pending governor to ``unilaterally`` accept the role of ``governor``. In a decentralized system, it's generally more secure to have some form of community ``consensus``, ``time delay``, or ``additional checks`` before a change in governance is accepted.

#### No Limit on Governance Transfers
The code doesn't appear to limit the number of times ``governance`` can be transferred. This could potentially lead to ``abuse`` or ``frequent changes``, which might not be in the best interest of the project or the community.

#### Lack of Time Locks
A ``time delay`` mechanism is often used in ``governance`` contracts to give the ``community time`` to react to changes. In this code, the transfer of governance is immediate, which doesn't allow for a period of consideration or intervention if a ``malicious`` or ``mistaken transfer occurs``.

#### Pending governor could indefinitely delay or ignore the acceptance of governance rights
The absence of a time limit to accept governance changes to the pendingGovernor introduces a potential risk. If there's no time limit, a pending governor could indefinitely delay or ignore the acceptance of governance rights, which could disrupt the governance process and potentially lead to centralization risks

### Mitigation Steps

- Multi-Signature or Governance Module
- Time Delay
- Consensus Mechanisms

### L1ERC20Bridge

``Systemic Risk``:

- The contract uses hardcoded addresses for ``zkSync`` and ``_l2TokenBeacon``. It's essential to ensure that these addresses are accurate 
-  During contract initialization, it requires specific gas fees for deploying the ``L2 bridge`` implementation and ``L2 bridge proxy``. Any discrepancies in these fees can lead to deployment failures.
-  The ``nonReentrant`` modifier is used to prevent reentrancy attacks. Ensure that it effectively protects the contract from reentrancy risks in all relevant functions
- The presence of two ``deposit`` methods can be confusing for users 
- Confirm that deposit limits are enforced correctly and cannot be bypassed, especially if the deposit limit information can change

### L2ERC20Bridge

``Systemic Risk``:

- Verify that value transfers are handled securely. The contract currently requires msg.value to be 0 for ERC20 bridge operations, but ensure that no unexpected value transfers can occur
- The contract uses proxy deployment to create ``L2 tokens`` and token ``proxies``. Ensure that the proxy deployment mechanism is secure and properly configured to prevent unauthorized contract creation
- Verify that the ``bridgeMint`` and ``bridgeBurn`` functions in the L2StandardToken are implemented correctly, as they are crucial to deposit and withdrawal functionality.
- The contract maps ``L1 token`` addresses to their ``L2 counterparts``. Ensure that this mapping is accurate and properly maintained to avoid discrepancies between L1 and L2 token addresses
- The ``sendMessageToL1`` function is used to pass messages to the ``L1 chain``. Ensure that this messaging mechanism is secure and that the messages are properly formatted to prevent unexpected behavior.
- Verify that functions that interact with other contracts, such as ``bridgeMint``, ``bridgeBurn``, and ``sendMessageToL1``, are protected against reentrancy attacks

### Compressor

``Systemic Risk``:
- The ``_decodeRawBytecode`` function is responsible for extracting the dictionary and encoded data from raw compressed data. Ensure that this decoding process is correct to prevent any data corruption
- The contract performs various size and length checks, including ``dictionary length``, ``encoded data length``, and ``compressed value length``. Ensure these checks are valid and protect against unexpected data lengths that could lead to out-of-bounds access.
- The ``_verifyValueCompression`` function validates the correctness of value compression given the ``initial value``, ``final value``, and ``operation``. Ensure that this validation process is accurate and prevents ``erroneous compression``

``Cenralization Risks`` :
``publishCompressedBytecode()`` is with Administrator control 

#### L1Messenger

``Systemic Risk``:
- Risks may arise if these external contracts have vulnerabilities or are upgraded without proper validation
- The contract performs various verifications on incoming ``pubdata``, including state diffs, Merkle trees, and more. Any errors or discrepancies in the verification process could lead to incorrect data being sent to L1
- Ensure that the contract doesn't expose itself to ``reentrancy attacks`` and carefully validate data from ``untrusted sources``
-  Large or costly operations may hit the block gas limit, causing transactions to revert
-  Potential issues if there are changes or upgrades to the Ethereum network that may affect contract functionality

``Cenralization Risks`` :
``sendL2ToL1Log()`` is with Administrator control 


## Competition analysis

| Competitor | Description | Advantages | Disadvantages |
|--------- |--------- |--------- |--------- |
|Optimism	| Optimistic rollup |	Fast withdrawals, low fees |	Fraud proofs can be slow |
|Arbitrum |	Optimistic rollup |	Fast withdrawals, low fees|	Fraud proofs can be slow |
|StarkNet |	zk-rollup |	High throughput, low fees |	Limited ecosystem |
|Immutable X |	zk-rollup	|Designed for NFTs, high throughput |	Limited ecosystem |
|Metis |	zk-rollup |	EVM-compatible, low fees |	Limited ecosystem |


### zkSync Era vs. Optimistic rollups

Optimistic rollups assume that transactions are valid and only challenge them if there is evidence of fraud. This allows for fast withdrawals, as transactions can be finalized immediately. However, if a transaction is challenged, it can take several days for the fraud proof to be processed.

zkSync Era does not assume that transactions are valid. Instead, it generates a zk-proof for every transaction. This means that withdrawals are not as fast as with optimistic rollups, as the zk-proof must be generated before the transaction can be finalized. However, zkSync Era is more secure than optimistic rollups, as there is no risk of fraud.

### zkSync Era vs. other zk-rollups

zkSync Era is unique in that it uses a Hyperchains architecture. This allows for different Hyperchains to be customized to meet the needs of different applications. Additionally, Hyperchains can be used to experiment with new features and technologies without affecting the main zkSync Era network.

StarkNet is another promising zk-rollup that is known for its high throughput. StarkNet is able to process a large number of transactions per second, making it a good choice for applications that require high scalability.

Immutable X is a zk-rollup that is designed specifically for NFTs. Immutable X offers a number of features that are beneficial for NFTs, such as gas-free minting and trading.

Metis is a zk-rollup that is EVM-compatible. This means that existing Ethereum applications can be deployed on Metis without any modifications. Metis is a good choice for developers who want to use a zk-rollup without having to learn a new programming language.


## Other Recommendations 

### Test Coverage

The audit scope of the contracts should be the aim of reaching 100% test coverage. However, the tests provided by the protocol are at a high level of comprehension, giving us the opportunity to easily conduct Proof of Concepts (PoCs) to test certain bugs present in the project.

### Suggests to add tests attack vectors, especially re-entrancy

The impact of a reentrancy vulnerability can be severe. An attacker can drain funds from the vulnerable contract, manipulate its state, or even render it unusable. Even reentrancy guard added should be tested 

Attack vectors, especially re-entrancy, seem untested and trusted

### Use More recent version of solidity 

```solidity

pragma solidity ^0.8.0;
pragma solidity ^0.8.13;

```
It's crucial to use consistent and ``up-to-date versions`` to avoid errors, benefit from ``gas optimizations``, and leverage the latest features and improvements. Mixing different versions of Solidity can lead to ``compatibility issues`` and ``unexpected behavior``.

### Use latest version of OpenZeppelin Contracts 

Currently contracts using vulnerable version like V4.8.0. Use atleast V4.9.2 to avoid known bugs 

```
OpenZeppelin Contracts (last updated v4.8.0)

```
### Consider Adding OpenZeppelin access control

OpenZeppelin provides a library for role-based access control, which allows you to manage who can execute specific functions within your smart contract

```solidity
import "@openzeppelin/contracts/access/AccessControl.sol";

```

## New insights and learning from this audit

From this audit, I have learned about the ``Hyperchains architecture``, ``zk rollups``, ``bridges``, ``zk opcodes``, ``the fees structure``, ``the batching process``, ``zero knowledge proofs``, and ``L1toL2 meesagings``.

Overall, I believe that zkSync Era is a promising ``scaling solution`` for Ethereum. The Hyperchains architecture is a novel and flexible approach to scaling, and the zkEVM is a powerful tool that can be used to create secure and scalable smart contracts.

## Time spent
50 hours


















 











    
    
      
     








### Time spent:
50 hours