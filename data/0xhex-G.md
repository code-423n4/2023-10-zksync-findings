| Number | Gas                                                                                   | context |
| :----: | :------------------------------------------------------------------------------------ | :-----: |
| [G-01] | Use assembly to hash instead of solidity                                              |   13    |
| [G-02] | Use bytes.concat() instead of abi.encodePacked(), since this is preferred since 0.8.4 |    4    |
| [G-03] | Use Mappings instead of arrays                                                        |   10    |
| [G-04] | Can make the variable outside the loop to save gas                                    |    6    |
| [G-05] | onlyowner no uses of the nonReentrant modifier                                        |   11    |
| [G-06] | Pre-increment and pre-decrement are cheaper than +1 ,-1                               |    9    |
| [G-07] | It is sometimes cheaper to cache calldata                                             |   23    |
| [G-08] | Should use arguments instead of state variable                                        |    5    |
| [G-09] | Using Storage instead of memory for structs/arrays saves gas                          |   13    |
| [G-10] | Use constants instead of type(uintx).max                                              |    3    |
| [G-11] | Empty blocks should be removed or emit something                                      |    4    |
| [G-12] | Do not calculate constants                                                            |   14    |
| [G-13] | Use do while  loops instead of for loops                                              |   19    |
| [G-14] | Using private rather than public for constants, saves Gas                             |    9    |
| [G-15] | Using fixex bytes is cheaper than using string                                        |    5    |
| [G-16] | Use hardcode address instead address(this)                                            |   10    |
| [G-17] | NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS   |   55    |
| [G-18] | Private functions used once can be inlined                                            |   11    |
| [G-19] | Use assembly to perform efficient back-to-back calls                                  |   11    |
| [G-20] | Sort Solidity operations using short-circuit mode                                     |   12    |
| [G-21] | abi.encode() is less efficient than abi.encodePacked()                                |   12    |

## [G-01] Use assembly to hash instead of solidity

Use assembly for small keccak256 hashes, in order to save gas

```solidity
File: contracts/zksync/DiamondInit.sol

77        s.storedBatchHashes[0] = keccak256(abi.encode(storedBatchZero));

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L77

```solidity
File: contracts/zksync/facets/Executor.sol

120        bytes32 providedL2ToL1PubdataHash = keccak256(_newBatch.totalL2ToL1Pubdata);

265            concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));


