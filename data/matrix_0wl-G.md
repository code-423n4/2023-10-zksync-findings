## Gas Optimizations

|       | Issue                                                                       |
| ----- | :-------------------------------------------------------------------------- |
| GAS-1 | BEFORE SOME FUNCTIONS, WE SHOULD CHECK SOME VARIABLES FOR POSSIBLE GAS SAVE |
| GAS-2 | USE FUNCTION INSTEAD OF MODIFIERS                                           |
| GAS-3 | DON'T INITIALIZE VARIABLES WITH DEFAULT VALUE                               |
| GAS-4 | USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS                   |
| GAS-5 | MODIFIERS ARE REDUNDANT IF USED ONLY ONCE OR NOT USED AT ALL                |
| GAS-6 | THE INCREMENT IN FOR LOOP POSTCONDITION CAN BE MADE UNCHECKED               |
| GAS-7 | USE A MORE RECENT VERSION OF SOLIDITY                                       |
| GAS-8 | USE BYTES32 INSTEAD OF STRING                                               |

### [GAS-1] BEFORE SOME FUNCTIONS, WE SHOULD CHECK SOME VARIABLES FOR POSSIBLE GAS SAVE

#### Description:

Before transfer, we should check for amount being 0 so the function doesnt run when its not gonna do anything:

#### **Proof Of Concept**

```solidity
File: system-contracts/contracts/L2EthToken.sol

32:     function transferFromTo(address _from, address _to, uint256 _amount) external override {

```

### [GAS-2] USE FUNCTION INSTEAD OF MODIFIERS

#### **Proof Of Concept**

```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

58:     modifier onlySelf() {

```

```solidity
File: contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

65:     modifier onlyValidator() {

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Base.sol

30:     modifier onlyValidator() {

```

```solidity
File: contracts/zksync/contracts/bridge/L2StandardERC20.sol

111:     modifier onlyBridge() {

```

```solidity
File: contracts/zksync/contracts/bridge/L2Weth.sol

68:     modifier onlyBridge() {

```

```solidity
File: system-contracts/contracts/ContractDeployer.sol

28:     modifier onlySelf() {

```

### [GAS-3] DON'T INITIALIZE VARIABLES WITH DEFAULT VALUE

#### Description:

