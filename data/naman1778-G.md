## [G-01] Using XOR (^) and AND (&) bitwise equivalents

Given 4 variables a, b, c and d represented as such:

    0 0 0 0 0 1 1 0 <- a
    0 1 1 0 0 1 1 0 <- b
    0 0 0 0 0 0 0 0 <- c
    1 1 1 1 1 1 1 1 <- d

To have a == b means that every 0 and 1 match on both variables. Meaning that a XOR (operator ^) would evaluate to 0 ((a ^ b) == 0), as it excludes by definition any equalities.Now, if a != b, this means that there’s at least somewhere a 1 and a 0 not matching between a and b, making (a ^ b) != 0.Both formulas are logically equivalent and using the XOR bitwise operator costs actually the same amount of gas.However, it is much cheaper to use the bitwise OR operator (|) than comparing the truthy or falsy values.These are logically equivalent too, as the OR bitwise operator (|) would result in a 1 somewhere if any value is not 0 between the XOR (^) statements, meaning if any XOR (^) statement verifies that its arguments are different.

There are 14 instances of this issue in 10 files:

```
File: ethereum/contracts/zksync/facets/Base.sol

25: require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");
```

    diff --git a/code/contracts/ethereum/contracts/zksync/facets/Base.sol b/code/contracts/ethereum/contracts/zksync/facets/Base.sol
    index b7ae397..7f5dc57 100644
    --- a/code/contracts/ethereum/contracts/zksync/facets/Base.sol
    +++ b/code/contracts/ethereum/contracts/zksync/facets/Base.sol
    @@ -22,7 +22,7 @@ contract Base is ReentrancyGuard, AllowListed {

         /// @notice Checks that the message sender is an active governor or admin
         modifier onlyGovernorOrAdmin() {
    -        require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");
    +        require(((msg.sender ^ s.governor) & (msg.sender ^ s.admin)) == 0, "Only by governor or admin");
             _;
         }

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol

```
File: ethereum/contracts/bridge/L1WethBridge.sol

311: require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");
```

    diff --git a/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol b/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
    index b53942d..39e780a 100644
    --- a/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
    +++ b/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
    @@ -308,7 +308,7 @@ contract L1WethBridge is IL1Bridge, AllowListed, ReentrancyGuard {
             // Expected to receive ether in two cases:
             // 1. l1 WETH sends ether on `withdraw`
             // 2. zkSync contract withdraw funds in `finalizeEthWithdrawal`
    -        require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");
    +        require(((msg.sender ^ l1WethAddress) & (msg.sender ^ address(zkSync))) == 0, "pn");
             emit EthReceived(msg.value);
         }
     }

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

