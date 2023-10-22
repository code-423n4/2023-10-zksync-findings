

## Gas Optimizations

| Number | Issue | Instances |
|--------|-------|-----------|
|[G-01]| Use assembly to validate msg.sender   | 6 |
|[G-02]| Expensive operation inside a for loop   | 3 |
|[G-03]| Use assembly to perform efficient back-to-back calls   | 6 |
|[G-04]| Use assembly in place of abi.decode to extract calldata values more efficiently   | 1 |
|[G-05]| The function should first verify if msg.value >= baseCost + _l2Value before performing any other operation (Saves 7146 Gas)   | 1 |
|[G-06]| public functions not called by the contract should be declared external instead   | 2 |
|[G-07]| Use constants instead of type(uintx).max   | 3 |
|[G-08]| uint256 variable initialization to default value of 0 can be omitted    | 20 |
|[G-09]| The result of function calls should be cached rather than re-calling the function   | 1 |
|[G-10]| OR in if-condition can be rewritten to two single if conditions   | 4 |
|[G-11]| Use assembly to write address storage values   | 2 |
|[G-12]| Cache state variables outside of loop to avoid reading/writing storage on every iteration   | 4 |
|[G-13]| Cache calldata/memory pointers for complex types to avoid offset calculations   | 5 |
|[G-14]| Combine events to save 2 Glogtopic (375 gas)   | 1 |
|[G-15]| Duplicated require()/revert() checks should be refactored to a modifier or function  | 2 |
|[G-16]| Failure to check the zero address in the constructor causes the contract to be deployed again   | 1 |
|[G-17]| bytes constants are more eficient than string constans   | 5 |
|[G-18]| Not using the named return variable when a function returns, wastes deployment gas only in view function    | 7 |
|[G-19]| Use uint256(1)/uint256(2) instead for true and false boolean states   | 15 |
|[G-20]| Can make the variable outside the loop to save gas   | 7 |
|[G-21]| Use assembly for loops   | 6 |
|[G-22]| Caching global variables is more expensive than using the actual variable (use msg.sender or block.timestamp instead of caching it)   | 1 |
|[G-23]| Make 3 event parameters indexed when possible   | 5 |
|[G-24]| Do not calculate constants   | 3 |


## [G-01] Use assembly to validate msg.sender

We can use assembly to efficiently validate msg.sender for the didPay and uniswapV3SwapCallback functions with the least amount of opcodes necessary. Additionally, we can use xor() instead of iszero(eq()), saving 3 gas. We can also potentially save gas on the unhappy path by using scratch space to store the error selector, potentially avoiding memory expansion costs.

```solidity
file:   contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

66      require(msg.sender == validator, "8h");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L66

```solidity
file:  contracts/ethereum/contracts/zksync/facets/Admin.sol

31     require(msg.sender == pendingGovernor, "n4")

55     require(msg.sender == pendingAdmin, "n4")

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L31

```solidity
file:  contracts/ethereum/contracts/zksync/facets/Base.sol

19    require(msg.sender == s.governor, "1g");

25    require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");

31    require(s.validators[msg.sender], "1h"); // validator is not active

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L19

## [G-02] Expensive operation inside a for loop

```solidity
file:   contracts/ethereum/contracts/zksync/facets/Executor.sol

293      for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
            _executeOneBatch(_batchesData[i], i);
            emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L293-L296

```solidity
file:    contracts/ethereum/contracts/zksync/libraries/Diamond.sol

100       for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
            Action action = facetCuts[i].action;
            address facet = facetCuts[i].facet;
            bool isFacetFreezable = facetCuts[i].isFreezable;
            bytes4[] memory selectors = facetCuts[i].selectors;

            require(selectors.length > 0, "B"); // no functions for diamond cut

            if (action == Action.Add) {
                _addFunctions(facet, selectors, isFacetFreezable);
            } else if (action == Action.Replace) {
                _replaceFunctions(facet, selectors, isFacetFreezable);
            } else if (action == Action.Remove) {
                _removeFunctions(facet, selectors);
            } else {
                revert("C"); // undefined diamond cut action
            }
        }


```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L100-L117

