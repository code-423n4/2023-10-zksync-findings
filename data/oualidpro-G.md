## Summary
## Gas Optimizations


| |Issue|Instances|Gas Saved|
|-|:-|:-:|-:|
| [GAS-1](#GAS-1-1693311695) | Use `assembly` to write address storage values | 10 | - |
| [GAS-2](#GAS-2-1695899988) | Do not cache immutable values | 1 | 13 |
| [GAS-3](#GAS-3-1693312130) | Do not calculate constants | 15 | - |
| [GAS-4](#GAS-4-1694877823) | Use `do while` loops instead of `for` loops | 31 | 3751 |
| [GAS-5](#GAS-5-1693312238) | Empty blocks should be removed or emit something | 1 | - |
| [GAS-6](#GAS-6-1693312354) | The result of function calls should be cached rather than re-calling the function | 1 | - |
| [GAS-7](#GAS-7-1694795215) | Importing an entire library while only using one function isn't necessary | 2 | - |
| [GAS-8](#GAS-8-1693312508) | Consider merging sequential for loops | 2 | - |
| [GAS-9](#GAS-9-1693312698) | Enable Solidity's optimizer | 1 | - |
| [GAS-10](#GAS-10-1693312824) | Using `private` rather than `public` for constants, saves gas | 5 | - |
| [GAS-11](#GAS-11-1693312999) | Use shift Left instead of multiplication if possible to save gas | 26 | - |
| [GAS-12](#GAS-12-1693313275) | Use assembly to validate `msg.sender` | 47 | 564 |
| [GAS-13](#GAS-13-1696083995) | `abi.encode()` is less efficient than `abi.encodepacked()` | 24 | 1464 |
| [GAS-14](#GAS-14-1693313317) | Can make the variable outside the loop to save gas | 48 | - |
| [GAS-15](#GAS-15-1693313355) | Consider activating via-ir for deploying | 1 | 250 |
| [GAS-16](#GAS-15-1693313356) | Use the result of the function call directly to save gas | 1 | 10 |
| [GAS-16](#GAS-15-1693313357) | The result of a mathematique operation is only used once | 2 | 116 |

Total: 215 instances over 15 issues with ** 6284 gas ** saved

### <a name="GAS-1-1693311695"></a>[GAS-1] Use `assembly` to write address storage values

*Instances (10)*:
<details><summary> see instances </summary>

```solidity
File: contracts/SystemContext.sol

88:         origin = _newOrigin;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L88-L88)

```solidity
File: contracts/bridge/L2ERC20Bridge.sol

49:         l1Bridge = _l1Bridge;

80:             l1TokenAddress[expectedL2Token] = _l1Token;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L80-L80)

```solidity
File: contracts/bridge/L2StandardERC20.sol

48:         l1Address = _l1Address;

50:         l2Bridge = msg.sender;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L50-L50)

```solidity
File: contracts/bridge/L2Weth.sol

55:         l2Bridge = _l2Bridge;

56:         l1Address = _l1Address;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L56-L56)

```solidity
File: contracts/bridge/L2WethBridge.sol

54:         l1Bridge = _l1Bridge;

55:         l1WethAddress = _l1WethAddress;

56:         l2WethAddress = _l2WethAddress;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L56-L56)

</details>

### <a name="GAS-2-1695899988"></a>[GAS-2] Do not cache immutable values
Caching immutable variables does not save gas. here is a [proof of concept](https://gist.github.com/he110-1/f8eb263ca85a0557fea1f31fdc1de6c2)

*Instances (1)*:
<details><summary> see instances </summary>

```solidity
File: ethereum/contracts/zksync/ValidatorTimelock.sol

133:         address contractAddress = zkSyncContract;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L133)

</details>

### <a name="GAS-3-1693312130"></a>[GAS-3] Do not calculate constants
Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

*Instances (15)*:
<details><summary> see instances </summary>

```solidity
File: contracts/Constants.sol

73: uint256 constant MAX_MSG_VALUE = 2 ** 128 - 1;

112: uint256 constant COMPRESSED_INITIAL_WRITE_SIZE = DERIVED_KEY_LENGTH + VALUE_LENGTH;

114: uint256 constant COMPRESSED_REPEATED_WRITE_SIZE = ENUM_INDEX_LENGTH + VALUE_LENGTH;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Constants.sol#L114-L114)

```solidity
File: contracts/NonceHolder.sol

28:     uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128;

31:     uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L31-L31)

```solidity
File: contracts/libraries/SystemContractsCaller.sol

41: uint256 constant META_GAS_PER_PUBDATA_BYTE_OFFSET = 0 * 8;

42: uint256 constant META_HEAP_SIZE_OFFSET = 8 * 8;

43: uint256 constant META_AUX_HEAP_SIZE_OFFSET = 12 * 8;

44: uint256 constant META_SHARD_ID_OFFSET = 28 * 8;

45: uint256 constant META_CALLER_SHARD_ID_OFFSET = 29 * 8;

46: uint256 constant META_CODE_SHARD_ID_OFFSET = 30 * 8;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L46-L46)

```solidity
File: ethereum/contracts/zksync/Config.sol

14: uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

26: uint256 constant MAX_INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE * 4765;

33: uint256 constant MAX_REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE * 7564;

92: uint256 constant BATCH_OVERHEAD_PUBDATA = BATCH_OVERHEAD_L1_GAS / L1_GAS_PER_PUBDATA_BYTE;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L92-L92)

</details>

### <a name="GAS-4-1694877823"></a>[GAS-4] Use `do while` loops instead of `for` loops
A `do while` loop will cost less gas since the condition is not being checked for the first iteration, Check my example on [github](https://github.com/he110-1/gasOptimization/blob/main/forToDoWhileOptimizationProof.sol). Actually, `do while` alwayse cast less gas compared to `For` check my second example [github](https://github.com/he110-1/gasOptimization/blob/main/forToDoWhileOptimizationProof2.sol)

*Instances (31)*:
<details><summary> see instances </summary>

```solidity
File: contracts/Compressor.sol

68:             for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {

134:         for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {

166:         for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L166)

```solidity
File: contracts/ContractDeployer.sol

248:         for (uint256 i = 0; i < deploymentsLength; ++i) {

253:         for (uint256 i = 0; i < deploymentsLength; ++i) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L253)

```solidity
File: contracts/ImmutableSimulator.sol

38:             for (uint256 i = 0; i < immutablesLength; ++i) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L38)

```solidity
File: contracts/KnownCodesStorage.sol

31:             for (uint256 i = 0; i < hashesLen; ++i) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol#L31)

```solidity
File: contracts/L1Messenger.sol

211:         for (uint256 i = 0; i < numberOfL2ToL1Logs; ++i) {

223:         for (uint256 i = numberOfL2ToL1Logs; i < L2_TO_L1_LOGS_MERKLE_TREE_LEAVES; ++i) {

229:             for (uint256 i = 0; i < nodesOnCurrentLevel; ++i) {

241:         for (uint256 i = 0; i < numberOfMessages; ++i) {

259:         for (uint256 i = 0; i < numberOfBytecodes; ++i) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L259)

```solidity
File: ethereum/contracts/common/AllowList.sol

62:         for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

96:         for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L96)

```solidity
File: ethereum/contracts/governance/Governance.sol

225:         for (uint256 i = 0; i < _calls.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L225)

```solidity
File: ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

204:         for (uint256 i = 0; i < _factoryDeps.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204)

```solidity
File: ethereum/contracts/zksync/ValidatorTimelock.sol

85:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

116:             for (uint256 i = 0; i < _newBatchesData.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L116)

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

123:         for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

209:         for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

241:         for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

263:         for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

293:         for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {

330:         for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L330)

```solidity
File: ethereum/contracts/zksync/facets/Getters.sol

182:         for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L182)

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

395:         for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L395)

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

100:         for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {

138:         for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

159:         for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

179:         for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L179)

```solidity
File: ethereum/contracts/zksync/libraries/Merkle.sol

29:         for (uint256 i; i < pathLength; i = i.uncheckedInc()) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L29)

</details>

### <a name="GAS-5-1693312238"></a>[GAS-5] Empty blocks should be removed or emit something
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty `if`-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if (x) {...} else if (y) {...} else {...}` => `if (!x) { if (y) {...} else {...} }`). Empty `receive()`/`fallback() payable` functions that are not used, can be removed to save deployment gas.

*Instances (1)*:
<details><summary> see instances </summary>

```solidity
File: contracts/test-contracts/MockKnownCodesStorage.sol

18:     fallback() external {}
    }

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/test-contracts/MockKnownCodesStorage.sol#L18-L19)

</details>

### <a name="GAS-6-1693312354"></a>[GAS-6] The result of function calls should be cached rather than re-calling the function
The instances below point to the second+ call of the function within a single function

*Instances (1)*:
<details><summary> see instances </summary>

```solidity
File: contracts/L1Messenger.sol

// @audit gasleft() is called 2 times in the function `sendToL1`
55:     uint256 internal constant KECCAK_ROUND_NUMBER_OF_BYTES = 136;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L55)

</details>

### <a name="GAS-7-1694795215"></a>[GAS-7] Importing an entire library while only using one function isn't necessary
If only one function is used from an imported library then, it is better to copy that same function into the contract source code or writting your own one.

*Instances (2)*:
<details><summary> see instances </summary>

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

//@audit the `Math` is imported but only `1` function is used
37: contract MailboxFacet is Base, IMailbox {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L37)

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

//@audit the `SafeCast` is imported but only `1` function is used
11: library Diamond {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L11)

</details>

### <a name="GAS-8-1693312508"></a>[GAS-8] Consider merging sequential for loops
Merging multiple `for` loops within a function in Solidity can enhance efficiency and reduce gas costs, especially when they share a common iterating variable or perform related operations. By minimizing redundant iterations over the same data set, execution becomes more cost-effective. However, while merging can optimize gas usage and simplify logic, it may also increase code complexity. Therefore, careful balance between optimization and maintainability is essential, along with thorough testing to ensure the refactored code behaves as expected.

*Instances (2)*:
<details><summary> see instances </summary>

```solidity
File: contracts/Compressor.sol

134:         for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {
                 bytes calldata stateDiff = _stateDiffs[i:i + STATE_DIFF_ENTRY_SIZE];
                 uint64 enumIndex = stateDiff.readUint64(84);
                 if (enumIndex != 0) {
                     // It is a repeated write, so we skip it.
                     continue;
                 }
     
                 numInitialWritesProcessed++;
     
                 bytes32 derivedKey = stateDiff.readBytes32(52);
                 uint256 initValue = stateDiff.readUint256(92);
                 uint256 finalValue = stateDiff.readUint256(124);
                 require(derivedKey == _compressedStateDiffs.readBytes32(stateDiffPtr), "iw: initial key mismatch");            
                 stateDiffPtr += 32;
     
                 uint8 metadata = uint8(bytes1(_compressedStateDiffs[stateDiffPtr]));
                 stateDiffPtr++;
                 uint8 operation = metadata & OPERATION_BITMASK;
                 uint8 len = operation == 0 ? 32 : metadata >> LENGTH_BITS_OFFSET;
                 _verifyValueCompression(
                     initValue,
                     finalValue,
                     operation,
                     _compressedStateDiffs[stateDiffPtr:stateDiffPtr + len]
                 );
                 stateDiffPtr += len;
             }
     
             require(numInitialWritesProcessed == numberOfInitialWrites, "Incorrect number of initial storage diffs");
     
             // Process repeated writes
             for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L134-L166)

```solidity
File: contracts/ContractDeployer.sol

248:         for (uint256 i = 0; i < deploymentsLength; ++i) {
                 sumOfValues += _deployments[i].value;
             }
             require(msg.value == sumOfValues, "`value` provided is not equal to the combined `value`s of deployments");
     
             for (uint256 i = 0; i < deploymentsLength; ++i) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L248-L253)

</details>

### <a name="GAS-9-1693312698"></a>[GAS-9] Enable Solidity's optimizer
Make sure Solidity's optimizer is enabled. It reduces gas costs. If you want to gas optimize for contract deployment (costs less to deploy a contract) then set the Solidity optimizer at a low number. If you want to optimize for run-time gas costs (when functions are called on a contract) then set the optimizer to a high number.
Set the optimization value higher than 800 in your hardhat.config.ts file.

*Instances (1)*:
<details><summary> see instances </summary>

```solidity
File: hardhat.config.ts

//@audit code/zksync/hardhat.config.ts
1: 

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/hardhat.config.ts#L1)

</details>

### <a name="GAS-10-1693312824"></a>[GAS-10] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances (5)*:
<details><summary> see instances </summary>

```solidity
File: ethereum/contracts/zksync/ValidatorTimelock.sol

24:     string public constant override getName = "ValidatorTimelock";

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24)

```solidity
File: ethereum/contracts/zksync/facets/Admin.sol

15:     string public constant override getName = "AdminFacet";

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15)

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

22:     string public constant override getName = "ExecutorFacet";

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22)

```solidity
File: ethereum/contracts/zksync/facets/Getters.sol

19:     string public constant override getName = "GettersFacet";

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19)

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

41:     string public constant override getName = "MailboxFacet";

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41)

</details>

### <a name="GAS-11-1693312999"></a>[GAS-11] Use shift Left instead of multiplication if possible to save gas

*Instances (26)*:
<details><summary> see instances </summary>

```solidity
File: contracts/BootloaderUtilities.sol

97:                 vInt += 8 + block.chainid * 2;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L97)

```solidity
File: contracts/Compressor.sol

62:             require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");

62:             require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");

64:                 encodedData.length * 4 == _bytecode.length,

69:                 uint256 indexOfEncodedChunk = uint256(encodedData.readUint16(encodedDataPointer)) * 8;

73:                 uint64 realChunk = _bytecode.readUint64(encodedDataPointer * 4);

208:             dictionary = _rawCompressedData[2:2 + dictionaryLen * 8];

209:             encodedData = _rawCompressedData[2 + dictionaryLen * 8:];

252:         number >>= (256 - (_calldataSlice.length * 8));

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L252)

```solidity
File: contracts/Constants.sol

73: uint256 constant MAX_MSG_VALUE = 2 ** 128 - 1;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Constants.sol#L73)

```solidity
File: contracts/L1Messenger.sol

156:             4 *

231:                     abi.encode(l2ToL1LogsTreeArray[2 * i], l2ToL1LogsTreeArray[2 * i + 1])

231:                     abi.encode(l2ToL1LogsTreeArray[2 * i], l2ToL1LogsTreeArray[2 * i + 1])

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L231)

```solidity
File: contracts/NonceHolder.sol

28:     uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128;

31:     uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L31)

```solidity
File: contracts/libraries/RLPEncoder.sol

37:                 uint256 shiftedVal = _val << (lbs * 8);

72:                 uint256 shiftedVal = uint256(_len) << (lbs * 8);

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L72)

```solidity
File: contracts/libraries/SystemContractsCaller.sol

41: uint256 constant META_GAS_PER_PUBDATA_BYTE_OFFSET = 0 * 8;

42: uint256 constant META_HEAP_SIZE_OFFSET = 8 * 8;

43: uint256 constant META_AUX_HEAP_SIZE_OFFSET = 12 * 8;

44: uint256 constant META_SHARD_ID_OFFSET = 28 * 8;

45: uint256 constant META_CALLER_SHARD_ID_OFFSET = 29 * 8;

46: uint256 constant META_CODE_SHARD_ID_OFFSET = 30 * 8;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L46)

```solidity
File: contracts/libraries/Utils.sol

87:         require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L87)