```
File: ethereum/contracts/governance/Governance.sol

72: msg.sender == owner() || msg.sender == securityCouncil,

91: return state == OperationState.Waiting || state == OperationState.Ready;
```

    diff --git a/code/contracts/ethereum/contracts/governance/Governance.sol b/code/contracts/ethereum/contracts/governance/Governance.sol
    index b702cab..6d5cee3 100644
    --- a/code/contracts/ethereum/contracts/governance/Governance.sol
    +++ b/code/contracts/ethereum/contracts/governance/Governance.sol
    @@ -69,7 +69,7 @@ contract Governance is IGovernance, Ownable2Step {
         /// @notice Checks that the message sender is an active owner or an active security council.
         modifier onlyOwnerOrSecurityCouncil() {
             require(
    -            msg.sender == owner() || msg.sender == securityCouncil,
    +            ((msg.sender ^ owner()) & (msg.sender ^ securityCouncil)) == 0,
                 "Only the owner and security council are allowed to call this function"
             );
             _;
    @@ -88,7 +88,7 @@ contract Governance is IGovernance, Ownable2Step {
         /// @dev Returns whether an operation is pending or not. Note that a "pending" operation may also be "ready".
         function isOperationPending(bytes32 _id) public view returns (bool) {
             OperationState state = getOperationState(_id);
    -        return state == OperationState.Waiting || state == OperationState.Ready;
    +        return ((state ^ OperationState.Waiting) & (state ^ OperationState.Ready)) == 0;
         }

         /// @dev Returns whether an operation is ready for execution. Note that a "ready" operation is also "pending".

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol

```
File: ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

129: _newVerifierParams.recursionNodeLevelVkHash == bytes32(0) ||
130: _newVerifierParams.recursionLeafLevelVkHash == bytes32(0) ||
131: _newVerifierParams.recursionCircuitsSetVksHash == bytes32(0)
```

    diff --git a/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol b/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol
    index 33116f1..7850668 100644
    --- a/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol
    +++ b/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol
    @@ -126,9 +126,9 @@ abstract contract BaseZkSyncUpgrade is Base {
         /// @param _newVerifierParams New parameters for the verifier
         function _setVerifierParams(VerifierParams calldata _newVerifierParams) private {
             if (
    -            _newVerifierParams.recursionNodeLevelVkHash == bytes32(0) ||
    -            _newVerifierParams.recursionLeafLevelVkHash == bytes32(0) ||
    -            _newVerifierParams.recursionCircuitsSetVksHash == bytes32(0)
    +            ((_newVerifierParams.recursionNodeLevelVkHash ^ bytes32(0)) &
    +            (_newVerifierParams.recursionLeafLevelVkHash ^ bytes32(0)) &
    +            (_newVerifierParams.recursionCircuitsSetVksHash ^ bytes32(0))) == 0
             ) {
                 return;
             }

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

```
File: system-contracts/contracts/libraries/TransactionHelper.sol

95: return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0;
```

    diff --git a/code/system-contracts/contracts/libraries/TransactionHelper.sol b/code/system-contracts/contracts/libraries/TransactionHelper.sol
    index 10065f5..4bc0b1b 100644
    --- a/code/system-contracts/contracts/libraries/TransactionHelper.sol
    +++ b/code/system-contracts/contracts/libraries/TransactionHelper.sol
    @@ -92,7 +92,7 @@ library TransactionHelper {
         /// @dev This method assumes that address is Ether either if the address is 0 (for convenience)
         /// or if the address is the address of the L2EthToken system contract.
         function isEthToken(uint256 _addr) internal pure returns (bool) {
    -        return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0;
    +        return ((_addr ^ uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT)))) & (_addr ^ 0)) == 0;
         }

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol

```
File: system-contracts/contracts/BootloaderUtilities.sol

92: require(vInt == 27 || vInt == 28, "Invalid v value");

191: require(vInt == 27 || vInt == 28, "Invalid v value");

286: require(vInt == 27 || vInt == 28, "Invalid v value");
```

    diff --git a/code/system-contracts/contracts/BootloaderUtilities.sol b/code/system-contracts/contracts/BootloaderUtilities.sol
    index 5a73eb2..e105c14 100644
    --- a/code/system-contracts/contracts/BootloaderUtilities.sol
    +++ b/code/system-contracts/contracts/BootloaderUtilities.sol
    @@ -89,7 +89,7 @@ contract BootloaderUtilities is IBootloaderUtilities {
             bytes memory vEncoded;
             {
                 uint256 vInt = uint256(uint8(_transaction.signature[64]));
    -            require(vInt == 27 || vInt == 28, "Invalid v value");
    +            require(((vInt ^ 27) & (vInt ^ 28)) == 0, "Invalid v value");

                 // If the `chainId` is specified in the transaction, then the `v` value is encoded as
                 // `35 + y + 2 * chainId == vInt + 8 + 2 * chainId`, where y - parity bit (see EIP-155).
    @@ -188,7 +188,7 @@ contract BootloaderUtilities is IBootloaderUtilities {
             bytes memory vEncoded;
             {
                 uint256 vInt = uint256(uint8(_transaction.signature[64]));
    -            require(vInt == 27 || vInt == 28, "Invalid v value");
    +            require(((vInt ^ 27) & (vInt ^ 28)) == 0, "Invalid v value");

                 vEncoded = RLPEncoder.encodeUint256(vInt - 27);
             }

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol

```
File: system-contracts/contracts/ContractDeployer.sol

240: msg.sender == FORCE_DEPLOYER || msg.sender == address(COMPLEX_UPGRADER_CONTRACT),
```

    diff --git a/code/system-contracts/contracts/ContractDeployer.sol b/code/system-contracts/contracts/ContractDeployer.sol
    index ed6d3fc..3ffc180 100644
    --- a/code/system-contracts/contracts/ContractDeployer.sol
    +++ b/code/system-contracts/contracts/ContractDeployer.sol
    @@ -237,7 +237,7 @@ contract ContractDeployer is IContractDeployer, ISystemContract {
         /// by `FORCE_DEPLOYER`.
         function forceDeployOnAddresses(ForceDeployment[] calldata _deployments) external payable {
             require(
    -            msg.sender == FORCE_DEPLOYER || msg.sender == address(COMPLEX_UPGRADER_CONTRACT),
    +            ((msg.sender ^ FORCE_DEPLOYER) & (msg.sender ^ address(COMPLEX_UPGRADER_CONTRACT))) == 0,
                 "Can only be called by FORCE_DEPLOYER or COMPLEX_UPGRADER_CONTRACT"
             );

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol

```
File: system-contracts/contracts/Compressor.sol

234: if (_operation == 0 || _operation == 3) {
```

    diff --git a/code/system-contracts/contracts/Compressor.sol b/code/system-contracts/contracts/Compressor.sol
    index 4b11fd3..1121914 100644
    --- a/code/system-contracts/contracts/Compressor.sol
    +++ b/code/system-contracts/contracts/Compressor.sol
    @@ -231,7 +231,7 @@ contract Compressor is ICompressor, ISystemContract {
             uint256 convertedValue = _sliceToUint256(_compressedValue);

             unchecked {
    -            if (_operation == 0 || _operation == 3) {
    +            if (((_operation ^ 0) & (_operation ^ 3)) == 0) {
                     require(convertedValue == _finalValue, "transform or no compression: compressed and final mismatch");
                 } else if (_operation == 1) {
                     require(_initialValue + convertedValue == _finalValue, "add: initial plus converted not equal to final");

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol

```
File: system-contracts/contracts/DefaultAccount.sol

146: selector == DEPLOYER_SYSTEM_CONTRACT.create.selector ||
147: selector == DEPLOYER_SYSTEM_CONTRACT.create2.selector ||
148: selector == DEPLOYER_SYSTEM_CONTRACT.createAccount.selector ||

175: require(v == 27 || v == 28, "v is neither 27 nor 28");
```

    diff --git a/code/system-contracts/contracts/DefaultAccount.sol b/code/system-contracts/contracts/DefaultAccount.sol
    index 0021839..9e5251c 100644
    --- a/code/system-contracts/contracts/DefaultAccount.sol
    +++ b/code/system-contracts/contracts/DefaultAccount.sol
    @@ -143,10 +143,10 @@ contract DefaultAccount is IAccount {
                 // Check that called function is the deployment method,
                 // the others deployer method is not supposed to be called from the default account.
                 isSystemCall =
    -                selector == DEPLOYER_SYSTEM_CONTRACT.create.selector ||
    -                selector == DEPLOYER_SYSTEM_CONTRACT.create2.selector ||
    -                selector == DEPLOYER_SYSTEM_CONTRACT.createAccount.selector ||
    -                selector == DEPLOYER_SYSTEM_CONTRACT.create2Account.selector;
    +                ((selector ^ DEPLOYER_SYSTEM_CONTRACT.create.selector) &
    +                (selector ^ DEPLOYER_SYSTEM_CONTRACT.create2.selector) &
    +                (selector ^ DEPLOYER_SYSTEM_CONTRACT.createAccount.selector) &
    +                (selector ^ DEPLOYER_SYSTEM_CONTRACT.create2Account.selector)) == 0;
             }
             bool success = EfficientCall.rawCall(gas, to, value, data, isSystemCall);
             if (!success) {

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol

```
File: system-contracts/contracts/L2EthToken.sol

34: msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
35:     msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
```

    diff --git a/code/system-contracts/contracts/L2EthToken.sol b/code/system-contracts/contracts/L2EthToken.sol
    index 6a2ca48..4cb1140 100644
    --- a/code/system-contracts/contracts/L2EthToken.sol
    +++ b/code/system-contracts/contracts/L2EthToken.sol
    @@ -31,9 +31,9 @@ contract L2EthToken is IEthToken, ISystemContract {
         /// later on.
         function transferFromTo(address _from, address _to, uint256 _amount) external override {
             require(
    -            msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
    -                msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
    -                msg.sender == BOOTLOADER_FORMAL_ADDRESS,
    +            ((msg.sender ^ MSG_VALUE_SYSTEM_CONTRACT) &
    +                (msg.sender ^ address(DEPLOYER_SYSTEM_CONTRACT)) &
    +                (msg.sender ^ BOOTLOADER_FORMAL_ADDRESS)) == 0,
                 "Only system contracts with special access can call this method"
             );

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.not_optimized(1,2);
            c1.optimized(1,2);
        }
    }

    contract Contract0 {
        function not_optimized(uint8 a,uint8 b) public returns(bool){
            return ((a==1) || (b==1));
        }
    }

    contract Contract1 {
        function optimized(uint8 a,uint8 b) public returns(bool){
            return ((a ^ 1) & (b ^ 1)) == 0;
        }
    }

#### Gas Report

| Contract0 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 46099                                     | 261             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| not_optimized                             | 456             | 456 | 456    | 456 | 1       |


| Contract1 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 42493                                     | 243             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| optimized                                 | 430             | 430 | 430    | 430 | 1       |

## [G-02] Instead of counting down in for statements, count up

Counting down is more gas efficient than counting up because neither we are making zero variable to non-zero variable and also we will get gas refund in the last transaction when making non-zero to zero variable.

There are 31 instances of this issue in 14 files:

```
File: ethereum/contracts/zksync/facets/Executor.sol

123: for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

209: for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

241: for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

263: for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

293: for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {

330: for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
```

    diff --git a/code/contracts/ethereum/contracts/zksync/facets/Executor.sol b/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
    index 8fb6611..d873187 100644
    --- a/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
    +++ b/code/contracts/ethereum/contracts/zksync/facets/Executor.sol
    @@ -120,7 +120,7 @@ contract ExecutorFacet is Base, IExecutor {
             bytes32 providedL2ToL1PubdataHash = keccak256(_newBatch.totalL2ToL1Pubdata);

             // linear traversal of the logs
    -        for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
    +        for (uint256 i = emittedL2Logs.length - 1; i >= 0;) {
                 // Extract the values to be compared to/used such as the log sender, key, and value
                 (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET);
                 (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);
    @@ -158,6 +158,9 @@ contract ExecutorFacet is Base, IExecutor {
                 } else {
                     revert("ul");
                 }
    +           unchecked {
    +               i = i - L2_TO_L1_LOG_SERIALIZE_SIZE;
    +            }
             }

             // We only require 7 logs to be checked, the 8th is if we are expecting a protocol upgrade
    @@ -206,7 +209,7 @@ contract ExecutorFacet is Base, IExecutor {
             StoredBatchInfo memory _lastCommittedBatchData,
             CommitBatchInfo[] calldata _newBatchesData
         ) internal {
    -        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
    +        for (uint256 i = _newBatchesData.length - 1; i >= 0;) {
                 _lastCommittedBatchData = _commitOneBatch(_lastCommittedBatchData, _newBatchesData[i], bytes32(0));

                 s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData);
    @@ -215,6 +218,9 @@ contract ExecutorFacet is Base, IExecutor {
                     _lastCommittedBatchData.batchHash,
                     _lastCommittedBatchData.commitment
                 );
    +           unchecked{
    +               i = i-1;
    +            }
             }
         }

    @@ -238,7 +244,7 @@ contract ExecutorFacet is Base, IExecutor {
             // Save the batch number where the upgrade transaction was executed.
             s.l2SystemContractsUpgradeBatchNumber = _newBatchesData[0].batchNumber;

    -        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
    +        for (uint256 i = _newBatchesData.length - 1; i >= 0;) {
                 // The upgrade transaction must only be included in the first batch.
                 bytes32 expectedUpgradeTxHash = i == 0 ? _systemContractUpgradeTxHash : bytes32(0);
                 _lastCommittedBatchData = _commitOneBatch(
    @@ -253,6 +259,9 @@ contract ExecutorFacet is Base, IExecutor {
                     _lastCommittedBatchData.batchHash,
                     _lastCommittedBatchData.commitment
                 );
    +           unchecked{
    +               i = i-1;
    +           }
             }
         }

    @@ -260,9 +269,12 @@ contract ExecutorFacet is Base, IExecutor {
         function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {
             concatHash = EMPTY_STRING_KECCAK;

    -        for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
    +        for (uint256 i = _nPriorityOps - 1; i >= 0; ) {
                 PriorityOperation memory priorityOp = s.priorityQueue.popFront();
                 concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
    +           unchecked{
    +               i = i-1;
    +           }
             }
         }

    @@ -290,9 +302,12 @@ contract ExecutorFacet is Base, IExecutor {
         /// @notice 2. Finalizes batch on Ethereum
         function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {
             uint256 nBatches = _batchesData.length;
    -        for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
    +        for (uint256 i = nBatches - 1; i >= 0 ; ) {
                 _executeOneBatch(_batchesData[i], i);
                 emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
    +           unchecked{
    +               i = i-1;
    +           }
             }

             uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;
    @@ -327,7 +342,7 @@ contract ExecutorFacet is Base, IExecutor {
             require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");

             bytes32 prevBatchCommitment = _prevBatch.commitment;
    -        for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
    +        for (uint256 i = committedBatchesLength - 1; i >= 0; ) {
                 currentTotalBatchesVerified = currentTotalBatchesVerified.uncheckedInc();
                 require(
                     _hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],
    @@ -342,6 +357,9 @@ contract ExecutorFacet is Base, IExecutor {
                 );

                 prevBatchCommitment = currentBatchCommitment;
    +           unchecked{
    +               i = i-1;
    +            }
             }
             require(currentTotalBatchesVerified <= s.totalBatchesCommitted, "q");

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol

```
File: ethereum/contracts/zksync/facets/Mailbox.sol

395: for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
```

    diff --git a/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol b/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
    index 66e11ee..cd76e34 100644
    --- a/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
    +++ b/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
    @@ -392,13 +392,16 @@ contract MailboxFacet is Base, IMailbox {
         {
             uint256 factoryDepsLen = _factoryDeps.length;
             hashedFactoryDeps = new uint256[](factoryDepsLen);
    -        for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
    +        for (uint256 i = factoryDepsLen - 1; i >= 0;) {
                 bytes32 hashedBytecode = L2ContractHelper.hashL2Bytecode(_factoryDeps[i]);

                 // Store the resulting hash sequentially in bytes.
                 assembly {
                     mstore(add(hashedFactoryDeps, mul(add(i, 1), 32)), hashedBytecode)
                 }
    +           unchecked{
    +               i = i-1;
    +           }
             }
         }

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

```
File: ethereum/contracts/zksync/libraries/Diamond.sol

100: for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {

138: for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

159: for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

179: for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
```

    diff --git a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
    index 0b1fbdc..36540f0 100644
    --- a/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
    +++ b/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
    @@ -97,7 +97,7 @@ library Diamond {
             address initAddress = _diamondCut.initAddress;
             bytes memory initCalldata = _diamondCut.initCalldata;
             uint256 facetCutsLength = facetCuts.length;
    -        for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
    +        for (uint256 i = facetCutsLength - 1; i >= 0; ) {
                 Action action = facetCuts[i].action;
                 address facet = facetCuts[i].facet;
                 bool isFacetFreezable = facetCuts[i].isFreezable;
    @@ -114,6 +114,9 @@ library Diamond {
                 } else {
                     revert("C"); // undefined diamond cut action
                 }
    +           unchecked{
    +               i = i-1;
    +           }
             }

             _initializeDiamondCut(initAddress, initCalldata);
    @@ -135,12 +138,15 @@ library Diamond {
             _saveFacetIfNew(_facet);

             uint256 selectorsLength = _selectors.length;
    -        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
    +        for (uint256 i = selectorsLength - 1; i >= 0 ; ) {
                 bytes4 selector = _selectors[i];
                 SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
                 require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists

                 _addOneFunction(_facet, selector, _isFacetFreezable);
    +           unchecked{
    +               i = i-1;
    +           }
             }
         }

    @@ -156,7 +162,7 @@ library Diamond {
             require(_facet != address(0), "K"); // cannot replace facet with zero address

             uint256 selectorsLength = _selectors.length;
    -        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
    +        for (uint256 i = selectorsLength - 1; i >= 0; ) {
                 bytes4 selector = _selectors[i];
                 SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
                 require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address
    @@ -165,6 +171,9 @@ library Diamond {
                 // Add facet to the list of facets if the facet address is a new one
                 _saveFacetIfNew(_facet);
                 _addOneFunction(_facet, selector, _isFacetFreezable);
    +           unchecked{
    +               i = i-1;
    +           }
             }
         }

    @@ -176,12 +185,15 @@ library Diamond {
             require(_facet == address(0), "a1"); // facet address must be zero

             uint256 selectorsLength = _selectors.length;
    -        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
    +        for (uint256 i = selectorsLength - 1; i >= 0; ) {
                 bytes4 selector = _selectors[i];
                 SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
                 require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet

                 _removeOneFunction(oldFacet.facetAddress, selector);
    +           unchecked{
    +               i = i-1;
    +           }
             }
         }

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

```
File: ethereum/contracts/zksync/facets/Getters.sol

182: for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
```

    diff --git a/code/contracts/ethereum/contracts/zksync/facets/Getters.sol b/code/contracts/ethereum/contracts/zksync/facets/Getters.sol
    index 65a381d..8a647ea 100644
    --- a/code/contracts/ethereum/contracts/zksync/facets/Getters.sol
    +++ b/code/contracts/ethereum/contracts/zksync/facets/Getters.sol
    @@ -179,11 +179,14 @@ contract GettersFacet is Base, IGetters, ILegacyGetters {
             uint256 facetsLen = ds.facets.length;
             result = new Facet[](facetsLen);

    -        for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
    +        for (uint256 i = facetsLen - 1; i >= 0;) {
                 address facetAddr = ds.facets[i];
                 Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];

                 result[i] = Facet(facetAddr, facetToSelectors.selectors);
    +           unchecked{
    +               i = i-1;
    +            }
             }
         }

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol

```
File: ethereum/contracts/zksync/ValidatorTimelock.sol

85: for (uint256 i = 0; i < _newBatchesData.length; ++i) {

116: for (uint256 i = 0; i _newBatchesData.length; ++i) {
```

    diff --git a/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol b/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
    index 69e5656..fe0c6c9 100644
    --- a/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
    +++ b/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
    @@ -82,7 +82,7 @@ contract ValidatorTimelock is IExecutor, Ownable2Step {
                 // This contract is only a temporary solution, that hopefully will be disabled until 2106 year, so...
                 // It is safe to cast.
                 uint32 timestamp = uint32(block.timestamp);
    -            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
    +            for (uint256 i = _newBatchesData.length - 1; i >= 0; --i) {
                     committedBatchTimestamp.set(_newBatchesData[i].batchNumber, timestamp);
                 }
             }
    @@ -113,7 +113,7 @@ contract ValidatorTimelock is IExecutor, Ownable2Step {
         function executeBatches(StoredBatchInfo[] calldata _newBatchesData) external onlyValidator {
             uint256 delay = executionDelay; // uint32
             unchecked {
    -            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
    +            for (uint256 i = _newBatchesData.length - 1; i >= 0; --i) {
                     uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);

                     // Note: if the `commitBatchTimestamp` is zero, that means either:

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

```
File: ethereum/contracts/zksync/libraries/Merkle.sol

29: for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
```

    diff --git a/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol b/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
    index 6675c8b..3f22bf5 100644
    --- a/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
    +++ b/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
    @@ -26,11 +26,14 @@ library Merkle {
             require(_index < (1 << pathLength), "px");

             bytes32 currentHash = _itemHash;
    -        for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
    +        for (uint256 i = pathLength - 1; i >= 0; ) {
                 currentHash = (_index % 2 == 0)
                     ? _efficientHash(currentHash, _path[i])
                     : _efficientHash(_path[i], currentHash);
                 _index /= 2;
    +           unchecked{
    +               i = i-1;
    +            }
             }

             return currentHash;

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol

```
File: ethereum/contracts/governance/Governance.sol

225: for (uint256 i = 0; i < _calls.length; ++i) {
```

    diff --git a/code/contracts/ethereum/contracts/governance/Governance.sol b/code/contracts/ethereum/contracts/governance/Governance.sol
    index b702cab..a64dab1 100644
    --- a/code/contracts/ethereum/contracts/governance/Governance.sol
    +++ b/code/contracts/ethereum/contracts/governance/Governance.sol
    @@ -222,7 +222,7 @@ contract Governance is IGovernance, Ownable2Step {
         /// @dev Execute an operation's calls.
         /// @param _calls The array of calls to be executed.
         function _execute(Call[] calldata _calls) internal {
    -        for (uint256 i = 0; i < _calls.length; ++i) {
    +        for (uint256 i = _calls.length - 1; i >= 0; --i) {
                 (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
                 if (!success) {
                     // Propage an error if the call fails.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol

```
File: ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

204: for (uint256 i = 0; i < _factoryDeps.length; ++i) {
```

    diff --git a/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol b/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol
    index 33116f1..1c4d998 100644
    --- a/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol
    +++ b/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol
    @@ -201,7 +201,7 @@ abstract contract BaseZkSyncUpgrade is Base {
             require(_factoryDeps.length == _expectedHashes.length, "Wrong number of factory deps");
             require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32");

    -        for (uint256 i = 0; i < _factoryDeps.length; ++i) {
    +        for (uint256 i = _factoryDeps.length - 1; i >= 0; --i) {
                 require(
                     L2ContractHelper.hashL2Bytecode(_factoryDeps[i]) == bytes32(_expectedHashes[i]),
                     "Wrong factory dep hash"

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

```
File: ethereum/contracts/common/AllowList.sol

62: for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

96: for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
```

    diff --git a/code/contracts/ethereum/contracts/common/AllowList.sol b/code/contracts/ethereum/contracts/common/AllowList.sol
    index e852b77..247f231 100644
    --- a/code/contracts/ethereum/contracts/common/AllowList.sol
    +++ b/code/contracts/ethereum/contracts/common/AllowList.sol
    @@ -59,8 +59,11 @@ contract AllowList is IAllowList, Ownable2Step {
             uint256 targetsLength = _targets.length;
             require(targetsLength == _accessModes.length, "yg"); // The size of arrays should be equal

    -        for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {
    +        for (uint256 i = targetsLength - 1; i >= 0 ; ) {
                 _setAccessMode(_targets[i], _accessModes[i]);
    +           unchecked{
    +               i = i-1;
    +           }
             }
         }

    @@ -93,8 +96,11 @@ contract AllowList is IAllowList, Ownable2Step {
             require(callersLength == _functionSigs.length, "yx");
             require(callersLength == _enables.length, "yy");

    -        for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
    +        for (uint256 i = callersLength - 1; i >= 0; ) {
                 _setPermissionToCall(_callers[i], _targets[i], _functionSigs[i], _enables[i]);
    +            unchecked{
    +               i = i-1;
    +            }
             }
         }

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol

```
File: system-contracts/contracts/L1Messenger.sol

211: for (uint256 i = 0; i < numberOfL2ToL1Logs; ++i) {

223: for (uint256 i = numberOfL2ToL1Logs; i < L2_TO_L1_LOGS_MERKLE_TREE_LEAVES; ++i) {

229: for (uint256 i = 0; i < nodesOnCurrentLevel; ++i) {

241: for (uint256 i = 0; i < numberOfMessages; ++i) {

259: for (uint256 i = 0; i < numberOfBytecodes; ++i) {
```

    diff --git a/code/system-contracts/contracts/L1Messenger.sol b/code/system-contracts/contracts/L1Messenger.sol
    index c5a03c8..e3e2af8 100644
    --- a/code/system-contracts/contracts/L1Messenger.sol
    +++ b/code/system-contracts/contracts/L1Messenger.sol
    @@ -208,7 +208,7 @@ contract L1Messenger is IL1Messenger, ISystemContract {

             bytes32[] memory l2ToL1LogsTreeArray = new bytes32[](L2_TO_L1_LOGS_MERKLE_TREE_LEAVES);
             bytes32 reconstructedChainedLogsHash;
    -        for (uint256 i = 0; i < numberOfL2ToL1Logs; ++i) {
    +        for (uint256 i = numberOfL2ToL1Logs - 1; i >= 0; --i) {
                 bytes32 hashedLog = EfficientCall.keccak(
                     _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + L2_TO_L1_LOG_SERIALIZE_SIZE]
                 );
    @@ -220,13 +220,13 @@ contract L1Messenger is IL1Messenger, ISystemContract {
                 reconstructedChainedLogsHash == chainedLogsHash,
                 "reconstructedChainedLogsHash is not equal to chainedLogsHash"
                 "reconstructedChainedLogsHash is not equal to chainedLogsHash"
             );
    -        for (uint256 i = numberOfL2ToL1Logs; i < L2_TO_L1_LOGS_MERKLE_TREE_LEAVES; ++i) {
    +        for (uint256 i = L2_TO_L1_LOGS_MERKLE_TREE_LEAVES - 1 ; i >= numberOfL2ToL1Logs ; --i) {
                 l2ToL1LogsTreeArray[i] = L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH;
             }
             uint256 nodesOnCurrentLevel = L2_TO_L1_LOGS_MERKLE_TREE_LEAVES;
             while (nodesOnCurrentLevel > 1) {
                 nodesOnCurrentLevel /= 2;
    -            for (uint256 i = 0; i < nodesOnCurrentLevel; ++i) {
    +            for (uint256 i = nodesOnCurrentLevel - 1; i >= 0; --i) {
                     l2ToL1LogsTreeArray[i] = keccak256(
                         abi.encode(l2ToL1LogsTreeArray[2 * i], l2ToL1LogsTreeArray[2 * i + 1])
                     );
    @@ -238,7 +238,7 @@ contract L1Messenger is IL1Messenger, ISystemContract {
             uint32 numberOfMessages = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
             calldataPtr += 4;
             bytes32 reconstructedChainedMessagesHash;
    -        for (uint256 i = 0; i < numberOfMessages; ++i) {
    +        for (uint256 i = numberOfMessages - 1; i >= 0; --i) {
                 uint32 currentMessageLength = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
                 calldataPtr += 4;
                 bytes32 hashedMessage = EfficientCall.keccak(
    @@ -256,7 +256,7 @@ contract L1Messenger is IL1Messenger, ISystemContract {
             uint32 numberOfBytecodes = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
             calldataPtr += 4;
             bytes32 reconstructedChainedL1BytecodesRevealDataHash;
    -        for (uint256 i = 0; i < numberOfBytecodes; ++i) {
    +        for (uint256 i = numberOfBytecodes - 1; i >= 0; --i) {
                 uint32 currentBytecodeLength = uint32(
                     bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4])
                 );

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol

```
File: system-contracts/contracts/ContractDeployer.sol

248: for (uint256 i = 0; i < deploymentsLength; ++i) {

253: for (uint256 i = 0; i < deploymentsLength; ++i) {
```

    diff --git a/code/system-contracts/contracts/ContractDeployer.sol b/code/system-contracts/contracts/ContractDeployer.sol
    index ed6d3fc..ea79598 100644
    --- a/code/system-contracts/contracts/ContractDeployer.sol
    +++ b/code/system-contracts/contracts/ContractDeployer.sol
    @@ -245,12 +245,12 @@ contract ContractDeployer is IContractDeployer, ISystemContract {
             // We need to ensure that the `value` provided by the call is enough to provide `value`
             // for all of the deployments
             uint256 sumOfValues = 0;
    -        for (uint256 i = 0; i < deploymentsLength; ++i) {
    +        for (uint256 i = deploymentsLength - 1; i >= 0; --i) {
                 sumOfValues += _deployments[i].value;
             }
             require(msg.value == sumOfValues, "`value` provided is not equal to the combined `value`s of deployments");

    -        for (uint256 i = 0; i < deploymentsLength; ++i) {
    +        for (uint256 i = deploymentsLength - 1; i >= 0; --i) {
                 this.forceDeployOnAddress{value: _deployments[i].value}(_deployments[i], msg.sender);
             }
         }

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol

```
File: system-contracts/contracts/Compressor.sol

68: for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {

134: for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {

166: for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {
```

    diff --git a/code/system-contracts/contracts/Compressor.sol b/code/system-contracts/contracts/Compressor.sol
    index 4b11fd3..44d820d 100644
    --- a/code/system-contracts/contracts/Compressor.sol
    +++ b/code/system-contracts/contracts/Compressor.sol
    @@ -65,7 +65,7 @@ contract Compressor is ICompressor, ISystemContract {
                     "Encoded data length should be 4 times shorter than the original bytecode"
                 );

    -            for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {
    +            for (uint256 encodedDataPointer = encodedData.length - 1; encodedDataPointer >= 0; ) {
                     uint256 indexOfEncodedChunk = uint256(encodedData.readUint16(encodedDataPointer)) * 8;
                     require(indexOfEncodedChunk < dictionary.length, "Encoded chunk index is out of bounds");

    @@ -73,6 +73,9 @@ contract Compressor is ICompressor, ISystemContract {
                     uint64 realChunk = _bytecode.readUint64(encodedDataPointer * 4);

                     require(encodedChunk == realChunk, "Encoded chunk does not match the original bytecode");
    +               unchecked{
    +                       encodedDataPointer = encodedDataPointer - 2;
    +               }
                 }
             }

    @@ -131,7 +134,7 @@ contract Compressor is ICompressor, ISystemContract {
             uint256 numInitialWritesProcessed = 0;

             // Process initial writes
    -        for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {
    +        for (uint256 i = (_numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE) - 1; i >= 0; ) {
                 bytes calldata stateDiff = _stateDiffs[i:i + STATE_DIFF_ENTRY_SIZE];
                 uint64 enumIndex = stateDiff.readUint64(84);
                 if (enumIndex != 0) {
    @@ -158,12 +161,15 @@ contract Compressor is ICompressor, ISystemContract {
                     _compressedStateDiffs[stateDiffPtr:stateDiffPtr + len]
                 );
                 stateDiffPtr += len;
    +           unchecked{
    +               i = i - STATE_DIFF_ENTRY_SIZE;
    +           }
             }

             require(numInitialWritesProcessed == numberOfInitialWrites, "Incorrect number of initial storage diffs");

             // Process repeated writes
    -        for (uint256 i = 0; i < _numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE; i += STATE_DIFF_ENTRY_SIZE) {
    +        for (uint256 i = (_numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE) - 1; i >= 0; ) {
                 bytes calldata stateDiff = _stateDiffs[i:i + STATE_DIFF_ENTRY_SIZE];
                 uint64 enumIndex = stateDiff.readUint64(84);
                 if (enumIndex == 0) {
    @@ -187,6 +193,9 @@ contract Compressor is ICompressor, ISystemContract {
                     _compressedStateDiffs[stateDiffPtr:stateDiffPtr + len]
                 );
                 stateDiffPtr += len;
    +           unchecked{
    +               i = i - STATE_DIFF_ENTRY_SIZE;
    +           }
             }

             require(stateDiffPtr == _compressedStateDiffs.length, "Extra data in _compressedStateDiffs");

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol

```
File: system-contracts/contracts/KnownCodesStorage.sol

31: for (uint256 i = 0; i < hashesLen; ++i) {
```

    diff --git a/code/system-contracts/contracts/KnownCodesStorage.sol b/code/system-contracts/contracts/KnownCodesStorage.sol
    index 2900638..d52305c 100644
    --- a/code/system-contracts/contracts/KnownCodesStorage.sol
    +++ b/code/system-contracts/contracts/KnownCodesStorage.sol
    @@ -28,7 +28,7 @@ contract KnownCodesStorage is IKnownCodesStorage, ISystemContract {
         function markFactoryDeps(bool _shouldSendToL1, bytes32[] calldata _hashes) external onlyCallFromBootloader {
             unchecked {
                 uint256 hashesLen = _hashes.length;
    -            for (uint256 i = 0; i < hashesLen; ++i) {
    +            for (uint256 i = hashesLen - 1; i >=0; --i) {
                     _markBytecodeAsPublished(_hashes[i], _shouldSendToL1);
                 }
             }

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol

```
File: system-contracts/contracts/ImmutableSimulator.sol

38: for (uint256 i = 0; i < immutablesLength; ++i) {
```

    diff --git a/code/system-contracts/contracts/ImmutableSimulator.sol b/code/system-contracts/contracts/ImmutableSimulator.sol
    index 54fb4c9..765da7c 100644
    --- a/code/system-contracts/contracts/ImmutableSimulator.sol
    +++ b/code/system-contracts/contracts/ImmutableSimulator.sol
    @@ -35,7 +35,7 @@ contract ImmutableSimulator is IImmutableSimulator {
             require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");
             unchecked {
                 uint256 immutablesLength = _immutables.length;
    -            for (uint256 i = 0; i < immutablesLength; ++i) {
    +            for (uint256 i = immutablesLength - 1; i >= 0; --i) {
                     uint256 index = _immutables[i].index;
                     bytes32 value = _immutables[i].value;
                     immutableDataStorage[uint256(uint160(_dest))][index] = value;

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol

#### Test Code

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

#### Gas Report

| Contract0 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 77011                                     | 311             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| AddNum                                    | 7040            | 7040 | 7040   | 7040 | 1       |


| Contract1 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 73811                                     | 295             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| AddNum                                    | 3819            | 3819 | 3819   | 3819 | 1       |