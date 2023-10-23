## [G-01] Make for loop unchecked
The risk of for loops getting overflowed is extremely low. Because it always increments by 1 and is
limited to the arrays length. Even if the arrays are extremely long, it will take a massive amount of time
and gas to let the for loop overflow.

```
   function _execute(Call[] calldata _calls) internal {
        for (uint256 i = 0; i < _calls.length; ++i) {
            (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
            if (!success) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L224C4-L227C28

```
  for (uint256 i = 0; i < _factoryDeps.length; ++i) {
            require(
                L2ContractHelper.hashL2Bytecode(_factoryDeps[i]) == bytes32(_expectedHashes[i]),
                "Wrong factory dep hash"
            );
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204C7-L208C15

## [G-02] Splitting require() statements that use && saves gas - (saves 8
gas per && )
Instead of using the && operator in a single require statement to check multiple conditions, using
multiple require statements with 1 condition per require statement will save 8 GAS per && .

```
 require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L41C8-L41C108

## [G-03] Caching global variables is expensive than using the variable itself.

```
  address sender = msg.sender;
        if (sender != tx.origin) {
            sender = AddressAliasHelper.applyL1ToL2Alias(msg.sender);
        }
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L247C7-L250C10

```
  uint32 timestamp = uint32(block.timestamp);
            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
                committedBatchTimestamp.set(_newBatchesData[i].batchNumber, timestamp);
            }
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L84C11-L87C14

```
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
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L195C8-L206C11

```
 if (_refundRecipient == address(0)) {
            refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
        }
        txHash = zkSync.requestL2Transaction{value: _amount + msg.value}(
            l2Bridge,
            _amount,
            l2TxCalldata,
            _l2TxGasLimit,
            _l2TxGasPerPubdataByte,
            new bytes[](0),
            refundRecipient
        );
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L182C8-L193C11

```
  timestamps[_id] = block.timestamp + _delay;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L219C7-L219C52

## [G-04] Multiple accesses of a mapping/array should use a storage pointer
Caching a mapping’s value in a storage pointer when the value is accessed multiple times saves ~40
gas per access due to not having to perform the same offset calculation every time. Help the Optimizer
by saving a storage variable’s reference instead of repeatedly fetching it.
To achieve this, declare a storage pointer for the variable and use it instead of repeatedly fetching the
reference in a map or an array. As an example, instead of repeatedly calling
_stakerS1Position[_staker] , save its reference via a storage pointer: uint256[] storage
s1Array = _stakerS1Position[_staker] and use the pointer instead.

```
function diamondCut(DiamondCutData memory _diamondCut) internal {
        FacetCut[] memory facetCuts = _diamondCut.facetCuts;
        address initAddress = _diamondCut.initAddress;
        bytes memory initCalldata = _diamondCut.initCalldata;
        uint256 facetCutsLength = facetCuts.length;
        for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
            Action action = facetCuts[i].action;
            address facet = facetCuts[i].facet;
            bool isFacetFreezable = facetCuts[i].isFreezable;
            bytes4[] memory selectors = facetCuts[i].selectors;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L95C5-L105C1

## [G-05] Refactor  WritePriorityOpParams struct to avoid using unnecessary storage slot
We can use uint32 for time variables instead of uint96/unit64 since it will be enough until the year 2106 but can save a decent amount of gas.

```
 struct WritePriorityOpParams {
        address sender;
        uint256 txId;
        uint256 l2Value;
        address contractAddressL2;
      - uint64 expirationTimestamp; 
	    + unit32 expirationTimestamp;  
        uint256 l2GasLimit;
        uint256 l2GasPrice;
        uint256 l2GasPricePerPubdata;
        uint256 valueToMint;
        address refundRecipient;
    }
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L76C4-L87C6

## [G-06] Unnecessary look up in if condition
If the || condition isn’t required, the second condition will have been looked up unnecessarily.

```
 if (
            _newVerifierParams.recursionNodeLevelVkHash == bytes32(0) ||
            _newVerifierParams.recursionLeafLevelVkHash == bytes32(0) ||
            _newVerifierParams.recursionCircuitsSetVksHash == bytes32(0)
        ) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L128C8-L132C12

## [G-07] It costs more gas to initialize variables to zero than to let the default of zero be applied

```
 uint256 costForPubdata = 0;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L92C8-L92C36

