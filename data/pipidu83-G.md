## Use custom errors instead of require/assert

It will lead to cheaper deployment cost as well as cheaper runtime cost when the revert condition is met.

### zkSync

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

```
file: zksync/facets/Executor.sol

32        require(_newBatch.batchNumber == _previousBatch.batchNumber + 1, "f"); // only commit next batch

45        require(_previousBatch.batchHash == previousBatchHash, "l");

47        require(expectedPriorityOperationsHash == _newBatch.priorityOperationsHash, "t");

49        require(expectedNumberOfLayer1Txs == _newBatch.numberOfLayer1Txs, "ta");

81        require(batchTimestamp == _expectedBatchTimestamp, "tb");

85        require(_previousBatchTimestamp < batchTimestamp, "h3");

93        require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small

94        require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big

130       require(!_checkBit(processedLogs, uint8(logKey)), "kp");

135       require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lm");

138       require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "ln");

139       require(providedL2ToL1PubdataHash == logValue, "wp");
141       require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lb");

144       require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sc");

147       require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sv");

150       require(logSender == L2_BOOTLOADER_ADDRESS, "bl");

151       require(logSender == L2_BOOTLOADER_ADDRESS, "bk");

156       require(logSender == L2_BOOTLOADER_ADDRESS, "bu");

157       require(_expectedSystemContractUpgradeTxHash == logValue, "ut");

167       require(processedLogs == 127, "b7");

169       require(processedLogs == 255, "b8");

184       require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data

185       require(_newBatchesData.length > 0, "No batches to commit");

236       require(s.l2SystemContractsUpgradeBatchNumber == 0, "ik");

275       require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k"); // Execute batches in order

276        require(
            _hashStoredBatchInfo(_storedBatch) == s.storedBatchHashes[currentBatchNumber],
            "exe10" // executing batch should be committed
        );

282        require(priorityOperationsHash == _storedBatch.priorityOperationsHash, "x"); // priority operations hash does not match to expected

300        require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.

327        require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");

332        require(
                _hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],
                "o1"
            );

346        require(currentTotalBatchesVerified <= s.totalBatchesCommitted, "q");

351        assert(block.chainid != 1);

360        require(successVerifyProof, "p"); // Proof verification fail

368        require(successVerifyProof, "p"); // Proof verification fail

399        require(s.totalBatchesCommitted > _newLastBatch, "v1"); // The last committed batch is less than new last batch

400        require(_newLastBatch >= s.totalBatchesExecuted, "v2"); // Already executed batches cannot be reverted

453        require(_batch.systemLogs.length <= MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES, "pu");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

```
file: zksync/facets/Mailbox.sol

119        require(callSuccess, "pz");

129        require(_batchNumber <= s.totalBatchesExecuted, "xx");

136        require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");

199        require(!s.isEthWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "jj");

210        require(proofValid, "pi"); // Failed to verify that withdrawal was actually initialized on L2

257        require(_l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp");

279        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");

294        require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "uj");

306        require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost

421        require(_message.length >= 56, "pm");

424        require(bytes4(functionSignature) == this.finalizeEthWithdrawal.selector, "is");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

```
file: zksync/libraries/Diamond.sol

106        require(selectors.length > 0, "B"); // no functions for diamond cut

132        require(_facet != address(0), "G"); // facet with zero address cannot be added

141        require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists

156        require(_facet != address(0), "K"); // cannot replace facet with zero address

162        require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address

176        require(_facet == address(0), "a1"); // facet address must be zero

182        require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet

220        require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");

285        require(_calldata.length == 0, "H"); // Non-empty calldata for zero address

302        require(data.length == 32, "lp");

303        require(abi.decode(data, (bytes32)) ==DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol

```
file: zksync/facets/Getters.sol

160        require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");

161        return ds.selectorToFacet[_selector].isFreezable;
```


https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol

```
file: zksync/libraries/TransactionValidator.sol

30        require(l2GasForTxBody <= _priorityTxMaxGasLimit, "ui");

32        require(l2GasForTxBody / _transaction.gasPerPubdataByteLimit <= PRIORITY_TX_MAX_PUBDATA, "uk");

36        require(
            getMinimalPriorityTransactionGasLimit(
                _encoded.length,
                _transaction.factoryDeps.length,
                _transaction.gasPerPubdataByteLimit
            ) <= _transaction.gasLimit,
            "up"
        );

