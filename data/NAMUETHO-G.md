
## Here Are the Issues found

### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.
This issue may result in unexpected behavior during unsigned integer comparisons.

#### The File that was found the Issue and the specific line of code:
```
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::275 => require(amount > 0, "y1");
```

### Don't Initialize Variables with Default Value

#### Impact
Uninitialized variables are assigned with the types default value.
Explicitly initializing a variable with it's default value costs unnecessary gas.
Example
🤦 Bad:

uint256 x = 0;
bool y = false;
🚀 Good:

uint256 x;
bool y;

#### The Files that was found the Issue and the specific line of code:
```
code\contracts\ethereum\contracts\common\AllowList.sol::62 => for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {
code\contracts\ethereum\contracts\common\AllowList.sol::96 => for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::204 => for (uint256 i = 0; i < _factoryDeps.length; ++i) {
code\contracts\ethereum\contracts\governance\Governance.sol::225 => for (uint256 i = 0; i < _calls.length; ++i) {    
```

### Cache Array Length Outside of Loop

#### Impact
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

🤦 Less Efficient:

for (uint256 i = 0; i < array.length; i++) {
    // Invariant: The length of the array remains unchanged
}
🚀 Optimized:

uint256 len = array.length;
for (uint256 i = 0; i < len; i++) {
    // Invariant: The length of the array remains unchanged
}

#### The Files that was found the Issue and the specific line of code:

```
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::93 => require(_factoryDeps.length == 3, "mk");
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::326 => // Check that the message length is correct.
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::327 => // It should be equal to the length of the function signature + address + address + uint256 = 4 + 20 + 20 + 32 =
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::329 => require(_l2ToL1message.length == 76, "kk");
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::90 => require(_factoryDeps.length == 2, "Invalid factory deps length provided");
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::275 => // Check that the message length is correct.
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::277 => // It should be equal to the length of the function signature + eth receiver address + uint256 amount +
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::279 => require(_message.length == 96, "Incorrect ETH message with additional data length");
code\contracts\ethereum\contracts\governance\Governance.sol::225 => for (uint256 i = 0; i < _calls.length; ++i) {
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::93 => require(_factoryDeps.length == 3, "mk");
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::326 => // Check that the message length is correct.
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::327 => // It should be equal to the length of the function signature + address + address + uint256 = 4 + 20 + 20 + 32 =
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::329 => require(_l2ToL1message.length == 76, "kk");
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::90 => require(_factoryDeps.length == 2, "Invalid factory deps length provided");
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::275 => // Check that the message length is correct.
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::277 => // It should be equal to the length of the function signature + eth receiver address + uint256 amount +
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::279 => require(_message.length == 96, "Incorrect ETH message with additional data length");
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::201 => require(_factoryDeps.length == _expectedHashes.length, "Wrong number of factory deps");
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::202 => require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32");
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::204 => for (uint256 i = 0; i < _factoryDeps.length; ++i) {
code\contracts\ethereum\contracts\common\AllowList.sol::59 => uint256 targetsLength = _targets.length;
code\contracts\ethereum\contracts\common\AllowList.sol::60 => require(targetsLength == _accessModes.length, "yg"); // The size of arrays should be equal
code\contracts\ethereum\contracts\common\AllowList.sol::89 => uint256 callersLength = _callers.length;
code\contracts\ethereum\contracts\common\AllowList.sol::92 => require(callersLength == _targets.length, "yw");
code\contracts\ethereum\contracts\common\AllowList.sol::93 => require(callersLength == _functionSigs.length, "yx");
code\contracts\ethereum\contracts\common\AllowList.sol::94 => require(callersLength == _enables.length, "yy");
code\contracts\ethereum\contracts\common\L2ContractAddresses.sol::17 => /// @dev The address of the special smart contract that can send arbitrary length message as an L2 log
code\contracts\ethereum\contracts\common\ReentrancyGuard.sol::31 => // solhint-disable-next-line max-line-length
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::18 => /// - Bytecode bytes length is not a multiple of 32
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::19 => /// - Bytecode bytes length is not less than 2^21 bytes (2^16 words)
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::20 => /// - Bytecode words length is not odd
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::22 => // Note that the length of the bytecode must be provided in 32-byte words.
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::23 => require(_bytecode.length % 32 == 0, "pq");
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::25 => uint256 bytecodeLenInWords = _bytecode.length / 32;
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::26 => require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::27 => require(bytecodeLenInWords % 2 == 1, "ps"); // bytecode length in words must be odd
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::31 => // Setting the length
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::43 => require(_bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::46 => /// @notice Returns the length of the bytecode associated with the given hash.
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::48 => /// @return codeLengthInWords The length of the bytecode in words.
code\contracts\ethereum\contracts\common\libraries\UnsafeBytes.sol::12 => * 1) Functions don't check the length of the bytes array, so it can go out of bounds.
code\contracts\ethereum\contracts\common\libraries\UnsafeBytes.sol::13 => * The user of the library must check for bytes length before using any functions from the library!
```

### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
🤦 Less Efficient:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

🚀 Optimized:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

#### The Files that was found the Issue and the specific line of code:

```
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::354 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""));
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::56 => keccak256(_constructorData)
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::354 => bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""));
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::56 => keccak256(_constructorData)
code\contracts\ethereum\contracts\governance\Governance.sol::205 => return keccak256(abi.encode(_operation));
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::6 => import "../zksync/interfaces/IMailbox.sol";
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::7 => import "../zksync/interfaces/IVerifier.sol";
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::8 => import "../common/libraries/L2ContractHelper.sol";
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::9 => import "../zksync/libraries/TransactionValidator.sol";
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::171 => require(_l2ProtocolUpgradeTx.txType == SYSTEM_UPGRADE_L2_TX_TYPE, "L2 system upgrade tx type is wrong");
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::187 => "The new protocol version should be included in the L2 system upgrade tx"
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::218 => "New protocol version is not greater than the current one"
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::222 => require(s.l2SystemContractsUpgradeTxHash == bytes32(0), "Previous upgrade has not been finalized");
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::225 => "The batch number of the previous upgrade has not been cleaned"
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::192 => bytes32 l2ProtocolUpgradeTxHash = keccak256(encodedTransaction);
code\contracts\ethereum\contracts\common\ReentrancyGuard.sol::28 => // keccak256("ReentrancyGuard") - 1;
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::12 => bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::67 => bytes32 data = keccak256(
```

### Long Revert Strings

#### Impact
 Shortening revert strings to fit in 32 bytes reduces deployment and execution gas costs. If Solidity version is >=0.8.4, using Custom Errors is more efficient, providing a detailed error description using NatSpec.
Example:

🤦 Less Efficient:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Optimized (Shorter String):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Optimized (Custom Errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### The Files that was found the Issue and the specific line of code:

```
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::5 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::6 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::13 => import "./libraries/BridgeInitializationHelper.sol";
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::16 => import "../common/interfaces/IAllowList.sol";
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::18 => import "../common/libraries/UnsafeBytes.sol";
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::19 => import "../common/libraries/L2ContractHelper.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::5 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::12 => import "../common/interfaces/IAllowList.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::14 => import "./libraries/BridgeInitializationHelper.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::17 => import "../common/libraries/UnsafeBytes.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::19 => import "../common/libraries/L2ContractHelper.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::20 => import {L2_ETH_TOKEN_SYSTEM_CONTRACT_ADDR} from "../common/L2ContractAddresses.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::90 => require(_factoryDeps.length == 2, "Invalid factory deps length provided");
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::93 => "Miscalculated deploy transactions fees"
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::223 => revert("Method not supported. Failed deposit funds are sent to the L2 refund recipient address.");
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::279 => require(_message.length == 96, "Incorrect ETH message with additional data length");
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::284 => "Incorrect ETH message function selector"
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::295 => require(l2Sender == l2Bridge, "The withdrawal was not initiated by L2 bridge");
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::5 => import "../../zksync/interfaces/IZkSync.sol";
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::6 => import "../../vendor/AddressAliasHelper.sol";
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::7 => import "../../common/libraries/L2ContractHelper.sol";
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::8 => import {L2_DEPLOYER_SYSTEM_CONTRACT_ADDR} from "../../common/L2ContractAddresses.sol";
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::9 => import "../../common/interfaces/IL2ContractDeployer.sol";
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::5 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::6 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::13 => import "./libraries/BridgeInitializationHelper.sol";
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::16 => import "../common/interfaces/IAllowList.sol";
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::18 => import "../common/libraries/UnsafeBytes.sol";
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::19 => import "../common/libraries/L2ContractHelper.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::5 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::12 => import "../common/interfaces/IAllowList.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::14 => import "./libraries/BridgeInitializationHelper.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::17 => import "../common/libraries/UnsafeBytes.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::19 => import "../common/libraries/L2ContractHelper.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::20 => import {L2_ETH_TOKEN_SYSTEM_CONTRACT_ADDR} from "../common/L2ContractAddresses.sol";
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::90 => require(_factoryDeps.length == 2, "Invalid factory deps length provided");
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::93 => "Miscalculated deploy transactions fees"
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::223 => revert("Method not supported. Failed deposit funds are sent to the L2 refund recipient address.");
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::279 => require(_message.length == 96, "Incorrect ETH message with additional data length");
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::284 => "Incorrect ETH message function selector"
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::295 => require(l2Sender == l2Bridge, "The withdrawal was not initiated by L2 bridge");
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::5 => import "../../zksync/interfaces/IZkSync.sol";
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::6 => import "../../vendor/AddressAliasHelper.sol";
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::7 => import "../../common/libraries/L2ContractHelper.sol";
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::8 => import {L2_DEPLOYER_SYSTEM_CONTRACT_ADDR} from "../../common/L2ContractAddresses.sol";
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::9 => import "../../common/interfaces/IL2ContractDeployer.sol";
code\contracts\ethereum\contracts\common\AllowList.sol::5 => import "@openzeppelin/contracts/access/Ownable2Step.sol";
code\contracts\ethereum\contracts\common\Dependencies.sol::5 => import "@openzeppelin/contracts/proxy/transparent/TransparentUpgradeableProxy.sol";
code\contracts\ethereum\contracts\governance\Governance.sol::5 => import {Ownable2Step} from "@openzeppelin/contracts/access/Ownable2Step.sol";
code\contracts\ethereum\contracts\governance\Governance.sol::59 => require(msg.sender == address(this), "Only governance contract itself allowed to call this function");
code\contracts\ethereum\contracts\governance\Governance.sol::65 => require(msg.sender == securityCouncil, "Only security council allowed to call this function");
code\contracts\ethereum\contracts\governance\Governance.sol::73 => "Only the owner and security council are allowed to call this function"
code\contracts\ethereum\contracts\governance\Governance.sol::88 => /// @dev Returns whether an operation is pending or not. Note that a "pending" operation may also be "ready".
code\contracts\ethereum\contracts\governance\Governance.sol::94 => /// @dev Returns whether an operation is ready for execution. Note that a "ready" operation is also "pending".
code\contracts\ethereum\contracts\governance\Governance.sol::172 => require(isOperationReady(id), "Operation must be ready before execution");
code\contracts\ethereum\contracts\governance\Governance.sol::177 => require(isOperationReady(id), "Operation must be ready after execution");
code\contracts\ethereum\contracts\governance\Governance.sol::191 => require(isOperationPending(id), "Operation must be pending before execution");
code\contracts\ethereum\contracts\governance\Governance.sol::196 => require(isOperationPending(id), "Operation must be pending after execution");
code\contracts\ethereum\contracts\governance\Governance.sol::216 => require(!isOperation(_id), "Operation with this proposal id already exists");
code\contracts\ethereum\contracts\governance\Governance.sol::217 => require(_delay >= minDelay, "Proposed delay is less than minimum delay");
code\contracts\ethereum\contracts\governance\Governance.sol::240 => require(_predecessorId == bytes32(0) || isOperationDone(_predecessorId), "Predecessor operation not completed");
```
### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.
While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;
#### Findings:
```
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::310 => // 2. zkSync contract withdraw funds in `finalizeEthWithdrawal`
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::310 => // 2. zkSync contract withdraw funds in `finalizeEthWithdrawal`
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::50 => codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
code\contracts\ethereum\contracts\common\libraries\UnsafeBytes.sol::15 => * 2) Read variables are not cleaned up - https://docs.soliditylang.org/en/v0.8.16/internals/variable_cleanup.html.
```