## [G-08] `variable == false` instead of `!variable`.

a bit cheapier when you replace:

```
 if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L277C8-L277C93

```
  if (!success) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L289C11-L289C28

```
    require(!_queue.isEmpty(), "D"); // priority queue is empty
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L65C5-L65C68

```
 require(!_queue.isEmpty(), "s"); // priority queue is empty
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L73C8-L73C68

```
 if (!limitData.depositLimitation) return; // no deposit limitation is placed for this token
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L342C8-L342C100


## [G-09] Use a more recent version of Solidity

Use a Solidity version of at least 0.8.2 to get simple compiler automatic inlining.

Use a Solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads.

Use a Solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings.

Use a Solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.


```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol#L2C1-L2C24

## [G-10] abi.encode() is less efficient than abi.encodePacked()

In terms of efficiency, abi.encodePacked() is generally considered to be more gas-efficient than abi.encode(), because it skips the step of adding function signatures and other metadata to the encoded data. However, this comes at the cost.

```
   abi.encode(
                l2ToL1LogsHash,
                _stateDiffHash,
                _batch.bootloaderHeapInitialContentsHash,
                _batch.eventsQueueStateHash
            );
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L458C10-L463C15

```
  bytes memory encodedTransaction = abi.encode(_l2ProtocolUpgradeTx);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L173C7-L173C76

## [G-11] Events are not indexed

The emitted events are not indexed, making off-chain scripts such as front-ends of dApps difficult to filter the events efficiently.

Recommend adding the `indexed` keyword in each event,

```
 event NewPriorityRequest(
        uint256 txId,
        bytes32 txHash,
        uint64 expirationTimestamp,
        L2CanonicalTransaction transaction,
        bytes[] factoryDeps
    );

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L142C4-L149C1

```
   /// @notice The delay between committing and executing batches is changed.
    event NewExecutionDelay(uint256 _newExecutionDelay);

    /// @notice The validator address is changed.
    event NewValidator(address _oldValidator, address _newValidator);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L26C2-L30C70

```
 event BlocksRevert(uint256 totalBatchesCommitted, uint256 totalBatchesVerified, uint256 totalBatchesExecuted);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L106C4-L106C115

```
 /// @notice Porter availability status changes
    event IsPorterAvailableStatusUpdate(bool isPorterAvailable);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L30C4-L31C65

```
  event NewPriorityTxMaxGasLimit(uint256 oldPriorityTxMaxGasLimit, uint256 newPriorityTxMaxGasLimit);

    /// @notice Emitted when an upgrade is executed.
    event ExecuteUpgrade(Diamond.DiamondCutData diamondCut);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L51C3-L54C61

```
  /// @notice Emitted when the security council address is changed.
    event ChangeSecurityCouncil(address _securityCouncilBefore, address _securityCouncilAfter);

    /// @notice Emitted when the minimum delay for future operations is modified.
    event ChangeMinDelay(uint256 _delayBefore, uint256 _delayAfter);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L73C3-L77C69

```
 /// @notice Verifier parameters changed
    event NewVerifierParams(VerifierParams oldVerifierParams, VerifierParams newVerifierParams);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L57C4-L58C97

## [G-12] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

```
uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L14C1-L14C93

```
uint256 constant MAX_INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE * 4765;

/// @dev Number of bytes in a one repeated storage change
/// @dev Equal to the bytes size of the tuple - (bytes8 key, bytes32 value)
uint256 constant REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE = 40;

/// @dev The maximum length of the bytes array with repeated storage changes
uint256 constant MAX_REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE * 7564;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L26C1-L33C116

## [G-13]  we shold choose less range instead of `uint256` like `uint8` so the best way is to put them in struct. It should be noted that in a struct, uint8 DOES cost less than a traditional uint, because of the tight packing feature. Also be sure that your uints are next to your other uints.

```
/// @dev Offset used to pull Address From Log. Equal to 4 (bytes for isService)
uint256 constant L2_LOG_ADDRESS_OFFSET = 4;

