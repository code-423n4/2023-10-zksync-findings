
## LOW Risk

| Number | Issue | Instances |
|--------|-------|-----------|
|[L-01]| Avoid double casting   | 47 |
|[L-02]| Functions calling contracts with transfer hooks are missing reentrancy guards  | 4 |
|[L-03]| Use of transfer instead of safeTransfer  | 5 |
|[L-04]| Using zero as a parameter  | 40 |
|[L-05]| Revert on transfer to the zero address  | 3 |
|[L-06]| Constant array index within iteration  | 33 |
|[L-07]| Use of ecrecover is susceptible to signature malleability  | 1 |
|[L-08]| Truncating block.timestamp can reduce the lifespan of a contract  | 2 |
|[L-09]| Execution at deadlines should be allowed  | 1 |
|[L-10]| Use of abi.encodePacked with dynamic types inside keccak256  | 6 |
|[L-11]| Using >/>= without specifying an upper bound in version pragma is unsafe  | 1 |
|[L-12]| Array is push()ed but not pop()ed  | 2 |
|[L-13]| Potential division by zero should have zero checks in place  | 3 |
|[L-14]| Upgradeable contract uses non-upgradeable version of the OpenZeppelin libraries/contracts  | 10 |
|[L-15]| Functions which set address state variables should have zero address checks  | 8 |
|[L-16]| Tokens may be minted to `address(0x0)`  | 1 |
|[L-17]| Array lengths not checked  | 4 |
|[L-18]| Consider implementing two-step procedure for updating protocol addresses  | 3 |
|[L-19]|  `_addOneFunction()` does note remove old entries before adding new ones  | 2 |
|[L-20]| decimals() is not a part of the ERC-20 standard  | 1 |
|[L-21]| symbol() is not a part of the ERC-20 standard  | 2 |
|[L-22]| Setters should have initial value check  | 8 |
|[L-23]| Large approvals may not work with some ERC20 tokens  | 2 |
|[L-24]| unchecked blocks with additions/multiplications may overflow  | 22 |
|[L-25]| Variable being initialized with the default value  | 4 |



## [L-01] Avoid double casting

Consider refactoring the following code, as double casting may introduce unexpected truncations and/or rounding issues.

Furthermore, double type casting can make the code less readable and harder to maintain, increasing the likelihood of errors and misunderstandings during development and debugging.

```solidity
file:  ethereum/contracts/bridge/L1ERC20Bridge.sol

355   bytes32 salt = bytes32(uint256(uint160(_l1Token)));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L255


```solidity
file:  ethereum/contracts/common/libraries/L2ContractHelper.sol

30  hashedBytecode = (hashedBytecode | bytes32(uint256(1 << 248)));

50   codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));

66   bytes32 senderBytes = bytes32(uint256(uint160(_sender)));

71    return address(uint160(uint256(data)));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L30


```solidity
file: ethereum/contracts/zksync/facets/Mailbox.sol

106    value: bytes32(uint256(_status))

156    key: bytes32(uint256(uint160(_message.sender))),

336    from: uint256(uint160(_priorityOpParams.sender)),

337    to: uint256(uint160(_priorityOpParams.contractAddressL2)),

346    reserved: [_priorityOpParams.valueToMint, uint256(uint160(_priorityOpParams.refundRecipient)), 0, 0],

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L106

```solidity
file: contracts/zksync/contracts/L2ContractHelper.sol

111    bytes32 senderBytes = bytes32(uint256(uint160(_sender)));

116    return address(uint160(uint256(data)));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/L2ContractHelper.sol#L111

```solidity
file:  contracts/zksync/contracts/bridge/L2ERC20Bridge.sol

141   salt = bytes32(uint256(uint160(_l1Token)));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L141

```solidity
file:  system-contracts/contracts/AccountCodeStorage.sol

69     uint256 addressAsKey = uint256(uint160(_address));

79     uint256 addressAsKey = uint256(uint160(_address));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L69


```solidity
file:  system-contracts/contracts/BootloaderUtilities.sol

81    uint256 rInt = uint256(bytes32(_transaction.signature[0:32]));

86    uint256 sInt = uint256(bytes32(_transaction.signature[32:64]));

91    uint256 vInt = uint256(uint8(_transaction.signature[64]));

180   uint256 rInt = uint256(bytes32(_transaction.signature[0:32]));

185   uint256 sInt = uint256(bytes32(_transaction.signature[32:64]));

190   uint256 vInt = uint256(uint8(_transaction.signature[64]));

275   uint256 rInt = uint256(bytes32(_transaction.signature[0:32]));

280   uint256 sInt = uint256(bytes32(_transaction.signature[32:64]));

285   uint256 vInt = uint256(uint8(_transaction.signature[64]));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L81

```solidity
file: code/system-contracts/contracts/Compressor.sol

251   number = uint256(bytes32(_calldataSlice));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L251


```solidity
file: system-contracts/contracts/Constants.sol

35   uint256 constant CURRENT_MAX_PRECOMPILE_ADDRESS = uint256(uint160(SHA256_SYSTEM_CONTRACT));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Constants.sol#L35

```solidity
file: system-contracts/contracts/ContractDeployer.sol