```solidity
file:  contracts/ethereum/contracts/zksync/libraries/Merkle.sol

29     for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
            currentHash = (_index % 2 == 0)
                ? _efficientHash(currentHash, _path[i])
                : _efficientHash(_path[i], currentHash);
            _index /= 2;
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L29-L34



## [G-03] Use assembly to perform efficient back-to-back calls

If similar external calls are performed back-to-back, we can use assembly to reuse any function signatures and function parameters that stay the same. In addition, we can also reuse the same memory space for each function call (scratch space + free memory pointer), which can potentially allow us to avoid memory expansion costs. In this case, we are also able to efficiently store the function signatures together in memory as one word, saving multiple MLOADs in the process.

Note: In order to do this optimization safely we will cache the free memory pointer value and restore it once we are done with our function calls. We will also set the zero slot back to 0 if neccessary.


### use for this UnsafeBytes.readAddress() back to bak  external call assembly

```solidity
file:  contracts/ethereum/contracts/zksync/facets/Mailbox.sol       

426    (l1Receiver, offset) = UnsafeBytes.readAddress(_message, offset);
425    (amount, offset) = UnsafeBytes.readUint256(_message, offset);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L426-L425

### use for this Math.ceilDiv() back to bak  external call assembly

```solidity
file:    contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol

141     uint256 txSlotOverhead = Math.ceilDiv(batchOverheadGas, MAX_TRANSACTIONS_IN_BATCH);
        batchOverheadForTransaction = Math.max(batchOverheadForTransaction, txSlotOverhead);

        // The overhead for occupying the bootloader memory can be derived from encoded_len
        uint256 overheadForLength = Math.ceilDiv(_encodingLength * batchOverheadGas, BOOTLOADER_TX_ENCODING_SPACE);
        batchOverheadForTransaction = Math.max(batchOverheadForTransaction, overheadForLength);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L141-L146

```solidity

file:   contracts/ethereum/contracts/zksync/facets/Executor.sol

125      (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET);
126      (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);
127      (bytes32 logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);


```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L125-L127

### use for this L2ContractHelper.hashL2Bytecode() back to bak  external call assembly

```solidity
file:  ethereum/contracts/bridge/L1ERC20Bridge.sol

99   bytes32 l2BridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
        bytes32 l2BridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L99-L100

### use for this _token.staticcall() back to bak  external call assembly

```solidity
file:  ethereum/contracts/bridge/L1ERC20Bridge.sol

241     (, bytes memory data1) = _token.staticcall(abi.encodeCall(IERC20Metadata.name, ()));
        (, bytes memory data2) = _token.staticcall(abi.encodeCall(IERC20Metadata.symbol, ()));
        (, bytes memory data3) = _token.staticcall(abi.encodeCall(IERC20Metadata.decimals, ()));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L241-L243

### use for this L2ContractHelper.hashL2Bytecode() back to bak  external call assembly

```solidity
file: ethereum/contracts/bridge/L1WethBridge.sol

98    bytes32 l2WethBridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
        bytes32 l2WethBridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L98-L99

## [G-04] Use assembly in place of abi.decode to extract calldata values more efficiently

Instead of using abi.decode, we can use assembly to decode our desired calldata values directly. This will allow us to avoid decoding calldata values that we will not use.

```solidity

file:  contracts/ethereum/contracts/zksync/libraries/Diamond.sol


303   require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");


```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L303


## [G-05] The function should first verify if msg.value >= baseCost + _l2Value before performing any other operation (Saves 7146 Gas)

In case of a revert on msg.value == 0 we save 7146 gas on average from the protocol gas tests (to simulate the sad path ie msg.value == 0 see the test modification).


```solidity
file:    contracts/ethereum/contracts/zksync/facets/Mailbox.sol

306      require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L306

## [G-06] public functions not called by the contract should be declared external instead

when a function is declared as public, it is generated with an internal and an external interface. This means the function can be called both internally (within the contract) and externally (by other contracts or accounts). However, if a public function is never called internally and is only expected to be invoked externally, it is more gas-efficient to explicitly declare it as external.

```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

