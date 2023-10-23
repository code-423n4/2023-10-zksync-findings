
## Gas Optimizations

| Number | Issue | Instances | Total gas saved |
|--------|-------|-----------|-----------------|
|[G-01]| Use uint256(1)/uint256(2) instead for true and false boolean states   |  51  | 510000 |
|[G-02]| Use assembly for loops   |  6  |  |
|[G-03]| Using private rather than public for constants, saves gas   |  5  | 252180 |
|[G-04]| Empty blocks should be removed or emit something   | 5  |  |
|[G-05]| Can make the variable outside the loop to save gas   |  11  | 8041 |
|[G-06]| abi.encode() is less efficient than abi.encodepacked()   |  12  | 615804 |
|[G-07]| Avoid contract existence checks by using low level calls   |  1  | 100 |
|[G-08]| Expensive operation inside a for loop   |  4  |  |
|[G-09]| Don’t cache a state variable if it’s used only once   | 4  | 6072 |
|[G-10]| The creation of an intermediary array can be avoided   | 7  |  |
|[G-11]| Use Assembly To Check For address(0)   | 29  |  |
|[G-12]| Gas saving is achieved by removing the delete keyword (~60k)   | 5 | 3725 |
|[G-13]| >= costs less gas than >   |  4  |12 |
|[G-14]| Do not calculate constants   |  8  |  |
|[G-15]| Loop best practice to save gas   |  4  |  |
|[G-16]| Using storage instead of memory for structs/arrays saves gas   | 31  |  |
|[G-17]| Counting down in for statements is more gas efficient  | 19  | 231249 |
|[G-18]| Use do while loops instead of for loops   |  19  |  |
|[G-19]| Create immutable variable to avoid an external call   |  9  | 9099 |
|[G-20]| Save loop calls   |  5  |  |
|[G-21]| Use assembly to validate msg.sender   |  9  | 27 |
|[G-22]| Expensive operation inside a for loop    |  4  |  |
|[G-23]| Use assembly to perform efficient back-to-back calls    |  6  |  |
|[G-24]| Use assembly in place of abi.decode to extract calldata values more efficiently    | 1 |  |
|[G-25]| The function should first verify if msg.value >= baseCost + _l2Value before performing any other operation (Saves 7146 Gas) |  1  | 7146 |
|[G-26]| public functions not called by the contract should be declared external instead  | 5 |  |
|[G-27]| Use constants instead of type(uintx).max    |  3  |  |
|[G-28]| uint256 variable initialization to default value of 0 can be omitted     | 26 |  |
|[G-29]| The result of function calls should be cached rather than re-calling the function    |  4  |  |
|[G-30]| OR in if-condition can be rewritten to two single if conditions   |  10  |  |
|[G-31]| Use assembly to write address storage values   |  6  |  |
|[G-32]| Cache state variables outside of loop to avoid reading/writing storage on every iteration    |  4  |  |
|[G-33]| Cache calldata/memory pointers for complex types to avoid offset calculations    | 5 |  |
|[G-34]| Combine events to save 2 Glogtopic (375 gas)    |  1   | 375 |
|[G-35]| Duplicated require()/revert() checks should be refactored to a modifier or function    |  2  |  |
|[G-36]| Failure to check the zero address in the constructor causes the contract to be deployed again    | 3  |  |
|[G-37]| bytes constants are more eficient than string constans    |  5  | 126655 |
|[G-38]| Not using the named return variable when a function returns, wastes deployment gas only in view function     |  11  | 297 |
|[G-39]| Caching global variables is more expensive than using the actual variable (use msg.sender or block.timestamp instead of caching it)    |  1  |  |
|[G-40]| Make 3 event parameters indexed when possible    | 10 |  |
|[G-41]| It is sometimes cheaper to cache calldata | 23 |  |
|[G-42]| Should use arguments instead of state variable | 5 |  |
|[G-43]| Private functions used once can be inlined | 11 | 220 |
|[G-44]| Use Mappings instead of arrays | 10 |  |
|[G-45]| Pre-increment and pre-decrement are cheaper than +1 ,-1 | 9 |  |
|[G-46]| Use bytes.concat() instead of abi.encodePacked(), since this is preferred since 0.8.4 | 4 |  |
|[G-47]| Sort Solidity operations using short-circuit mode | 11 |  |
|[G-48]| Onlyowner no uses of the nonReentrant modifier | 11 |  |
|[G-49]| Not using the named return variable when a function returns, wastes deployment gas| 60 |  |
|[G-50]| With assembly, .call (bool success) transfer can be done gas-optimized | 1 |  |
|[G-51]| require() Should Be Used Instead Of assert() | 2 |  |



## [G-01] Use uint256(1)/uint256(2) instead for true and false boolean states

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. see source:

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27


```solidity
file:   ethereum/contracts/bridge/L1ERC20Bridge.sol

315    isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex] = true;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L315


```solidity
file: ethereum/contracts/zksync/facets/Admin.sol

124   diamondStorage.isFrozen = false;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L124

```solidity
file:  ethereum/contracts/bridge/L1WethBridge.sol

265    isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex] = true;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L265 

```solidity
file: ethereum/contracts/zksync/DiamondInit.sol

43   bool zkPorterIsAvailable;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L43

```solidity
file:  ethereum/contracts/zksync/facets/Executor.sol

472   function _checkBit(uint256 _bitMap, uint8 _index) internal pure returns (bool) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L472

```solidity
file:  ethereum/contracts/zksync/facets/Getters.sol