```solidity
File: ethereum/contracts/common/libraries/L2ContractHelper.sol

26:         require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L26)

```solidity
File: ethereum/contracts/zksync/DiamondInit.sol

87:         assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L87)

</details>

### <a name="GAS-12-1693313275"></a>[GAS-12] Use assembly to validate `msg.sender`
We can use assembly to efficiently validate msg.sender with the least amount of opcodes necessary. For more details check the following report [Here](https://code4rena.com/reports/2023-05-juicebox#g-06-use-assembly-to-validate-msgsender)

*Instances (47)*:
<details><summary> see instances </summary>

```solidity
File: contracts/AccountCodeStorage.sol

26:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L26-L26)

```solidity
File: contracts/ComplexUpgrader.sol

22:         require(msg.sender == FORCE_DEPLOYER, "Can only be called by FORCE_DEPLOYER");

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ComplexUpgrader.sol#L22-L22)

```solidity
File: contracts/ContractDeployer.sol

29:         require(msg.sender == address(this), "Callable only by self");

240:             msg.sender == FORCE_DEPLOYER || msg.sender == address(COMPLEX_UPGRADER_CONTRACT),

240:             msg.sender == FORCE_DEPLOYER || msg.sender == address(COMPLEX_UPGRADER_CONTRACT),

240:             msg.sender == FORCE_DEPLOYER || msg.sender == address(COMPLEX_UPGRADER_CONTRACT),

240:             msg.sender == FORCE_DEPLOYER || msg.sender == address(COMPLEX_UPGRADER_CONTRACT),

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L240-L240)

```solidity
File: contracts/DefaultAccount.sol

29:         if (msg.sender != BOOTLOADER_FORMAL_ADDRESS) {

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L29-L29)

```solidity
File: contracts/ImmutableSimulator.sol

35:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L35-L35)

```solidity
File: contracts/KnownCodesStorage.sol

21:         require(msg.sender == address(COMPRESSOR_CONTRACT), "Callable only by the compressor");

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol#L21-L21)

```solidity
File: contracts/L2EthToken.sol

34:             msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
                    msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
                    msg.sender == BOOTLOADER_FORMAL_ADDRESS,

34:             msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
                    msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
                    msg.sender == BOOTLOADER_FORMAL_ADDRESS,

34:             msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
                    msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
                    msg.sender == BOOTLOADER_FORMAL_ADDRESS,

34:             msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
                    msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||

34:             msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
                    msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||

34:             msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||

35:                 msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||

36:                 msg.sender == BOOTLOADER_FORMAL_ADDRESS,

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L36-L36)

```solidity
File: contracts/NonceHolder.sol

136:         require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "");

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L136-L136)