103   bytes.concat(CREATE2_PREFIX, bytes32(uint256(uint160(_sender))), _salt, _bytecodeHash, constructorInputHash)

106   newAddress = address(uint160(uint256(hash)));

119   bytes.concat(CREATE_PREFIX, bytes32(uint256(uint160(_sender))), bytes32(_senderNonce))

122   newAddress = address(uint160(uint256(hash)));

269   ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getCodeHash(uint256(uint160(_newAddress))) == 0x0,


```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L103


```solidity
file: system-contracts/contracts/ImmutableSimulator.sol

28   return immutableDataStorage[uint256(uint160(_dest))][_index];

41    immutableDataStorage[uint256(uint160(_dest))][index] = value;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L28


```solidity
file: system-contracts/contracts/L1Messenger.sol

133    key: bytes32(uint256(uint160(msg.sender))),

323    SystemContractHelper.toL1(true, bytes32(uint256(SystemLogKey.L2_TO_L1_LOGS_TREE_ROOT_KEY)), l2ToL1LogsTreeRoot);

326    bytes32(uint256(SystemLogKey.TOTAL_L2_TO_L1_PUBDATA_KEY)),

329    SystemContractHelper.toL1(true, bytes32(uint256(SystemLogKey.STATE_DIFF_HASH_KEY)), stateDiffHash);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L133

```solidity
file: system-contracts/contracts/NonceHolder.sol

68    uint256 addressAsKey = uint256(uint160(msg.sender));

92    uint256 addressAsKey = uint256(uint160(msg.sender));

103   uint256 addressAsKey = uint256(uint160(msg.sender));

111   uint256 addressAsKey = uint256(uint160(msg.sender));

126   uint256 addressAsKey = uint256(uint160(_address));

137   uint256 addressAsKey = uint256(uint160(_address));

148   uint256 addressAsKey = uint256(uint160(_address));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L68


```solidity
file:  contracts/libraries/TransactionHelper.sol

95   return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L95


```solidity
file: system-contracts/contracts/libraries/Utils.sol

46    codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));

93    hashedBytecode = (hashedBytecode | bytes32(uint256(1 << 248)));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L46


## [L-02] Functions calling contracts with transfer hooks are missing reentrancy guards

Even if the function follows the best practice of check-effects-interaction, not using a reentrancy guard when there may be transfer hooks will open the users of this protocol up to read-only reentrancies with no way to protect against it, except by block-listing the whole protocol.


```solidity
file: ethereum/contracts/bridge/L1ERC20Bridge.sol

218   _token.safeTransferFrom(_from, address(this), _amount);

317    IERC20(l1Token).safeTransfer(l1Receiver, amount);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L218


```solidity
file: contracts/zksync/contracts/bridge/L2ERC20Bridge.sol

54    l2TokenBeacon.transferOwnership(_governor);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L54

```solidity
file:  system-contracts/contracts/ContractDeployer.sol

334  ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo(address(this), _newAddress, value);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L334

## [L-03] Use of transfer instead of safeTransfer

It is good to add a require statement that checks the return value of token transfers, or to use something like OpenZeppelin's safeTransfer/safeTransferFrom, even if one is sure that the given token reverts in case of a failure.

This reduces the risk to zero even if these contracts are upgreadable, and it also helps with security reviews, as the auditor will not have to check this specific edge case.

```solidity
file: ethereum/contracts/bridge/L1ERC20Bridge.sol

218   _token.safeTransferFrom(_from, address(this), _amount);

282   IERC20(_l1Token).safeTransfer(_depositSender, amount);

317   IERC20(l1Token).safeTransfer(l1Receiver, amount);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L218

```solidity
file:  ethereum/contracts/bridge/L1WethBridge.sol

171   IERC20(l1WethAddress).safeTransferFrom(msg.sender, address(this), _amount);

263   IERC20(l1WethAddress).safeTransfer(l1WethWithdrawReceiver, amount);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L171

## [L‑04] Using zero as a parameter

Taking 0 as a valid argument in Solidity without checks can lead to severe security issues. A historical example is the infamous 0x0 address bug where numerous tokens were lost. This happens because '0' can be interpreted as an uninitialized address, leading to transfers to the '0x0' address, effectively burning tokens. Moreover, 0 as a denominator in division operations would cause a runtime exception. It's also often indicative of a logical error in the caller's code. It's important to always validate input and handle edge cases like 0 appropriately. Use require() statements to enforce conditions and provide clear error messages to facilitate debugging and safer code.

```solidity
file: ethereum/contracts/zksync/DiamondProxy.sol

37    calldatacopy(ptr, 0, calldatasize())

39    let result := delegatecall(gas(), facetAddress, ptr, calldatasize(), 0, 0)

43    returndatacopy(ptr, 0, size)

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L37