50        require(_transaction.from <= type(uint16).max, "ua");

51        require(_transaction.to <= type(uint160).max, "ub");

52        require(_transaction.paymaster == 0, "uc");

53        require(_transaction.value == 0, "ud");

54        require(_transaction.reserved[0] == 0, "ue");

55        require(_transaction.reserved[1] <= type(uint160).max, "uf");

56        require(_transaction.reserved[2] == 0, "ug");

57        require(_transaction.reserved[3] == 0, "uo");

58        require(_transaction.signature.length == 0, "uh");

59        require(_transaction.paymasterInput.length == 0, "ul");

60        require(_transaction.reservedDynamic.length == 0, "um");

117        require(_totalGasLimit >= overhead, "my"); // provided gas limit doesn't cover transaction overhead
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

```
file: zksync/ValidatorTimelock.sol

66        require(msg.sender == validator, "8h");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol

```
file: zksync/facets/Admin.sol

31         require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights

55         require(msg.sender == pendingAdmin, "n4"); // Only proposed by current admin address can claim the admin rights

84         require(_newPriorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "n5");

112        require(!diamondStorage.isFrozen, "a9"); // diamond proxy is frozen already

123        require(diamondStorage.isFrozen, "a7"); // diamond proxy is not frozen
```



https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol

```
file: zksync/DiamondInit.sol

56        require(address(_initalizeData.verifier) != address(0), "vt");

57        require(_initalizeData.governor != address(0), "vy");

58        require(_initalizeData.admin != address(0), "hc");

59        require(_initalizeData.priorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "vu");

87        assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol

```
file: zksync/libraries/PriorityQueue.sol

65        require(!_queue.isEmpty(), "D"); // priority queue is empty

73        require(!_queue.isEmpty(), "s"); // priority queue is empty
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

```
file: zksync/DiamondProxy.sol

14        require(_chainId == block.chainid, "pr");

25        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");

30        require(facetAddress != address(0), "F"); // Proxy has no facet for this selector

31        require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen
```


https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol

```
file: zksync/libraries/Merkle.sol

24        require(pathLength > 0, "xc");

25        require(pathLength < 256, "bt");

26        require(_index < (1 << pathLength), "px");
```




https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol

```
file: zksync/facets

19         require(msg.sender == s.governor, "1g"); // only by governor

25         require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");

31        require(s.validators[msg.sender], "1h"); // validator is not active
```

### Bridges

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

```
file: bridge/L1ERC20Bridge.sol

90        require(_l2TokenBeacon != address(0), "nf");

91        require(_governor != address(0), "nh");

93        require(_factoryDeps.length == 3, "mk");

95        require(msg.value == _deployBridgeImplementationFee + _deployBridgeProxyFee, "fee");

184        require(_amount != 0, "2T"); // empty deposit amount

186        require(amount == _amount, "1T"); // The token has non-standard transfer logic

272        require(proofValid, "yn");

275        require(amount > 0, "y1");

300        require(!isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "pw");

312        require(success, "nq");

329        require(_l2ToL1message.length == 76, "kk");

332        require(bytes4(functionSignature) == this.finalizeWithdrawal.selector, "nt");

347        require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

```
file: bridge/L1WethBridge.sol

88        require(_l2WethAddress != address(0), "L2 WETH address cannot be zero");

89        require(_governor != address(0), "Governor address cannot be zero");

90        require(_factoryDeps.length == 2, "Invalid factory deps length provided");

91        require(
            msg.value == _deployBridgeImplementationFee + _deployBridgeProxyFee,
            "Miscalculated deploy transactions fees"
        );

167        require(_l1Token == l1WethAddress, "Invalid L1 token address");

168        require(_amount != 0, "Amount cannot be zero");

240        require(!isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "Withdrawal is already finalized");

254        require(success, "vq");

279        require(_message.length == 96, "Incorrect ETH message with additional data length");

282        require(
            bytes4(functionSignature) == IMailbox.finalizeEthWithdrawal.selector,
            "Incorrect ETH message function selector"
        );

289        require(l1EthReceiver == address(this), "Wrong L1 ETH withdraw receiver");

295        require(l2Sender == l2Bridge, "The withdrawal was not initiated by L2 bridge");

311        require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");
```

### Governance

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol

```
file: governance/Governance.sol

42        require(_admin != address(0), "Admin should be non zero address");

59        require(msg.sender == address(this), "Only governance contract itself allowed to call this function");

65        require(msg.sender == securityCouncil, "Only security council allowed to call this function");

71        require(
            msg.sender == owner() || msg.sender == securityCouncil,
            "Only the owner and security council are allowed to call this function"
        );

155        require(isOperationPending(_id), "Operation must be pending");

172        require(isOperationReady(id), "Operation must be ready before execution");

177        require(isOperationReady(id), "Operation must be ready after execution");

191        require(isOperationPending(id), "Operation must be pending before execution");

196        require(isOperationPending(id), "Operation must be pending after execution");

216        require(!isOperation(_id), "Operation with this proposal id already exists");

217        require(_delay >= minDelay, "Proposed delay is less than minimum delay");

240        require(_predecessorId == bytes32(0) || isOperationDone(_predecessorId), "Predecessor operation not completed");
```

### Upgrades

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

```
file: upgrades/BaseZkSyncUpgrade.sol

72        require(block.timestamp >= _proposedUpgrade.upgradeTimestamp, "Upgrade is not ready yet");

171        require(_l2ProtocolUpgradeTx.txType == SYSTEM_UPGRADE_L2_TX_TYPE, "L2 system upgrade tx type is wrong");

185        require(
            _l2ProtocolUpgradeTx.nonce == _newProtocolVersion,
            "The new protocol version should be included in the L2 system upgrade tx"
        );

201        require(_factoryDeps.length == _expectedHashes.length, "Wrong number of factory deps");

202        require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32");

205        require(
                L2ContractHelper.hashL2Bytecode(_factoryDeps[i]) == bytes32(_expectedHashes[i]),
                "Wrong factory dep hash"
            );

216        require(
            _newProtocolVersion > previousProtocolVersion,
            "New protocol version is not greater than the current one"
        );

222        require(s.l2SystemContractsUpgradeTxHash == bytes32(0), "Previous upgrade has not been finalized");

223        require(
            s.l2SystemContractsUpgradeBatchNumber == 0,
            "The batch number of the previous upgrade has not been cleaned"
        );
```

### Other

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol

```
file: common/AllowList.sol

60        require(targetsLength == _accessModes.length, "yg"); // The size of arrays should be equal

92        require(callersLength == _targets.length, "yw");

93        require(callersLength == _functionSigs.length, "yx");

94        require(callersLength == _enables.length, "yy");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

```
file: common/libraries/L2ContractHelper.sol

23        require(_bytecode.length % 32 == 0, "pq");

26        require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words

27        require(bytecodeLenInWords % 2 == 1, "ps"); // bytecode length in words must be odd

41        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash

43        require(_bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol

```
file: common/ReentrancyGuard.sol

58        require(lockSlotOldValue == 0, "1B");

75        require(_status == _NOT_ENTERED, "r1");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol

```
file: common/AllowListed.sol

13            require(_allowList.canCall(msg.sender, address(this), msg.sig), "nr");
```

## ```>=``` costs less gas than ```>```

This is because ```>``` uses the extra ```ISZERO``` opcode, which costs 3 gas. Consider replacing ```>``` occurrences with ```>=```

### zkSync

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

```
file: zksync/facets/Executor.sol

185        require(_newBatchesData.length > 0, "No batches to commit");

354        if (_proof.serializedProof.length > 0) {

399        require(s.totalBatchesCommitted > _newLastBatch, "v1"); // The last committed batch is less than new last batch

409        if (s.l2SystemContractsUpgradeBatchNumber > _newLastBatch) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

```
file: zksync/facets/Mailbox.sol

312        if (refundRecipient.code.length > 0) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

```
file: zksync/libraries/Diamond.sol

106         require(selectors.length > 0, "B"); // no functions for diamond cut
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol

```
file: zksync/libraries/Merkle.sol

24        require(pathLength > 0, "xc");
```

### Bridges

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

```
file: bridge/L1ERC20Bridge.sol

275        require(amount > 0, "y1");
```

### Governance

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol

```
file: governance/Governance.sol

111        } else if (timestamp > block.timestamp) {
```

### Upgrades

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

```
file: upgrades/BaseZkSyncUpgrade.sol

217            _newProtocolVersion > previousProtocolVersion,
```

## ```<=``` costs less gas than ```<```

This is because ```<``` uses the extra ```ISZERO``` opcode, which costs 3 gas. Consider replacing ```<``` occurrences with ```<=```

### zkSync

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

```
file: zksync/facets/Executor.sol

85        require(_previousBatchTimestamp < batchTimestamp, "h3");

123        for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

209        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

241        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

263        for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

293        for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {

330        for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {

402        if (_newLastBatch < s.totalBatchesVerified) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

```
file: zksync/facets/Mailbox.sol

395        for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

```
file: zksync/libraries/Diamond.sol

100        for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {

138        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

159        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

179        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol

```
file: zksync/facets/Getters.sol

182        for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

```
file: zksync/ValidatorTimelock.sol

85            for (uint256 i = 0; i < _newBatchesData.length; ++i) {

116           for (uint256 i = 0; i < _newBatchesData.length; ++i) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol

```
file: zksync/libraries/Merkle.sol

25        require(pathLength < 256, "bt");

26        require(_index < (1 << pathLength), "px");

29        for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
```

### Governance

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol

```
file: governance/Governance.sol

225        for (uint256 i = 0; i < _calls.length; ++i) {
```

### Upgrades

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

```
file: upgrades/BaseZkSyncUpgrade.sol

204        for (uint256 i = 0; i < _factoryDeps.length; ++i) {
```

### Other

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol

```
file: common/AllowList.sol

62        for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

96        for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

```
file: common/libraries/L2ContractHelper.sol

26        require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words
```

## Use Assembly To Check For address(0)

Using ```!=``` or ```==``` operators generates instructions in the EVM bytecode, making the contract less gas efficient. This gas cost can be reduced by using Assembly to check for the zero address, for example with the below function (converting the input address to ```uint256``` and using assembly to compare it with zero).

```
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
```

Note however that this makes the contract less readable as assembly is more difficult to read than solidity.

### zkSync


https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

```
file: zksync/libraries/Diamond.sol

132        require(_facet != address(0), "G"); // facet with zero address cannot be added

141        require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists

156        require(_facet != address(0), "K"); // cannot replace facet with zero address

162        require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address

176        require(_facet == address(0), "a1"); // facet address must be zero

182        require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet

284        if (_init == address(0)) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol

```
file: zksync/facets/Getters.sol

160        require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol

```
file: zksync/DiamondInit.sol

56        require(address(_initalizeData.verifier) != address(0), "vt");

57        require(_initalizeData.governor != address(0), "vy");

58        require(_initalizeData.admin != address(0), "hc");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol

```
file: zksync/DiamondProxy.sol

30       require(facetAddress != address(0), "F"); // Proxy has no facet for this selector
```

### Bridges

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

```
file: bridge/L1ERC20Bridge.sol

90        require(_l2TokenBeacon != address(0), "nf");

91        require(_governor != address(0), "nh");

195      if (_refundRecipient == address(0)) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

```
file: bridge/L1WethBridge.sol

88        require(_l2WethAddress != address(0), "L2 WETH address cannot be zero");

89        require(_governor != address(0), "Governor address cannot be zero");

182      if (_refundRecipient == address(0)) {
```

### Governance 

```
file: governance/Governance.sol

42        require(_admin != address(0), "Admin should be non zero address");
```

### Upgrades

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

```
file: upgrades/BaseZkSyncUpgrade.sol

116        if (_newVerifier == IVerifier(address(0))) {

235        if (_newAllowList == IAllowList(address(0))) {
```

## Use assembly to hash instead of solidity

This will save approximately 80 gas per hash. 
Note that hashing is already optimized in the Merkle library, and a similar function could be used in other parts of the code:

```
    /// @dev Keccak hash of the concatenation of two 32-byte words
    function _efficientHash(bytes32 _lhs, bytes32 _rhs) private pure returns (bytes32 result) {
        assembly {
            mstore(0x00, _lhs)
            mstore(0x20, _rhs)
            result := keccak256(0x00, 0x40)
        }
    }
```



### zkSync

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

```
file: zksync/facets/Executor.sol

120        bytes32 providedL2ToL1PubdataHash = keccak256(_newBatch.totalL2ToL1Pubdata);

265        concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));

383        keccak256(
                    abi.encodePacked(
                        _prevBatchCommitment,
                        _currentBatchCommitment,
                        _verifierParams.recursionNodeLevelVkHash,
                        _verifierParams.recursionLeafLevelVkHash
                    )
                )

427        bytes32 passThroughDataHash = keccak256(_batchPassThroughData(_newBatchData));

428        bytes32 metadataHash = keccak256(_batchMetaParameters());

429        bytes32 auxiliaryOutputHash = keccak256(_batchAuxiliaryOutput(_newBatchData, _stateDiffHash));

431        return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));

455        bytes32 l2ToL1LogsHash = keccak256(_batch.systemLogs);

468        return keccak256(abi.encode(_storedBatchInfo));
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

```
file: zksync/facets/Mailbox.sol

131        bytes32 hashedLog = keccak256(
            abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBatch, _log.sender, _log.key, _log.value)
        );

157         value: keccak256(_message.data)

367        canonicalTxHash = keccak256(transactionEncoding);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol

```
file: zksync/DiamondInit.sol

77        s.storedBatchHashes[0] = keccak256(abi.encode(storedBatchZero));
```

### Bridges 

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

```
file: bridge/L1ERC20Bridge.sol

354        bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""));
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol

```
file: bridge/libraries/BridgeInitializationHelper.sol

56          keccak256(_constructorData)
```

### Governance

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol

```
file: governance/Governance.sol

205        return keccak256(abi.encode(_operation));
```

### Upgrades

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

```
file: upgrades/BaseZkSyncUpgrade.sol

192        bytes32 l2ProtocolUpgradeTxHash = keccak256(encodedTransaction);
```

### Other

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

```
file: common/libraries/L2ContractHelper.sol

12        bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");

67        bytes32 data = keccak256(
            bytes.concat(CREATE2_PREFIX, senderBytes, _salt, _bytecodeHash, _constructorInputHash)
        );
```

## Use constants instead of type(uintx).max

```type(uintx).max``` implies a computation at a runtime, which costs gas, as opposed to a constant which is evaluated at compile time.
Each call to this expression will result in additional gas consumption, and setting a constant will prevent this.

As an example, we could use ```MAX_VALUE = 2**120-1;``` instead of ```type(uint120).max``` and so on.

Defining these values as constants will also help make the code more readable by centralizing the definition of these values.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol

```
file: zksync/libraries/TransactionValidator.sol

50        require(_transaction.from <= type(uint16).max, "ua");

51        require(_transaction.to <= type(uint160).max, "ub");

55        require(_transaction.reserved[1] <= type(uint160).max, "uf");
```

## Make event parameters indexed to save gas

Using ```indexed``` events will help reduce gas costs. Events are non-indexed by default which means the entire event data is stored which can be expensive in case of large amounts of data.

With ```indexed``` parameters, arguments will be stored in a different data structure easily accessible, meaning the search for specific event occurrences will be overall less costly.

Setting up to 3 event parameters (which is the maximum amount of indexed parameters allowed) to ```indexed``` will hence help save on gas costs.

### zkSync

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

```
file: zksync/libraries/Diamond.sol

23    event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol

```
file: zksync/interfaces/IMailbox.sol

142    event NewPriorityRequest(
        uint256 txId,
        bytes32 txHash,
        uint64 expirationTimestamp,
        L2CanonicalTransaction transaction,
        bytes[] factoryDeps
    );

153    event EthWithdrawalFinalized(address indexed to, uint256 amount);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

```
file: zksync/ValidatorTimelock.sol

27    event NewExecutionDelay(uint256 _newExecutionDelay);

30    event NewValidator(address _oldValidator, address _newValidator);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IExecutor.sol

```
file: zksync/interfaces/IExecutor.sol

106    event BlocksRevert(uint256 totalBatchesCommitted, uint256 totalBatchesVerified, uint256 totalBatchesExecuted);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol

```
file: zksync/interfaces/IAdmin.sol

31    event IsPorterAvailableStatusUpdate(bool isPorterAvailable);

34    event ValidatorStatusUpdate(address indexed validatorAddress, bool isActive);

51    event NewPriorityTxMaxGasLimit(uint256 oldPriorityTxMaxGasLimit, uint256 newPriorityTxMaxGasLimit);

54    event ExecuteUpgrade(Diamond.DiamondCutData diamondCut);
```

### Bridges

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

```
file: bridge/L1WethBridge.sol

41    event EthReceived(uint256 amount);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol

```
file: bridge/interfaces/IL1Bridge.sol

15    event WithdrawalFinalized(address indexed to, address indexed l1Token, uint256 amount);

17    event ClaimedFailedDeposit(address indexed to, address indexed l1Token, uint256 amount);
```

### Governance

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol

```
file: governance/IGovernance.sol

65    event TransparentOperationScheduled(bytes32 indexed _id, uint256 delay, Operation _operation);

68    event ShadowOperationScheduled(bytes32 indexed _id, uint256 delay);
```

### Upgrades

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

```
file: upgrades/BaseZkSyncUpgrade.sol

58    event NewVerifierParams(VerifierParams oldVerifierParams, VerifierParams newVerifierParams);

61    event UpgradeComplete(uint256 indexed newProtocolVersion, bytes32 indexed l2UpgradeTxHash, ProposedUpgrade upgrade);
```

### Other 

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol

```
file: common/interfaces/IAllowList.sol

11    event UpdateAccessMode(address indexed target, AccessMode previousMode, AccessMode newMode);
```

## Using > 0 costs more gas than != 0 when used on a uint in a require() statement

### zkSync

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

```
file: zksync/facets/Executor.sol

185        require(_newBatchesData.length > 0, "No batches to commit");

354        if (_proof.serializedProof.length > 0) {

473        return (_bitMap & (1 << _index)) > 0;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

```
file: zksync/facets/Mailbox.sol

312        if (refundRecipient.code.length > 0) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

```
file: zksync/libraries/Diamond.sol

106            require(selectors.length > 0, "B"); // no functions for diamond cut
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol

```
file: zksync/libraries/Merkle.sol

24        require(pathLength > 0, "xc");
```

### Bridges

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

```
file: bridges/L1ERC20Bridge.sol

275        require(amount > 0, "y1");
```

## Declare variables outside of the loop to save gas

Solidity will create a new instance of the variable for each iteration, which is suboptimal in terms of gas costs, and variables notably array’s length should be defined outside of the loop.

### zkSync

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

```
file: zksync/facets/Executor.sol

123        for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

209        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

241        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

```
file: zksync/ValidatorTimelock.sol

85            for (uint256 i = 0; i < _newBatchesData.length; ++i) {

116            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
```

### Governance

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol

```
file: governance/Governance.sol

225        for (uint256 i = 0; i < _calls.length; ++i) {
```

### Upgrades

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

```
file: upgrades/BaseZkSyncUpgrade.sol

204        for (uint256 i = 0; i < _factoryDeps.length; ++i) {
```

## Save gas by using assembly to emit events

[Reference](https://code4rena.com/reports/2023-08-goodentry#g-08-use-assembly-to-emit-events)

### zkSync

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

```
file: zksync/facets/Executor.sol

213            emit BlockCommit(
                _lastCommittedBatchData.batchNumber,
                _lastCommittedBatchData.batchHash,
                _lastCommittedBatchData.commitment
            );

251            emit BlockCommit(
                _lastCommittedBatchData.batchNumber,
                _lastCommittedBatchData.batchHash,
                _lastCommittedBatchData.commitment
            );

295            emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);

371        emit BlocksVerification(s.totalBatchesVerified, currentTotalBatchesVerified);

413        emit BlocksRevert(s.totalBatchesCommitted, s.totalBatchesVerified, s.totalBatchesExecuted);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

```
file: zksync/facets/Mailbox.sol

215        emit EthWithdrawalFinalized(_l1WithdrawReceiver, _amount);

378        emit NewPriorityRequest(
            _priorityOpParams.txId,
            canonicalTxHash,
            _priorityOpParams.expirationTimestamp,
            transaction,
            _factoryDeps
        );
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

```
file: zksync/libraries/Diamond.sol

120        emit DiamondCut(facetCuts, initAddress, initCalldata);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

```
file: zksync/ValidatorTimelock.sol

55        emit NewValidator(oldValidator, _newValidator);

61        emit NewExecutionDelay(_executionDelay);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol

```
file: zksync/facets/Admin.sol

25        emit NewPendingGovernor(oldPendingGovernor, _newPendingGovernor);

37        emit NewPendingGovernor(pendingGovernor, address(0));

38        emit NewGovernor(previousGovernor, pendingGovernor);

49        emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);

61        emit NewPendingAdmin(pendingAdmin, address(0));

62        emit NewAdmin(previousAdmin, pendingAdmin);

70        emit ValidatorStatusUpdate(_validator, _active);

78        emit IsPorterAvailableStatusUpdate(_zkPorterIsAvailable);

88        emit NewPriorityTxMaxGasLimit(oldPriorityTxMaxGasLimit, _newPriorityTxMaxGasLimit);

100        emit ExecuteUpgrade(_diamondCut);

115        emit Freeze();

126        emit Unfreeze();
```

### Bridges

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

```
file: bridges/L1ERC20Bridge.sol

211        emit DepositInitiated(l2TxHash, msg.sender, _l2Receiver, _l1Token, amount);

284        emit ClaimedFailedDeposit(_depositSender, _l1Token, amount);

319        emit WithdrawalFinalized(l1Receiver, l1Token, amount);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

```
file: bridge/L1WethBridge.sol

195        emit DepositInitiated(txHash, msg.sender, _l2Receiver, _l1Token, _amount);

267        emit WithdrawalFinalized(l1WethWithdrawReceiver, l1WethAddress, amount);

312        emit EthReceived(msg.value);
```

### Governance

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol

```
file: governance/Governance.sol

47        emit ChangeSecurityCouncil(address(0), _securityCouncil);

50        emit ChangeMinDelay(0, _minDelay);

132        emit TransparentOperationScheduled(id, _delay, _operation);

144        emit ShadowOperationScheduled(_id, _delay);

157        emit OperationCancelled(_id);

180        emit OperationExecuted(id);

199        emit OperationExecuted(id);

250        emit ChangeMinDelay(minDelay, _newDelay);

257        emit ChangeSecurityCouncil(securityCouncil, _newSecurityCouncil);
```

### Upgrades

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

```
file: upgrades/BaseZkSyncUpgrade.sol

89        emit NewL2DefaultAccountBytecodeHash(previousDefaultAccountBytecodeHash, _l2DefaultAccountBytecodeHash);

106        emit NewL2BootloaderBytecodeHash(previousBootloaderBytecodeHash, _l2BootloaderBytecodeHash);

122        emit NewVerifier(address(oldVerifier), address(_newVerifier));

138        emit NewVerifierParams(oldVerifierParams, _newVerifierParams);

229        emit NewProtocolVersion(previousProtocolVersion, _newProtocolVersion);

241        emit NewAllowList(address(oldAllowList), address(_newAllowList));
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol

```
file: upgrades/DefaultUpgrade.sol

43        emit UpgradeComplete(_proposedUpgrade.newProtocolVersion, txHash, _proposedUpgrade);
```

### Other

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol

```
file: common/AllowList.sol

73            emit UpdateAccessMode(_target, accessMode, _accessMode);

121            emit UpdateCallPermission(_caller, _target, _functionSig, _enable);
```

## Do not calculate constants

Calculating constants wastes gas as expressions assigned to constant variables are recalculated every time the variable is called.

### zkSync

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol

```
file: zksync/Config.sol

14	uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

26	uint256 constant MAX_INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE * 4765;

33	uint256 constant MAX_REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE * 7564;

51	uint256 constant UPGRADE_NOTICE_PERIOD = $$(defined(UPGRADE_NOTICE_PERIOD) ? UPGRADE_NOTICE_PERIOD : "14 days");

54	uint256 constant COMMIT_TIMESTAMP_NOT_OLDER = $$(
    defined(COMMIT_TIMESTAMP_NOT_OLDER) ? COMMIT_TIMESTAMP_NOT_OLDER : "365 days"
);

60	uint256 constant COMMIT_TIMESTAMP_APPROXIMATION_DELTA = $$(
    defined(COMMIT_TIMESTAMP_APPROXIMATION_DELTA) ? COMMIT_TIMESTAMP_APPROXIMATION_DELTA : "365 days"
);

92	uint256 constant BATCH_OVERHEAD_PUBDATA = BATCH_OVERHEAD_L1_GAS / L1_GAS_PER_PUBDATA_BYTE;
```

### Other

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

```
file: common/libraries/L2ContractHelper.sol

12	bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

## Use double if statements instead of &&

This will lead to gas savings in cases the ```if``` condition is not followed by a ```else```.

### zkSync

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

```
file: zksync/facets/Executor.sol

303        if (batchWhenUpgradeHappened != 0 && batchWhenUpgradeHappened <= newTotalBatchesExecuted) {
```