```solidity
File: contracts/bridge/L2ERC20Bridge.sol

71:         require(AddressAliasHelper.undoL1ToL2Alias(msg.sender) == l1Bridge, "mq");

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L71-L71)

```solidity
File: contracts/bridge/L2StandardERC20.sol

101:         require(msg.sender == l2Bridge, "xnt"); // Only L2 bridge can call this method

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L101-L101)

```solidity
File: contracts/bridge/L2Weth.sol

60:         require(msg.sender == l2Bridge, "permission denied"); // Only L2 bridge can call this method

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L60-L60)

```solidity
File: contracts/bridge/L2WethBridge.sol

96:             AddressAliasHelper.undoL1ToL2Alias(msg.sender) == l1Bridge,

120:         require(msg.sender == l2WethAddress, "pd");

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L120-L120)

```solidity
File: contracts/interfaces/ISystemContract.sol

17:             SystemContractHelper.isSystemCall() || SystemContractHelper.isSystemContract(msg.sender),

36:         require(msg.sender == caller, "Inappropriate caller");

43:         require(msg.sender == BOOTLOADER_FORMAL_ADDRESS, "Callable only by the bootloader");

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/ISystemContract.sol#L43-L43)

```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

196:             refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L196-L196)

```solidity
File: ethereum/contracts/bridge/L1WethBridge.sol

183:             refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;

311:         require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");

311:         require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");

311:         require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");

311:         require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L311-L311)

```solidity
File: ethereum/contracts/governance/Governance.sol