/// @dev Offset used to pull Key From Log. Equal to 4 (bytes for isService) + 20 (bytes for address)
uint256 constant L2_LOG_KEY_OFFSET = 24;

/// @dev Offset used to pull Value From Log. Equal to 4 (bytes for isService) + 20 (bytes for address) + 32 (bytes for key)
uint256 constant L2_LOG_VALUE_OFFSET = 56;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L19C1-L26C43

```
/// @dev Bytes in raw L2 log
/// @dev Equal to the bytes size of the tuple - (uint8 ShardId, bool isService, uint16 txNumberInBatch, address sender,
/// bytes32 key, bytes32 value)
uint256 constant L2_TO_L1_LOG_SERIALIZE_SIZE = 88;

/// @dev The maximum length of the bytes array with L2 -> L1 logs
uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L8C1-L15C1

```
/// @dev Number of bytes in a one initial storage change
/// @dev Equal to the bytes size of the tuple - (bytes32 key, bytes32 value)
uint256 constant INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE = 64;

/// @dev The maximum length of the bytes array with initial storage changes
uint256 constant MAX_INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE * 4765;

/// @dev Number of bytes in a one repeated storage change
/// @dev Equal to the bytes size of the tuple - (bytes8 key, bytes32 value)
uint256 constant REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE = 40;

/// @dev The maximum length of the bytes array with repeated storage changes
uint256 constant MAX_REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE * 7564;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L21C1-L34C1