```solidity
file:  ethereum/contracts/zksync/ValidatorTimelock.sol

136    calldatacopy(0, 0, calldatasize())

138    let result := call(gas(), contractAddress, 0, 0, calldatasize(), 0, 0)

142    returndatacopy(0, 0, size)

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L136

```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

100     L2Log memory l2Log = L2Log({
            l2ShardId: 0,
            isService: true,
            txNumberInBatch: _l2TxNumberInBatch,
            sender: L2_BOOTLOADER_ADDRESS,
            key: _l2TxHash,
            value: bytes32(uint256(_status))
        });
117    callSuccess := call(gas(), _to, _amount, 0, 0, 0, 0)

151   L2Log({
                l2ShardId: 0,
                isService: true,
                txNumberInBatch: _message.txNumberInBatch,
                sender: L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR,
                key: bytes32(uint256(uint160(_message.sender))),
                value: keccak256(_message.data)
            });

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L100-L107

```solidity
file:  ethereum/contracts/zksync/DiamondInit.sol

66  IExecutor.StoredBatchInfo memory storedBatchZero = IExecutor.StoredBatchInfo(
            0,
            _initalizeData.genesisBatchHash,
            _initalizeData.genesisIndexRepeatedStorageChanges,
            0,
            EMPTY_STRING_KECCAK,
            DEFAULT_L2_LOGS_TREE_ROOT_HASH,
            0,
            _initalizeData.genesisBatchCommitment
        );

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L66-L75

```solidity
file:  ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol

41    _zkSync.requestL2Transaction{value: _deployTransactionFee}(
            L2_DEPLOYER_SYSTEM_CONTRACT_ADDR,
            0,
            deployCalldata,
            DEPLOY_L2_BRIDGE_COUNTERPART_GAS_LIMIT,
            REQUIRED_L2_GAS_PRICE_PER_PUBDATA,
            _factoryDeps,
            msg.sender
        );

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol#L41-L49

```solidity
file:   contracts/zksync/contracts/SystemContractsCaller.sol

51     uint256 farCallAbi = getFarCallABI(
            0,
            0,
            dataStart,
            dataLength,
            gasLimit,
            // Only rollup is supported for now
            0,
            CalldataForwardingMode.UseHeap,
            false,
            true
        );

67   success := call(to, callAddr, 0, 0, farCallAbi, 0, 0)

96   returndatacopy(add(returnData, 0x20), 0, size)

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/SystemContractsCaller.sol#L51-L62

```solidity
file:  system-contracts/contracts/libraries/EfficientCall.sol

136    success := call(_address, callAddr, 0, 0, 0xFFFF, 0, 0)

164    success := staticcall(_address, callAddr, 0, 0xFFFF, 0, 0)

178    success := delegatecall(_address, callAddr, 0, 0xFFFF, 0, 0)

208    success := call(_address, callAddr, 0, 0, _whoToMimic, 0, 0)

224    returndatacopy(add(returnData, 0x20), 0, size)

235    returndatacopy(0, 0, size)

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L136

```solidity
file: system-contracts/contracts/libraries/SystemContractHelper.sol

82   let success := call(_isService, callAddr, _key, _value, 0xFFFF, 0, 0)

93   addr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

104  pop(staticcall(0, callAddr, 0, 0xFFFF, 0, 0))

115  pop(staticcall(_farCallAbi, callAddr, 0, 0xFFFF, 0, 0))

127  pop(staticcall(_value, callAddr, 0, 0xFFFF, 0, 0))

139  pop(staticcall(_shrink, callAddr, 0, 0xFFFF, 0, 0))\

182  success := staticcall(_rawParams, callAddr, _gasToBurn, 0xFFFF, 0, 0)

195  success := call(0, callAddr, _value, 0, 0xFFFF, 0, 0)

205  pop(call(initializer, callAddr, value1, 0, 0xFFFF, 0, 0))

215  pop(call(value1, callAddr, value2, 0, 0xFFFF, 0, 0))

226  meta := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

315  callFlags := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

326  ptr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

339  extraAbiData := staticcall(index, callAddr, 0, 0xFFFF, 0, 0)


```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L82

```solidity
file: system-contracts/contracts/libraries/TransactionHelper.sol

382   IERC20(token).safeApprove(paymaster, 0);

400   success := call(gas(), bootloaderAddr, amount, 0, 0, 0, 0)

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L382

```solidity
file:  system-contracts/contracts/openzeppelin/utils/Address.sol
 
96   functionCallWithValue(
                target,
                data,
                0,
                "Address: low-level call failed"
            );
115  return functionCallWithValue(target, data, 0, errorMessage);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L96-L101

```solidity 
file: contracts/ethereum/contracts/common/AllowList.sol

49  function setAccessMode(address _target, AccessMode _accessMode) external onlyOwner {
 
58   function setBatchAccessMode(address[] calldata _targets, AccessMode[] calldata _accessModes) external onlyOwner {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L49

## [L‑05] Revert on transfer to the zero address

It's good practice to revert a token transfer transaction if the recipient's address is the zero address. This can prevent unintentional transfers to the zero address due to accidental operations or programming errors. Many token contracts implement such a safeguard, such as


```solidity
file:  ethereum/contracts/bridge/L1ERC20Bridge.sol