59:         require(msg.sender == address(this), "Only governance contract itself allowed to call this function");

65:         require(msg.sender == securityCouncil, "Only security council allowed to call this function");

72:             msg.sender == owner() || msg.sender == securityCouncil,
                "Only the owner and security council are allowed to call this function"

72:             msg.sender == owner() || msg.sender == securityCouncil,
                "Only the owner and security council are allowed to call this function"

72:             msg.sender == owner() || msg.sender == securityCouncil,

72:             msg.sender == owner() || msg.sender == securityCouncil,
                "Only the owner and security council are allowed to call this function"

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L72-L73)

```solidity
File: ethereum/contracts/zksync/ValidatorTimelock.sol

66:         require(msg.sender == validator, "8h");

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L66-L66)

```solidity
File: ethereum/contracts/zksync/facets/Admin.sol

31:         require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights

55:         require(msg.sender == pendingAdmin, "n4"); // Only proposed by current admin address can claim the admin rights

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L55-L55)

```solidity
File: ethereum/contracts/zksync/facets/Base.sol

19:         require(msg.sender == s.governor, "1g"); // only by governor

25:         require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");

25:         require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");

25:         require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");

25:         require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L25-L25)

</details>

### <a name="GAS-13-1696083995"></a>[GAS-13] `abi.encode()` is less efficient than `abi.encodepacked()`
when dealing with non address type parameters `encodepacked()` function less gas than `encode()`. For more details check the following [comparison](https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison)

*Instances (24)*:
<details><summary> see instances </summary>

```solidity
File: contracts/L1Messenger.sol

