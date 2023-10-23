# [G-01] Remove useless variable from memory

3 instances: 

- Remove balanceAfter variable from [L1ERC20Bridge::_depositFunds](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L219)

```
@@ -216,9 +217,9 @@ contract L1ERC20Bridge is IL1Bridge, IL1BridgeLegacy, AllowListed, ReentrancyGua
     function _depositFunds(address _from, IERC20 _token, uint256 _amount) internal returns (uint256) {
         uint256 balanceBefore = _token.balanceOf(address(this));
         _token.safeTransferFrom(_from, address(this), _amount);
-        uint256 balanceAfter = _token.balanceOf(address(this));
 
-        return balanceAfter - balanceBefore;
+        return _token.balanceOf(address(this)) - balanceBefore;
```

Gain:
```
Contract            ·  Method           ·  Min        ·  Max         ·  Avg   
L1ERC20Bridge           ·  deposit                   ·     169248  ·     272310  ·         220895  ·
L1ERC20Bridge           ·  deposit                   ·     169248  ·     272303  ·         220889  ·
```

- Remove timestamp variable from [ValidatorTimelock::commitBatches](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L84)
```
@@ -81,9 +81,9 @@ contract ValidatorTimelock is IExecutor, Ownable2Step {
         unchecked {
             // This contract is only a temporary solution, that hopefully will be disabled until 2106 year, so...
             // It is safe to cast.
-            uint32 timestamp = uint32(block.timestamp);
             for (uint256 i = 0; i < _newBatchesData.length; ++i) {
-                committedBatchTimestamp.set(_newBatchesData[i].batchNumber, timestamp);
+                committedBatchTimestamp.set(_newBatchesData[i].batchNumber, uint32(block.timestamp));
```
Gain:
```
[PASS] test_SuccessfullyCommitBatch() (gas: 227036) // BEFORE
[PASS] test_SuccessfullyCommitBatch() (gas: 227025) // AFTER
```
- Remove baseCost variable from [Mailbox::_requestL2Transaction](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L305)

```
@@ -302,15 +300,16 @@ contract MailboxFacet is Base, IMailbox {
         // Using a new scope to prevent "stack too deep" error
         {
             params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);
-            uint256 baseCost = params.l2GasPrice * _l2GasLimit;
-            require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost
+            require(msg.value >= (params.l2GasPrice * _l2GasLimit) + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost
         }
```
Gain:
```
[PASS] test_DepositSuccessfully() (gas: 198184) // BEFORE
[PASS] test_DepositSuccessfullyIfRefundRecipientIsNotSpecified() (gas: 200741) // BEFORE

[PASS] test_DepositSuccessfully() (gas: 198179) // AFTER
[PASS] test_DepositSuccessfullyIfRefundRecipientIsNotSpecified() (gas: 200736) // AFTER
```

# [G-02] Reestructure the Governance.sol storage
Change [Governance.sol::minDelay](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L35) variable to uint32 and reposition it alongside the `securityCouncil` variable in the storage layout.
```
@@ -24,7 +24,7 @@ contract Governance is IGovernance, Ownable2Step {
     /// @notice The address of the security council.
     /// @dev It is supposed to be multisig contract.
     address public securityCouncil;
-
+    uint32 public minDelay;
     /// @notice A mapping to store timestamps where each operation will be ready for execution.
     /// @dev - 0 means the operation is not created.
     /// @dev - 1 (EXECUTED_PROPOSAL_TIMESTAMP) means the operation is already executed.
@@ -32,7 +32,7 @@ contract Governance is IGovernance, Ownable2Step {
     mapping(bytes32 => uint256) public timestamps;
 
     /// @notice The minimum delay in seconds for operations to be ready for execution.
-    uint256 public minDelay;
@@ -46,7 +46,7 @@ contract Governance is IGovernance, Ownable2Step {
         securityCouncil = _securityCouncil;
         emit ChangeSecurityCouncil(address(0), _securityCouncil);
 
-        minDelay = _minDelay;
+        minDelay = uint32(_minDelay);
         emit ChangeMinDelay(0, _minDelay);
     }
@@ -248,7 +248,7 @@ contract Governance is IGovernance, Ownable2Step {
     /// @param _newDelay The new minimum delay time (in seconds) for future operations.
     function updateDelay(uint256 _newDelay) external onlySelf {
         emit ChangeMinDelay(minDelay, _newDelay);
-        minDelay = _newDelay;
+        minDelay = uint32(_newDelay);
     }
```

Gain:
```
[PASS] test_ScheduleShadowAndExecute() (gas: 63737) // Before
[PASS] test_ScheduleShadowAndExecuteInstant() (gas: 63133) // Before
[PASS] test_ScheduleTransparentAndExecute() (gas: 61925) // Before
[PASS] test_ScheduleTransparentAndExecuteInstant() (gas: 66018) // Before

[PASS] test_ScheduleShadowAndExecute() (gas: 61760) // After
[PASS] test_ScheduleShadowAndExecuteInstant() (gas: 61156) // After
[PASS] test_ScheduleTransparentAndExecute() (gas: 61948) // After
[PASS] test_ScheduleTransparentAndExecuteInstant() (gas: 64041) // After
```

# [G-03] Cache variables
Cache variable `_factoryDeps.length` from [BaseZkSyncUpgrade::_verifyFactoryDeps](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L200)

```
@@ -198,10 +198,11 @@ abstract contract BaseZkSyncUpgrade is Base {
     /// @param _factoryDeps The list of factory deps
     /// @param _expectedHashes The list of expected bytecode hashes
     function _verifyFactoryDeps(bytes[] calldata _factoryDeps, uint256[] calldata _expectedHashes) private pure {
-        require(_factoryDeps.length == _expectedHashes.length, "Wrong number of factory deps");
-        require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32");
+        uint256 factoryDepsLength = _factoryDeps.length;
+        require(factoryDepsLength == _expectedHashes.length, "Wrong number of factory deps");
+        require(factoryDepsLength <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32");
 
-        for (uint256 i = 0; i < _factoryDeps.length; ++i) {
+        for (uint256 i = 0; i < factoryDepsLength; ++i) {
```

# [G-04] Short-circuit booleans
Short-circuit opportunity on [DiamondProxy::fallback](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L25)
The order of conditions in the `require` statement can be rearranged to check for the shorter condition first.

```
 @@ -22,7 +22,8 @@ contract DiamondProxy {
         // Check whether the data contains a "full" selector or it is empty.
         // Required because Diamond proxy finds a facet by function signature,
         // which is not defined for data length in range [1, 3].
-        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
+        require(msg.data.length == 0 || msg.data.length >= 4, "Ut");
```