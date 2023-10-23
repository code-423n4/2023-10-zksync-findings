|     |     |
| --- | --- |
| ID  | Description |
| \[G-01\] | Use assembly to validate `msg.sender` |
| \[G-02\] | Use hardcode address instead `address(this)` |
| \[G‑03\] | Simple checks for zero can be done using assembly to save gas |
| \[G-04\] | Empty Blocks Should Be Removed Or Emit Something |
| \[G-05\] | you can save storage by reordering Holding struct fields in the following way. |
| \[G-06\] | Make 3 event parameters indexed when possible |
| \[G-07\] | Events are not indexed |
| \[G-08\] | use Mappings Instead of Arrays |
| \[G-09\] | Do not calculate constants |
| \[G-10\] | Use bytes32 rather than string/bytes. |
| \[G-11\] | Using storage instead of memory for structs/arrays saves gas |
| \[G-12\] | Return values from external calls can be cached to avoid unnecessary call |
| **\[Gas-13\]** | Compute known value `off-chain` |
| \[G-14\] | There is no need to initialize variables with default values |
| \[G-15\] | uint8 is not always cheaper than uint256 |
| \[G-16\] | Upgrade Solidity’s optimizer |
| \[G-17\] | Use Short Circuiting rules to your advantage. |
| \[G-18\] | Make fewer external calls. |
| \[ 0 \] | CONCLUSION |

## \[G-01\] Use assembly to validate `msg.sender`

We can use assembly to efficiently validate msg.sender for the didPay and uniswapV3SwapCallback functions with the least amount of opcodes necessary. Additionally, we can use xor() instead of iszero(eq()), saving 3 gas. We can also potentially save gas on the unhappy path by using scratch space to store the error selector, potentially avoiding memory expansion costs.

```
66: require(msg.sender == validator, "8h");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L66

```
311:   require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L311

```
196:  refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L196

```
183:   refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L183

```
58: modifier onlySelf() {
        require(msg.sender == address(this), "Only governance contract itself allowed to call this function");
        _;
    }


    /// @notice Checks that the message sender is an active security council.
64:  modifier onlySecurityCouncil() {
        require(msg.sender == securityCouncil, "Only security council allowed to call this function");
        _;
    }


    /// @notice Checks that the message sender is an active owner or an active security council.
70:  modifier onlyOwnerOrSecurityCouncil() {
        require(
            msg.sender == owner() || msg.sender == securityCouncil,
            "Only the owner and security council are allowed to call this function"
        );
        _;
    }
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L58C1-L77C1

## \[G-02\] Use hardcode address instead `address(this)`

Instead of using `address(this)`, it is more gas-efficient to pre-calculate and use the hardcoded `address`. Foundry’s script.sol and solmate’s `LibRlp.sol` contracts can help achieve this.

References: <ins>https://book.getfoundry.sh/reference/forge-std/compute-create-address</ins>

<ins>https://twitter.com/transmissions11/status/1518507047943245824</ins>

```
59:    require(msg.sender == address(this), "Only governance contract itself allowed to call this function");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L59

```
217:     uint256 balanceBefore = _token.balanceOf(address(this));
218:    _token.safeTransferFrom(_from, address(this), _amount);
219:    uint256 balanceAfter = _token.balanceOf(address(this));
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L217C9-L219C64

```
116:   (address(this), l1WethAddress, _l2WethAddress)
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L116

```
53:     AddressAliasHelper.applyL1ToL2Alias(address(this)),
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol#L53

```
13:   require(_allowList.canCall(msg.sender, address(this), msg.sig), "nr");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L13

## \[G‑03\] Simple checks for zero can be done using assembly to save gas

Note: missed from bots

```
310: address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L310

## \[G-04\] Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){…}else{…} => if(!x){if(y){…}else{…}})

```
/// @notice Placeholder function for custom logic for upgrading L1 contract.
    /// Typically this function will never be used.  @audit 
    /// @param _customCallDataForUpgrade Custom data for an upgrade, which may be interpreted differently for each
    /// upgrade.
    function _upgradeL1Contract(bytes calldata _customCallDataForUpgrade) internal virtual {}

    /// @notice placeholder function for custom logic for post-upgrade logic.
    /// Typically this function will never be used.   @audit
    /// @param _customCallDataForUpgrade Custom data for an upgrade, which may be interpreted differently for each
    /// upgrade.
    function _postUpgrade(bytes calldata _customCallDataForUpgrade) internal virtual {}
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L11C3-L21C88

## \[G-05\] you can save storage by reordering Holding struct fields in the following way.

Note: missed from bots

```
28: struct UpgradeStorage {
    bytes32 proposedUpgradeHash;
    address securityCouncil;
    uint40 proposedUpgradeTimestamp;
    uint40 currentProposalId;
    bool approvedBySecurityCouncil;
    UpgradeState state; 
}
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L28

```
47: struct L2Log {
    bytes32 key;
    bytes32 value;
    address sender;
    uint16 txNumberInBatch;
    uint8 l2ShardId;
    bool isService;
}
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L47