110:         chainedLogsHash = keccak256(abi.encode(chainedLogsHash, hashedLog));

125:         chainedMessagesHash = keccak256(abi.encode(chainedMessagesHash, hash));

168:         chainedL1BytecodesRevealDataHash = keccak256(abi.encode(chainedL1BytecodesRevealDataHash, _bytecodeHash));

217:             reconstructedChainedLogsHash = keccak256(abi.encode(reconstructedChainedLogsHash, hashedLog));

231:                     abi.encode(l2ToL1LogsTreeArray[2 * i], l2ToL1LogsTreeArray[2 * i + 1])

248:             reconstructedChainedMessagesHash = keccak256(abi.encode(reconstructedChainedMessagesHash, hashedMessage));

265:                 abi.encode(

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L265)

```solidity
File: contracts/SystemContext.sol

130:             hash = keccak256(abi.encode(_block));

198:         return keccak256(abi.encode(_blockNumber, _blockTimestamp, _prevL2BlockHash, _blockTxsRollingHash));

374:         currentL2BlockTxsRollingHash = keccak256(abi.encode(currentL2BlockTxsRollingHash, _txHash));

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L374)

```solidity
File: contracts/libraries/TransactionHelper.sol

120:             abi.encode(

139:             abi.encode(EIP712_DOMAIN_TYPEHASH, keccak256("zkSync"), keccak256("2"), block.chainid)

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L139)