282   IERC20(_l1Token).safeTransfer(_depositSender, amount);

317   IERC20(l1Token).safeTransfer(l1Receiver, amount);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L282 

```solidity
file:  ethereum/contracts/bridge/L1WethBridge.sol

263   IERC20(l1WethAddress).safeTransfer(l1WethWithdrawReceiver, amount);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L263


## [L-06] Constant array index within iteration

Using constant array indexes within for loops is error prone as typically [i] is used instead as by using a constant index such as [0] means that only one value will be used/modified within an array, historically many bugs have been introduced through using a constant index rather than the array iteration index i.e i,j. Provided this is intentional and not a typo consider using enum values to make this distinction clear.


```solidity
file: ethereum/contracts/bridge/L1ERC20Bridge.sol

96    l2TokenProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[2]);

99    bytes32 l2BridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);

100   bytes32 l2BridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L99

```solidity
file:  ethereum/contracts/bridge/L1WethBridge.sol

98    bytes32 l2WethBridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);

99    bytes32 l2WethBridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L98


```solidity
file:  system-contracts/contracts/libraries/Utils.sol

52   return _bytecodeHash[1] == 0x00;

57   return _bytecodeHash[1] == 0x01;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L52

```solidity
file: ethereum/contracts/common/libraries/L2ContractHelper.sol

40    uint8 version = uint8(_bytecodeHash[0]);

41    require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash

50    codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L40


```solidity
file:  ethereum/contracts/zksync/DiamondInit.sol

77    s.storedBatchHashes[0] = keccak256(abi.encode(storedBatchZero));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L77

```solidity
file:  ethereum/contracts/zksync/facets/Executor.sol

239   s.l2SystemContractsUpgradeBatchNumber = _newBatchesData[0].batchNumber;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L239

```solidity
file:  ethereum/contracts/zksync/facets/Getters.sol

142    bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L142

```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

219    bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L219

```solidity
file:  ethereum/contracts/zksync/libraries/TransactionValidator.sol

54   require(_transaction.reserved[0] == 0, "ue");

55   require(_transaction.reserved[1] <= type(uint160).max, "uf");

56   require(_transaction.reserved[2] == 0, "ug");

57   require(_transaction.reserved[3] == 0, "uo");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L54

```solidity
file: system-contracts/contracts/BootloaderUtilities.sol

72    } else if (_transaction.data[0] >= 0x80) {

96    if (_transaction.reserved[0] != 0) {

168   } else if (_transaction.data[0] >= 0x80) {

263   } else if (_transaction.data[0] >= 0x80) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L72

```solidity
file:  system-contracts/contracts/KnownCodesStorage.sol

76    uint8 version = uint8(_bytecodeHash[0]);

77   require(version == 1 && _bytecodeHash[1] == bytes1(0), "Incorrectly formatted bytecodeHash");

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol#L76

```solidity
file:  system-contracts/contracts/L1Messenger.sol

235   bytes32 l2ToL1LogsTreeRoot = l2ToL1LogsTreeArray[0];

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L235

```solidity
file:  system-contracts/contracts/libraries/RLPEncoder.sol

29    encoded[0] = (_val == 0) ? bytes1(uint8(128)) : bytes1(uint8(_val));

34    encoded[0] = bytes1(uint8(hbs + 0x81));

64    encoded[0] = bytes1(uint8(_len + _offset));

69    encoded[0] = bytes1(uint8(_offset + hbs + 56));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L29

```solidity
file:  system-contracts/contracts/libraries/TransactionHelper.sol

175    } else if (_transaction.data[0] >= 0x80) {

184    if (_transaction.reserved[0] != 0) {

253    } else if (_transaction.data[0] >= 0x80) {

325    } else if (_transaction.data[0] >= 0x80) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L175

## [L-07] Use of ecrecover is susceptible to signature malleability

The built-in EVM precompile ecrecover is susceptible to signature malleability, which could lead to replay attacks.

Consider using OpenZeppelin’s ECDSA library instead of the built-in function.

```solidity
file: system-contracts/contracts/DefaultAccount.sol

188  address recoveredAddress = ecrecover(_hash, v, r, s);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L188

## [L-08] Truncating block.timestamp can reduce the lifespan of a contract

Consider removing the casting to extend the lifespan of these contracts.

```solidity
file: ethereum/contracts/zksync/ValidatorTimelock.sol

84   uint32 timestamp = uint32(block.timestamp);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L84

```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

295   uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L295

## [L-09] Execution at deadlines should be allowed

The condition may be wrong in these cases, as when block.timestamp is equal to the compared > or < variable these blocks will not be executed.

```solidity
file:  ethereum/contracts/governance/Governance.sol

111    } else if (timestamp > block.timestamp) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L111

## [L-10] Use of abi.encodePacked with dynamic types inside keccak256

abi.encodePacked should not be used with dynamic types when passing the result to a hash function such as keccak256. Use abi.encode instead, which will pad items to 32 bytes, to prevent any hash collisions.

```solidity
file:  ethereum/contracts/zksync/facets/Executor.sol