## \[G-06\] Make 3 event parameters indexed when possible

it’s the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

events are used to emit information about state changes in a contract. When defining an event, it's important to consider the gas cost of emitting the event, as well as the efficiency of searching for events in the Ethereum blockchain.

Making event parameters indexed can help reduce the gas cost of emitting events and improve the efficiency of searching for events. When an event parameter is marked as indexed, its value is stored in a separate data structure called the event topic, which allows for more efficient searching of events.

[Reference](https://code4rena.com/reports/2023-01-drips#g-05-make-3-event-parameters-indexed-when-possible)

```
41: event EthReceived(uint256 amount);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L41

```
153:     event EthWithdrawalFinalized(address indexed to, uint256 amount);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L153

```
event NewExecutionDelay(uint256 _newExecutionDelay);

    /// @notice The validator address is changed.
    event NewValidator(address _oldValidator, address _newValidator);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L27C5-L30C70

```
event IsPorterAvailableStatusUpdate(bool isPorterAvailable);

    /// @notice Validator's status changed
    event ValidatorStatusUpdate(address indexed validatorAddress, bool isActive);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L31C4-L34C82

```
event NewPriorityTxMaxGasLimit(uint256 oldPriorityTxMaxGasLimit, uint256 newPriorityTxMaxGasLimit);

    /// @notice Emitted when an upgrade is executed.
    event ExecuteUpgrade(Diamond.DiamondCutData diamondCut);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L51C1-L54C61

```
7:  event DepositInitiated(
        bytes32 indexed l2DepositTxHash,
        address indexed from,
        address indexed to,
        address l1Token,
        uint256 amount
    );
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L7

```
event ChangeMinDelay(uint256 _delayBefore, uint256 _delayAfter);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L77

```
event NewVerifierParams(VerifierParams oldVerifierParams, VerifierParams newVerifierParams);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L58

## \[G-07\] Events are not indexed

The emitted events are not indexed, making off-chain scripts such as front-ends of dApps difficult to filter the events efficiently.

Recommend adding the `indexed` keyword in each event,

```
106:     event BlocksRevert(uint256 totalBatchesCommitted, uint256 totalBatchesVerified, uint256 totalBatchesExecuted);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L106

```
event NewExecutionDelay(uint256 _newExecutionDelay);

    /// @notice The validator address is changed.
    event NewValidator(address _oldValidator, address _newValidator);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L27C5-L30C70

```
event IsPorterAvailableStatusUpdate(bool isPorterAvailable);

    /// @notice Validator's status changed
    event ValidatorStatusUpdate(address indexed validatorAddress, bool isActive);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L31C4-L34C82

```
event NewPriorityTxMaxGasLimit(uint256 oldPriorityTxMaxGasLimit, uint256 newPriorityTxMaxGasLimit);

    /// @notice Emitted when an upgrade is executed.
    event ExecuteUpgrade(Diamond.DiamondCutData diamondCut);
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L51C1-L54C61\[\](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L7)](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L51C1-L54C61%5B%5D%28https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L7%29 "https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L51C1-L54C61%5B%5D(https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L7)")

```
event ChangeMinDelay(uint256 _delayBefore, uint256 _delayAfter);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L77

## \[G-08\] use Mappings Instead of Arrays

Mappings, are useful when you need to store and access data based on a key, rather than an index. Mappings have a lower gas cost for read and write operations, especially when the size of the mapping is large, since Solidity can perform these operations based on the key directly, without needing to iterate over the entire data structure.

```
81:     uint256[7] __DEPRECATED_diamondCutStorage;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L81

```
39:   bytes4[] selectors;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L39

```
52:  address[] facets;

65: bytes4[] selectors;

73: FacetCut[] facetCuts;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L52

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L65

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L73C9-L73C30

```
57: uint256[] factoryDeps;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L57

```
75:     uint256[] recursiveAggregationInput;
76:     uint256[] serializedProof;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L75

```
69: bytes4[] selectors;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L69

## \[G-09\] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

```
87: assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L87

## \[G-10\] Use bytes32 rather than string/bytes.

If you can fit your data in 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is much cheaper in solidity. Basically, Any fixed size variable in solidity is cheaper than variable size.

```
41: string public constant override getName = "MailboxFacet";
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41

```
19: string public constant override getName = "GettersFacet";
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19

```
24:     string public constant override getName = "ValidatorTimelock";
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24

```
15:     string public constant override getName = "AdminFacet";
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15

## \[G-11\] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

```
File:	ethereum/contracts/zksync/facets/Executor.sol


324: uint256[] memory proofPublicInput = new uint256[](committedBatchesLength);
```

## \[G-12\] Return values from external calls can be cached to avoid unnecessary call