383                keccak256(


427        bytes32 passThroughDataHash = keccak256(_batchPassThroughData(_newBatchData));

428        bytes32 metadataHash = keccak256(_batchMetaParameters());

429        bytes32 auxiliaryOutputHash = keccak256(_batchAuxiliaryOutput(_newBatchData, _stateDiffHash));

455        bytes32 l2ToL1LogsHash = keccak256(_batch.systemLogs);


468        return keccak256(abi.encode(_storedBatchInfo));

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L120

```solidity
File: contracts/zksync/facets/Mailbox.sol

131        bytes32 hashedLog = keccak256(
132            abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBatch, _log.sender, _log.key, _log.value)
133        );


367        canonicalTxHash = keccak256(transactionEncoding);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L131C2-L133C11

```solidity
File: contracts/upgrades/BaseZkSyncUpgrade.sol

192        bytes32 l2ProtocolUpgradeTxHash = keccak256(encodedTransaction);

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L192

```solidity
File: contracts/bridge/libraries/BridgeInitializationHelper.sol

56            keccak256(_constructorData)
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol#L56

## [G-02] Use bytes.concat() instead of abi.encodePacked(), since this is preferred since 0.8.4

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

## [G-03] Use Mappings instead of arrays

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

## [G-04] Can make the variable outside the loop to save gas

```solidity
File: contracts/zksync/ValidatorTimelock.sol

86                committedBatchTimestamp.set(_newBatchesData[i].batchNumber, timestamp);

117                uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L86C1-L87C1

```solidity
File: contracts/zksync/facets/Executor.sol

212            s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData);

250            s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData);

264            PriorityOperation memory priorityOp = s.priorityQueue.popFront();

333                _hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L212

## [G-05] onlyowner no uses of the nonReentrant modifier

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

## [G-06] Pre-increment and pre-decrement are cheaper than +1 ,-1

```solidity
File: contracts/zksync/facets/Executor.sol

32        require(_newBatch.batchNumber == _previousBatch.batchNumber + 1, "f"); // only commit next batch

275        require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k"); // Execute batches in order
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L275

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

## [G-07] It is sometimes cheaper to cache calldata

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

## [G-08] Should use arguments instead of state variable

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

## [G-09] Using Storage instead of memory for structs/arrays saves gas

```solidity
File: contracts/zksync/facets/Executor.sol

321        VerifierParams memory verifierParams = s.verifierParams;

324        uint256[] memory proofPublicInput = new uint256[](committedBatchesLength);

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L212C1-L213C1

```solidity
File: contracts/zksync/facets/Mailbox.sol

276        IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH

299        WritePriorityOpParams memory params;

361        L2CanonicalTransaction memory transaction = _serializeL2Transaction(_priorityOpParams, _calldata, _factoryDeps);

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L276

```solidity
File: contracts/zksync/libraries/Diamond.sol

96        FacetCut[] memory facetCuts = _diamondCut.facetCuts;

104            bytes4[] memory selectors = facetCuts[i].selectors;

140            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

161            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

181            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L96C1-L97C1

```solidity
File: contracts/zksync/facets/Getters.sol

184            Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L184C1-L185C1

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

341        IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L341

```solidity
File: contracts/zksync/DiamondProxy.sol

27        Diamond.SelectorToFacet memory facet = diamondStorage.selectorToFacet[msg.sig];
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L27C1-L28C1

```solidity
File: contracts/upgrades/BaseZkSyncUpgrade.sol

136        VerifierParams memory oldVerifierParams = s.verifierParams;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L136

## [G-10] Use constants instead of type(uintx).max

```solidity
File: contracts/zksync/libraries/TransactionValidator.sol

50        require(_transaction.from <= type(uint16).max, "ua");

51        require(_transaction.to <= type(uint160).max, "ub");

55        require(_transaction.reserved[1] <= type(uint160).max, "uf");

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L50

## [G-11] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.
If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation.

If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be
nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when
the code is later modified (if( x ) {}else if(y){ . . . }else{ . . . } => if ( !x) {if(y) { . . . }else{ . . .}}) .
Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

```solidity
File: contracts/upgrades/DefaultUpgrade.sol

15    function _upgradeL1Contract(bytes calldata _customCallDataForUpgrade) internal virtual {}

21    function _postUpgrade(bytes calldata _customCallDataForUpgrade) internal virtual {}
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L15

```solidity
File: contracts/zksync/DiamondInit.sol

50    constructor() reentrancyGuardInitializer {}
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L50

```solidity
File: contracts/zksync/interfaces/IZkSync.sol

10      interface IZkSync is IMailbox, IAdmin, IExecutor, IGetters {}
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IZkSync.sol#L10

## [G-12] Do not calculate constants

```solidity
File: contracts/zksync/Config.sol

14  uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

26  uint256 constant MAX_INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE * 4765;

33  uint256 constant MAX_REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE * 7564;

54  uint256 constant COMMIT_TIMESTAMP_NOT_OLDER = $$(
55        defined(COMMIT_TIMESTAMP_NOT_OLDER) ? COMMIT_TIMESTAMP_NOT_OLDER : "365 days"
56  );

60  uint256 constant COMMIT_TIMESTAMP_APPROXIMATION_DELTA = $$(
61        defined(COMMIT_TIMESTAMP_APPROXIMATION_DELTA) ? COMMIT_TIMESTAMP_APPROXIMATION_DELTA : "365 days"
62   );

65  uint256 constant INPUT_MASK = $$(~uint256(0) >> 32);

92  uint256 constant BATCH_OVERHEAD_PUBDATA = BATCH_OVERHEAD_L1_GAS / L1_GAS_PER_PUBDATA_BYTE;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L26

```solidity
File: contracts/vendor/AddressAliasHelper.sol

22    uint160 constant offset = uint160(0x1111000000000000000000000000000000001111);

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L22

```solidity
File: contracts/L2ContractHelper.sol

74  address constant BOOTLOADER_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x01);

75  address constant MSG_VALUE_SYSTEM_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x09);

76  address constant DEPLOYER_SYSTEM_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x06);

78  IL2Messenger constant L2_MESSENGER = IL2Messenger(address(SYSTEM_CONTRACTS_OFFSET + 0x08));

80  IEthToken constant L2_ETH_ADDRESS = IEthToken(address(SYSTEM_CONTRACTS_OFFSET + 0x0a));

89    bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/L2ContractHelper.sol#L74

## [G-13] Use do while  loops instead of for loops

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

## [G-14] Using private rather than public for constants, saves Gas

```solidity
File: contracts/zksync/facets/Mailbox.sol

41    string public constant override getName = "MailboxFacet";
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41

```solidity
File: contracts/zksync/facets/Getters.sol

19    string public constant override getName = "GettersFacet";
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19

```solidity
File: contracts/zksync/ValidatorTimelock.sol

24    string public constant override getName = "ValidatorTimelock";

33    address public immutable zkSyncContract;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24

```solidity
File: contracts/zksync/facets/Admin.sol

15    string public constant override getName = "AdminFacet";

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15

```solidity
File: contracts/bridge/L1WethBridge.sol

44    address payable public immutable l1WethAddress;

    /// @dev The smart contract that manages the list with permission to call contract functions
47    IAllowList public immutable allowList;

    /// @dev zkSync smart contract that is used to operate with L2 via asynchronous L2 <-> L1 communication
50    IZkSync public immutable zkSync;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L44

```solidity
File: zksync/facets/Executor.sol

22    string public constant override getName = "ExecutorFacet";

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22

## [G-15] Using fixex bytes is cheaper than using string

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

```solidity
File: contracts/zksync/facets/Executor.sol

22    string public constant override getName = "ExecutorFacet";
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22C28-L22C36

```solidity
File: contracts/zksync/facets/Mailbox.sol

41    string public constant override getName = "MailboxFacet";
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41C28-L41C36

```solidity
File: contracts/zksync/facets/Getters.sol

19    string public constant override getName = "GettersFacet";
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19

```solidity
File: contracts/zksync/ValidatorTimelock.sol

24    string public constant override getName = "ValidatorTimelock";
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24

```solidity
File: contracts/zksync/facets/Admin.sol

15    string public constant override getName = "AdminFacet";
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15

## [G-16] Use hardcode address instead address(this)

```solidity
File: contracts/common/AllowListed.sol

13            require(_allowList.canCall(msg.sender, address(this), msg.sig), "nr");

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L13

```solidity
File: contracts/bridge/libraries/BridgeInitializationHelper.sol

53            AddressAliasHelper.applyL1ToL2Alias(address(this)),

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol#L53

```solidity
File: contracts/governance/Governance.sol

59        require(msg.sender == address(this), "Only governance contract itself allowed to call this function");

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L59

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

117                (address(this), l2TokenProxyBytecodeHash, _governor)


217        uint256 balanceBefore = _token.balanceOf(address(this));

218        _token.safeTransferFrom(_from, address(this), _amount);

219        uint256 balanceAfter = _token.balanceOf(address(this));
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L117

```solidity
File: contracts/bridge/L1WethBridge.sol

116                (address(this), l1WethAddress, _l2WethAddress)

171        IERC20(l1WethAddress).safeTransferFrom(msg.sender, address(this), _amount);

289        require(l1EthReceiver == address(this), "Wrong L1 ETH withdraw receiver");

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L116

## [G-17] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

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

## [G-18] Private functions used once can be inlined

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

## [G-19] Use assembly to perform efficient back-to-back calls

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

217       uint256 balanceBefore = _token.balanceOf(address(this));
218        _token.safeTransferFrom(_from, address(this), _amount);
219        uint256 balanceAfter = _token.balanceOf(address(this));
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L217C1-L219C64

```solidity
File:

171        IERC20(l1WethAddress).safeTransferFrom(msg.sender, address(this), _amount);
173        IWETH9(l1WethAddress).withdraw(_amount);
185        txHash = zkSync.requestL2Transaction{value: _amount + msg.value}(




245        bool alreadyFinalised = zkSync.isEthWithdrawalFinalized(_l2BatchNumber, _l2MessageIndex);
253            bool success = zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);
257            zkSync.finalizeEthWithdrawal(_l2BatchNumber, _l2MessageIndex, _l2TxNumberInBatch, _message, _merkleProof);
261        IWETH9(l1WethAddress).deposit{value: amount}();
263        IERC20(l1WethAddress).safeTransfer(l1WethWithdrawReceiver, amount);

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L171

## [G-20] Sort Solidity operations using short-circuit mode

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

## [G-21] abi.encode() is less efficient than abi.encodePacked()

```solidity
File: contracts/upgrades/BaseZkSyncUpgrade.sol

173        bytes memory encodedTransaction = abi.encode(_l2ProtocolUpgradeTx);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L173

```solidity
File: contracts/zksync/DiamondInit.sol

77        s.storedBatchHashes[0] = keccak256(abi.encode(storedBatchZero));

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L77

```solidity
File: contracts/zksync/facets/Executor.sol


265            concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));

431        return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));

458            abi.encode(

468        return keccak256(abi.encode(_storedBatchInfo));
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L265

```solidity
File: contracts/governance/Governance.sol

205        return keccak256(abi.encode(_operation));
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L205

```solidity
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

119            l2BridgeProxyConstructorData = abi.encode(bridgeImplementationAddr, _governor, proxyInitializationParams);

244        data = abi.encode(data1, data2, data3);

354        bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""));
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L119

```solidity
File:

118            l2WethBridgeProxyConstructorData = abi.encode(
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L118

```solidity
File: contracts/zksync/facets/Mailbox.sol

363        bytes memory transactionEncoding = abi.encode(transaction);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L363