382       uint256(
           keccak256(
               abi.encodePacked(
                   _prevBatchCommitment,
                   _currentBatchCommitment,
                   _verifierParams.recursionNodeLevelVkHash,
                   _verifierParams.recursionLeafLevelVkHash
               )
           )
       ) & INP
       
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L382-L393

```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

131     bytes32 hashedLog = keccak256(
            abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBatch, _log.sender, _log.key, _log.value)
        );

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L131-L133

```solidity
file:  system-contracts/contracts/L1Messenger.sol

99   bytes32 hashedLog = keccak256(
            abi.encodePacked(
                _l2ToL1Log.l2ShardId,
                _l2ToL1Log.isService,
                _l2ToL1Log.txNumberInBlock,
                _l2ToL1Log.sender,
                _l2ToL1Log.key,
                _l2ToL1Log.value
            )
        );

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L99-L108

```solidity
file: system-contracts/contracts/SystemContext.sol

205   system-contracts/contracts/L1Messenger.sol

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L205

```solidity
file:  system-contracts/contracts/libraries/TransactionHelper.sol

133  keccak256(abi.encodePacked(_transaction.factoryDeps)),

142   return keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L133

## [L‑11] Using >/>= without specifying an upper bound in version pragma is unsafe

There will be breaking changes in future versions of solidity, and at that point your code will no longer be compatible. While you may have the specific version to use in a configuration file, others that include your source files may not.

```solidity
file: system-contracts/contracts/libraries/Utils.sol

2  pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L2

## [L‑12] Array is push()ed but not pop()ed

Array entries are added but are never removed. Consider whether this should be the case, or whether there should be a maximum, or whether old entries should be removed. Cases where there are specific potential problems will be flagged separately under a different issue.


```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

197    ds.facets.push(_facet);

228    ds.facetToSelectors[_facet].selectors.push(_selector);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L197

## [L-13] Potential division by zero should have zero checks in place

```solidity
file: ethereum/contracts/zksync/facets/Mailbox.sol

181   uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L181

```solidity
file: ethereum/contracts/zksync/libraries/TransactionValidator.sol

32   require(l2GasForTxBody / _transaction.gasPerPubdataByteLimit <= PRIORITY_TX_MAX_PUBDATA, "uk");

165  overheadForGas = (numerator - 1) / denominator;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L32

## [L‑14] Upgradeable contract uses non-upgradeable version of the OpenZeppelin libraries/contracts

OpenZeppelin has an Upgradeable variants of each of its libraries and contracts, and upgradeable contracts should use those variants.

```solidity
file: contracts/zksync/contracts/bridge/L2ERC20Bridge.sol

5  import "@openzeppelin/contracts/proxy/beacon/BeaconProxy.sol";

6  import "@openzeppelin/contracts/proxy/beacon/UpgradeableBeacon.sol";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L5

```solidity
file:  ethereum/contracts/zksync/libraries/TransactionValidator.sol

5   import "@openzeppelin/contracts/utils/math/Math.sol";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L5

```solidity
file: ethereum/contracts/zksync/libraries/Diamond.sol

5   import "@openzeppelin/contracts/utils/math/SafeCast.sol";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L5

```solidity
file:  ethereum/contracts/zksync/facets/Mailbox.sol

5   import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L5

```solidity
file: ethereum/contracts/zksync/facets/Base.sol

9   import "@openzeppelin/contracts/access/Ownable.sol";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L9

```solidity
file: ethereum/contracts/bridge/L1WethBridge.sol

5  import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L5

```solidity
file: contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

5  import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

6  import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L5

## [L-15] Functions which set address state variables should have zero address checks

```solidity
file:  system-contracts/contracts/SystemContext.sol

87   function setTxOrigin(address _newOrigin) external onlyCallFromBootloader {
        origin = _newOrigin;
    }