```
/// @dev Denotes the type of the zkSync transaction that came from L1.
uint256 constant PRIORITY_OPERATION_L2_TX_TYPE = 255;

/// @dev Denotes the type of the zkSync transaction that is used for system upgrades.
uint256 constant SYSTEM_UPGRADE_L2_TX_TYPE = 254;

/// @dev The amount of time in seconds the validator has to process the priority transaction
/// NOTE: The constant is set to zero for the Alpha release period
uint256 constant PRIORITY_EXPIRATION = 0 days;

/// @dev Notice period before activation preparation status of upgrade mode (in seconds)
/// @dev NOTE: we must reserve for users enough time to send full exit operation, wait maximum time for processing this
/// operation and withdraw funds from it.
uint256 constant UPGRADE_NOTICE_PERIOD = $$(defined(UPGRADE_NOTICE_PERIOD) ? UPGRADE_NOTICE_PERIOD : "14 days");

/// @dev Timestamp - seconds since unix epoch
uint256 constant COMMIT_TIMESTAMP_NOT_OLDER = $$(
    defined(COMMIT_TIMESTAMP_NOT_OLDER) ? COMMIT_TIMESTAMP_NOT_OLDER : "365 days"
);

/// @dev Maximum available error between real commit block timestamp and analog used in the verifier (in seconds)
/// @dev Must be used cause miner's `block.timestamp` value can differ on some small value (as we know - 15 seconds)
uint256 constant COMMIT_TIMESTAMP_APPROXIMATION_DELTA = $$(
    defined(COMMIT_TIMESTAMP_APPROXIMATION_DELTA) ? COMMIT_TIMESTAMP_APPROXIMATION_DELTA : "365 days"
);

/// @dev Bit mask to apply for verifier public input before verifying.
uint256 constant INPUT_MASK = $$(~uint256(0) >> 32);

/// @dev The maximum number of L2 gas that a user can request for an L2 transaction
uint256 constant L2_TX_MAX_GAS_LIMIT = $(L2_TX_MAX_GAS_LIMIT);

/// @dev The maximum number of the pubdata an L2 operation should be allowed to use.
uint256 constant MAX_PUBDATA_PER_BATCH = $(MAX_PUBDATA_PER_BATCH);

/// @dev The maximum number of the pubdata an priority operation should be allowed to use.
/// For now, it is somewhat lower than the maximum number of pubdata allowed for an L2 transaction,
/// to ensure that the transaction is definitely processable on L2 despite any potential overhead.
uint256 constant PRIORITY_TX_MAX_PUBDATA = $(PRIORITY_TX_MAX_PUBDATA);

/// @dev The default price per L2 gas to be used for L1->L2 transactions
uint256 constant FAIR_L2_GAS_PRICE = $(FAIR_L2_GAS_PRICE);

/// @dev Even though the price for 1 byte of pubdata is 16 L1 gas, we have a slightly increased
/// value.
uint256 constant L1_GAS_PER_PUBDATA_BYTE = $(L1_GAS_PER_PUBDATA_BYTE);

/// @dev The computational overhead of processing an L2 batch.
uint256 constant BATCH_OVERHEAD_L2_GAS = $(BATCH_OVERHEAD_L2_GAS);

/// @dev The overhead in L1 gas of interacting with the L1
uint256 constant BATCH_OVERHEAD_L1_GAS = $(BATCH_OVERHEAD_L1_GAS);

/// @dev The equivalent in L1 pubdata of L1 gas used for working with L1
uint256 constant BATCH_OVERHEAD_PUBDATA = BATCH_OVERHEAD_L1_GAS / L1_GAS_PER_PUBDATA_BYTE;

/// @dev The maximum number of transactions in L2 batch:
uint256 constant MAX_TRANSACTIONS_IN_BATCH = $(MAX_TRANSACTIONS_IN_BATCH);

/// @dev The size of the bootloader memory dedicated to the encodings of transactions
uint256 constant BOOTLOADER_TX_ENCODING_SPACE = $(BOOTLOADER_TX_ENCODING_SPACE);

/// @dev The intrinsic cost of the L1->l2 transaction in computational L2 gas
uint256 constant L1_TX_INTRINSIC_L2_GAS = $(L1_TX_INTRINSIC_L2_GAS);

/// @dev The intrinsic cost of the L1->l2 transaction in pubdata
uint256 constant L1_TX_INTRINSIC_PUBDATA = $(L1_TX_INTRINSIC_PUBDATA);

/// @dev The minimal base price for L1 transaction
uint256 constant L1_TX_MIN_L2_GAS_BASE = $(L1_TX_MIN_L2_GAS_BASE);

/// @dev The number of L2 gas the transaction starts costing more with each 544 bytes of encoding
uint256 constant L1_TX_DELTA_544_ENCODING_BYTES = $(L1_TX_DELTA_544_ENCODING_BYTES);

/// @dev The number of L2 gas an L1->L2 transaction gains with each new factory dependency
uint256 constant L1_TX_DELTA_FACTORY_DEPS_L2_GAS = $(L1_TX_DELTA_FACTORY_DEPS_L2_GAS);

/// @dev The number of L2 gas an L1->L2 transaction gains with each new factory dependency
uint256 constant L1_TX_DELTA_FACTORY_DEPS_PUBDATA = $(L1_TX_DELTA_FACTORY_DEPS_PUBDATA);

/// @dev The number of pubdata an L1->L2 transaction requires with each new factory dependency
uint256 constant MAX_NEW_FACTORY_DEPS = $(MAX_NEW_FACTORY_DEPS);

/// @dev The L2 gasPricePerPubdata required to be used in bridges.
uint256 constant REQUIRED_L2_GAS_PRICE_PER_PUBDATA = $(REQUIRED_L2_GAS_PRICE_PER_PUBDATA);

/// @dev The mask which should be applied to the packed batch and L2 block timestamp in order
/// to obtain the L2 block timestamp. Applying this mask is equivalent to calculating modulo 2**128
uint256 constant PACKED_L2_BLOCK_TIMESTAMP_MASK = 0xffffffffffffffffffffffffffffffff;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L38C1-L126C86

## [G-14] Use hardcode address instead address(this)
Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.

```
   uint256 balanceBefore = _token.balanceOf(address(this));
        _token.safeTransferFrom(_from, address(this), _amount);
        uint256 balanceAfter = _token.balanceOf(address(this));

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L217C6-L220C1

```
  IERC20(l1WethAddress).safeTransferFrom(msg.sender, address(this), _amount);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L171

```
  require(l1EthReceiver == address(this), "Wrong L1 ETH withdraw receiver");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L289C7-L289C83

```
   AddressAliasHelper.applyL1ToL2Alias(address(this)),
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol#L53C10-L53C64

```
 modifier onlySelf() {
        require(msg.sender == address(this), "Only governance contract itself allowed to call this function");
        _;
    }
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L58C4-L61C6