78     function isValidator(address _address) external view returns (bool) {

129    function isDiamondStorageFrozen() external view returns (bool) {

135    function isFacetFreezable(address _facet) external view returns (bool isFreezable) { 

158    function isFunctionFreezable(bytes4 _selector) external view returns (bool) {

167    function isEthWithdrawalFinalized(uint256 _l2BatchNumber, uint256 _l2MessageIndex) external view returns (bool) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L78

```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

54     ) public view returns (bool) {

69    ) external view returns (bool) {

89    ) public view override returns (bool) {

128   ) internal view returns (bool) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L54

```solidity
file:  ethereum/contracts/zksync/interfaces/IGetters.sol

34     function isValidator(address _address) external view returns (bool);

46     function isDiamondStorageFrozen() external view returns (bool);

58     function isEthWithdrawalFinalized(uint256 _l2BatchNumber, uint256 _l2MessageIndex) external view returns (bool);

80     function isFunctionFreezable(bytes4 _selector) external view returns (bool);

82     function isFacetFreezable(address _facet) external view returns (bool isFreezable);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L34

```solidity
file: ethereum/contracts/zksync/interfaces/IMailbox.sol

94     ) external view returns (bool);

101    ) external view returns (bool);

110    ) external view returns (bool);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L94

```solidity
file:  ethereum/contracts/zksync/libraries/PriorityQueue.sol

50    function isEmpty(Queue storage _queue) internal view returns (bool) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L50

```solidity
file: ethereum/contracts/zksync/libraries/Diamond.sol

32   bool isFreezable;

53   bool isFrozen;

64   bool isFreezable;

103  bool isFacetFreezable = facetCuts[i].isFreezable;

128  bool _isFacetFreezable

152  bool _isFacetFreezable

209  bool _isSelectorFreezable

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L32

```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

114    bool callSuccess;

209    bool proofValid 

291    bool _isFree,

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L114

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

```solidity
file: ethereum/contracts/common/interfaces/IAllowList.sol

30   bool depositLimitation;

73    bool _enable

82    bool _depositLimitation,

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol#L30


## [G-02] Use assembly for loops

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


## [G‑03] Using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.


```solidity
file: ethereum/contracts/zksync/ValidatorTimelock.sol

24     string public constant override getName = "ValidatorTimelock";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24

```solidity
file: ethereum/contracts/zksync/facets/Admin.sol

15    string public constant override getName = "AdminFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15

```solidity
file: ethereum/contracts/zksync/facets/Executor.sol

22    string public constant override getName = "ExecutorFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22


```solidity
file: ethereum/contracts/zksync/facets/Getters.sol

19    string public constant override getName = "GettersFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19

```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

41     string public constant override getName = "MailboxFacet";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41

## [G-04] Empty blocks should be removed or emit something

f the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if( x ) {}else if(y){ . . . }else{ . . . } => if ( !x) {if(y) { . . . }else{ . . .}}).
Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

```solidity
file: ethereum/contracts/governance/Governance.sol

262   receive() external payable {}

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L262

```solidity
file: ethereum/contracts/upgrades/DefaultUpgrade.sol

15    function _upgradeL1Contract(bytes calldata _customCallDataForUpgrade) internal virtual {}

21    function _postUpgrade(bytes calldata _customCallDataForUpgrade) internal virtual {}

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L15

```solidity
file:  contracts/ethereum/contracts/zksync/DiamondInit.sol

50   constructor() reentrancyGuardInitializer {}

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L50

```solidity
file: ethereum/contracts/zksync/interfaces/IZkSync.sol

10   interface IZkSync is IMailbox, IAdmin, IExecutor, IGetters {}

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IZkSync.sol#L10

## [G-05] Can make the variable outside the loop to save gas

When you declare a variable inside a loop, Solidity creates a new instance of the variable for each iteration of the loop. This can lead to unnecessary gas costs, especially if the loop is executed frequently or iterates over a large number of elements.

By declaring the variable outside the loop, you can avoid the creation of multiple instances of the variable and reduce the gas cost of your contract. Here’s an example:

```solidity
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

### Making variable inside the loop gas value:   662604
### Making variable outside the loop gas value:  661873

### Tools used Remix 

```solidity
file: ethereum/contracts/zksync/ValidatorTimelock.sol

117   uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L117

```solidity
file:  ethereum/contracts/zksync/facets/Executor.sol

243   bytes32 expectedUpgradeTxHash = i == 0 ? _systemContractUpgradeTxHash : bytes32(0);

264   PriorityOperation memory priorityOp = s.priorityQueue.popFront();

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L243


```solidity 
file: ethereum/contracts/zksync/facets/Getters.sol

183    address facetAddr = ds.facets[i];

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L183


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

## [G-06] abi.encode() is less efficient than abi.encodepacked()

In terms of efficiency, abi.encodePacked() is generally considered to be more gas-efficient than abi.encode(), because it skips the step of adding function signatures and other metadata to the encoded data. However, this comes at the cost of reduced safety, as abi.encodePacked() does not perform any type checking or padding of data.

Refference: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison

```solidity
file:  ethereum/contracts/zksync/DiamondInit.sol

77    s.storedBatchHashes[0] = keccak256(abi.encode(storedBatchZero));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L77

```solidity
file: ethereum/contracts/zksync/facets/Executor.sol

265   concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));

431   return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));

458   abi.encode(

468   return keccak256(abi.encode(_storedBatchInfo));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L265

```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

363     bytes memory transactionEncoding = abi.encode(transaction);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L363


```solidity
file: ethereum/contracts/bridge/L1ERC20Bridge.sol

119   l2BridgeProxyConstructorData = abi.encode(bridgeImplementationAddr, _governor, proxyInitializationParams);

244   data = abi.encode(data1, data2, data3);

354   bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L119

```solidity
file:  ethereum/contracts/bridge/L1WethBridge.sol

118   l2WethBridgeProxyConstructorData = abi.encode(

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L118

```solidity
file:   ethereum/contracts/governance/Governance.sol

205     return keccak256(abi.encode(_operation));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L205

```solidity
file:  ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol
 
173     bytes memory encodedTransaction = abi.encode(_l2ProtocolUpgradeTx);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L173

## [G-07] Avoid contract existence checks by using low level calls

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence.

```solidity
file: contracts/zksync/libraries/Diamond.sol

213   uint16 selectorPosition = (ds.facetToSelectors[_facet].selectors.length).toUint16();

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L213

## [G-08] Expensive operation inside a for loop

```solidity
file:  ethereum/contracts/zksync/facets/Executor.sol

293     for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
            _executeOneBatch(_batchesData[i], i);
            emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L293-L296


```solidity
file:  ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

204  for (uint256 i = 0; i < _factoryDeps.length; ++i) {
            require(
                L2ContractHelper.hashL2Bytecode(_factoryDeps[i]) == bytes32(_expectedHashes[i]),
                "Wrong factory dep hash"
            );
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204-L209

```solidity
file:  ethereum/contracts/zksync/facets/Getters.sol

182   for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
            address facetAddr = ds.facets[i];
            Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];

            result[i] = Facet(facetAddr, facetToSelectors.selectors);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L182-L187


```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

100   for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
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


## [G-09] Don’t cache a state variable if it’s used only once

### caching state variable which is used only once inside the functin gas value: 663101
### using the state variable which is used only once inside the functin gas value: 661583

### Tools Used Remix

### Do not cache the hasSpecialAccessToCall[_caller][_target][_functionSig]; variable  they used only once inside the function 


```solidity
file: ethereum/contracts/common/AllowList.sol

117   bool currentPermission = hasSpecialAccessToCall[_caller][_target][_functionSig];

        if (currentPermission != _enable) {
            hasSpecialAccessToCall[_caller][_target][_functionSig] = _enable;
            emit UpdateCallPermission(_caller, _target, _functionSig, _enable);
        }
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L117-L123

### Do not cache the executionDelay variable and also zkSyncContract variable they used only once inside the function 

```solidity
file: ethereum/contracts/zksync/ValidatorTimelock.sol

114    uint256 delay = executionDelay; // uint32
        unchecked {
            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
                uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);

                // Note: if the `commitBatchTimestamp` is zero, that means either:
                // * The batch was committed, but not through this contract.
                // * The batch wasn't committed at all, so execution will fail in the zkSync contract.
                // We allow executing such batches.
                require(block.timestamp >= commitBatchTimestamp + delay, "5c"); // The delay is not passed
            }
        }