93   function setGasPrice(uint256 _gasPrice) external onlyCallFromBootloader {
        gasPrice = _gasPrice;
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L87-L89

```solidity
file: ethereum/contracts/zksync/ValidatorTimelock.sol

52    function setValidator(address _newValidator) external onlyOwner {
        address oldValidator = validator;
        validator = _newValidator;
        emit NewValidator(oldValidator, _newValidator);
    }

59  function setExecutionDelay(uint32 _executionDelay) external onlyOwner {
        executionDelay = _executionDelay;
        emit NewExecutionDelay(_executionDelay);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L52-L56

```solidity
file: ethereum/contracts/governance/Governance.sol

256  function updateSecurityCouncil(address _newSecurityCouncil) external onlySelf {
        emit ChangeSecurityCouncil(securityCouncil, _newSecurityCouncil);
        securityCouncil = _newSecurityCouncil;
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L256-L259

```solidity
file: code/system-contracts/contracts/L2EthToken.sol

64  function mint(address _account, uint256 _amount) external override onlyCallFromBootloader {
        totalSupply += _amount;
        balance[_account] += _amount;
        emit Mint(_account, _amount);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L64-L68

```solidity
file: ethereum/contracts/common/AllowList.sol

68   function _setAccessMode(address _target, AccessMode _accessMode) internal {
        AccessMode accessMode = getAccessMode[_target];

        if (accessMode != _accessMode) {
            getAccessMode[_target] = _accessMode;
            emit UpdateAccessMode(_target, accessMode, _accessMode);
        }
    }
116  function _setPermissionToCall(address _caller, address _target, bytes4 _functionSig, bool _enable) internal {
        bool currentPermission = hasSpecialAccessToCall[_caller][_target][_functionSig];

        if (currentPermission != _enable) {
            hasSpecialAccessToCall[_caller][_target][_functionSig] = _enable;
            emit UpdateCallPermission(_caller, _target, _functionSig, _enable);
        }
    }


```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L68-L75

## [L-16]  Tokens may be minted to `address(0x0)`

Neither the listed functions, nor `_mint()` prevent minting to `address(0x0)`

```solidity
file:  system-contracts/contracts/L2EthToken.sol

64    function mint(address _account, uint256 _amount) external override onlyCallFromBootloader {
        totalSupply += _amount;
        balance[_account] += _amount;
        emit Mint(_account, _amount);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L64-L68

## [L-17] Array lengths not checked

If the length of the arrays are not required to be of the same length, user operations may not be fully executed due to a mismatch in the number of items iterated over, versus the number of items provided in the second array

```solidity
file:  ethereum/contracts/common/AllowList.sol

83    function setBatchPermissionToCall(
        address[] calldata _callers,
        address[] calldata _targets,
        bytes4[] calldata _functionSigs,
        bool[] calldata _enables
    ) external onlyOwner {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L83-L88

```solidity
file:  ethereum/contracts/common/interfaces/IAllowList.sol 

62     function setBatchPermissionToCall(
        address[] calldata _callers,
        address[] calldata _targets,
        bytes4[] calldata _functionSigs,
        bool[] calldata _enables
    ) external;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol#L62-L67

```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

125    function _addFunctions(
        address _facet,
        bytes4[] memory _selectors,
        bool _isFacetFreezable
    ) private {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L125-L131


```solidity
file:  ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol

30       function requestDeployTransaction(
        IZkSync _zkSync,
        uint256 _deployTransactionFee,
        bytes32 _bytecodeHash,
        bytes memory _constructorData,
        bytes[] memory _factoryDeps
    ) internal returns (address deployedAddress) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol#L30-L36


## [L-18] Consider implementing two-step procedure for updating protocol addresses

A copy-paste error or a typo may end up bricking protocol functionality, or sending tokens to an address with no known private key. Consider implementing a two-step procedure for updating protocol addresses, where the recipient is set as pending, and must 'accept' the assignment by making an affirmative call. A straight forward way of doing this would be to have the target contracts implement [EIP-165](https://eips.ethereum.org/EIPS/eip-165), and to have the 'set' functions ensure that the recipient is of the right interface type.

```solidity
file:  ethereum/contracts/bridge/L1ERC20Bridge.sol

144      function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        uint256 _l2TxGasLimit,
        uint256 _l2TxGasPerPubdataByte
    ) external payable returns (bytes32 l2TxHash) {
        l2TxHash = deposit(_l2Receiver, _l1Token, _amount, _l2TxGasLimit, _l2TxGasPerPubdataByte, address(0));
    }

176     function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        uint256 _l2TxGasLimit,
        uint256 _l2TxGasPerPubdataByte,
        address _refundRecipient
    ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) {
        require(_amount != 0, "2T"); // empty deposit amount
        uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
        require(amount == _amount, "1T"); // The token has non-standard transfer logic
        // verify the deposit amount is allowed

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L144-L152

```solidity
file:  ethereum/contracts/common/AllowList.sol

106    function setPermissionToCall(
        address _caller,
        address _target,
        bytes4 _functionSig,
        bool _enable
    ) external onlyOwner {
        _setPermissionToCall(_caller, _target, _functionSig, _enable);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L106-L113

## [L-19]  `_addOneFunction()` does note remove old entries before adding new ones

Each time `_addOneFunction()` is called, new entries are added to the array, but doing so does not remove any old entries. By calling the function multiple times, an attacker can can increase their voting power indefinitely, without having to acquire new tokens.

```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

206   function _addOneFunction(
        address _facet,
        bytes4 _selector,
        bool _isSelectorFreezable
    ) private {
        DiamondStorage storage ds = getDiamondStorage();

        uint16 selectorPosition = (ds.facetToSelectors[_facet].selectors.length).toUint16();

        // if selectorPosition is nonzero, it means it is not a new facet
        // so the freezability of the first selector must be matched to _isSelectorFreezable
        // so all the selectors in a facet will have the same freezability
        if (selectorPosition != 0) {
            bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];
            require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
        }

        ds.selectorToFacet[_selector] = SelectorToFacet({
            facetAddress: _facet,
            selectorPosition: selectorPosition,
            isFreezable: _isSelectorFreezable
        });
        ds.facetToSelectors[_facet].selectors.push(_selector);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L206-L229

```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

190      function _saveFacetIfNew(address _facet) private {
        DiamondStorage storage ds = getDiamondStorage();

        uint256 selectorsLength = ds.facetToSelectors[_facet].selectors.length;
        // If there are no selectors associated with facet then save facet as new one
        if (selectorsLength == 0) {
            ds.facetToSelectors[_facet].facetPosition = ds.facets.length.toUint16();
            ds.facets.push(_facet);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L190-L198

## [L‑20] decimals() is not a part of the ERC-20 standard

The decimals() function is not a part of the ERC-20 standard, and was added later as an optional extension. As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.

```solidity
file:   contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

243    (, bytes memory data3) = _token.staticcall(abi.encodeCall(IERC20Metadata.decimals, ()));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L243


## [L‑21] symbol() is not a part of the ERC-20 standard

The symbol() function is not a part of the ERC-20 standard, and was added later as an optional extension. As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.


```solidity
file: contracts/zksync/contracts/bridge/L2StandardERC20.sol

132   return super.symbol();

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L132 

```solidity
file:  ethereum/contracts/bridge/L1ERC20Bridge.sol

242    (, bytes memory data2) = _token.staticcall(abi.encodeCall(IERC20Metadata.symbol, ()));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L242

## [L‑22] Setters should have initial value check

Setters should have initial value check to prevent assigning wrong value to the variable. Assginment of wrong value can lead to unexpected behavior of the contract.


```solidity
file:  ethereum/contracts/common/interfaces/IAllowList.sol
   
58   function setBatchAccessMode(address[] calldata _targets, AccessMode[] calldata _accessMode) external;

60   function setAccessMode(address _target, AccessMode _accessMode) external;

62   function setBatchPermissionToCall(

69   function setPermissionToCall(

60   function setDepositLimit(

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol#L58

```solidity
file: system-contracts/contracts/libraries/SystemContractHelper.sol

189   function setValueForNextFarCall(uint128 _value) internal returns (bool success) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L189

```solidity
file: ethereum/contracts/zksync/facets/Admin.sol

20  function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {

44  function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L20

## [L-23] Large approvals may not work with some ERC20 tokens

Not all IERC20 implementations are totally compliant, and some (e.g UNI, COMP) may fail if the valued passed to approve is larger than uint96. If the approval amount is type(uint256).max, which may cause issues with systems that expect the value passed to approve to be reflected in the allowances mapping.

```solidity
file: system-contracts/contracts/libraries/TransactionHelper.sol

382    IERC20(token).safeApprove(paymaster, 0);

383    IERC20(token).safeApprove(paymaster, minAllowance);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L382

## [L-24] unchecked blocks with additions/multiplications may overflow

There aren't any checks to avoid an overflow which can happen inside an unchecked block, so the following expressions may overflow silently.


```solidity
file: ethereum/contracts/vendor/AddressAliasHelper.sol

29   unchecked {
            l2Address = address(uint160(l1Address) + offset);
        }
    
39   unchecked {
            l1Address = address(uint160(l2Address) - offset);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L29-L31


```solidity
file: ethereum/contracts/zksync/libraries/TransactionValidator.sol

118   unchecked {
            // We enforce the fact that `_totalGasLimit >= overhead` explicitly above.
            txBodyGasLimit = _totalGasLimit - overhead;
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L118-L21

```solidity
file:  system-contracts/contracts/BootloaderUtilities.sol

104      unchecked {
            uint256 listLength = encodedNonce.length +
                encodedGasParam.length +
                encodedTo.length +
                encodedValue.length +
                encodedDataLength.length +
                _transaction.data.length +
                rEncoded.length +
                sEncoded.length +
                vEncoded.length;

            // Safe cast, because the length of the list can't be so large.
            encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
        }

197     unchecked {
            uint256 listLength = encodedFixedLengthParams.length +
                encodedDataLength.length +
                _transaction.data.length +
                encodedAccessListLength.length +
                rEncoded.length +
                sEncoded.length +
                vEncoded.length;

            // Safe cast, because the length of the list can't be so large.
            encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
        }

292    unchecked {
            uint256 listLength = encodedFixedLengthParams.length +
                encodedDataLength.length +
                _transaction.data.length +
                encodedAccessListLength.length +
                rEncoded.length +
                sEncoded.length +
                vEncoded.length;

            // Safe cast, because the length of the list can't be so large.
            encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L104-L117

```solidity
file: system-contracts/contracts/L2EthToken.sol

42     unchecked {
            balance[_from] = fromBalance - _amount;
            // Overflow not possible: the sum of all balances is capped by totalSupply, and the sum is preserved by
            // decrementing then incrementing.
            balance[_to] += _amount;
        }

103    unchecked {
            // This is safe, since this contract holds the ether balances, and if user
            // send a `msg.value` it will be added to the contract (`this`) balance.
            balance[address(this)] -= amount;
            totalSupply -= amount;
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L42-L47

```solidity
file: code/system-contracts/contracts/NonceHolder.sol
 
71    unchecked {
            rawNonces[addressAsKey] = (oldRawNonce + _value);
        }
117   unchecked {
            rawNonces[addressAsKey] = oldRawNonce + 1;
        }
    
140     unchecked {
            rawNonces[addressAsKey] = (oldRawNonce + DEPLOY_NONCE_MULTIPLIER);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L71-L73

```solidity
file:  system-contracts/contracts/libraries/TransactionHelper.sol
 
189    unchecked {
            uint256 listLength = encodedNonce.length +
                encodedGasParam.length +
                encodedTo.length +
                encodedValue.length +
                encodedDataLength.length +
                _transaction.data.length +
                encodedChainId.length;

            // Safe cast, because the length of the list can't be so large.
            encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
        }

264   unchecked {
            uint256 listLength = encodedFixedLengthParams.length +
                encodedDataLength.length +
                _transaction.data.length +
                encodedAccessListLength.length;

            // Safe cast, because the length of the list can't be so large.
            encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
        }

336   unchecked {
            uint256 listLength = encodedFixedLengthParams.length +
                encodedDataLength.length +
                _transaction.data.length +
                encodedAccessListLength.length;

            // Safe cast, because the length of the list can't be so large.
            encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L189-L100

```solidity
file: system-contracts/contracts/L1Messenger.sol

143    unchecked {
            // 4 bytes used to encode the length of the message (see `publishPubdataAndClearState`)
            // L2_TO_L1_LOG_SERIALIZE_SIZE bytes used to encode L2ToL1Log
            pubdataLen = 4 + _message.length + L2_TO_L1_LOG_SERIALIZE_SIZE;
        }
177   unchecked {
            // 4 bytes used to encode the length of the bytecode (see `publishPubdataAndClearState`)
            pubdataLen = 4 + bytecodeLen;
        }


```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L143-L147

```solidity
file: ethereum/contracts/vendor/AddressAliasHelper.sol

29    unchecked {
            l2Address = address(uint160(l1Address) + offset);
        }

39     unchecked {
            l1Address = address(uint160(l2Address) - offset);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L29-L31

```solidity
file:   ethereum/contracts/zksync/libraries/LibMap.sol

17     unchecked {
            // Each storage slot can store 256 bits of data.
            // As uint32 is 32 bits long, 8 uint32s can be packed into one storage slot.
            // Hence, `_index / 8` is done to find the storage slot that contains the required uint32.
            uint256 mapValue = _map.map[_index / 8];

            // First three bits of the original `_index` denotes the position of the uint32 in that slot.
            // So, '(_index & 7) * 32' is done to find the bit position of the uint32 in that storage slot.
            uint256 bitOffset = (_index & 7) * 32;

            // Shift the bits to the right and retrieve the uint32 value.
            result = uint32(mapValue >> bitOffset);
        }
41     unchecked {
            // Each storage slot can store 256 bits of data.
            // As uint32 is 32 bits long, 8 uint32s can be packed into one storage slot.
            // Hence, `_index / 8` is done to find the storage slot that contains the required uint32.
            uint256 mapIndex = _index / 8;
            uint256 mapValue = _map.map[mapIndex];

            // First three bits of the original `_index` denotes the position of the uint32 in that slot.
            // So, '(_index & 7) * 32' is done to find the bit position of the uint32 in that storage slot.
            uint256 bitOffset = (_index & 7) * 32;

            // XORing a value A with B, and then with A again, gives the original value B.
            // We will use this property to update the uint32 value in the slot.

            // Shift the bits to the right and retrieve the uint32 value.
            uint32 oldValue = uint32(mapValue >> bitOffset);

            // Calculate the XOR of the new value and the existing value.
            uint256 newValueXorOldValue = uint256(oldValue ^ _value);

            // Finally, we XOR the slot with the XOR of the new value and the existing value,
            // shifted to its proper position. The XOR operation will effectively replace the old value with the new value.
            _map.map[mapIndex] = (newValueXorOldValue << bitOffset) ^ mapValue;
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L17-L29

```solidity
file:  ethereum/contracts/common/libraries/UncheckedMath.sol

12    unchecked {
            return _number + 1;
        }

18    unchecked {
            return _lhs + _rhs;
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UncheckedMath.sol#L18-L20


## [L-25] Variable being initialized with the default value

Unsigned integers will already be initalized with zero on their declaration, e.g. there’s no need to manually assign zero.

```solidity
file:  ethereum/contracts/zksync/libraries/TransactionValidator.sol

92   uint256 costForPubdata = 0;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L92


```solidity
file:  system-contracts/contracts/Compressor.sol

131    uint256 numInitialWritesProcessed = 0;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L131


```solidity
file:  system-contracts/contracts/ContractDeployer.sol

247   uint256 sumOfValues = 0;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L247

```solidity
file:  system-contracts/contracts/L1Messenger.sol

202    uint256 calldataPtr = 0;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L202