```solidity
File: ethereum/contracts/bridge/L1ERC20Bridge.sol

119:             l2BridgeProxyConstructorData = abi.encode(bridgeImplementationAddr, _governor, proxyInitializationParams);

244:         data = abi.encode(data1, data2, data3);

354:         bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""));

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L354)

```solidity
File: ethereum/contracts/bridge/L1WethBridge.sol

118:             l2WethBridgeProxyConstructorData = abi.encode(

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L118)

```solidity
File: ethereum/contracts/governance/Governance.sol

205:         return keccak256(abi.encode(_operation));

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L205)

```solidity
File: ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

173:         bytes memory encodedTransaction = abi.encode(_l2ProtocolUpgradeTx);

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L173)

```solidity
File: ethereum/contracts/zksync/DiamondInit.sol

77:         s.storedBatchHashes[0] = keccak256(abi.encode(storedBatchZero));

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L77)

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

265:             concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));

431:         return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));

458:             abi.encode(

468:         return keccak256(abi.encode(_storedBatchInfo));

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L468)

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

363:         bytes memory transactionEncoding = abi.encode(transaction);

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L363)

</details>

### <a name="GAS-14-1693313317"></a>[GAS-14] Can make the variable outside the loop to save gas
Creating variables inside the loop consum more gas compared to declaring them outside and just reaffecting values to them inside the loop.

*Instances (48)*:
<details><summary> see instances </summary>

```solidity
File: contracts/Compressor.sol

//@audit variable `indexOfEncodedChunk` is created inside a loop.
69:                 uint256 indexOfEncodedChunk = uint256(encodedData.readUint16(encodedDataPointer)) * 8;

//@audit variable `encodedChunk` is created inside a loop.
72:                 uint64 encodedChunk = dictionary.readUint64(indexOfEncodedChunk);

//@audit variable `realChunk` is created inside a loop.
73:                 uint64 realChunk = _bytecode.readUint64(encodedDataPointer * 4);

//@audit variable `stateDiff` is created inside a loop.
135:             bytes calldata stateDiff = _stateDiffs[i:i + STATE_DIFF_ENTRY_SIZE];