82   function proveL1ToL2TransactionStatus(
        bytes32 _l2TxHash,
        uint256 _l2BatchNumber,
        uint256 _l2MessageIndex,
        uint16 _l2TxNumberInBatch,
        bytes32[] calldata _merkleProof,
        TxStatus _status
    ) public view override returns (bool) {


166   function l2TransactionBaseCost(
        uint256 _gasPrice,
        uint256 _l2GasLimit,
        uint256 _l2GasPerPubdataByteLimit
    ) public pure returns (uint256) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L82-L89

## [G-07] Use constants instead of type(uintx).max

using type(uintx).max can result in higher gas costs because it involves a runtime operation to calculate the maximum value at runtime. This calculation is performed every time the expression is evaluated.

To save gas, it is recommended to use constants instead of type(uintx).max to represent the maximum value. By declaring a constant with the maximum value, the value is known at compile-time and does not require any runtime calculations.

```solidity
file:  ethereum/contracts/zksync/libraries/TransactionValidator.sol

50    require(_transaction.from <= type(uint16).max, "ua");

51    require(_transaction.to <= type(uint160).max, "ub");

55    require(_transaction.reserved[1] <= type(uint160).max, "uf");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L50


## [G-08] uint256 variable initialization to default value of 0 can be omitted 

There is no need to initialize variables to their default values during declaration, since they are any way initialized to default value once declared.


```solidity
file:  ethereum/contracts/zksync/facets/Admin.sol
 
124     diamondStorage.isFrozen = false;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L124

```solidity
file:  ethereum/contracts/zksync/facets/Admin.sol

113    diamondStorage.isFrozen = true;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L113


```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

212    s.isEthWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex] = true;  

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L212


```solidity
file:  contracts/ethereum/contracts/zksync/Config.sol

46    uint256 constant PRIORITY_EXPIRATION = 0 days;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L46


```solidity
file: ethereum/contracts/zksync/ValidatorTimelock.sol

