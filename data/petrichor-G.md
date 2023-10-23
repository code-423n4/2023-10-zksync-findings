# Gas Optimization

| no     |  issue  |  instance  |
|------|---------|------------|
[G-01]| abi.encode() is less efficient than abi.encodePacked()|13|
[G-02]| Do not calculate constants|3|
[G-03]| >=/<= costs less gas than >/<|25|
[G-04]|Make 3 event parameters indexed when possible|14|
[G-05]| Access mappings directly rather than using accessor functions|1|
[G-06]|Use Modifiers Instead of Functions To Save Gas|6|
[G-07]|Gas savings can be achieved by changing the model for assigning value to the structure |6|
[G-08]|State variables can be packed into fewer storage slots|1|
[G-09]| Can Make The Variable Outside The Loop To Save Gas |14|
[G-10]|Use assembly in place of abi.decode to extract calldata values more |1|
[G-11]| bytes constants are more eficient than string constans|5|
[G-12]| Use do while loops instead of for loops|1|
[G-13]|The result of function calls should be cached rather than re-calling the function|2|
[G-14]| Empty blocks should be removed or emit something|2|
[G-15]|Refactor event to avoid emitting empty data|2|
[G-16]|Sort Solidity operations using short-circuit mode|1|
[G-17]|Using Private Rather Than Public For Constants, Saves Gas|4|
[G-18]|Expressions for constant values such as a call to keccak256(), should use immutable rather than constants|1|
[G-19]| Pack structs by putting data types that can fit together next to each other|6|
[G-20]| Use hardcode address instead address(this)|7|





## [G-01] abi.encode() is less efficient than abi.encodePacked()

abi.encodePacked() is generally considered to be more gas-efficient than abi.encode(), because it skips the step of adding function signatures and other metadata to the encoded data. However, this comes at the cost of reduced safety, as abi.encodePacked() does not perform any type checking or padding of data.


```solidity
File:  code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
119  l2BridgeProxyConstructorData = abi.encode(bridgeImplementationAddr, _governor, proxyInitializationParams);

244  data = abi.encode(data1, data2, data3);

354  bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""));
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L119
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L244
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L354



```solidity
File:  code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
118         l2WethBridgeProxyConstructorData = abi.encode(
                wethBridgeImplementationAddr,
                _governor,
                proxyInitializationParams
            );
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L118-L122


```solidity
File:  code/contracts/ethereum/contracts/governance/Governance.sol
205   return keccak256(abi.encode(_operation));
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L205


```solidity
File:  code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol
173  bytes memory encodedTransaction = abi.encode(_l2ProtocolUpgradeTx);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L173


```solidity
File:  code/contracts/ethereum/contracts/zksync/DiamondInit.sol
77  s.storedBatchHashes[0] = keccak256(abi.encode(storedBatchZero));
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L77


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Executor.sol
265   concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));

431   return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));

468   return keccak256(abi.encode(_storedBatchInfo));
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L265
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L431
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L468



```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
363   bytes memory transactionEncoding = abi.encode(transaction);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L363


## [G-02] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas

```solidity
File:  code/contracts/ethereum/contracts/zksync/Config.sol
14   uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

26   uint256 constant MAX_INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE * 4765;

33   uint256 constant MAX_REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE * 7564;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L14
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L26
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L33



## [G-03] >=/<= costs less gas than >/<


The compiler uses opcodes GT and ISZERO for code that uses >, but only requires LT for >=. A similar behaviour applies for >, which uses opcodes LT and ISZERO, but only requires GT for <=.

```solidity
File:  code/contracts/ethereum/contracts/common/AllowList.sol
62  for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