If a variable is not set/initialized, the default value is assumed (0, false, 0x0 … depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

#### **Proof Of Concept**

```solidity
File: contracts/ethereum/contracts/common/AllowList.sol

62:         for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

96:         for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {

```

```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

225:         for (uint256 i = 0; i < _calls.length; ++i) {

```

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

204:         for (uint256 i = 0; i < _factoryDeps.length; ++i) {

```

```solidity
File: contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

85:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

116:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Executor.sol

123:         for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

209:         for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

241:         for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

263:         for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

293:         for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {

330:         for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Getters.sol

182:         for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Mailbox.sol

380:         for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {

```

```solidity
File: contracts/ethereum/contracts/zksync/libraries/Diamond.sol

100:         for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {

134:         for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

151:         for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

171:         for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

```

```solidity
File: contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol

92:         uint256 costForPubdata = 0;

```

```solidity
File: system-contracts/contracts/Compressor.sol

68:             for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {

131:         uint256 numInitialWritesProcessed = 0;

134:         for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {

166:         for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {

```

```solidity
File: system-contracts/contracts/ContractDeployer.sol

247:         uint256 sumOfValues = 0;

248:         for (uint256 i = 0; i < deploymentsLength; ++i) {

253:         for (uint256 i = 0; i < deploymentsLength; ++i) {

```

```solidity
File: system-contracts/contracts/ImmutableSimulator.sol

38:             for (uint256 i = 0; i < immutablesLength; ++i) {

```

```solidity
File: system-contracts/contracts/KnownCodesStorage.sol

31:             for (uint256 i = 0; i < hashesLen; ++i) {

```

```solidity
File: system-contracts/contracts/L1Messenger.sol

202:         uint256 calldataPtr = 0;

211:         for (uint256 i = 0; i < numberOfL2ToL1Logs; ++i) {

229:             for (uint256 i = 0; i < nodesOnCurrentLevel; ++i) {

241:         for (uint256 i = 0; i < numberOfMessages; ++i) {

259:         for (uint256 i = 0; i < numberOfBytecodes; ++i) {

```

### [GAS-4] USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS

#### Description:

When constants are marked public, extra getter functions are created, increasing the deployment cost. Marking these functions private will decrease gas cost. One can still read these variables through the source code. If they need to be accessed by an external contract, a separate single getter function can be used to return all constants as a tuple. There are four instances of public constants.

#### **Proof Of Concept**

```solidity
File: contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

24:     string public constant override getName = "ValidatorTimelock";

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Admin.sol

15:     string public constant override getName = "AdminFacet";

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Executor.sol

22:     string public constant override getName = "ExecutorFacet";

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Getters.sol

19:     string public constant override getName = "GettersFacet";

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Mailbox.sol

28:     string public constant override getName = "MailboxFacet";

```

### [GAS-5] MODIFIERS ARE REDUNDANT IF USED ONLY ONCE OR NOT USED AT ALL

#### **Proof Of Concept**

```solidity
File: contracts/ethereum/contracts/common/AllowListed.sol

10:     modifier senderCanCallFunction(IAllowList _allowList) {

```

```solidity
File: contracts/ethereum/contracts/common/ReentrancyGuard.sol

41:     modifier reentrancyGuardInitializer() {

68:     modifier nonReentrant() {

```

```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

64:     modifier onlySecurityCouncil() {

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Base.sol

18:     modifier onlyGovernor() {

24:     modifier onlyGovernorOrAdmin() {

30:     modifier onlyValidator() {

```

```solidity
File: contracts/zksync/contracts/bridge/L2Weth.sol

68:     modifier onlyBridge() {

```

```solidity
File: system-contracts/contracts/ContractDeployer.sol

28:     modifier onlySelf() {

```

```solidity
File: system-contracts/contracts/KnownCodesStorage.sol

20:     modifier onlyCompressor() {

```

### [GAS-6] THE INCREMENT IN FOR LOOP POSTCONDITION CAN BE MADE UNCHECKED

#### Description:

This is only relevant if you are using the default solidity checked arithmetic.

The for loop postcondition, i.e., `i++` involves checked arithmetic, which is not required. This is because the value of i is always strictly less than `length <= 2**256 - 1`. Therefore, the theoretical maximum value of i to enter the for-loop body is `2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. One can manually do this.

[Source](https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/6)

#### **Proof Of Concept**

```solidity
File: system-contracts/contracts/Compressor.sol

142:             numInitialWritesProcessed++;

151:             stateDiffPtr++;

```

### [GAS-7] USE A MORE RECENT VERSION OF SOLIDITY

#### Description:

Using at least `0.8.10` will save gas due to skipped extcodesize check if there is a return value. Currently the contracts are compiled using version `0.8.0`, `0.8.1`, `0.8.13` (Foundry default). It is easily changeable to `0.8.21`.

#### **Proof Of Concept**

```solidity
File: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/bridge/L1WethBridge.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/bridge/interfaces/IL1BridgeLegacy.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/bridge/interfaces/IL2ERC20Bridge.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/common/AllowList.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/common/AllowListed.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/common/L2ContractAddresses.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/ethereum/contracts/common/ReentrancyGuard.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/common/interfaces/IAllowList.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/common/libraries/UncheckedMath.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/governance/Governance.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/governance/IGovernance.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/vendor/AddressAliasHelper.sol

19: pragma solidity ^0.8.0;

```

```solidity
File: contracts/ethereum/contracts/zksync/Config.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/DiamondInit.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/DiamondProxy.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/Storage.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Admin.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Base.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Executor.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Getters.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Mailbox.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/interfaces/IBase.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/interfaces/IGetters.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/interfaces/ILegacyGetters.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/interfaces/IZkSync.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/libraries/Diamond.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/libraries/LibMap.sol

2: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/libraries/Merkle.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/zksync/contracts/bridge/L2ERC20Bridge.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/zksync/contracts/bridge/L2StandardERC20.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/zksync/contracts/bridge/L2Weth.sol

3: pragma solidity ^0.8.13;

```

```solidity
File: contracts/zksync/contracts/bridge/L2WethBridge.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/zksync/contracts/bridge/interfaces/IL1Bridge.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/zksync/contracts/bridge/interfaces/IL2Bridge.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/zksync/contracts/bridge/interfaces/IL2StandardToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: contracts/zksync/contracts/bridge/interfaces/IL2Weth.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/AccountCodeStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/BootloaderUtilities.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/ComplexUpgrader.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/Compressor.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/Constants.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/ContractDeployer.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/DefaultAccount.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/EmptyContract.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/ImmutableSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/KnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/L1Messenger.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/L2EthToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/MsgValueSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/NonceHolder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/SystemContext.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/IAccount.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/IAccountCodeStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/IBootloaderUtilities.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/IComplexUpgrader.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/ICompressor.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/IContractDeployer.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/IEthToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/IImmutableSimulator.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/IKnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/IL1Messenger.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/IL2StandardToken.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/IMailbox.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/INonceHolder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/IPaymaster.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/IPaymasterFlow.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/ISystemContext.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/ISystemContextDeprecated.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/interfaces/ISystemContract.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/libraries/EfficientCall.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/libraries/RLPEncoder.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/libraries/SystemContractHelper.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/libraries/SystemContractsCaller.sol

3: pragma solidity ^0.8;

```

```solidity
File: system-contracts/contracts/libraries/TransactionHelper.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/libraries/UnsafeBytesCalldata.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: system-contracts/contracts/libraries/Utils.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: system-contracts/contracts/openzeppelin/utils/Address.sol

4: pragma solidity ^0.8.1;

```

```solidity
File: system-contracts/contracts/test-contracts/MockKnownCodesStorage.sol

3: pragma solidity ^0.8.0;

```

### [GAS-8] USE BYTES32 INSTEAD OF STRING

#### Description:

Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

#### **Proof Of Concept**

```solidity
File: contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

24:     string public constant override getName = "ValidatorTimelock";

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Admin.sol

15:     string public constant override getName = "AdminFacet";

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Executor.sol

22:     string public constant override getName = "ExecutorFacet";

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Getters.sol

19:     string public constant override getName = "GettersFacet";

```

```solidity
File: contracts/ethereum/contracts/zksync/facets/Mailbox.sol

28:     string public constant override getName = "MailboxFacet";

```

```solidity
File: contracts/ethereum/contracts/zksync/interfaces/IBase.sol

5:     function getName() external view returns (string memory);

```

```solidity
File: contracts/zksync/contracts/bridge/L2StandardERC20.sol

63:         string memory decodedName;

64:         string memory decodedSymbol;

75:         try this.decodeString(nameBytes) returns (string memory nameString) {

82:             string memory symbolString

140:     function name() public view override returns (string memory) {

146:     function symbol() public view override returns (string memory) {

161:     ) external pure returns (string memory result) {

162:         (result) = abi.decode(_input, (string));

```

```solidity
File: contracts/zksync/contracts/bridge/L2Weth.sol

40:         string memory name_,

41:         string memory symbol_

```

```solidity
File: contracts/zksync/contracts/bridge/interfaces/IL2StandardToken.sol

6:     event BridgeInitialize(address indexed l1Token, string name, string symbol, uint8 decimals);

6:     event BridgeInitialize(address indexed l1Token, string name, string symbol, uint8 decimals);

```

```solidity
File: contracts/zksync/contracts/bridge/interfaces/IL2Weth.sol

5:     event Initialize(string name, string symbol, uint8 decimals);

5:     event Initialize(string name, string symbol, uint8 decimals);

```

```solidity
File: system-contracts/contracts/L2EthToken.sol

128:     function name() external pure override returns (string memory) {

134:     function symbol() external pure override returns (string memory) {

```

```solidity
File: system-contracts/contracts/interfaces/IEthToken.sol

12:     function name() external pure returns (string memory);

14:     function symbol() external pure returns (string memory);

```

```solidity
File: system-contracts/contracts/libraries/TransactionHelper.sol

82:     bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");

82:     bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");

```

```solidity
File: system-contracts/contracts/openzeppelin/utils/Address.sol

113:         string memory errorMessage

153:         string memory errorMessage

199:         string memory errorMessage

238:         string memory errorMessage

260:         string memory errorMessage

283:         string memory errorMessage

292:     function _revert(bytes memory returndata, string memory errorMessage)

```