85   for (uint256 i = 0; i < _newBatchesData.length; ++i) {

116  for (uint256 i = 0; i < _newBatchesData.length; ++i) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L85

```solidity
file: ethereum/contracts/zksync/facets/Executor.sol

123    for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

209    for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

241    for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

263    for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

293    for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {

330    for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L123

```solidity
file:  ethereum/contracts/zksync/facets/Getters.sol

141   if (selectorsArrayLen != 0) {

182   for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
    
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L141


```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

395     for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L395


```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

100    for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {

138    for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

159    for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

179    for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L100

```solidity
file:  ethereum/contracts/zksync/libraries/TransactionValidator.sol
  
92      uint256 costForPubdata = 0;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L92

## [G-09] The result of function calls should be cached rather than re-calling the function

Function uncheckedInc() is being called 2 times. Firstly, in the for loop  (line 330), then inside the for loop 


```solidity
file:  ethereum/contracts/zksync/facets/Executor.sol

330        for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
            currentTotalBatchesVerified = currentTotalBatchesVerified.uncheckedInc();
            require(

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L330-L332

## [G-10] OR in if-condition can be rewritten to two single if conditions

```solidity
file: contracts/zksync/facets/Executor.sol

189   if (systemContractsUpgradeTxHash == bytes32(0) || s.l2SystemContractsUpgradeBatchNumber != 0) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L189

```solidity
file: ethereum/contracts/zksync/DiamondProxy.sol

25   require(msg.data.length >= 4 || msg.data.length == 0, "Ut");

31   require(!diamondStorage.isFrozen || !facet.isFreezable, "q1");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L25

```solidity
file: ethereum/contracts/zksync/facets/Base.sol

25    require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L25



## [G-11] Use assembly to write address storage values

```solidity
file:  ethereum/contracts/zksync/ValidatorTimelock.sol

48  validator = _validator;

54  validator = _newValidator;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L48

## [G-12] Cache state variables outside of loop to avoid reading/writing storage on every iteration

Reading from storage should always try to be avoided within loops. In the following instances, we are able to cache state variables outside of the loop to save a Gwarmaccess (100 gas) per loop iteration. In addition, for some instances we are also able to increment the cached variable in the loop and update the storage variable outside the loop to save 1 SSTORE per loop iteration.

### Cache committedBatchTimestamp outside loop, increment cached variable in loop, and update storage outside loop to save 1 SLOADs + 1 SSTORE per iteration

```solidity
file:  ethereum/contracts/zksync/ValidatorTimelock.sol

117    uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L117

```solidity
file: ethereum/contracts/zksync/facets/Getters.sol

183   address facetAddr = ds.facets[i];

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L183

```solidity
file: ethereum/contracts/zksync/libraries/Diamond.sol

161   SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

181   SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L161


## [G-13] Cache calldata/memory pointers for complex types to avoid offset calculations

The function parameters in the following instances are complex types (i.e. arrays which contain structs) and thus will result in more complex offset calculations to retrieve specific data from calldata/memory. We can avoid peforming some of these offset calculations by instantiating calldata/memory pointers.

```solidity
file: ethereum/contracts/zksync/facets/Executor.sol

264  PriorityOperation memory priorityOp = s.priorityQueue.popFront();

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L264


```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

101   Action action = facetCuts[i].action;

140   SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

161   SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

181   SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L101

## [G-14] Combine events to save 2 Glogtopic (375 gas)

The events below are only emitted once in the handleRewards function. We can combine the events into one singular event to save two Glogtopic (375 gas) that would otherwise be paid for the additional two events.

```solidity
file:  ethereum/contracts/zksync/facets/Admin.sol
 
37     emit NewPendingGovernor(pendingGovernor, address(0));
38     emit NewGovernor(previousGovernor, pendingGovernor);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L37-38

## [G-15] Duplicated require()/revert() checks should be refactored to a modifier or function

```solidity
file:  ethereum/contracts/zksync/DiamondInit.sol

/// @audit this require is duplicated on line 57

56  require(address(_initalizeData.verifier) != address(0), "vt");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L56


```soldity
file: ethereum/contracts/zksync/facets/Executor.sol

/// @audit this require is duplicated on line 368

360  require(successVerifyProof, "p");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L360

## [G-16] Failure to check the zero address in the constructor causes the contract to be deployed again

Zero address control is not performed in the constructor in all 3 contracts within the scope of the audit. Bypassing this check could cause the contract to be deployed by mistakenly entering a zero address. In this case, the contract will need to be redeployed. This means extra gas consumption as contract deployment costs are high.

```solidity
file: ethereum/contracts/zksync/ValidatorTimelock.sol

44   constructor(address _initialOwner, address _zkSyncContract, uint32 _executionDelay, address _validator) {
        _transferOwnership(_initialOwner);
        zkSyncContract = _zkSyncContract;
        executionDelay = _executionDelay;
        validator = _validator;
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L44-L49


## [G-17] bytes constants are more eficient than string constans

If the data can fit in 32 bytes, the bytes32 data type can be used instead of bytes or strings, as it is less robust in terms of robustness.


```solidity
file: ethereum/contracts/zksync/ValidatorTimelock.sol
 
24   string public constant override getName = "ValidatorTimelock";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24


```solidity
file: ethereum/contracts/zksync/facets/Admin.sol

15   string public constant override getName = "AdminFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15

```solidity
file: ethereum/contracts/zksync/facets/Executor.sol

22   string public constant override getName = "ExecutorFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#l22


```solidity
file: ethereum/contracts/zksync/facets/Getters.sol

19  string public constant override getName = "GettersFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19

```solidity
file: ethereum/contracts/zksync/facets/Mailbox.sol

41   string public constant override getName = "MailboxFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41

## [G-18] Not using the named return variable when a function returns, wastes deployment gas only in view function 

```solidity
file: ethereum/contracts/zksync/facets/Executor.sol

444  function _batchMetaParameters() internal view returns (bytes memory) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L444

```solidity
file:  ethereum/contracts/zksync/facets/Getters.sol

135   function isFacetFreezable(address _facet) external view returns (bool isFreezable) {

176   function facets() external view returns (Facet[] memory result) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L135

```solidity
file: ethereum/contracts/zksync/interfaces/IGetters.sol

76    function facetAddresses() external view returns (address[] memory facets);

78    function facetAddress(bytes4 _selector) external view returns (address facet);

82    function isFacetFreezable(address _facet) external view returns (bool isFreezable);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L76


```solidity
file: ethereum/contracts/zksync/interfaces/IGetters.sol

16   function get(Uint32Map storage _map, uint256 _index) internal view returns (uint32 result) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L16

## [G-19] Use uint256(1)/uint256(2) instead for true and false boolean states

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. see source:

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

```solidity
file:  ethereum/contracts/zksync/facets/Admin.sol

113     diamondStorage.isFrozen = true;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L113

```solidity
file: ethereum/contracts/zksync/facets/Mailbox.sol

212   s.isEthWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex] = true;'

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L212

```solidity
file: ethereum/contracts/zksync/facets/Admin.sol

68  function setValidator(address _validator, bool _active) external onlyGovernorOrAdmin {

75  function setPorterAvailability(bool _zkPorterIsAvailable) external onlyGovernor {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L68

```solidity
file: ethereum/contracts/zksync/facets/Executor.sol

355   bool successVerifyProof = s.verifier.verify(

363   bool successVerifyProof = s.verifier.verify(

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L355


```solidity
file:  ethereum/contracts/bridge/L1ERC20Bridge.sol

264    bool proofValid = zkSync.proveL1ToL2TransactionStatus(

311    bool success = zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);

341    function _verifyDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L264

```solidity
file:  ethereum/contracts/bridge/L1WethBridge.sol

245    bool alreadyFinalised = zkSync.isEthWithdrawalFinalized(_l2BatchNumber, _l2MessageIndex);

253    bool success = zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L245

```solidity
file:  ethereum/contracts/common/AllowList.sol

110   bool _enable

116   function _setPermissionToCall(address _caller, address _target, bytes4 _functionSig, bool _enable) internal {

117   bool currentPermission = hasSpecialAccessToCall[_caller][_target][_functionSig];

129   function setDepositLimit(address _l1Token, bool _depositLimitation, uint256 _depositCap) external onlyOwner {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L110

## [G-20] Can make the variable outside the loop to save gas

When you declare a variable inside a loop, Solidity creates a new instance of the variable for each iteration of the loop. This can lead to unnecessary gas costs, especially if the loop is executed frequently or iterates over a large number of elements.

```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

396    bytes32 hashedBytecode = L2ContractHelper.hashL2Bytecode(_factoryDeps[i]);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L396

```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

102    address facet = facetCuts[i].facet;

103    bool isFacetFreezable = facetCuts[i].isFreezable;

104    bytes4[] memory selectors = facetCuts[i].selectors;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L102


```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

139    bytes4 selector = _selectors[i];

160    bytes4 selector = _selectors[i];

180    bytes4 selector = _selectors[i];

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L139

## [G-21] Use assembly for loops

In the following instances, assembly is used for more gas efficient loops. The only memory slots that are manually used in the loops are scratch space (0x00-0x20), the free memory pointer (0x40), and the zero slot (0x60). This allows us to avoid using the free memory pointer to allocate new memory, which may result in memory expansion costs.

Note that in order to do this optimization safely we will need to cache and restore the free memory pointer after the loop. We will also set the zero slot (0x60) back to 0.

```solidity
file:  ethereum/contracts/common/AllowList.sol

62    for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {
            _setAccessMode(_targets[i], _accessModes[i]);
        }

96   for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
            _setPermissionToCall(_callers[i], _targets[i], _functionSigs[i], _enables[i]);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L62-L64

```solidity
file:  ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

204    for (uint256 i = 0; i < _factoryDeps.length; ++i) {
            require(
                L2ContractHelper.hashL2Bytecode(_factoryDeps[i]) == bytes32(_expectedHashes[i]),
                "Wrong factory dep hash"
            );
        }

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204-L209

```solidity
file: ethereum/contracts/zksync/ValidatorTimelock.sol

85   for (uint256 i = 0; i < _newBatchesData.length; ++i) {
                committedBatchTimestamp.set(_newBatchesData[i].batchNumber, timestamp);
            }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L85-L87

```solidity
file:  ethereum/contracts/zksync/facets/Executor.sol

263      for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
            PriorityOperation memory priorityOp = s.priorityQueue.popFront();
            concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
        }

293     for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
            _executeOneBatch(_batchesData[i], i);
            emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L263-L266


## [G-22] Caching global variables is more expensive than using the actual variable (use msg.sender or block.timestamp instead of caching it)


```solidity
file: ethereum/contracts/zksync/facets/Mailbox.sol

247    address sender = msg.sender;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L247


## [G-23] Make 3 event parameters indexed when possible

It’s the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

```solidity 
file: ethereum/contracts/zksync/ValidatorTimelock.sol

27   event NewExecutionDelay(uint256 _newExecutionDelay);

30   event NewValidator(address _oldValidator, address _newValidator);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L27

```solidity
file: ethereum/contracts/zksync/interfaces/IAdmin.sol

31    event IsPorterAvailableStatusUpdate(bool isPorterAvailable);

34    event ValidatorStatusUpdate(address indexed validatorAddress, bool isActive);

51    event NewPriorityTxMaxGasLimit(uint256 oldPriorityTxMaxGasLimit, uint256 newPriorityTxMaxGasLimit);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L31

## [G-24] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time the variable is used, which wastes some gas.

```solidity
file:  ethereum/contracts/zksync/Config.sol

14    uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

26    uint256 constant MAX_INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE * 4765;

33    uint256 constant MAX_REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE * 7564;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L14