96  for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {    
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L62
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L96



```solidity
File:  code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol
26   require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L26


```solidity
File:  code/contracts/ethereum/contracts/governance/Governance.sol
225  for (uint256 i = 0; i < _calls.length; ++i) {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L225


```solidity
File:  code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol
204   for (uint256 i = 0; i < _factoryDeps.length; ++i) {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204


```solidity
File:  code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
85   for (uint256 i = 0; i < _newBatchesData.length; ++i) {

116  for (uint256 i = 0; i < _newBatchesData.length; ++i) {    
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L85
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L116



```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Executor.sol
85  require(_previousBatchTimestamp < batchTimestamp, "h3");

123 for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

209 for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

241 for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

263 for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

293 for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {

330 for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {

402 if (_newLastBatch < s.totalBatchesVerified) {                     
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L85
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L123
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L209
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L241
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L263
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L293
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L330
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L402




```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Getters.sol
182  for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {    
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L182


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
295    for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L


```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
100  for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {

138  for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

159  for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

179  for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {            
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L100
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L138
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L159
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L179



```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
25  require(pathLength < 256, "bt");

26  require(_index < (1 << pathLength), "px");

29  for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L25
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L26
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/M


## [G-04] Make 3 event parameters indexed when possible

It is the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

```solidity
File:   code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
41   event EthReceived(uint256 amount);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L41


```solidity
File:  code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol
15       event WithdrawalFinalized(address indexed to, address indexed l1Token, uint256 amount);

17       event ClaimedFailedDeposit(address indexed to, address indexed l1Token, uint256 amount);
```
https://github.com/code-423n4/2023-10-zksync/blob/maincode/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L15-L17


```solidity
File:  code/contracts/ethereum/contracts/governance/IGovernance.sol
65   event TransparentOperationScheduled(bytes32 indexed _id, uint256 delay, Operation _operation);

68   event ShadowOperationScheduled(bytes32 indexed _id, uint256 delay);

74   event ChangeSecurityCouncil(address _securityCouncilBefore, address _securityCouncilAfter);

77   event ChangeMinDelay(uint256 _delayBefore, uint256 _delayAfter);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L65
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L68
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L74
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L77



```solidity
File:  code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
27   event NewExecutionDelay(uint256 _newExecutionDelay);

30   event NewValidator(address _oldValidator, address _newValidator);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L27
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L30



```solidity
File:  code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol
51  event NewPriorityTxMaxGasLimit(uint256 oldPriorityTxMaxGasLimit, uint256 newPriorityTxMaxGasLimit);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L51


```solidity
File:  code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol
106   event BlocksRevert(uint256 totalBatchesCommitted, uint256 totalBatchesVerified, uint256 totalBatchesExecuted);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L106


```solidity
File:  code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol
142  event NewPriorityRequest(
        uint256 txId,
        bytes32 txHash,
        uint64 expirationTimestamp,
        L2CanonicalTransaction transaction,
        bytes[] factoryDeps
    );

153   event EthWithdrawalFinalized(address indexed to, uint256 amount);    
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L142
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L153



```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
23   event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L23


erkle.sol#L29


## [G-05] Access mappings directly rather than using accessor functions



```solidity
File:   code/contracts/ethereum/contracts/common/AllowList.sol
137     return tokenDeposit[_l1Token];
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L137


## [G-06] Use Modifiers Instead of Functions To Save Gas

Modifiers in Solidity are a way to add reusable code that can be applied to multiple functions within a contract. They allow developers to enforce certain conditions or validations before executing a function. By utilizing modifiers effectively, gas consumption can be reduced by avoiding unnecessary code duplication.


```solidity
File:  code/contracts/ethereum/contracts/governance/Governance.sol
84  function isOperation(bytes32 _id) public view returns (bool) {
        return getOperationState(_id) != OperationState.Unset;
    }

    /// @dev Returns whether an operation is pending or not. Note that a "pending" operation may also be "ready".
    function isOperationPending(bytes32 _id) public view returns (bool) {
        OperationState state = getOperationState(_id);
        return state == OperationState.Waiting || state == OperationState.Ready;
    }

    /// @dev Returns whether an operation is ready for execution. Note that a "ready" operation is also "pending".
    function isOperationReady(bytes32 _id) public view returns (bool) {
        return getOperationState(_id) == OperationState.Ready;
    }

    /// @dev Returns whether an operation is done or not.
    function isOperationDone(bytes32 _id) public view returns (bool) {
        return getOperationState(_id) == OperationState.Done;
    }



```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L84-L102


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Getters.sol
78     function isValidator(address _address) external view returns (bool) {

129    function isDiamondStorageFrozen() external view returns (bool) {    

167    function isEthWithdrawalFinalized(uint256 _l2BatchNumber, uint256 _l2MessageIndex) external view returns (bool) {    
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L78
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L129
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L167



```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol
50  function isEmpty(Queue storage _queue) internal view returns (bool) {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L50


## [G-07] Gas savings can be achieved by changing the model for assigning value to the structure (260 gas)

To achieve gas savings, one can consider changing the model for assigning value to a structure in a way that reduces gas consumption. By optimizing the structure's data organization or utilizing more efficient data types, gas costs can be minimized.

 packages\v2-option\src\TimeswapV2OptionDeployer.sol:  32: 
    
     function deploy(address optionFactory, address identifier0, address identifier1) internal returns (address optionPair) {        
     - parameter = Parameter({optionFactory: optionsFactory, token0: token0, token1: token1});
     +parameter.optionFactory = optionFactory;
     +parameter.token0 = token0;
     +parameter.token1 = token1; 



```solidity
File:  code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
302     L2Message memory l2ToL1Message = L2Message({
            txNumberInBatch: _l2TxNumberInBatch,
            sender: l2Bridge,
            data: _message
        });
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L302


```solidity
File:  code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
248         L2Message memory l2ToL1Message = L2Message({
                txNumberInBatch: _l2TxNumberInBatch,
                sender: L2_ETH_TOKEN_SYSTEM_CONTRACT_ADDR,
                data: _message
            });
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L248


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
100      L2Log memory l2Log = L2Log({
            l2ShardId: 0,
            isService: true,
            txNumberInBatch: _l2TxNumberInBatch,
            sender: L2_BOOTLOADER_ADDRESS,
            key: _l2TxHash,
            value: bytes32(uint256(_status))
        });


151         L2Log({
                l2ShardId: 0,
                isService: true,
                txNumberInBatch: _message.txNumberInBatch,
                sender: L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR,
                key: bytes32(uint256(uint160(_message.sender))),
                value: keccak256(_message.data)
            });   


201     L2Message memory l2ToL1Message = L2Message({
            txNumberInBatch: _l2TxNumberInBatch,
            sender: L2_ETH_TOKEN_SYSTEM_CONTRACT_ADDR,
            data: _message
        });     


334     transaction = L2CanonicalTransaction({
            txType: PRIORITY_OPERATION_L2_TX_TYPE,
            from: uint256(uint160(_priorityOpParams.sender)),
            to: uint256(uint160(_priorityOpParams.contractAddressL2)),
            gasLimit: _priorityOpParams.l2GasLimit,
            gasPerPubdataByteLimit: _priorityOpParams.l2GasPricePerPubdata,
            maxFeePerGas: uint256(_priorityOpParams.l2GasPrice),
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
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L100
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L151
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L201
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L334


## [G‑08] State variables can be packed into fewer storage slots

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables can also be cheaper

```solidity
File:  code/contracts/ethereum/contracts/governance/Governance.sol
    address public securityCouncil;

    /// @notice A mapping to store timestamps where each operation will be ready for execution.
    /// @dev - 0 means the operation is not created.
    /// @dev - 1 (EXECUTED_PROPOSAL_TIMESTAMP) means the operation is already executed.
    /// @dev - any other value means timestamp in seconds when the operation will be ready for execution.
    mapping(bytes32 => uint256) public timestamps;

    /// @notice The minimum delay in seconds for operations to be ready for execution.
    uint256 public minDelay;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L26-L35



## [G-09] Can Make The Variable Outside The Loop To Save Gas 

By declaring the variable outside the loop, you can avoid the creation of multiple instances of the variable and reduce the gas ,

```
contract MyContract {
    function sum(uint256[] memory values) public pure returns (uint256) {
        uint256 total = 0;
        
        for (uint256 i = 0; i < values.length; i++) {
            total += values[i];
        }
        
        return total;
    }
}
```
```solidity
File: code/contracts/ethereum/contracts/governance/Governance.sol
226    (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L226


```solidity
File:  code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
117    uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber); 
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L117


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Executor.sol
125         (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET);
126         (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);
127         (bytes32 logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);

243         bytes32 expectedUpgradeTxHash = i == 0 ? _systemContractUpgradeTxHash : bytes32(0);

337         bytes32 currentBatchCommitment = _committedBatches[i].commitment;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L125-L127
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L125-L243
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L125-L337



```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Getters.sol
183     address facetAddr = ds.facets[i];
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L183


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
396   bytes32 hashedBytecode = L2ContractHelper.hashL2Bytecode(_factoryDeps[i]);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L396


```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
102         address facet = facetCuts[i].facet;
103         bool isFacetFreezable = facetCuts[i].isFreezable;

139         bytes4 selector = _selectors[i];

160         bytes4 selector = _selectors[i];

180         bytes4 selector = _selectors[i];
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L102-L102
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L102-L103
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L102-L139
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L102-L160
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L102-L180





## [G-10] Use assembly in place of abi.decode to extract calldata values more 


Instead of using abi.decode, we can use assembly to decode our desired calldata values directly. This will allow us to avoid decoding calldata values that we will not use.

```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
303   require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L303









## [G-11] bytes constants are more eficient than string constans

In Solidity, using byte constants instead of string constants can be more efficient in terms of gas consumption. This is because bytes are stored in memory as fixed-size arrays, while strings are dynamically sized and require additional gas for storage and manipulation.


```solidity
File:  code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
24    string public constant override getName = "ValidatorTimelock";
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Admin.sol
15   string public constant override getName = "AdminFacet";
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Executor.sol
22   string public constant override getName = "ExecutorFacet";
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Getters.sol
19   string public constant override getName = "GettersFacet";
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
41   string public constant override getName = "MailboxFacet";
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41


## [G-12] Use do while loops instead of for loops

A do while loop will cost less gas since the condition is not being checked for the first iteration.

```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
// @audit in line number 24   require(pathLength > 0, "xc");
29  for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L25


## [G‑13] The result of function calls should be cached rather than re-calling the function

By caching the result of a function call, you store the computed value in a variable and reuse it whenever needed, avoiding the need to execute the function multiple times. This eliminates redundant computations and reduces gas consumption.


```solidity
File:  code/contracts/ethereum/contracts/zksync/DiamondProxy.sol
/// @audit calldatasize()on line 39
37   calldatacopy(ptr, 0, calldatasize())
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L36-L39


```solidity
File:  code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
/// @audit calldatasize()on line 138
136  calldatacopy(0, 0, calldatasize())
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L136


## [G‑14] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.
If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation.

```solidity
File:  code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol
15    function _upgradeL1Contract(bytes calldata _customCallDataForUpgrade) internal virtual {}

21    function _postUpgrade(bytes calldata _customCallDataForUpgrade) internal virtual {}
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L15
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L21


## [G-15] Refactor event to avoid emitting empty data





When emitting events in Solidity, it is important to consider the gas costs associated with emitting unnecessary or empty data. To optimize gas usage, you can refactor your event implementation to avoid emitting events when the data being emitted is empty or irrelevant.

```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Admin.sol
115    emit Freeze();

126    emit Unfreeze();
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L115
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L126








## [G-16] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.


```

```solidity
File:  code/contracts/ethereum/contracts/zksync/DiamondProxy.sol
// @audit diamondStorage is state variable access of state variable use more gas
31   require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L31






## [G‑17] Using Private Rather Than Public For Constants, Saves Gas


By using private instead of public for constants, you can prevent the generation of the getter function and reduce the overall gas cost of your contract.


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Admin.sol
15   string public constant override getName = "AdminFacet";
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Executor.sol
22   string public constant override getName = "ExecutorFacet";
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Getters.sol
19   string public constant override getName = "GettersFacet";
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
41   string public constant override getName = "MailboxFacet";
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41


## [G-18] Expressions for constant values such as a call to keccak256(), should use immutable rather than constants

 This means that any expensive operations performed as part of the constant expression, such as a call to keccak256(), will be executed every time the contract is deployed, even if the result is always the same. This can result in higher gas costs.

```solidity
12    bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L12





## [G-19] Pack structs by putting data types that can fit together next to each other

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot. This saves gas when writing to storage ~20000 gas

```solidity
File:  code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol
31  struct ProposedUpgrade {
        IMailbox.L2CanonicalTransaction l2ProtocolUpgradeTx;
        bytes[] factoryDeps;
        bytes32 bootloaderHash;
        bytes32 defaultAccountHash;
        address verifier;
        VerifierParams verifierParams;
        bytes l1ContractsUpgradeCalldata;
        bytes postUpgradeCalldata;
        uint256 upgradeTimestamp;
        uint256 newProtocolVersion;
        address newAllowList;
    }
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L31-L43


```solidity
File:  code/contracts/ethereum/contracts/zksync/DiamondInit.sol
34   struct InitializeData {
        IVerifier verifier;
        address governor;
        address admin;
        bytes32 genesisBatchHash;
        uint64 genesisIndexRepeatedStorageChanges;
        bytes32 genesisBatchCommitment;
        IAllowList allowList;
        VerifierParams verifierParams;
        bool zkPorterIsAvailable;
        bytes32 l2BootloaderBytecodeHash;
        bytes32 l2DefaultAccountBytecodeHash;
        uint256 priorityTxMaxGasLimit;
    }
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L34-L47


```solidity
File:  code/contracts/ethereum/contracts/zksync/Storage.sol
79  struct AppStorage {
    /// @dev Storage of variables needed for deprecated diamond cut facet
    uint256[7] __DEPRECATED_diamondCutStorage;
    /// @notice Address which will exercise critical changes to the Diamond Proxy (upgrades, freezing & unfreezing)
    address governor;
    /// @notice Address that the governor proposed as one that will replace it
    address pendingGovernor;
    /// @notice List of permitted validators
    mapping(address => bool) validators;
    /// @dev Verifier contract. Used to verify aggregated proof for batches
    IVerifier verifier;
    /// @notice Total number of executed batches i.e. batches[totalBatchesExecuted] points at the latest executed batch
    /// (batch 0 is genesis)
    uint256 totalBatchesExecuted;
    /// @notice Total number of proved batches i.e. batches[totalBatchesProved] points at the latest proved batch
    uint256 totalBatchesVerified;
    /// @notice Total number of committed batches i.e. batches[totalBatchesCommitted] points at the latest committed
    /// batch
    uint256 totalBatchesCommitted;
    /// @dev Stored hashed StoredBatch for batch number
    mapping(uint256 => bytes32) storedBatchHashes;
    /// @dev Stored root hashes of L2 -> L1 logs
    mapping(uint256 => bytes32) l2LogsRootHashes;
    /// @dev Container that stores transactions requested from L1
    PriorityQueue.Queue priorityQueue;
    /// @dev The smart contract that manages the list with permission to call contract functions
    IAllowList allowList;
    /// @notice Part of the configuration parameters of ZKP circuits. Used as an input for the verifier smart contract
    VerifierParams verifierParams;
    /// @notice Bytecode hash of bootloader program.
    /// @dev Used as an input to zkp-circuit.
    bytes32 l2BootloaderBytecodeHash;
    /// @notice Bytecode hash of default account (bytecode for EOA).
    /// @dev Used as an input to zkp-circuit.
    bytes32 l2DefaultAccountBytecodeHash;
    /// @dev Indicates that the porter may be touched on L2 transactions.
    /// @dev Used as an input to zkp-circuit.
    bool zkPorterIsAvailable;
    /// @dev The maximum number of the L2 gas that a user can request for L1 -> L2 transactions
    /// @dev This is the maximum number of L2 gas that is available for the "body" of the transaction, i.e.
    /// without overhead for proving the batch.
    uint256 priorityTxMaxGasLimit;
    /// @dev Storage of variables needed for upgrade facet
    UpgradeStorage __DEPRECATED_upgrades;
    /// @dev A mapping L2 batch number => message number => flag.
    /// @dev The L2 -> L1 log is sent for every withdrawal, so this mapping is serving as
    /// a flag to indicate that the message was already processed.
    /// @dev Used to indicate that eth withdrawal was already processed
    mapping(uint256 => mapping(uint256 => bool)) isEthWithdrawalFinalized;
    /// @dev The most recent withdrawal time and amount reset
    uint256 __DEPRECATED_lastWithdrawalLimitReset;
    /// @dev The accumulated withdrawn amount during the withdrawal limit window
    uint256 __DEPRECATED_withdrawnAmountInWindow;
    /// @dev A mapping user address => the total deposited amount by the user
    mapping(address => uint256) totalDepositedAmountPerUser;
    /// @dev Stores the protocol version. Note, that the protocol version may not only encompass changes to the
    /// smart contracts, but also to the node behavior.
    uint256 protocolVersion;
    /// @dev Hash of the system contract upgrade transaction. If 0, then no upgrade transaction needs to be done.
    bytes32 l2SystemContractsUpgradeTxHash;
    /// @dev Batch number where the upgrade transaction has happened. If 0, then no upgrade transaction has happened
    /// yet.
    uint256 l2SystemContractsUpgradeBatchNumber;
    /// @dev Address which will exercise non-critical changes to the Diamond Proxy (changing validator set & unfreezing)
    address admin;
    /// @notice Address that the governor or admin proposed as one that will replace admin role
    address pendingAdmin;
}
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L79-L146


```solidity
File:  code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol
38  struct StoredBatchInfo {
        uint64 batchNumber;
        bytes32 batchHash;
        uint64 indexRepeatedStorageChanges;
        uint256 numberOfLayer1Txs;
        bytes32 priorityOperationsHash;
        bytes32 l2LogsTreeRoot;
        uint256 timestamp;
        bytes32 commitment;
    }
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L38-L47


```solidity
File:  code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol
35  struct L2CanonicalTransaction {
        uint256 txType;
        uint256 from;
        uint256 to;
        uint256 gasLimit;
        uint256 gasPerPubdataByteLimit;
        uint256 maxFeePerGas;
        uint256 maxPriorityFeePerGas;
        uint256 paymaster;
        uint256 nonce;
        uint256 value;
        // In the future, we might want to add some
        // new fields to the struct. The `txData` struct
        // is to be passed to account and any changes to its structure
        // would mean a breaking change to these accounts. To prevent this,
        // we should keep some fields as "reserved".
        // It is also recommended that their length is fixed, since
        // it would allow easier proof integration (in case we will need
        // some special circuit for preprocessing transactions).
        uint256[4] reserved;
        bytes data;
        bytes signature;
        uint256[] factoryDeps;
        bytes paymasterInput;
        // Reserved dynamic type for the future use-case. Using it should be avoided,
        // But it is still here, just in case we want to enable some additional functionality.
        bytes reservedDynamic;
    }


76  struct WritePriorityOpParams {
        address sender;
        uint256 txId;
        uint256 l2Value;
        address contractAddressL2;
        uint64 expirationTimestamp;
        uint256 l2GasLimit;
        uint256 l2GasPrice;
        uint256 l2GasPricePerPubdata;
        uint256 valueToMint;
        address refundRecipient;
    }
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L35-L62


## [G-20] Use hardcode address instead address(this)


The reason for this is that using address(this) requires an additional EXTCODESIZE operation to retrieve the contract's address from its bytecode, which can increase the gas cost of your contract. By pre-calculating and using a hardcoded address, you can avoid this additional operation and reduce the overall gas cost of your contract.






```solidity
File:  code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
117   (address(this), l2TokenProxyBytecodeHash, _governor)

217   uint256 balanceBefore = _token.balanceOf(address(this));
219  _token.safeTransferFrom(_from, address(this), _amount);
219   uint256 balanceAfter = _token.balanceOf(address(this));
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L117
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L217
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L219




```solidity
File:  code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
116    (address(this), l1WethAddress, _l2WethAddress)

171    IERC20(l1WethAddress).safeTransferFrom(msg.sender, address(this), _amount);

289    require(l1EthReceiver == address(this), "Wrong L1 ETH withdraw receiver");
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L116
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L1171``
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L289