//@audit variable `enumIndex` is created inside a loop.
136:             uint64 enumIndex = stateDiff.readUint64(84);

//@audit variable `derivedKey` is created inside a loop.
144:             bytes32 derivedKey = stateDiff.readBytes32(52);

//@audit variable `initValue` is created inside a loop.
145:             uint256 initValue = stateDiff.readUint256(92);

//@audit variable `finalValue` is created inside a loop.
146:             uint256 finalValue = stateDiff.readUint256(124);

//@audit variable `metadata` is created inside a loop.
150:             uint8 metadata = uint8(bytes1(_compressedStateDiffs[stateDiffPtr]));

//@audit variable `operation` is created inside a loop.
152:             uint8 operation = metadata & OPERATION_BITMASK;

//@audit variable `len` is created inside a loop.
153:             uint8 len = operation == 0 ? 32 : metadata >> LENGTH_BITS_OFFSET;

//@audit variable `stateDiff` is created inside a loop.
167:             bytes calldata stateDiff = _stateDiffs[i:i + STATE_DIFF_ENTRY_SIZE];

//@audit variable `enumIndex` is created inside a loop.
168:             uint64 enumIndex = stateDiff.readUint64(84);

//@audit variable `initValue` is created inside a loop.
173:             uint256 initValue = stateDiff.readUint256(92);

//@audit variable `finalValue` is created inside a loop.
174:             uint256 finalValue = stateDiff.readUint256(124);

//@audit variable `compressedEnumIndex` is created inside a loop.
175:             uint256 compressedEnumIndex = _sliceToUint256(_compressedStateDiffs[stateDiffPtr:stateDiffPtr + _enumerationIndexSize]);

//@audit variable `metadata` is created inside a loop.
179:             uint8 metadata = uint8(bytes1(_compressedStateDiffs[stateDiffPtr]));

//@audit variable `operation` is created inside a loop.
181:             uint8 operation = metadata & OPERATION_BITMASK;

//@audit variable `len` is created inside a loop.
182:             uint8 len = operation == 0 ? 32 : metadata >> LENGTH_BITS_OFFSET;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L182)

```solidity
File: contracts/ImmutableSimulator.sol

//@audit variable `index` is created inside a loop.
39:                 uint256 index = _immutables[i].index;

//@audit variable `value` is created inside a loop.
40:                 bytes32 value = _immutables[i].value;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L40)

```solidity
File: contracts/L1Messenger.sol

//@audit variable `hashedLog` is created inside a loop.
212:             bytes32 hashedLog = EfficientCall.keccak(

//@audit variable `i` is created inside a loop.
229:             for (uint256 i = 0; i < nodesOnCurrentLevel; ++i) {

//@audit variable `currentMessageLength` is created inside a loop.
242:             uint32 currentMessageLength = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));

//@audit variable `hashedMessage` is created inside a loop.
244:             bytes32 hashedMessage = EfficientCall.keccak(

//@audit variable `currentBytecodeLength` is created inside a loop.
260:             uint32 currentBytecodeLength = uint32(

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L260)

```solidity
File: ethereum/contracts/governance/Governance.sol

//@audit variable `success` is created inside a loop.
226:             (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);

//@audit variable `returnData` is created inside a loop.
226:             (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L226)

```solidity
File: ethereum/contracts/zksync/ValidatorTimelock.sol

//@audit variable `commitBatchTimestamp` is created inside a loop.
117:                 uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L117)

```solidity
File: ethereum/contracts/zksync/facets/Executor.sol

//@audit variable `logSender` is created inside a loop.
125:             (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET);

//@audit variable `logKey` is created inside a loop.
126:             (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);

//@audit variable `logValue` is created inside a loop.
127:             (bytes32 logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);

//@audit variable `expectedUpgradeTxHash` is created inside a loop.
243:             bytes32 expectedUpgradeTxHash = i == 0 ? _systemContractUpgradeTxHash : bytes32(0);

//@audit variable `priorityOp` is created inside a loop.
264:             PriorityOperation memory priorityOp = s.priorityQueue.popFront();

//@audit variable `currentBatchCommitment` is created inside a loop.
337:             bytes32 currentBatchCommitment = _committedBatches[i].commitment;

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L337)

```solidity
File: ethereum/contracts/zksync/facets/Getters.sol