133   address contractAddress = zkSyncContract;
        assembly {
            // Copy function signature and arguments from calldata at zero position into memory at pointer position
            calldatacopy(0, 0, calldatasize())
            // Call method of the zkSync contract returns 0 on error
            let result := call(gas(), contractAddress, 0, 0, calldatasize(), 0, 0)
            // Get the size of the last return data
            let size := returndatasize()
            // Copy the size length of bytes 


```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L114-L125

### Do not cache the  DIAMOND_STORAGE_POSITION variable they used only once inside the function 


```solidity
file: ethereum/contracts/zksync/libraries/Diamond.sol

86    function getDiamondStorage() internal pure returns (DiamondStorage storage diamondStorage) {
        bytes32 position = DIAMOND_STORAGE_POSITION;
        assembly {
            diamondStorage.slot := position
        }
    }


```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L86-L91

## [G-10] The creation of an intermediary array can be avoided

Sometimes, it’s not necessary to create an intermediate array to store values.


```solidity
file:  ethereum/contracts/zksync/facets/Getters.sol
 
176   function facets() external view returns (Facet[] memory result) {

191   function facetFunctionSelectors(address _facet) external view returns (bytes4[] memory) {

197   function facetAddresses() external view returns (address[] memory) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L176


```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

391   returns (uint256[] memory hashedFactoryDeps)

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L391 


```solidity
file: ethereum/contracts/zksync/interfaces/IGetters.sol

72    function facets() external view returns (Facet[] memory);

74    function facetFunctionSelectors(address _facet) external view returns (bytes4[] memory);

76    function facetAddresses() external view returns (address[] memory facets);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L72

## [G-11] Use Assembly To Check For address(0)

It’s generally more gas-efficient to use assembly to check for a zero address (address(0)) than to use the == operator.

The reason for this is that the == operator generates additional instructions in the EVM bytecode, which can increase the gas cost of your contract. By using assembly, you can perform the zero address check more efficiently and reduce the overall gas cost of your contract.

Here’s an example of how you can use assembly to check for a zero address:


```solidity

contract MyContract {
    function isZeroAddress(address addr) public pure returns (bool) {
        uint256 addrInt = uint256(addr);
        
        assembly {
            // Load the zero address into memory
            let zero := mload(0x00)
            
            // Compare the address to the zero address
            let isZero := eq(addrInt, zero)
            
            // Return the result
            mstore(0x00, isZero)
            return(0, 0x20)
        }
    }
}

```
In the above example, we have a function isZeroAddress that takes an address as input and returns a boolean value, indicating whether the address is equal to the zero address. Inside the function, we convert the address to an integer using uint256(addr), and then use assembly to compare the integer to the zero address.

By using assembly to perform the zero address check, we can make our code more gas-efficient and reduce the overall cost of our contract. It’s important to note that assembly can be more difficult to read and maintain than Solidity code, so it should be used with caution and only when necessary

```solidity
file: ethereum/contracts/bridge/L1ERC20Bridge.sol

90   require(_l2TokenBeacon != address(0), "nf");

91   require(_governor != address(0), "nh");

151  l2TxHash = deposit(_l2Receiver, _l1Token, _amount, _l2TxGasLimit, _l2TxGasPerPubdataByte, address(0));

195  if (_refundRecipient == address(0)) {


```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L90

```solidity
file: ethereum/contracts/bridge/L1WethBridge.sol

88    require(_l2WethAddress != address(0), "L2 WETH address cannot be zero");

89    require(_governor != address(0), "Governor address cannot be zero");

182   if (_refundRecipient == address(0)) {

303   l2Token = _l1Token == l1WethAddress ? l2WethAddress : address(0);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L88


```solidity
file: ethereum/contracts/governance/Governance.sol

42    require(_admin != address(0), "Admin should be non zero address");

47    emit ChangeSecurityCouncil(address(0), _securityCouncil);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L42

```solidity
file:  ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol
  
116    if (_newVerifier == IVerifier(address(0))) {

235    if (_newAllowList == IAllowList(address(0))) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L116

```solidity
file: ethereum/contracts/zksync/DiamondInit.sol

56   require(address(_initalizeData.verifier) != address(0), "vt");

57   require(_initalizeData.governor != address(0), "vy");

58   require(_initalizeData.admin != address(0), "hc");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L56

```solidity
file: ethereum/contracts/zksync/DiamondProxy.sol

30    require(facetAddress != address(0), "F"); // Proxy has no facet for this selector

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L30

```solidity
file: ethereum/contracts/zksync/facets/Admin.sol

37    emit NewPendingGovernor(pendingGovernor, address(0));

61   emit NewPendingAdmin(pendingAdmin, address(0));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L37

```solidity
file:  ethereum/contracts/zksync/facets/Getters.sol

160   require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L160\


```solidity
file:   ethereum/contracts/zksync/facets/Mailbox.sol

276     IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH

310   address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L276


```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

132    require(_facet != address(0), "G"); // facet with zero address cannot be added

141    require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists

156    require(_facet != address(0), "K"); // cannot replace facet with zero address

162    require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address

176    require(_facet == address(0), "a1"); // facet address must be zero

182    require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet

284    if (_init == address(0)) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L132

## [G-12] Gas saving is achieved by removing the delete keyword (~60k)

30k gas savings were made by removing the delete keyword. The reason for using the delete keyword here is to reset the struct values (set to default value) in every operation. However, the struct values do not need to be zero each time the function is run. Therefore, the delete keyword is unnecessary. If it is removed, around 30k gas savings will be achieved.


```solidity
file:  ethereum/contracts/zksync/facets/Admin.sol

35   delete s.pendingGovernor;

59   delete s.pendingAdmin;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L35


```solidity
file:  ethereum/contracts/zksync/facets/Executor.sol

304    delete s.l2SystemContractsUpgradeTxHash;

305    delete s.l2SystemContractsUpgradeBatchNumber;

410    delete s.l2SystemContractsUpgradeBatchNumber;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L304

## [G-13] >= costs less gas than >

The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas.

```solidity
file: ethereum/contracts/governance/Governance.sol

111  } else if (timestamp > block.timestamp) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L111

```solidity
file:  ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

217    _newProtocolVersion > previousProtocolVersion,

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L217

```solidity
file:  ethereum/contracts/zksync/facets/Executor.sol

399   require(s.totalBatchesCommitted > _newLastBatch, "v1");

409   if (s.l2SystemContractsUpgradeBatchNumber > _newLastBatch) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L399

## [G-14] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time the variable is used, which wastes some gas.

```solidity
file:  ethereum/contracts/zksync/Config.sol

14    uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

26    uint256 constant MAX_INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE * 4765;

33    uint256 constant MAX_REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE * 7564;

54  uint256 constant COMMIT_TIMESTAMP_NOT_OLDER = $$(
55        defined(COMMIT_TIMESTAMP_NOT_OLDER) ? COMMIT_TIMESTAMP_NOT_OLDER : "365 days"
56  );

60  uint256 constant COMMIT_TIMESTAMP_APPROXIMATION_DELTA = $$(
61        defined(COMMIT_TIMESTAMP_APPROXIMATION_DELTA) ? COMMIT_TIMESTAMP_APPROXIMATION_DELTA : "365 days"
62   );

65  uint256 constant INPUT_MASK = $$(~uint256(0) >> 32);

92  uint256 constant BATCH_OVERHEAD_PUBDATA = BATCH_OVERHEAD_L1_GAS / L1_GAS_PER_PUBDATA_BYTE;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L14

```solidity
File: contracts/vendor/AddressAliasHelper.sol

22    uint160 constant offset = uint160(0x1111000000000000000000000000000000001111);

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L22



## [G-15] Loop best practice to save gas

```solidity
function Plusi() public view {
		for(uint i=0; i<10;) {
			console.log("Number ==",i);
			unchecked{
				++i;
			}
		}
	}
best practice
-----------------------------------------------------
for (uint i = 0; i < length; i = unchecked_inc(i)) {
    // do something that doesn't change the value of i
}
function unchecked_inc(uint i) returns (uint) {
    unchecked {
        return i + 1;
    }
}
```

```solidity
file: ethereum/contracts/zksync/ValidatorTimelock.sol

85   for (uint256 i = 0; i < _newBatchesData.length; ++i) {

116  for (uint256 i = 0; i < _newBatchesData.length; ++i) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L85

```solidity
file:  ethereum/contracts/governance/Governance.sol

225   for (uint256 i = 0; i < _calls.length; ++i) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L225

```solidity
file: ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

204   for (uint256 i = 0; i < _factoryDeps.length; ++i) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204

## [G-16] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct.

```solidity
file: ethereum/contracts/zksync/DiamondInit.sol

66  IExecutor.StoredBatchInfo memory storedBatchZero = IExecutor.StoredBatchInfo(

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L66

```solidity
file: ethereum/contracts/zksync/DiamondProxy.sol

27   Diamond.SelectorToFacet memory facet = diamondStorage.selectorToFacet[msg.sig];

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L27

```solidity
file: ethereum/contracts/zksync/facets/Executor.sol

28   StoredBatchInfo memory _previousBatch,

114  bytes memory emittedL2Logs = _newBatch.systemLogs;

206  StoredBatchInfo memory _lastCommittedBatchData,

226  StoredBatchInfo memory _lastCommittedBatchData,

264  PriorityOperation memory priorityOp = s.priorityQueue.popFront();

273  StoredBatchInfo memory _storedBatch,

321  VerifierParams memory verifierParams = s.verifierParams;

324  uint256[] memory proofPublicInput = new uint256[](committedBatchesLength);

379  VerifierParams memory _verifierParams

467  StoredBatchInfo memory _storedBatchInfo

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L28


```solidity
file:  ethereum/contracts/zksync/facets/Getters.sol

184   Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L184


```solidity
file: contracts/zksync/facets/Mailbox.sol

52   L2Message memory _message,

67   L2Log memory _log,

100  L2Log memory l2Log = L2Log({

149  L2Message memory _message

201  L2Message memory l2ToL1Message = L2Message({

276  IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH

299  WritePriorityOpParams memory params;

330   WritePriorityOpParams memory _priorityOpParams,

357   WritePriorityOpParams memory _priorityOpParams,

361   L2CanonicalTransaction memory transaction = _serializeL2Transaction(_priorityOpParams, _calldata, _factoryDeps);

363   bytes memory transactionEncoding = abi.encode(transaction);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L52


```solidity
file: ethereum/contracts/zksync/libraries/Diamond.sol

96   FacetCut[] memory facetCuts = _diamondCut.facetCuts;

98   bytes memory initCalldata = _diamondCut.initCalldata;

104  bytes4[] memory selectors = facetCuts[i].selectors;

140  SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

161  SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

181  SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];


```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L96

```solidity
file: ethereum/contracts/bridge/L1ERC20Bridge.sol

341   IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token)

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L341



## [G‑17] Counting down in for statements is more gas efficient

Counting down is more gas efficient than counting up because neither we are making zero variable to non-zero variable and also we will get gas refund in the last transaction when making non-zero to zero variable.

by changing this logic you can save 12171 gas per one for loop 

Tools used Remix

```solidity
file: contracts/zksync/ValidatorTimelock.sol

85    for (uint256 i = 0; i < _newBatchesData.length; ++i) {

116   for (uint256 i = 0; i < _newBatchesData.length; ++i) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L85

```solidity
file: ethereum/contracts/zksync/facets/Executor.sol

123    for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

209    for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

241    for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

263    for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

293    for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {

330    for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {]

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L123

```solidity
file:  ethereum/contracts/zksync/facets/Getters.sol

182     for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L182

```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

395   for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L395

```solidity
file: ethereum/contracts/zksync/libraries/Diamond.sol

100   for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {

138   for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

159   for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

179   for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L100

```solidity
file:  ethereum/contracts/zksync/libraries/Merkle.sol

29    for (uint256 i; i < pathLength; i = i.uncheckedInc()) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L29

```solidity
file: ethereum/contracts/common/AllowList.sol

62   for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

96   for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L62

```solidity
file:  ethereum/contracts/governance/Governance.sol

225   for (uint256 i = 0; i < _calls.length; ++i) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L225

```solidity
file: ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

204   for (uint256 i = 0; i < _factoryDeps.length; ++i) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204

### Test Code

```solidity
contract GasTest is DSTest {
    Contract0 c0;
    Contract1 c1;
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    function testGas() public {
        c0.AddNum();
        c1.AddNum();
    }
}
contract Contract0 {
    uint256 num = 3;
    function AddNum() public {
        uint256 _num = num;
        for(uint i=0;i<=9;i++){
            _num = _num +1;
        }
        num = _num;
    }
}
contract Contract1 {
    uint256 num = 3;
    function AddNum() public {
        uint256 _num = num;
        for(uint i=9;i>=0;i--){
            _num = _num +1;
        }
        num = _num;
    }
}
```

## [G‑18] Use do while loops instead of for loops

A do while loop will cost less gas since the condition is not being checked for the first iteration.



```solidity
File: contracts/common/AllowList.sol

62        for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

96        for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L62

```solidity
File: contracts/upgrades/BaseZkSyncUpgrade.sol

204        for (uint256 i = 0; i < _factoryDeps.length; ++i) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204

```solidity
File: contracts/zksync/libraries/Diamond.sol

100        for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {

138        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

159        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

179        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L100

```solidity
File: contracts/zksync/facets/Executor.sol

123        for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

209        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

241        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

263        for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

293        for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {

330        for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L123

```solidity
File: contracts/zksync/facets/Getters.sol

182        for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L182

```solidity
File: contracts/governance/Governance.sol

225        for (uint256 i = 0; i < _calls.length; ++i) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L225

```solidity
File: contracts/zksync/facets/Mailbox.sol

395        for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L395

```solidity
File: contracts/zksync/libraries/Merkle.sol

29        for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L29

```solidity
File: contracts/zksync/ValidatorTimelock.sol

85            for (uint256 i = 0; i < _newBatchesData.length; ++i) {

116            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L85



## [G-19] Create immutable variable to avoid an external call

Instead of performing an external call to get the root address each time _enableNode is invoked, we can perform this external call once in the constructor and store the root as an immutable variable. Doing this will save 1 external call each time _enableNode is invoked.


```solidity
file: ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

120    IVerifier oldVerifier = s.verifier;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L120

```soldiity
file: ethereum/contracts/zksync/facets/Executor.sol

321    VerifierParams memory verifierParams = s.verifierParams;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L321


```soldity
file: ethereum/contracts/zksync/facets/Mailbox.sol

52    L2Message memory _message,

67    L2Log memory _log,

88    TxStatus _status

201   L2Message memory l2ToL1Message = L2Message({

299   WritePriorityOpParams memory params;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L52

```solidity
file:  ethereum/contracts/bridge/L1ERC20Bridge.sol

302    L2Message memory l2ToL1Message = L2Message({

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L302

```solidity
file: ethereum/contracts/common/AllowList.sol

40   AccessMode accessMode = getAccessMode[_target];

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L40

## [G-20] Save loop calls


### Instead of calling _batchesData[i] 4 times in each loop for fetching data, it can be saved as a variable.

```solidity
file: ethereum/contracts/zksync/facets/Executor.sol
 
293   for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
            _executeOneBatch(_batchesData[i], i);
            emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
        }
    
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L293-296

### Instead of calling facetCuts[i] 4 times in each loop for fetching data, it can be saved as a variable.


```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

100  for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
            Action action = facetCuts[i].action;
            address facet = facetCuts[i].facet;
            bool isFacetFreezable = facetCuts[i].isFreezable;
            bytes4[] memory selectors = facetCuts[i].selectors;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L100-104

### Instead of calling _calls[i] 3 times in each loop for fetching data, it can be saved as a variable.

```solidity
file:  ethereum/contracts/governance/Governance.sol

225    for (uint256 i = 0; i < _calls.length; ++i) {
        (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L225-L226

### Instead of calling _committedBatches[i] 2 times in each loop for fetching data, it can be saved as a variable.

```solidity
file: ethereum/contracts/zksync/facets/Executor.sol

330   for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
            currentTotalBatchesVerified = currentTotalBatchesVerified.uncheckedInc();
            require(
                _hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],
                "o1"
            );

            bytes32 currentBatchCommitment = _committedBatches[i].commitment;
            proofPublicInput[i] = _getBatchProofPublicInput(
                prevBatchCommitment,
                currentBatchCommitment,
                verifierParams
            );

            prevBatchCommitment = currentBatchCommitment;
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L330-345

### Instead of calling _path[i] 2 times in each loop for fetching data, it can be saved as a variable.

```solidity 
file: ethereum/contracts/zksync/libraries/Merkle.sol

29   for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
            currentHash = (_index % 2 == 0)
                ? _efficientHash(currentHash, _path[i])
                : _efficientHash(_path[i], currentHash);
            _index /= 2;
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L29-L34


 
## [G-21] Use assembly to validate msg.sender

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

```solidity
file: ethereum/contracts/bridge/L1WethBridge.sol

311   require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L311

```solidity
file: ethereum/contracts/governance/Governance.sol

59    require(msg.sender == address(this), "Only governance contract itself allowed to call this function");

65    require(msg.sender == securityCouncil, "Only security council allowed to call this function");

72     msg.sender == owner() || msg.sender == securityCouncil,

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L59

## [G-22] Expensive operation inside a for loop

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

```solidity
file:  ethereum/contracts/common/AllowList.sol

96   for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
            _setPermissionToCall(_callers[i], _targets[i], _functionSigs[i], _enables[i]);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L96-L98

## [G-23] Use assembly to perform efficient back-to-back calls

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

## [G-24] Use assembly in place of abi.decode to extract calldata values more efficiently

Instead of using abi.decode, we can use assembly to decode our desired calldata values directly. This will allow us to avoid decoding calldata values that we will not use.

```solidity

file:  contracts/ethereum/contracts/zksync/libraries/Diamond.sol


303   require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");


```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L303




## [G-25] The function should first verify if msg.value >= baseCost + _l2Value before performing any other operation (Saves 7146 Gas)

In case of a revert on msg.value == 0 we save 7146 gas on average from the protocol gas tests (to simulate the sad path ie msg.value == 0 see the test modification).


```solidity
file:    contracts/ethereum/contracts/zksync/facets/Mailbox.sol

306      require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L306

## [G-26] public functions not called by the contract should be declared external instead

when a function is declared as public, it is generated with an internal and an external interface. This means the function can be called both internally (within the contract) and externally (by other contracts or accounts). However, if a public function is never called internally and is only expected to be invoked externally, it is more gas-efficient to explicitly declare it as external.

```solidity
file: ethereum/contracts/bridge/L1ERC20Bridge.sol

353   function l2TokenAddress(address _l1Token) public view returns (address) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L353

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

```solidity
file:  ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol
 
67   function upgrade(ProposedUpgrade calldata _proposedUpgrade) public virtual returns (bytes32) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L67

```solidity
file:  ethereum/contracts/bridge/L1WethBridge.sol

302    function l2TokenAddress(address _l1Token) public view override returns (address l2Token) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L302

## [G-27] Use constants instead of type(uintx).max

using type(uintx).max can result in higher gas costs because it involves a runtime operation to calculate the maximum value at runtime. This calculation is performed every time the expression is evaluated.

To save gas, it is recommended to use constants instead of type(uintx).max to represent the maximum value. By declaring a constant with the maximum value, the value is known at compile-time and does not require any runtime calculations.

```solidity
file:  ethereum/contracts/zksync/libraries/TransactionValidator.sol

50    require(_transaction.from <= type(uint16).max, "ua");

51    require(_transaction.to <= type(uint160).max, "ub");

55    require(_transaction.reserved[1] <= type(uint160).max, "uf");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L50


## [G-28] uint256 variable initialization to default value of 0 can be omitted 

There is no need to initialize variables to their default values during declaration, since they are any way initialized to default value once declared.

```solidity
file:  ethereum/contracts/bridge/L1ERC20Bridge.sol

315    isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex] = true;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L315

```solidity
file: ethereum/contracts/bridge/L1WethBridge.sol

265   isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex] = true;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L265

```solidity
file:  ethereum/contracts/common/AllowList.sol

62     for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

96     for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L62

```solidity
file: ethereum/contracts/governance/Governance.sol

225   for (uint256 i = 0; i < _calls.length; ++i) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L225

```solidity
file: ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

204   for (uint256 i = 0; i < _factoryDeps.length; ++i) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204

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

## [G-29] The result of function calls should be cached rather than re-calling the function

Function uncheckedInc() is being called 2 times. Firstly, in the for loop  (line 330), then inside the for loop 


```solidity
file:  ethereum/contracts/zksync/facets/Executor.sol

330        for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
            currentTotalBatchesVerified = currentTotalBatchesVerified.uncheckedInc();
            require(

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L330-L332

Function hashL2Bytecode(_factoryDeps[2]) is being called 2 times. in line 99, and laso line 100  

```solidity
file: ethereum/contracts/bridge/L1ERC20Bridge.sol

96   l2TokenProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[2]);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#l96

Function readAddress(_l2ToL1message, offset) is being called 2 times. in line 335, and laso line 336  

```solidity
file: ethereum/contracts/bridge/L1ERC20Bridge.sol

334   (l1Receiver, offset) = UnsafeBytes.readAddress(_l2ToL1message, offset);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#l334

Function readAddress(_message, offset) is being called 2 times. in line 294, and laso line 298  

```solidity
file: ethereum/contracts/bridge/L1WethBridge.sol
 
288   (l1EthReceiver, offset) = UnsafeBytes.readAddress(_message, offset);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L288

## [G-30] OR in if/require condataion can be rewritten to two single if/require conditions

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


```solidity
file:  ethereum/contracts/bridge/L1WethBridge.sol

311    require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L311

```solidity
file:  ethereum/contracts/common/AllowList.sol

41  return
            accessMode == AccessMode.Public ||
            (accessMode == AccessMode.SpecialAccessOnly && hasSpecialAccessToCall[_caller][_target][_functionSig]);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L41-L43

```solidity
file:  ethereum/contracts/governance/Governance.sol

71  require(
            msg.sender == owner() || msg.sender == securityCouncil,
            "Only the owner and security council are allowed to call this function"
        );

91   return state == OperationState.Waiting || state == OperationState.Ready;

240  require(_predecessorId == bytes32(0) || isOperationDone(_predecessorId), "Predecessor operation not completed");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L71-L73

```solidity
file: ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

128   if (
            _newVerifierParams.recursionNodeLevelVkHash == bytes32(0) ||
            _newVerifierParams.recursionLeafLevelVkHash == bytes32(0) ||
            _newVerifierParams.recursionCircuitsSetVksHash == bytes32(0)
        ) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L128-L132

## [G-31] Use assembly to write address storage values

```solidity
file:  ethereum/contracts/governance/Governance.sol

46     securityCouncil = _securityCouncil;

258    securityCouncil = _newSecurityCouncil;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L46

```solidity
file:  ethereum/contracts/zksync/ValidatorTimelock.sol

48  validator = _validator;

54  validator = _newValidator;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L48

```solidity
file: ethereum/contracts/bridge/L1ERC20Bridge.sol
 
97   l2TokenBeacon = _l2TokenBeacon;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L97

```solidity
file: ethereum/contracts/bridge/L1WethBridge.sol

96    l2WethAddress = _l2WethAddress;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L96

## [G-32] Cache state variables outside of loop to avoid reading/writing storage on every iteration

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


## [G-33] Cache calldata/memory pointers for complex types to avoid offset calculations

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

## [G-34] Combine events to save 2 Glogtopic (375 gas)

The events below are only emitted once in the handleRewards function. We can combine the events into one singular event to save two Glogtopic (375 gas) that would otherwise be paid for the additional two events.

```solidity
file:  ethereum/contracts/zksync/facets/Admin.sol
 
37     emit NewPendingGovernor(pendingGovernor, address(0));
38     emit NewGovernor(previousGovernor, pendingGovernor);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L37-38

## [G-35] Duplicated require()/revert() checks should be refactored to a modifier or function

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

## [G-36] Failure to check the zero address in the constructor causes the contract to be deployed again

Zero address control is not performed in the constructor in all 3 contracts within the scope of the audit. Bypassing this check could cause the contract to be deployed by mistakenly entering a zero address. In this case, the contract will need to be redeployed. This means extra gas consumption as contract deployment costs are high.

```solidity
file: ethereum/contracts/common/AllowList.sol

31   constructor(address _initialOwner) {
        _transferOwnership(_initialOwner);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L31-L33

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

```solidity
file: ethereum/contracts/bridge/L1WethBridge.sol

64    constructor(address payable _l1WethAddress, IZkSync _zkSync, IAllowList _allowList) reentrancyGuardInitializer {
        l1WethAddress = _l1WethAddress;
        zkSync = _zkSync;
        allowList = _allowList;
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L64-L68


## [G-37] bytes constants are more eficient than string constans

If the data can fit in 32 bytes, the bytes32 data type can be used instead of bytes or strings, as it is less robust in terms of robustness.

### Using of string constant gas value: 711113
### Using of bytes constant gas value: 685782

### Tools Used Remix 

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

## [G-38] Not using the named return variable when a function returns, wastes deployment gas only in view function 

### Tools used remix

berfore vast of gas: 115075
after of removing of they name vast of gas: 115048

```solidity
file:  ethereum/contracts/bridge/L1ERC20Bridge.sol

230    ) internal view returns (bytes memory txCalldata) {

240    function _getERC20Getters(address _token) internal view returns (bytes memory data) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L230

```solidity
file:  ethereum/contracts/bridge/L1WethBridge.sol

274   ) internal view returns (address l1WethReceiver, uint256 ethAmount) {

302   function l2TokenAddress(address _l1Token) public view override returns (address l2Token) {
    
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L274

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

## [G-39] Caching global variables is more expensive than using the actual variable (use msg.sender or block.timestamp instead of caching it)


```solidity
file: ethereum/contracts/zksync/facets/Mailbox.sol

247    address sender = msg.sender;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L247


## [G-40] Make 3 event parameters indexed when possible

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

```solidity
file:  ethereum/contracts/bridge/L1WethBridge.sol

41   event EthReceived(uint256 amount);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L41

```solidity
file: ethereum/contracts/governance/IGovernance.sol

65   event TransparentOperationScheduled(bytes32 indexed _id, uint256 delay, Operation _operation);

68   event ShadowOperationScheduled(bytes32 indexed _id, uint256 delay);

74  event ChangeSecurityCouncil(address _securityCouncilBefore, address _securityCouncilAfter);

77  event ChangeMinDelay(uint256 _delayBefore, uint256 _delayAfter);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L65


## [G-41] It is sometimes cheaper to cache calldata

```solidity
File: contracts/common/AllowList.sol

58    function setBatchAccessMode(address[] calldata _targets, AccessMode[] calldata _accessModes) external onlyOwner {



83    function setBatchPermissionToCall(
84        address[] calldata _callers,
85        address[] calldata _targets,
86        bytes4[] calldata _functionSigs,
87        bool[] calldata _enables
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L58

```solidity
file: contracts/zksync/facets/Executor.sol

177    function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)

207        CommitBatchInfo[] calldata _newBatchesData

227        CommitBatchInfo[] calldata _newBatchesData,

291    function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {

313        StoredBatchInfo[] calldata _committedBatches,
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L177

```solidity
File: contracts/upgrades/BaseZkSyncUpgrade.sol

163        bytes[] calldata _factoryDeps,

200    function _verifyFactoryDeps(bytes[] calldata _factoryDeps, uint256[] calldata _expectedHashes) private pure {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L163

```solidity
File: contracts/common/interfaces/IAllowList.sol

58    function setBatchAccessMode(address[] calldata _targets, AccessMode[] calldata _accessMode) external;


63        address[] calldata _callers,
64        address[] calldata _targets,
65        bytes4[] calldata _functionSigs,
66        bool[] calldata _enables
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol#L58C5-L58C13

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

84        bytes[] calldata _factoryDeps,

262        bytes32[] calldata _merkleProof

298        bytes32[] calldata _merkleProof
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L84

```solidity
File: contracts/bridge/L1WethBridge.sol

82        bytes[] calldata _factoryDeps,

221        bytes32[] calldata // _merkleProof

238        bytes32[] calldata _merkleProof
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L82


## [G-42] Should use arguments instead of state variable

state variables should not used in emit  ,  This will save near 97 gas   

```solidity
File: contracts/bridge/L1WethBridge.sol

267        emit WithdrawalFinalized(l1WethWithdrawReceiver, l1WethAddress, amount);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L267

```solidity
File: contracts/zksync/facets/Executor.sol

317        emit BlocksVerification(s.totalBatchesVerified, currentTotalBatchesVerified);

413        emit BlocksRevert(s.totalBatchesCommitted, s.totalBatchesVerified, s.totalBatchesExecuted);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L413

```solidity
File: contracts/governance/Governance.sol

250        emit ChangeMinDelay(minDelay, _newDelay);

257        emit ChangeSecurityCouncil(securityCouncil, _newSecurityCouncil);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L250C1-L251C1

## [G-43] Private functions used once can be inlined

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```solidity
File: contracts/upgrades/BaseZkSyncUpgrade.sol

77    function _setL2DefaultAccountBytecodeHash(bytes32 _l2DefaultAccountBytecodeHash) private {

94    function _setL2BootloaderBytecodeHash(bytes32 _l2BootloaderBytecodeHash) private {

111    function _setVerifier(IVerifier _newVerifier) private {

127    function _setVerifierParams(VerifierParams calldata _newVerifierParams) private {

200    function _verifyFactoryDeps(bytes[] calldata _factoryDeps, uint256[] calldata _expectedHashes) private pure {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L77C14-L77C47

```solidity
File: contracts/zksync/libraries/Diamond.sol

125    function _addFunctions(

149    function _replaceFunctions(

173    function _removeFunctions(address _facet, bytes4[] memory _selectors) private {

262    function _removeFacet(address _facet) private {

283    function _initializeDiamondCut(address _init, bytes memory _calldata) private {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L125

```solidity
File: contracts/common/ReentrancyGuard.sol

46    function _initializeReentrancyGuard() private {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L46

## [G-44] Use Mappings instead of arrays

When we say "use mappings instead of arrays," we mean that when storing and accessing data in a smart contract, it's more gas-efficient to use mappings instead of arrays. This is because mappings do not require iteration to find a specific element, whereas arrays do.

```solidity
File: contracts/zksync/libraries/Diamond.sol

39        bytes4[] selectors;

52        address[] facets;

65        bytes4[] selectors;

73        FacetCut[] facetCuts;

96        FacetCut[] memory facetCuts = _diamondCut.facetCuts;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L39

```solidity
File: contracts/zksync/interfaces/IExecutor.sol

75        uint256[] recursiveAggregationInput;

76        uint256[] serializedProof;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol#L75C1-L76C35

```solidity
File: contracts/zksync/interfaces/IGetters.sol

69        bytes4[] selectors;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L69

```solidity
File: contracts/governance/IGovernance.sol

33        Call[] calls;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L33

```solidity
File: contracts/upgrades/BaseZkSyncUpgrade.sol

33        bytes[] factoryDeps;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L33


## [G-45] Pre-increment and pre-decrement are cheaper than +1 ,-1

```solidity
File: contracts/zksync/facets/Executor.sol

32        require(_newBatch.batchNumber == _previousBatch.batchNumber + 1, "f"); // only commit next batch

275        require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k"); // Execute batches in order
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L32

```solidity
File: contracts/zksync/libraries/PriorityQueue.sol

60        _queue.tail = tail + 1;

80        _queue.head = head + 1;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L60

```solidity
File: contracts/common/libraries/UncheckedMath.sol

13            return _number + 1;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UncheckedMath.sol#L13

```solidity
File: contracts/zksync/libraries/Diamond.sol

238        uint256 lastSelectorPosition = ds.facetToSelectors[_facet].selectors.length - 1;

267        uint256 lastFacetPosition = ds.facets.length - 1;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L238

```solidity
File: contracts/zksync/facets/Mailbox.sol

181        uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L181

```solidity
File: contracts/zksync/libraries/TransactionValidator.sol

165            overheadForGas = (numerator - 1) / denominator;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L165


## [G-46] Use bytes.concat() instead of abi.encodePacked(), since this is preferred since 0.8.4

```solidity
File: contracts/zksync/facets/Mailbox.sol

132            abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBatch, _log.sender, _log.key, _log.value)

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L132

```solidity
File: contracts/zksync/facets/Executor.sol

384                    abi.encodePacked(

436            abi.encodePacked(

445        return abi.encodePacked(s.zkPorterIsAvailable, s.l2BootloaderBytecodeHash, s.l2DefaultAccountBytecodeHash);

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L384


## [G-47] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```solidity
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 
//Sort operations with different gas costs as follows
 f(x) || g(y) 
 f(x) && g(y)
```
Instances:

```solidity
File: contracts/common/AllowList.sol

42            accessMode == AccessMode.Public ||
43            (accessMode == AccessMode.SpecialAccessOnly && hasSpecialAccessToCall[_caller][_target][_functionSig]);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L42

```solidity
File: contracts/zksync/facets/Base.sol

25        require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L25

```solidity
File: contracts/upgrades/BaseZkSyncUpgrade.sol

128        if (
129            _newVerifierParams.recursionNodeLevelVkHash == bytes32(0) ||
130            _newVerifierParams.recursionLeafLevelVkHash == bytes32(0) ||
131            _newVerifierParams.recursionCircuitsSetVksHash == bytes32(0)
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L128C1-L131C73

```solidity
File: contracts/zksync/DiamondProxy.sol

25        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");

31        require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L25

```solidity
File: contracts/zksync/facets/Executor.sol

189        if (systemContractsUpgradeTxHash == bytes32(0) || s.l2SystemContractsUpgradeBatchNumber != 0) {

303        if (batchWhenUpgradeHappened != 0 && batchWhenUpgradeHappened <= newTotalBatchesExecuted) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L189

```solidity
File: contracts/governance/Governance.sol

72            msg.sender == owner() || msg.sender == securityCouncil,

91        return state == OperationState.Waiting || state == OperationState.Ready;

240        require(_predecessorId == bytes32(0) || isOperationDone(_predecessorId), "Predecessor operation not completed");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L72

```solidity
File: contracts/bridge/L1WethBridge.sol

311        require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L311


## [G-48] Onlyowner no uses of the nonReentrant modifier

```solidity
File: contracts/bridge/L1WethBridge.sol

166    ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {

239    ) external nonReentrant senderCanCallFunction(allowList) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L166

```solidity
File: contracts/zksync/facets/Executor.sol

177    function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)
178        external
179        override
180        nonReentrant
181        onlyValidator
    {


291    function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {


315    ) external nonReentrant onlyValidator {


398    function revertBatches(uint256 _newLastBatch) external nonReentrant onlyValidator {

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L177C1-L182C6

```solidity
File: contracts/zksync/facets/Mailbox.sol

198    ) external override nonReentrant senderCanCallFunction(s.allowList) {


244    ) external payable nonReentrant senderCanCallFunction(s.allowList) returns (bytes32 canonicalTxHash) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L198

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

183    ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) {

263    ) external nonReentrant senderCanCallFunction(allowList) {

299    ) external nonReentrant senderCanCallFunction(allowList) {

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L183


## [G-49] Not using the named return variable when a function returns, wastes deployment gas

When you execute a function that returns values in Solidity, the EVM still performs the necessary operations to execute and return those values. This includes the cost of allocating memory and packing the return values. If the returned values are not utilized, it can be seen as wasteful since you are incurring gas costs for operations that have no effect.

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

221        return balanceAfter - balanceBefore;

257        return L2ContractHelper.computeCreate2Address(l2Bridge, salt, l2TokenProxyBytecodeHash, constructorInputHash);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L221

```solidity
File: contracts/governance/Governance.sol

85        return getOperationState(_id) != OperationState.Unset;

91        return state == OperationState.Waiting || state == OperationState.Ready;

96        return getOperationState(_id) == OperationState.Ready;

101        return getOperationState(_id) == OperationState.Done;

108            return OperationState.Unset;

110            return OperationState.Done;

112            return OperationState.Waiting;

114            return OperationState.Ready;

205        return keccak256(abi.encode(_operation));
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L85

```solidity
File: contracts/zksync/facets/Executor.sol

418        return a < b ? b : a;

431        return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));

445        return abi.encodePacked(s.zkPorterIsAvailable, s.l2BootloaderBytecodeHash, s.l2DefaultAccountBytecodeHash);

468        return keccak256(abi.encode(_storedBatchInfo));

473        return (_bitMap & (1 << _index)) > 0;

478        return _bitMap | (1 << _index);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L418

```solidity
File: contracts/zksync/facets/Mailbox.sol

55        return _proveL2LogInclusion(_batchNumber, _index, _L2MessageToLog(_message), _proof);

70        return _proveL2LogInclusion(_batchNumber, _index, _log, _proof);

108        return _proveL2LogInclusion(_l2BatchNumber, _l2MessageIndex, l2Log, _merkleProof);

145        return actualRootHash == calculatedRootHash;

172        return l2GasPrice * _l2GasLimit;

183        return Math.max(FAIR_L2_GAS_PRICE, minL2GasPriceETH);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L55

```solidity
File: contracts/zksync/facets/Getters.sol

27        return address(s.verifier);

32        return s.governor;

37        return s.pendingGovernor;

42        return s.totalBatchesCommitted;

47        return s.totalBatchesVerified;

52        return s.totalBatchesExecuted;

57        return s.priorityQueue.getTotalPriorityTxs();

64        return s.priorityQueue.getFirstUnprocessedPriorityTx();

69        return s.priorityQueue.getSize();

74        return s.priorityQueue.front();

79        return s.validators[_address];

84        return s.l2LogsRootHashes[_batchNumber];

91        return s.storedBatchHashes[_batchNumber];

96        return s.l2BootloaderBytecodeHash;

101        return s.l2DefaultAccountBytecodeHash;

106        return s.verifierParams;

111        return s.protocolVersion;

116        return s.l2SystemContractsUpgradeTxHash;

125        return s.l2SystemContractsUpgradeBatchNumber;

131        return ds.isFrozen;

149        return s.priorityTxMaxGasLimit;

154        return address(s.allowList);

161        return ds.selectorToFacet[_selector].isFreezable;

168        return s.isEthWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex];

193        return ds.facetToSelectors[_facet].selectors;

199        return ds.facets;

205        return ds.selectorToFacet[_selector].facetAddress;

215        return s.totalBatchesCommitted;

221        return s.totalBatchesVerified;

227        return s.totalBatchesExecuted;

235        return s.storedBatchHashes[_batchNumber];

245        return s.l2SystemContractsUpgradeBatchNumber;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L27

```solidity
File: contracts/zksync/libraries/PriorityQueue.sol

36        return _queue.head;

41        return _queue.tail;

46        return uint256(_queue.tail - _queue.head);

51        return _queue.tail == _queue.head;

67        return _queue.data[_queue.head];
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L36



## [G-50] With assembly, .call (bool success) transfer can be done gas-optimized

Did you know that the normal "(bool success, bytes memory returnData) = http://target.call()" automatically copies the return data to memory even if you omit the returnData variable? If a relayer executes transactions with such calls it can lead to a gas-griefing attack

solidity
file: /contracts/zksync/libraries/Diamond.sol

288            (bool success, bytes memory data) = _init.delegatecall(_calldata);

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L288



## [G-51] require() Should Be Used Instead Of assert()

solidity
file: /contracts/ethereum/contracts/zksync/facets/Executor.sol

351        assert(block.chainid != 1);

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L351


solidity
file: /contracts/ethereum/contracts/zksync/DiamondInit.sol

87        assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L87