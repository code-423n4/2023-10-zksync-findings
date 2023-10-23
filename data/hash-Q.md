## Low Risk Issues

### [L-01] Virtual blocks implementation allows virtual blocks to forever lag behind the current block
The enforced condition is that atleast one virtual block must be produced in a batch. Hence if there are multiple blocks in a batch, then the virtual blocks can lag behind the L2 block forever. This will not affect the project unless the operator role is handed over by the team before the migration is complete.
*There are 1 instances of this issue*

```solidity
File: system-contracts/contracts/SystemContext.sol

319:                if (_isFirstInBatch) {
320:                    uint128 currentBatchTimestamp = currentBatchInfo.timestamp;
321:                    require(
322:                        _l2BlockTimestamp >= currentBatchTimestamp,
323:                        "The timestamp of the L2 block must be greater than or equal to the timestamp of the current batch"
324:                    );
325:                    require(_maxVirtualBlocksToCreate > 0, "There must be a virtual block created at the start of the batch");
326:                }

```

*GitHub*: [319](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L319-#L326)


### [L-02] Rounding down may lead to loss for the operator
Since `BATCH_OVERHEAD_PUBDATA` will be rounded down due to precision loss, the BATCH_OVERHEAD_PUBDATA will be slightly lower than the exact. This will mean that unless this precision is adjusted by the other variables, the opeartor can only claim a lower overhead for transaction.
*There are 1 instances of this issue*

```solidity
File: contracts/ethereum/contracts/zksync/Config.sol

92:        uint256 constant BATCH_OVERHEAD_PUBDATA = BATCH_OVERHEAD_L1_GAS / L1_GAS_PER_PUBDATA_BYTE;

```
The pubdata can be rounded up to keep the operator on the better side:
```
uint256 constant BATCH_OVERHEAD_PUBDATA = (BATCH_OVERHEAD_L1_GAS + L1_GAS_PER_PUBDATA_BYTE - 1 ) / L1_GAS_PER_PUBDATA_BYTE;
```

*GitHub*: [92](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L92-#L92)


### [L-03] Incorrect event emitted 
*There are 1 instances of this issue*

```diff
File: contracts/ethereum/contracts/zksync/facets/Admin.sol

43:            function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {
44:                // Save previous value into the stack to put it into the event later
45:                address oldPendingAdmin = s.pendingAdmin;
46:                // Change pending admin
47:                s.pendingAdmin = _newPendingAdmin;
---                emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);
+++                emit NewPendingAdmin(oldPendingAdmin, _newPendingAdmin);
49:            }

```

*GitHub*: [43](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L43-#L49)


### [L-04] Incorrect Comparison 
*There are 1 instances of this issue*

```diff
File: system-contracts/contracts/L1Messenger.sol

206:
---                require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
+++                require(numberOfL2ToL1Logs <= L2_TO_L1_LOGS_MERKLE_TREE_LEAVES, "Too many L2->L1 logs");

```
This can delay the revert of the call which can cause unwanted gas expenditure. 

*GitHub*: [206](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L206-#L206)

### [L-05] User's are not guarenteed to be able to publish GUARANTEED_PUBDATA_PER_TX 
It is mentioned in the document that all transacitons must be able to send atleast GUARANTEED_PUBDATA_PER_TX amount of pubdata:

```
To make common transactions always executable, we must enforce that the users are always able to send at least `GUARANTEED_PUBDATA_PER_TX` bytes of pubdata in their transaction. Because of that, the needed `gas_per_pubdata_limit` for transactions should never grow beyond `MAX_TRANSACTION_GAS_LIMIT/GUARANTEED_PUBDATA_PER_TX`
```
But since there are unavoidable overheads associated with a transaction execution, the user is not guaranteed to be able to publish the specified amount of pubdata which can mislead an user who might think it is possible to publish `GUARANTEED_PUBDATA_PER_TX` amount of pubdata if `MAX_TRANSACTION_GAS_LIMIT` is given as the gas limit. This can result in the user loosing his spent gas for the transaction which would be pretty high for such type of transactions since the gas limit would be `MAX_TRANSACTION_GAS_LIMIT`.

*There are 1 instances of this issue*

```solidity
File: system-contracts/bootloader/bootloader.yul

51:                    /// @dev It should be always possible to submit a transaction 
52:                    /// that consumes such amount of public data.
53:                    function GUARANTEED_PUBDATA_PER_TX() -> ret {
54:                        ret := {{GUARANTEED_PUBDATA_BYTES}}
55:                    }

```

*GitHub*: [51](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L51-#L55)

### [L-06] Users may be misguided by the expiration timestamp of L1->L2 opeartions 
It is mentioned that a priority operation must be satisfied before the expiration timestamp. But this is not enforced anywhere in code except the order of the priority transactions should be maintained.
```solidity
/// @param expirationTimestamp Expiration timestamp for this request (must be satisfied before)
/// @param layer2Tip Additional payment to the validator as an incentive to perform the operation
struct PriorityOperation {
    bytes32 canonicalTxHash;
    uint64 expirationTimestamp;
    uint192 layer2Tip;
}
```
Hence an operator can execute the priority operation at any time and this might change user's assumptions. 

*GitHub*: [8](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L8-L14)


## Non-critical Issues


### [NC-01] Update comment to improvise the grammar
*There are 29 instances of this issue*

<details>
<summary>see instances</summary>

```diff
File: contracts/ethereum/contracts/zksync/facets/Getters.sol

77:
---             /// @return Whether the address has a validator access
+++             /// @return Whether the address has validator access
  
```

*GitHub*: [77](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L77-#L77)
```diff
File: contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

131:
---            /// Note: it is called the zkSync contract, not delegatecalled!
+++            /// Note: it has called the zkSync contract, not delegatecalled! 
  
```

*GitHub*: [131](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L131-#L131)
```diff
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

26:
---        /// @dev It is standard implementation of ERC20 Bridge that can be used as a reference
+++        /// @dev It is a standard implementation of ERC20 Bridge that can be used as a reference
27:        /// for any other custom token bridges.

```

*GitHub*: [26](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L26-#L27)
```diff
File: contracts/ethereum/contracts/common/AllowList.sol

38:
---            /// @param _functionSig The function signature (selector), access to which need to check
+++            /// @param _functionSig The function signature (selector), access to which needs to be checked
  
```

*GitHub*: [38](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L38-#L38)
```diff
File: system-contracts/bootloader/bootloader.yul

1371:
---                        // For bytecodes published in the previous step, no need pubdata will have to be published  
+++                        // For bytecodes published in the previous step, no need to publish pubdata

1954:
---                    /// @dev Checks whether an address is an EOA (i.e. has not code deployed on it) 
+++                    /// @dev Checks whether an address is an EOA (i.e. has no code deployed on it)

2229:
---                        // The Solidity always pads such value to 32 bytes and so we expect the magic to be
+++                        // Solidity always pads such value to 32 bytes and so we expect the magic to be
  
```

*GitHub*: [1371](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1371-#L1371), [1954](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1954-#L1954), [2229](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L2229-#L2229)
```diff
File: system-contracts/contracts/libraries/TransactionHelper.sol

68:
---            // Reserved dynamic type for the future use-case. Using it should be avoided,
+++            // Reserved dynamic type for future use-case. Using it should be avoided,

148:
---                // Hash of legacy transactions are encoded as one of the:
+++                // Hash of legacy transactions are encoded as one of these/ the following:

386:
---                    // Do nothing. general(bytes) paymaster flow means that the paymaster must interpret these bytes on his own. 
+++                    // Do nothing. general(bytes) paymaster flow means that the paymaster must interpret these bytes on its own
  
```

*GitHub*: [68](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L68-#L68), [148](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L148-#L148), [386](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L386-#L386)
```diff
File: system-contracts/contracts/BootloaderUtilities.sol

45:                
---                // Hash of legacy transactions are encoded as one of the:
+++                // Hash of legacy transactions are encoded as one of these/ the following:

```

*GitHub*: [45](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L45-#L45)
```diff
File: system-contracts/contracts/ContractDeployer.sol

142:
---            /// It is not used anywhere and it needed simply for the consistency for the compiler 
+++            /// It is not used anywhere and is needed simply for the consistency for the compiler

181:
---            /// It is not used anywhere and it needed simply for the consistency for the compiler 
+++            /// It is not used anywhere and is needed simply for the consistency for the compiler

```

*GitHub*: [142](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L142-#L142), [181](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L181-#L181)
```diff
File: system-contracts/contracts/libraries/SystemContractsCaller.sol

48:
---        /// @notice The way to forward the calldata:
+++        /// @notice Ways to forward the calldata:
49:        /// - Use the current heap (i.e. the same as on EVM).
50:        /// - Use the auxiliary heap.
51:        /// - Forward via a pointer

```

*GitHub*: [48](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L48-#L51)
```diff
File: system-contracts/contracts/Compressor.sol

91:
---            /// @return stateDiffHash Hash of the encoded (uncompressed) state diffs to be committed to via system log.
+++            /// @return stateDiffHash Hash of the encoded (uncompressed) state diffs to be committed via system log / to L1 via system log.

```

*GitHub*: [91](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L91-#L91)
```diff
File: system-contracts/contracts/DefaultAccount.sol

96:
---                // The fact there is are enough balance for the account
+++                // The fact that there is enough balance for the account
97:                // should be checked explicitly to prevent user paying for fee for a
98:                // transaction that wouldn't be included on Ethereum.


143:                    // Check that called function is the deployment method,
144:
---                    // the others deployer method is not supposed to be called from the default account.
+++                    // the other deployer methods are not supposed to be called from the default account.
145:                    isSystemCall =
146:                        selector == DEPLOYER_SYSTEM_CONTRACT.create.selector ||
147:                        selector == DEPLOYER_SYSTEM_CONTRACT.create2.selector ||
148:                        selector == DEPLOYER_SYSTEM_CONTRACT.createAccount.selector ||
149:                        selector == DEPLOYER_SYSTEM_CONTRACT.create2Account.selector;
150:                }

```

*GitHub*: [96](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L96-#L98), [143](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L143-#L150)
```diff
File: system-contracts/contracts/NonceHolder.sol

18:
---         * The users can either marked a range of nonces by increasing the `minNonce`. This way all the nonces 
+++         * The users can mark a range of nonces by increasing the `minNonce`. This way all the nonces

29:
---            /// The minNonce can be increased by at 2^32 at a time to prevent it from
+++            /// The minNonce can be increased by at most 2^32 at a time to prevent it from

```

*GitHub*: [18](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L18-#L18), [29](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L29-#L29)
```diff
File: system-contracts/contracts/libraries/RLPEncoder.sol

47:
---            /// @param _len The length of the bytes to encode. It has a `uint64` type since as larger values are not supported.
+++            /// @param _len The length of the bytes to encode. It has a `uint64` type since larger values are not supported.

55:
---            /// @param _len The length of the bytes to encode. It has a `uint64` type since as larger values are not supported.
+++            /// @param _len The length of the bytes to encode. It has a `uint64` type since larger values are not supported.

```

*GitHub*: [47](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L47-#L47), [55](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L55-#L55)
```diff
File: system-contracts/contracts/L2EthToken.sol

95:
---            /// @dev The function burn the sent `msg.value`.
+++            /// @dev The function burns the sent `msg.value`.
96:            /// NOTE: Since this contract holds the mapping of all ether balances of the system,
97:            /// the sent `msg.value` is added to the `this` balance before the call.
98:            /// So the balance of `address(this)` is always bigger or equal to the `msg.value`!
99:            function _burnMsgValue() internal returns (uint256 amount) {

```

*GitHub*: [95](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L95-#L99)
```diff
File: system-contracts/contracts/AccountCodeStorage.sol

33:            /// @dev This method trusts the ContractDeployer to make sure that the bytecode is known and well-formed,
34:
---            /// but checks whether the bytecode hash corresponds to the constructing smart contract.
+++            /// but checks whether the bytecode hash corresponds to a constructing smart contract.
35:            function storeAccountConstructingCodeHash(address _address, bytes32 _hash) external override onlyDeployer {
36:                // Check that code hash corresponds to the deploying smart contract
37:                require(Utils.isContractConstructing(_hash), "Code hash is not for a contract on constructor");
38:                _storeCodeHash(_address, _hash);
39:            }


44:            /// @dev This method trusts the ContractDeployer to make sure that the bytecode is known and well-formed,
45:
---            /// but checks whether the bytecode hash corresponds to the constructed smart contract.
+++            /// but checks whether the bytecode hash corresponds to a constructed smart contract.
46:            function storeAccountConstructedCodeHash(address _address, bytes32 _hash) external override onlyDeployer {
47:                // Check that code hash corresponds to the deploying smart contract
48:                require(Utils.isContractConstructed(_hash), "Code hash is not for a constructed contract");
49:                _storeCodeHash(_address, _hash);

```

*GitHub*: [33](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L33-#L39), [44](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L44-#L49)
```diff
File: system-contracts/contracts/libraries/Utils.sol

50:
---            /// @notice Denotes whether bytecode hash corresponds to a contract that already constructed
+++            /// @notice Denotes whether bytecode hash corresponds to a contract that is already constructed

```

*GitHub*: [50](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L50-#L50)
```diff
File: system-contracts/contracts/MsgValueSimulator.sol

21:
---            /// @dev The contract accepts value, the callee and whether the call should a system one via its ABI params.
+++            /// @dev The contract accepts value, the callee and whether the call should be a system one via its ABI params.
  
```

*GitHub*: [21](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L21-#L21)
```diff
File: system-contracts/contracts/interfaces/ISystemContract.sol

11:
---        /// system contracts rely on this abstract contract as on interface!
+++        /// system contracts rely on this abstract contract as an interface!

```

*GitHub*: [11](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/ISystemContract.sol#L11-#L11)
```diff
File: system-contracts/contracts/ImmutableSimulator.sol

12:
---         * @dev The contract stores the immutable variables created during deployment by other contracts on his storage.
+++         * @dev The contract stores the immutable variables created during deployment by other contracts on its storage.
  
```

*GitHub*: [12](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L12-#L12)
```diff
File: contracts/zksync/contracts/bridge/L2StandardERC20.sol

66:
---                // - Decode them manually, i.e. write a function that will validate that data in the correct format
+++                // - Decode them manually, i.e. write a function that will validate that data is in the correct format

```

*GitHub*: [66](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L66-#L66)


</details>

### [NC-02] Update comment to provide clearer meaning
*There are 6 instances of this issue*

```diff
File: system-contracts/bootloader/bootloader.yul

860:                    /// @param gasPerPubdata The price per pubdata to be used
861:                    /// @param isPriorityOp Whether the transaction is a priority one
862:
---                    /// should be stored.
863:                    function processL1Tx(
864:                        txDataOffset,
865:                        resultPtr,
866:                        transactionIndex,
867:                        gasPerPubdata,
868:                        isPriorityOp
869:                    ) {


1233:
---                    /// @return ergsSpentOnExecute The ergs spent on the transaction execution. 
+++                    /// @return gasSpentOnExecute The gas spent on the transaction execution.

1562:
---                        // Note, that it is safe because the
1563:                        let success := call(
1564:                            gas(),
1565:                            BYTECODE_COMPRESSOR_ADDR(),
1566:                            0,
1567:                            calldataPtr,
1568:                            totalLen,
1569:                            0,
1570:                            32
1571:                        )

```

*GitHub*: [860](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L860-#L869), [1233](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1233-#L1233), [1562](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1562-#L1571)
```diff
File: system-contracts/contracts/SystemContext.sol

423:
---                require(previousBatchNumber + 1 == _expectedNewNumber, "The provided block number is not correct");
+++                require(previousBatchNumber + 1 == _expectedNewNumber, "The provided batch number is not correct");

429:
---                // Setting new block number and timestamp
+++                // Setting new batch number and timestamp
430:                BlockInfo memory newBlockInfo = BlockInfo({number: previousBatchNumber + 1, timestamp: _newTimestamp}); 
431:        
432:                currentBatchInfo = newBlockInfo;

```

*GitHub*: [423](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L423-#L423), [429](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L429-#L432)
```diff
File: system-contracts/contracts/ContractDeployer.sol

307:
---            /// @notice Ensures that the _newAddress and assigns a new contract hash to it

```

*GitHub*: [307](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L307-#L307)


### [NC-03] Update namings to provide a clearer meaning
*There are 3 instances of this issue*

```solidity
File: system-contracts/bootloader/bootloader.yul

// @audit it calls the markFactoryDeps() function while the contract has another funciton named markBytecodeAsPublished. Hence consider renaming the variable to match with markFactoryDeps.
2267:                        mstore(ptr, {{MARK_BATCH_AS_REPUBLISHED_SELECTOR}}) 

// @audit the txPtr points to the Transaction’s meta descriptions while the currentExpectedTxOffset points to the actual transaction data. Consider renaming txPtr to indicate that it points to the transactions metadata.
3701:                    let currentExpectedTxOffset := add(TXS_IN_BATCH_LAST_PTR(), mul(MAX_POSTOP_SLOTS(), 32))
3702:        
3703:                    let txPtr := TX_DESCRIPTION_BEGIN_BYTE() 

```

*GitHub*: [2267](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L2267-#L2267), [3701](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L3701-#L3703)
```solidity
File: system-contracts/contracts/SystemContext.sol

@audit the newBlockInfo is actually storing the newBatchInfo. Hence consider renaming to newBatchInfo
430:                BlockInfo memory newBlockInfo = BlockInfo({number: previousBatchNumber + 1, timestamp: _newTimestamp});
431:        
432:                currentBatchInfo = newBlockInfo;

```

*GitHub*: [430](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L430-#L432)


### [NC-04] Differing from documentation
*There are 4 instances of this issue*

In the documentation there is a gap between [transactions metadescriptions](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md#transactions-meta-descriptions) and the paymasters postOp operations while in implementation it is continuous. 
```solidity
File: contracts/ethereum/contracts/zksync/Config.sol

3701:                 let currentExpectedTxOffset := add(TXS_IN_BATCH_LAST_PTR(), mul(MAX_POSTOP_SLOTS(), 32))

```

*GitHub*: [98](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L98-#L98)
```solidity
File: system-contracts/bootloader/bootloader.yul

/*
* In documentation, the rolling hash is stored in the second byte and the number of opeartions is stored in the first byte
* https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Handling%20L1%E2%86%92L2%20ops%20on%20zkSync.md#bootloader
* It is reversed in implementation
*/
3709:                    mstore(PRIORITY_TXS_L1_DATA_BEGIN_BYTE(), EMPTY_STRING_KECCAK())
3710:                    mstore(add(PRIORITY_TXS_L1_DATA_BEGIN_BYTE(), 32), 0)

/*
* @audit in documentation the offset occupies the first slot and the metaInfo occupies the second part
* (https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md#transactions-meta-descriptions)
* while it is reversed in implementation
*/   
3732:                        let txDataOffset := mload(add(txPtr, 32)) 

```

*GitHub*: [291](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L291-#L291)
```solidity
File: system-contracts/contracts/MsgValueSimulator.sol

/*
* @audit It is mentioned that the firstAbi param will contatin both value and the system flag.
* But in the implementation, the system flag is stored seperately in the 3rd register
*/
22:            /// @dev The first ABI param contains the value in the [0..127] bits. The 128th contains
23:            /// the flag whether or not the call should be a system one.
24:            /// The second ABI params contains the callee. 
25:            function _getAbiParams() internal view returns (uint256 value, bool isSystemCall, address to) {
26:                value = SystemContractHelper.getExtraAbiData(0);
27:                uint256 addressAsUint = SystemContractHelper.getExtraAbiData(1);
28:                uint256 mask = SystemContractHelper.getExtraAbiData(2);
29:        
30:                isSystemCall = (mask & MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT) != 0;

```

*GitHub*: [22](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L22-#L30)


### [NC-05] Comment provided is wrong
*There are 12 instances of this issue*

<details>
<summary>see instances</summary>

```diff
File: contracts/ethereum/contracts/zksync/facets/Executor.sol

89:
---                // All L2 blocks have timestamps within the range of [batchTimestamp, lastL2BatchTimestamp].
+++                // All L2 blocks have timestamps within the range of [batchTimestamp, lastL2BlockTimestamp].

```

*GitHub*: [418](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L418-#L418)
```diff
File: contracts/ethereum/contracts/zksync/Config.sol

115:
---        /// @dev The number of L2 gas an L1->L2 transaction gains with each new factory dependency
+++        /// @dev The number of L2 pubdata an L1->L2 transaction gains with each new factory dependency
116:        uint256 constant L1_TX_DELTA_FACTORY_DEPS_PUBDATA = $(L1_TX_DELTA_FACTORY_DEPS_PUBDATA);

  
118:
---        /// @dev The number of pubdata an L1->L2 transaction requires with each new factory dependency
+++        /// @dev The maximum number factory dependencies
119:        uint256 constant MAX_NEW_FACTORY_DEPS = $(MAX_NEW_FACTORY_DEPS);

```

*GitHub*: [115](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L115-#L116), [118](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L118-#L119)
```diff
File: contracts/ethereum/contracts/governance/Governance.sol

190:                // Ensure that the operation is in a pending state before proceeding.
191:                require(isOperationPending(id), "Operation must be pending before execution");
192:                // Execute operation.
193:                _execute(_operation.calls);
194:
---                // Reconfirming that the operation is still pending before execution.
+++                // Reconfirming that the operation is still pending after execution.
195:                // This is needed to avoid unexpected reentrancy attacks of re-executing the same operation.
196:                require(isOperationPending(id), "Operation must be pending after execution");

```

*GitHub*: [190](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L190-#L196)
```diff
File: system-contracts/bootloader/bootloader.yul

2063:
---                            64 // The output has size of 32 (signed tx hash and explorer tx hash are expected)
+++                            64 // The output has size of 64 (signed tx hash and explorer tx hash are expected)

3538:
---                    /// @notice The id of the VM hook that notifies the operator that the transaction's execution has started.
+++                    /// @notice The id of the VM hook that notifies the operator that the transaction's execution has ended.
3539:                    function VM_HOOK_TX_HAS_ENDED() -> ret {
3540:                        ret := 4
3541:                    }

3792:
---                    // Transfering all the ETH received in the block to the operator
+++                    // Transfering all the ETH received in the batch to the operator
  
```

*GitHub*: [2063](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L2063-#L2063), [3538](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L3538-#L3541), [3792](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L3792-#L3792)
```diff
File: system-contracts/contracts/libraries/TransactionHelper.sol

19:
---        /// @dev The type id of legacy transactions. 
+++        /// @dev The type id of access list transactions.
20:        uint8 constant EIP_2930_TX_TYPE = 0x01;

```

*GitHub*: [19](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L19-#L20)
```diff
File: system-contracts/contracts/ContractDeployer.sol

160:            /// Note: this method may be callable only in system mode,
161:
---            /// that is checked in the `createAccount` by `onlySystemCall` modifier.
+++            /// that is checked in the `create2Account` by `onlySystemCall` modifier.
162:            function create2Account(
163:                bytes32 _salt,
164:                bytes32 _bytecodeHash,
165:                bytes calldata _input,
166:                AccountAbstractionVersion _aaVersion
167:            ) public payable override onlySystemCall returns (address) {

```

*GitHub*: [160](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L160-#L167)
```diff
File: system-contracts/contracts/AccountCodeStorage.sol

17:
---         * @dev The length of each bytecode MUST be odd.  It's internal code format requirements, due to padding of SHA256 function.
+++         * @dev The word length of each bytecode MUST be odd.  It's internal code format requirements, due to padding of SHA256 function.
  
```

*GitHub*: [17](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L17-#L17)
```diff
File: system-contracts/contracts/libraries/Utils.sol

55:
---            /// @notice Denotes whether bytecode hash corresponds to a contract that is on constructor or has already been constructed
+++            /// @notice Denotes whether bytecode hash corresponds to a contract that is on constructor
56:            function isContractConstructing(bytes32 _bytecodeHash) internal pure returns (bool) {
57:                return _bytecodeHash[1] == 0x01;
58:            }

```

*GitHub*: [55](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L55-#L58)
```diff
File: contracts/zksync/contracts/bridge/L2ERC20Bridge.sol

59:
---            /// @param _l2Receiver The account address that would receive minted ether
+++            /// @param _l2Receiver The account address that would receive minted token

```

*GitHub*: [59](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L59-#L59)


</details>

### [NC-06] Typos
*There are 16 instances of this issue*

<details>
<summary>see instances</summary>

```diff
File: contracts/ethereum/contracts/zksync/facets/Mailbox.sol

254:                // VERY IMPORTANT: nobody should rely on this constant to be fixed and every contract should give their users the ability to provide the
255:
---                // ability to provide `_l2GasPerPubdataByteLimit` for each independent transaction.
+++                // `_l2GasPerPubdataByteLimit` for each independent transaction.
  
```

*GitHub*: [254](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L254-#L255)
```diff
File: contracts/ethereum/contracts/zksync/facets/Getters.sol

12:
---        /// @title Getters Contract implements functions for getting contract state from outside the batchchain.
+++        /// @title Getters Contract implements functions for getting contract state from outside the blockchain.

```

*GitHub*: [12](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L12-#L12)
```diff
File: contracts/ethereum/contracts/zksync/interfaces/ILegacyGetters.sol

10:
---        /// but still are keot for backward compatibility.
+++        /// but still are kept for backward compatibility.

```

*GitHub*: [10](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/ILegacyGetters.sol#L10-#L10)
```diff
File: contracts/ethereum/contracts/governance/Governance.sol

135:
---            /// @notice Propose "shadow" upgrade, upgrade data is not publishing on-chain.
+++            /// @notice Propose "shadow" upgrade, upgrade data is not published on-chain.

228:
---                        // Propage an error if the call fails.
+++                        // Propagate an error if the call fails.

```

*GitHub*: [135](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L135-#L135), [228](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L228-#L228)
```diff
File: system-contracts/bootloader/bootloader.yul

1357:
---                                // Here we are making sure that the bytecode is indeed not yet know and needs to be published,
+++                                // Here we are making sure that the bytecode is indeed not yet known and needs to be published,

1358:
---                                // preveting users from being overcharged by the operator.
+++                                // preventing users from being overcharged by the operator.

2681:
---                                // For L2 transactions, we use near call panic, it will triger the validation 
+++                                // For L2 transactions, we use near call panic, it will trigger the validation 

```

*GitHub*: [1357](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1357-#L1357), [1358](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1358-#L1358), [2681](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L2681-#L2681)
```diff
File: system-contracts/contracts/libraries/TransactionHelper.sol

176:                        // If input is a byte in [0x80, 0xff] range, RLP encoding will concatenates 0x81 with the byte. 


254:                        // If input is a byte in [0x80, 0xff] range, RLP encoding will concatenates 0x81 with the byte. 


326:                        // If input is a byte in [0x80, 0xff] range, RLP encoding will concatenates 0x81 with the byte. 

+++                         concatenates => concatenate

379:                        // Some tokens, e.g. USDT require that the allowance is firsty set to zero
+++                           firsty => firstly

```

*GitHub*: [176](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L176-#L176), [254](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L254-#L254), [326](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L326-#L326), [379](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L379-#L379)
```diff
File: system-contracts/contracts/BootloaderUtilities.sol

73:                        // If input is a byte in [0x80, 0xff] range, RLP encoding will concatenates 0x81 with the byte. 


169:                        // If input is a byte in [0x80, 0xff] range, RLP encoding will concatenates 0x81 with the byte. 


264:                        // If input is a byte in [0x80, 0xff] range, RLP encoding will concatenates 0x81 with the byte. 
  
+++                         concatenates => concatenate

```

*GitHub*: [73](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L73-#L73), [169](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L169-#L169), [264](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L264-#L264)
```diff
File: system-contracts/contracts/libraries/SystemContractHelper.sol

347:                // When the system call is passed, the 2-bit it set to 1
+++                 2-bit it => 2-bit is
  

```

*GitHub*: [347](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L347-#L347)


</details>

### [NC-07] Unused Variables
*There are 2 instances of this issue*

```solidity
File: system-contracts/bootloader/bootloader.yul

1296:                        let innerTxDataOffset := add(txDataOffset, 32) // mns-unused-vars

```

*GitHub*: [1296](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1296-#L1296)
```solidity
File: system-contracts/contracts/SystemContext.sol

384:                bytes32 prevBatchHash = batchHash[currentBatchNumber - 1]; // mns-unused-vars

```

*GitHub*: [384](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L384-#L384)