//@audit variable `facetAddr` is created inside a loop.
183:             address facetAddr = ds.facets[i];

//@audit variable `facetToSelectors` is created inside a loop.
184:             Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L184)

```solidity
File: ethereum/contracts/zksync/facets/Mailbox.sol

//@audit variable `hashedBytecode` is created inside a loop.
396:             bytes32 hashedBytecode = L2ContractHelper.hashL2Bytecode(_factoryDeps[i]);

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L396)

```solidity
File: ethereum/contracts/zksync/libraries/Diamond.sol

//@audit variable `action` is created inside a loop.
101:             Action action = facetCuts[i].action;

//@audit variable `facet` is created inside a loop.
102:             address facet = facetCuts[i].facet;

//@audit variable `isFacetFreezable` is created inside a loop.
103:             bool isFacetFreezable = facetCuts[i].isFreezable;

//@audit variable `selectors` is created inside a loop.
104:             bytes4[] memory selectors = facetCuts[i].selectors;

//@audit variable `selector` is created inside a loop.
139:             bytes4 selector = _selectors[i];

//@audit variable `oldFacet` is created inside a loop.
140:             SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

//@audit variable `selector` is created inside a loop.
160:             bytes4 selector = _selectors[i];

//@audit variable `oldFacet` is created inside a loop.
161:             SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

//@audit variable `selector` is created inside a loop.
180:             bytes4 selector = _selectors[i];

//@audit variable `oldFacet` is created inside a loop.
181:             SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L181)

</details>

### <a name="GAS-15-1693313355"></a>[GAS-15] Consider activating via-ir for deploying
The Solidity compiler's Intermediate Representation (IR) based code generator, which can be activated using --via-ir on the command line or {""viaIR"": true} in the options, serves a dual purpose. Firstly, it boosts the transparency and audibility of code generation, which enhances developers' comprehension and control over the contract's final bytecode. Secondly, it enables more sophisticated optimization passes that span multiple functions, thereby potentially leading to more efficient bytecode.
It's important to note that using the IR- based code generator may lengthen compile times due to the extra optimization steps.Therefore, it's advised to test your contract with and without this option enabled to measure the performance and gas cost implications.If the IR- based code generator significantly enhances your contract's performance or reduces gas costs, consider using the --via-ir flag during deployment.This way, you can leverage more advanced compiler optimizations without hindering your development workflow.

*Instances (1)*:
<details><summary> see instances </summary>

```solidity
File: hardhat.config.ts

//@audit /2023-10-zksync/code/zksync/hardhat.config.ts
1: 

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/main/hardhat.config.ts#L1)

</details>

### <a name="GAS-15-1693313356"></a>[GAS-16] Use the result of the function call directly to save gas
As the result of the function call is used only once, then you can use the function result directly to save gas rather than caching its result. this way you can save **10 gas**

*Instances (1)*:
<details><summary> see instances </summary>

Change this:

```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

166:    function l2TransactionBaseCost(
            uint256 _gasPrice,
            uint256 _l2GasLimit,
            uint256 _l2GasPerPubdataByteLimit
        ) public pure returns (uint256) {
            uint256 l2GasPrice = _deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit);
            return l2GasPrice * _l2GasLimit;
        }
```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L166-L173)


</details>

### <a name="GAS-16-1693313357"></a>[GAS-17] The result of a mathematique operation is only used once
As the result of the mathematique operation is used only once, then you can combine both operation in one to save gas rather than caching its result. this way you can save **116 gas**

*Instances (2)*:
<details><summary> see instances </summary>


```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
179:        function _deriveL2GasPrice(uint256 _l1GasPrice, uint256 _gasPricePerPubdata) internal pure returns (uint256) {
                uint256 pubdataPriceETH = L1_GAS_PER_PUBDATA_BYTE * _l1GasPrice;
                uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;


                return Math.max(FAIR_L2_GAS_PRICE, minL2GasPriceETH);
            }
```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L179-L182)


```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
303:    {
            params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);
            uint256 baseCost = params.l2GasPrice * _l2GasLimit;
            require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost
        }

```
[Link to code](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L305)
</details>