External calls are expensive as they use the `STATICCALL`/`CALL` opcode (~100 gas). If you are calling the same external function more than once you should cache the return value to avoid an unnecessary `STATICCALL`/`CALL`.

```
56:   function getTotalPriorityTxs() external view returns (uint256) {
        return s.priorityQueue.getTotalPriorityTxs();
    }

    /// @notice Returns zero if and only if no operations were processed from the queue
    /// @notice Reverts if there are no unprocessed priority transactions
    /// @return Index of the oldest priority operation that wasn't processed yet
    function getFirstUnprocessedPriorityTx() external view returns (uint256) {
        return s.priorityQueue.getFirstUnprocessedPriorityTx();
    }

    /// @return The number of priority operations currently in the queue
    function getPriorityQueueSize() external view returns (uint256) {
        return s.priorityQueue.getSize();
    }

    /// @return The first unprocessed priority operation from the queue
    function priorityQueueFrontOperation() external view returns (PriorityOperation memory) {
        return s.priorityQueue.front();
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L56C3-L74C40

```
71: function getCommittedBatchTimestamp(uint256 _l2BatchNumber) external view returns (uint256) {
        return committedBatchTimestamp.get(_l2BatchNumber);
    }
    
96:   function revertBatches(uint256) external onlyValidator {
        _propagateToZkSync();
    }
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L71

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L96

## **\[Gas-13\]** Compute known value `off-chain`

If You know what data to hash, there is no need to consume more computational power to hash it using keccak256 , you’ll end up consuming 2x amount of gas.

```
12:    bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L12

## \[G-14\]  There is no need to initialize variables with default values

If the variable is not set/initialized, it is assumed to have a default value (0, false, 0x0, etc., depending on the data type). If you explicitly initialize it with its default value, you are just wasting gas.

```
uint256 foo = 0;//bad, expensive
uint256 bar;//good, cheap
```

```
92:   uint256 costForPubdata = 0;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L92

```
File:	ethereum/contracts/zksync/facets/Executor.sol

123: for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

209:  for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

241:  for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

263:  for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

293:  for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {

330:  for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
```

## \[G-15\] uint8 is not always cheaper than uint256

The EVM only operates on 32 bytes/ 256 bits at a time. This means that if you use uint8, EVM has to first convert it uint256 to work on it and the conversion costs extra gas! You may wonder, What were the devs thinking? Why did they create smaller variables then? The answer lies in packing. In solidity, you can pack multiple small variables into one slot, but if you are defining a lone variable and can’t pack it, it’s optimal to use a uint256 rather than uint8.

```
File:	ethereum/contracts/zksync/facets/Executor.sol

130:        require(!_checkBit(processedLogs, uint8(logKey)), "kp");
131:        processedLogs = _setBit(processedLogs, uint8(logKey));

472:   function _checkBit(uint256 _bitMap, uint8 _index) internal pure returns (bool) {

477:     function _setBit(uint256 _bitMap, uint8 _index) internal pure returns (uint256) {
```

```
48:     uint8 l2ShardId;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L48

## \[G-16\] Upgrade Solidity’s optimizer

Make sure Solidity’s optimizer is enabled. It reduces gas costs. If you want to gas optimize for contract deployment (costs less to deploy a contract) then set the Solidity optimizer at a low number. If you want to optimize for run-time gas costs (when functions are called on a contract) then set the optimizer to a high number.

Set the optimization value higher than 800 in your hardhat.config.ts file.

```
solidity: {
        version: '0.8.17',
        settings: {
            optimizer: {
              enabled: true,
              runs: 200
            },
            viaIR: true
        }
    },
```

## \[G-17\] Use Short Circuiting rules to your advantage.

When using logical disjunction (||), logical conjunction (&&), make sure to order your functions correctly for optimal gas usage. In logical disjunction (OR), if the first function resolves to true, the second one won’t be executed and hence save you gas. In logical disjunction (AND), if the first function evaluates to false, the next function won’t be evaluated. Therefore, you should order your functions accordingly in your solidity code to reduce the probability of needing to evaluate the second function.

```
File:	ethereum/contracts/zksync/facets/Executor.sol

189:  if (systemContractsUpgradeTxHash == bytes32(0) || s.l2SystemContractsUpgradeBatchNumber != 0) {
```

## \[G-18\] Make fewer external calls.

Every call to an external contract costs a decent amount of gas. For optimization of gas usage, It’s better to call one function and have it return all the data you need rather than calling a separate function for every piece of data. This might go against the best coding practices for other languages, but solidity is special.

## CONCLUSION

As you move forward with implementing the suggested optimizations, we urge you to exercise caution and conduct meticulous testing. It is essential to verify that these changes do not introduce any new vulnerabilities and effectively deliver the desired performance improvements. Carefully review the code modifications and perform rigorous testing to validate the security and effectiveness of the refactored code.