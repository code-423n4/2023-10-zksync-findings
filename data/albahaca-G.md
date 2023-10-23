# gas optimization

note: [G-02],[G-03],[G-04],[G-05] these issue missed from bots.

# summary
|      |  issue  |  instance  |
|------|---------|------------|
|[G-01]|Can Make The Variable Outside The Loop To Save Gas |14|
|[G-02]|Avoid contract existence checks by using low-level calls|8|
|[G-03]|Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate|2|
|[G-04]|Avoid updating storage when the value hasn't changed|14|
|[G-05]|Use Assembly To Check For address(0)|3|
|[G-06]|Use assembly in place of abi.decode to extract calldata values more efficiently|1|
|[G-07]|Access mappings directly rather than using accessor functions|1|
|[G-08]|Make 3 event parameters indexed when possible|14|
|[G-09]|Sort Solidity operations using short-circuit mode|1|
|[G-10]|Using Private Rather Than Public For Constants, Saves Gas|4|
|[G-11]|Expressions for constant values such as a call to keccak256(), should use immutable rather than constants|1|
|[G-12]|>=/<= costs less gas than >/<|25|
|[G-13]|abi.encode() is less efficient than abi.encodePacked()|11|
|[G-14]|Do not calculate constants|3|
|[G-15]|Pack structs by putting data types that can fit together next to each other|6|
|[G-16]|Use hardcode address instead address(this)|7|
|[G-17]|bytes constants are more eficient than string constans|5|
|[G-18]|se do while loops instead of for loops|1|
|[G-19]|The result of function calls should be cached rather than re-calling the function|2|
|[G-20]|Empty blocks should be removed or emit something|2|
|[G-21]|Refactor event to avoid emitting empty data|2|
|[G-22]|Use Modifiers Instead of Functions To Save Gas|8|
|[G-23]|Gas savings can be achieved by changing the model for assigning value to the structure (260 gas)|6|
|[G-24]|State variables can be packed into fewer storage slots|1|
|[G-25]|Refactor a modifier to call a local function instead of directly having the code in the modifier, saving bytecode size and thereby deployment cost|9|
|[G-26]|Count from n to zero instead of counting from zero to n|20|
|[G-27]|Use fallback or receive instead of deposit() when transferring Ether|1|
|[G-28]|Using assembly to revert with an error message|~|
|[G-29]|Test if a number is even or odd by checking the last bit instead of using a modulo operator|2|
|[G-30]|Write gas-optimal for-loops|~|
|[G-31]|Don’t make variables public unless it is necessary to do so|7|
|[G-32]|Common math operations, like min and max have gas efficient alternatives|3|
|[G-33]|Admin functions can be payable|~|
|[G-34]|Use ECDSA signatures instead of merkle trees for allowlists and airdrops|~|
|[G-35]|Bitshifting is cheaper than multiplying or dividing by a power of two|2|



## [G-01] Can Make The Variable Outside The Loop To Save Gas 
When you declare a variable inside a loop, Solidity creates a new instance of the variable for each iteration of the loop. This can lead to unnecessary gas costs, especially if the loop is executed frequently or iterates over a large number of elements.

By declaring the variable outside the loop, you can avoid the creation of multiple instances of the variable and reduce the gas cost of your contract. Here's an example:

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
There are 14 instances of this issue
```solidity
File: code/contracts/ethereum/contracts/governance/Governance.sol
226    (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L226

### `Fix code`:
By declaring success and returnData outside the loop, you avoid creating multiple instances of these variables for each iteration of the loop
```diff
    function _execute(Call[] calldata _calls) internal {
+     bool success;
+     bytes memory returnData;
        for (uint256 i = 0; i < _calls.length; ++i) {
-           (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
+           (success, returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
            if (!success) {
                // Propage an error if the call fails.
                assembly {
                    revert(add(returnData, 0x20), mload(returnData))
                }
            }
        }
    }
```


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
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L102-L103


## [G‑02] Avoid contract existence checks by using low-level calls
Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low-level calls never check for contract existence


There are 8 instances of this issue
```solidity
File:  code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
282  IERC20(_l1Token).safeTransfer(_depositSender, amount);

317  IERC20(l1Token).safeTransfer(l1Receiver, amount);

341  IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L282


```solidity
File:  code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
171  IERC20(l1WethAddress).safeTransferFrom(msg.sender, address(this), _amount);

173  IWETH9(l1WethAddress).withdraw(_amount);

261  IWETH9(l1WethAddress).deposit{value: amount}();

263  IERC20(l1WethAddress).safeTransfer(l1WethWithdrawReceiver, amount);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L171


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
276    IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L276

## [G‑03] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

There are 2 instances of this issue
```solidity
File:  code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
    mapping(address => uint256) public __DEPRECATED_lastWithdrawalLimitReset;

    /// @dev A mapping L1 token address => the accumulated withdrawn amount during the withdrawal limit window
    mapping(address => uint256) public __DEPRECATED_withdrawnAmountInWindow;

    /// @dev The accumulated deposited amount per user.
    /// @dev A mapping L1 token address => user address => the total deposited amount by the user
    mapping(address => mapping(address => uint256)) public totalDepositedAmountPerUser;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L54-L61


```solidity
File:  code/contracts/ethereum/contracts/common/AllowList.sol
    mapping(address => AccessMode) public getAccessMode;

    /// @notice The mapping that stores permissions to call the function on the target address by the caller
    /// @dev caller => target => function signature => permission to call target function for the given caller address
    mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall;

    /// @dev The mapping L1 token address => struct Deposit
    mapping(address => Deposit) public tokenDeposit;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L22-L29


## [G‑04] Avoid updating storage when the value hasn't changed
If the old value is equal to the new value, not re-storing the value will avoid a Gsreset (2900 gas), potentially at the expense of a Gcoldsload (2100 gas) or a Gwarmaccess (100 gas)

There are 14 instances of this issue
```solidity
File:  code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
96      l2TokenProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[2]);
        l2TokenBeacon = _l2TokenBeacon;

209     depositAmount[msg.sender][_l1Token][l2TxHash] = amount;        
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L96-L97


```solidity
File:  code/contracts/ethereum/contracts/zksync/DiamondInit.sol
61      s.verifier = _initalizeData.verifier;
        s.governor = _initalizeData.governor;
        s.admin = _initalizeData.admin;


77      s.storedBatchHashes[0] = keccak256(abi.encode(storedBatchZero));
        s.allowList = _initalizeData.allowList;
        s.verifierParams = _initalizeData.verifierParams;
        s.zkPorterIsAvailable = _initalizeData.zkPorterIsAvailable;
        s.l2BootloaderBytecodeHash = _initalizeData.l2BootloaderBytecodeHash;
        s.l2DefaultAccountBytecodeHash = _initalizeData.l2DefaultAccountBytecodeHash;
        s.priorityTxMaxGasLimit = _initalizeData.priorityTxMaxGasLimit;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L61-L63


```solidity
File: code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol
59   _queue.data[tail] = _operation; 
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L59


## [G-05] Use Assembly To Check For address(0)
it's generally more gas-efficient to use assembly to check for a zero address (address(0)) than to use the == operator.

The reason for this is that the == operator generates additional instructions in the EVM bytecode, which can increase the gas cost of your contract. By using assembly, you can perform the zero address check more efficiently and reduce the overall gas cost of your contract.

Here's an example of how you can use assembly to check for a zero address:

```
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
In the above example, we have a function isZeroAddress that takes an address as input and returns a boolean value indicating whether the address is equal to the zero address. Inside the function, we convert the address to an integer using uint256(addr), and then use assembly to compare the integer to the zero address.

By using assembly to perform the zero address check, we can make our code more gas-efficient and reduce the overall cost of our contract. It's important to note that assembly can be more difficult to read and maintain than Solidity code, so it should be used with caution and only when necessary


There are 3 instances of this issue
```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
141   require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists

176   require(_facet == address(0), "a1"); // facet address must be zero

284   if (_init == address(0)) {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L141




## [G-06] Use assembly in place of abi.decode to extract calldata values more efficiently
Instead of using abi.decode, we can use assembly to decode our desired calldata values directly. This will allow us to avoid decoding calldata values that we will not use.
[Reffrence](https://code4rena.com/reports/2023-08-goodentry#gas-optimizations:~:text=%5BG%2D02%5D%20Use%20assembly%20in%20place%20of%20abi.decode%20to%20extract%20calldata%20values%20more%20efficiently)

There are 1 instance of this issue
```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
303   require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L303


## [G-07] Access mappings directly rather than using accessor functions
Saves having to do two JUMP instructions, along with stack setup


There are 1 instance of this issue
```solidity
File:   code/contracts/ethereum/contracts/common/AllowList.sol
137     return tokenDeposit[_l1Token];
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L137



## [G-08] Make 3 event parameters indexed when possible
It is the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

There are 3 instances of this issue
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


```solidity
File:  code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
27   event NewExecutionDelay(uint256 _newExecutionDelay);

30   event NewValidator(address _oldValidator, address _newValidator);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L27


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


```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
23   event DiamondCut(FacetCut[] facetCuts, address initAddress, bytes initCalldata);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L23


## [G-09] Sort Solidity operations using short-circuit mode
Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 
//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```

There are 1 instance of this issue
```solidity
File:  code/contracts/ethereum/contracts/zksync/DiamondProxy.sol
// @audit diamondStorage is state variable access of state variable use more gas
31   require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L31

`Fix code`
```diff
-       require(!diamondStorage.isFrozen || !facet.isFreezable, "q1");
+       require(!facet.isFreezable, "q1" || !diamondStorage.isFrozen);
```





## [G‑10] Using Private Rather Than Public For Constants, Saves Gas
When you declare a constant variable as public, Solidity generates a getter function that allows anyone to read the value of the constant. This getter function can consume gas, especially if the constant is read frequently or the contract is called by multiple users.

By using private instead of public for constants, you can prevent the generation of the getter function and reduce the overall gas cost of your contract.


There are 4 instances of this issue
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


## [G-11] Expressions for constant values such as a call to keccak256(), should use immutable rather than constants
The reason for this is that constant variables are evaluated at runtime and their value is included in the bytecode of the contract. This means that any expensive operations performed as part of the constant expression, such as a call to keccak256(), will be executed every time the contract is deployed, even if the result is always the same. This can result in higher gas costs.

There are 1 instance of this issue
```solidity
File:  code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol
12    bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L12

## [G-12] >=/<= costs less gas than >/<
The compiler uses opcodes GT and ISZERO for code that uses >, but only requires LT for >=. A similar behaviour applies for >, which uses opcodes LT and ISZERO, but only requires GT for <=.
[Reffrence](https://gist.github.com/CloudEllie/05fda0fdecba2ed7d85f68c709e46c8d#gas-18--costs-less-gas-than-)

There are 25 instances of this issue
```solidity
File:  code/contracts/ethereum/contracts/common/AllowList.sol
62  for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

96  for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {    
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L62


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


```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
25  require(pathLength < 256, "bt");

26  require(_index < (1 << pathLength), "px");

29  for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L25


## [G-13] abi.encode() is less efficient than abi.encodePacked()
In terms of efficiency, abi.encodePacked() is generally considered to be more gas-efficient than abi.encode(), because it skips the step of adding function signatures and other metadata to the encoded data. However, this comes at the cost of reduced safety, as abi.encodePacked() does not perform any type checking or padding of data.


```solidity
File:  code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
119  l2BridgeProxyConstructorData = abi.encode(bridgeImplementationAddr, _governor, proxyInitializationParams);

244  data = abi.encode(data1, data2, data3);

354  bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""));
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L119


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


```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
363   bytes memory transactionEncoding = abi.encode(transaction);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L363


## [G-14] Do not calculate constants
Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas
[Reffrence](https://code4rena.com/reports/2022-07-golom#g-24-do-not-calculate-constants)

```solidity
File:  code/contracts/ethereum/contracts/zksync/Config.sol
14   uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

26   uint256 constant MAX_INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE * 4765;

33   uint256 constant MAX_REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE * 7564;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L14


## [G-15] Pack structs by putting data types that can fit together next to each other
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


## [G-16] Use hardcode address instead address(this)
it can be more gas-efficient to use a hardcoded address instead of the address(this) expression, especially if you need to use the same address multiple times in your contract.

The reason for this is that using address(this) requires an additional EXTCODESIZE operation to retrieve the contract's address from its bytecode, which can increase the gas cost of your contract. By pre-calculating and using a hardcoded address, you can avoid this additional operation and reduce the overall gas cost of your contract.

Here's an example of how you can use a hardcoded address instead of address(this):

```
contract MyContract {
    address public myAddress = 0x1234567890123456789012345678901234567890;
    #L
    function doSomething() public {
        // Use myAddress instead of address(this)
        require(msg.sender == myAddress, "Caller is not authorized");
        
        // Do something
    }
}
```
In the above example, we have a contract MyContract with a public address variable myAddress. Instead of using address(this) to retrieve the contract's address, we have pre-calculated and hardcoded the address in the variable. This can help to reduce the gas cost of our contract and make our code more efficient.

[References](https://book.getfoundry.sh/reference/forge-std/compute-create-address)


```solidity
File:  code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
117   (address(this), l2TokenProxyBytecodeHash, _governor)

217   uint256 balanceBefore = _token.balanceOf(address(this));
219  _token.safeTransferFrom(_from, address(this), _amount);
219   uint256 balanceAfter = _token.balanceOf(address(this));
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L117


```solidity
File:  code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
116    (address(this), l1WethAddress, _l2WethAddress)

171    IERC20(l1WethAddress).safeTransferFrom(msg.sender, address(this), _amount);

289    require(l1EthReceiver == address(this), "Wrong L1 ETH withdraw receiver");
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L116


## [G-17] bytes constants are more eficient than string constans
If the data can fit in 32 bytes, the bytes32 data type can be used instead of bytes or strings, as it is less robust in terms of robustness.


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


## [G-18] Use do while loops instead of for loops
A do while loop will cost less gas since the condition is not being checked for the first iteration.
[Reffrence](https://code4rena.com/reports/2023-05-ajna#g-09-use-do-while-loops-instead-of-for-loops)

```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
// @audit in line number 24   require(pathLength > 0, "xc");
29  for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L25


## [G‑19] The result of function calls should be cached rather than re-calling the function
The instances in this report point to the second+ call of the function within a single function.

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


## [G‑20] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.
If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation.

```solidity
File:  code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol
15    function _upgradeL1Contract(bytes calldata _customCallDataForUpgrade) internal virtual {}

21    function _postUpgrade(bytes calldata _customCallDataForUpgrade) internal virtual {}
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L15

## [G-21] Refactor event to avoid emitting empty data

```solidity
File:  code/contracts/ethereum/contracts/zksync/facets/Admin.sol
115    emit Freeze();

126    emit Unfreeze();
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L115


## [G-22] Use Modifiers Instead of Functions To Save Gas
Example of two contracts with modifiers and internal view function:
```
// SPDX-License-Identifier: MIT
pragma solidity 0.8.9;
contract Inlined {
    function isNotExpired(bool _true) internal view {
        require(_true == true, "Exchange: EXPIRED");
    }
function foo(bool _test) public returns(uint){
            isNotExpired(_test);
            return 1;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity 0.8.9;
contract Modifier {
modifier isNotExpired(bool _true) {
        require(_true == true, "Exchange: EXPIRED");
        _;
    }
function foo(bool _test) public isNotExpired(_test)returns(uint){
        return 1;
    }
}
```
Differences:
```
Deploy Modifier.sol
108727
Deploy Inlined.sol
110473
Modifier.foo
21532
Inlined.foo
21556
```
This with 0.8.9 compiler and optimization enabled. As you can see it's cheaper to deploy with a modifier, and it will save you about 30 gas. But sometimes modifiers increase code size of the contract.
There are 1 instances of this issue:


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


```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol
50  function isEmpty(Queue storage _queue) internal view returns (bool) {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L50


## [G-23] Gas savings can be achieved by changing the model for assigning value to the structure (260 gas)

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

## [G‑24] State variables can be packed into fewer storage slots
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


`Fix code`
can save one storage slots
```diff
address public securityCouncil;
uint64 public minDelay;
```

## [G-25] Refactor a modifier to call a local function instead of directly having the code in the modifier, saving bytecode size and thereby deployment cost [Ref](https://0xmacro.com/blog/solidity-gas-optimizations-cheat-sheet/#:~:text=Refactor%20a%20modifier%20to%20call%20a%20local%20function%20instead%20of%20directly%20having%20the%20code%20in%20the%20modifier%2C%20saving%20bytecode%20size%20and%20thereby%20deployment%20cost)
Modifiers code is copied in all instances where it's used, increasing bytecode size. By doing a refractor to the internal function, one can reduce bytecode size significantly at the cost of one JUMP. Consider doing this only if you are constrained by bytecode size.

`Before:`
```
modifier onlyOwner() {
		require(owner() == msg.sender, "Ownable: caller is not the owner");
		_;
}
```

`After:`

```
modifier onlyOwner() {
		_checkOwner();
		_;
}

function _checkOwner() internal view virtual {
    require(owner() == msg.sender, "Ownable: caller is not the owner");
}
```

```solidity
File: code/contracts/ethereum/contracts/common/AllowListed.sol
10   modifier senderCanCallFunction(IAllowList _allowList) {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L10


```solidity
File:  code/contracts/ethereum/contracts/common/ReentrancyGuard.sol
68   modifier nonReentrant() {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L68



```solidity
File:   code/contracts/ethereum/contracts/governance/Governance.sol
58     modifier onlySelf() {

64     modifier onlySecurityCouncil() {

70     modifier onlyOwnerOrSecurityCouncil() {        
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L58


```solidity
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
65    modifier onlyValidator() {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L65


```solidity
File:   code/contracts/ethereum/contracts/zksync/facets/Base.sol
18   modifier onlyGovernor() {

24   modifier onlyGovernorOrAdmin() {

30   modifier onlyValidator() {        
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L30


## [G-26] Count from n to zero instead of counting from zero to n
When setting a storage variable to zero, a refund is given, so the net gas spent on counting will be less if the final state of the storage variable is zero.
[Reffrence](https://www.rareskills.io/post/gas-optimization#viewer-dkmii:~:text=to%20one%20writes.-,13.%20Count%20from%20n%20to%20zero%20instead%20of%20counting%20from%20zero%20to%20n,-When%20setting%20a)


There are 20 instances of this issue
```solidity
File:  code/contracts/ethereum/contracts/common/AllowList.sol
62  for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {

96  for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {    
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L62


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


```solidity
File:  code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol
29  for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L25

## [G-27] Use fallback or receive instead of deposit() when transferring Ether
Similar to above, you can “just transfer” ether to a contract and have it respond to the transfer instead of using a payable function. This of course, depends on the rest of the contract’s architecture.
[Reffrence](https://www.rareskills.io/post/gas-optimization#viewer-dkmii:~:text=2.-,Use%20fallback%20or%20receive%20instead%20of%20deposit()%20when%20transferring%20Ether,-Similar%20to%20above)
Example Deposit in AAVE

```
contract AddLiquidity{

    receive() external payable {
      IWETH(weth).deposit{msg.value}();
      AAVE.deposit(weth, msg.value, msg.sender, REFERRAL_CODE)
    }
}
```
The fallback function is capable of receiving bytes data which can be parsed with abi.decode. This servers as an alternative to supplying arguments to a deposit function.

```solidity
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
261   IWETH9(l1WethAddress).deposit{value: amount}();
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L261


## [G-28] Using assembly to revert with an error message
When reverting in solidity code, it is common practice to use a require or revert statement to revert execution with an error message. This can in most cases be further optimized by using assembly to revert with the error message.
Here’s an example;
```
/// calling restrictedAction(2) with a non-owner address: 24042
contract SolidityRevert {
    address owner;
    uint256 specialNumber = 1;

    constructor() {
        owner = msg.sender;
    }

    function restrictedAction(uint256 num)  external {
        require(owner == msg.sender, "caller is not owner");
        specialNumber = num;
    }
}

/// calling restrictedAction(2) with a non-owner address: 23734
contract AssemblyRevert {
    address owner;
    uint256 specialNumber = 1;

    constructor() {
        owner = msg.sender;
    }

    function restrictedAction(uint256 num)  external {
        assembly {
            if sub(caller(), sload(owner.slot)) {
                mstore(0x00, 0x20) // store offset to where length of revert message is stored
                mstore(0x20, 0x13) // store length (19)
                mstore(0x40, 0x63616c6c6572206973206e6f74206f776e657200000000000000000000000000) // store hex representation of message
                revert(0x00, 0x60) // revert with data
            }
        }
        specialNumber = num;
    }
}

```

From the example above we can see that we get a gas saving of over 300 gas when reverting wth the same error message with assembly against doing so in solidity. This gas savings come from the memory expansion costs and extra type checks the solidity compiler does under the hood.


## [G-29] Test if a number is even or odd by checking the last bit instead of using a modulo operator
The conventional way to check if a number is even or odd is to do x % 2 == 0 where x is the number in question. You can instead check if x & uint256(1) == 0. where x is assumed to be a uint256. Bitwise and is cheaper than the modulo op code. In binary, the rightmost bit represents "1" whereas all the bits to the are multiples of 2, which are even. Adding "1" to an even number causes it to be odd.


```solidity
File: code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol
27   require(bytecodeLenInWords % 2 == 1, "ps"); // bytecode length in words must be odd

43   require(_bytecodeLen(_bytecodeHash) % 2 == 1, "uy"); // Code length in words must be odd
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L27


## [G-30] Write gas-optimal for-loops
This is what a gas-optimal for loop looks like, if you combine the two tricks above:
```
for (uint256 i; i < limit; ) {
    
    // inside the loop
    
    unchecked {
        ++i;
    }
}
```
The two differences here from a conventional for loop is that i++ becomes ++i (as noted above), and it is unchecked because the limit variable ensures it won’t overflow.


`make all loops with above format`


## [G-31] Don’t make variables public unless it is necessary to do so
A public storage variable has an implicit public function of the same name. A public function increases the size of the jump table and adds bytecode to read the variable in question. That makes the contract larger.

Remember, private variables aren’t private, it’s not difficult to extract the variable value using [web3.js](https://www.rareskills.io/post/web3-js-tutorial).

This is especially true for constants which are meant to be read by humans rather than smart contracts.


```solidity
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol
    address public l2Bridge;

    /// @dev The address that acts as a beacon for L2 tokens
    address public l2TokenBeacon;

    /// @dev The bytecode hash of the L2 token contract
    bytes32 public l2TokenProxyBytecodeHash;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L46-L52


```solidity
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol
    address public l2Bridge;

    /// @dev The address of the WETH on L2
    address public l2WethAddress;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L53-L57


```solidity
File: code/contracts/ethereum/contracts/governance/Governance.sol
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



## [G-32] Common math operations, like min and max have gas efficient alternatives
Unoptimized
```
function max(uint256 x, uint256 y) public pure returns (uint256 z) {
	z = x > y ? x : y;
}
```


Optimized

```
function max(uint256 x, uint256 y) public pure returns (uint256 z) {
    /// @solidity memory-safe-assembly
    assembly {
        z := xor(x, mul(xor(x, y), gt(y, x)))
    }
}
```

The code above is taken from the math section of the Solady Library, more math operations can be found there. It is worth exploring the library to see what gas efficient operations are available to you.


The reason the above example is more gas efficient is because the ternary operator (and in general, code with conditionals in it) contain conditional jumps in the opcodes, which are more costly.


```solidity
File: code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol
101   return costForComputation + costForPubdata;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L101


## [G-33] Admin functions can be payable
We can make admin specific functions payable to save gas, because the compiler won’t be checking the callvalue of the function.

This will also make the contract smaller and cheaper to deploy as there will be fewer opcodes in the creation and runtime code.

- in wich functions is the follwing modifier make those functions payable to save gas
```solidity
File:   code/contracts/ethereum/contracts/governance/Governance.sol
58     modifier onlySelf() {

64     modifier onlySecurityCouncil() {

70     modifier onlyOwnerOrSecurityCouncil() {        
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L58


```solidity
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
65    modifier onlyValidator() {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L65


```solidity
File:   code/contracts/ethereum/contracts/zksync/facets/Base.sol
18   modifier onlyGovernor() {

24   modifier onlyGovernorOrAdmin() {

30   modifier onlyValidator() {        
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L30

## [G-34] Use ECDSA signatures instead of merkle trees for allowlists and airdrops
Merkle trees use a considerable amount of calldata and increase in cost with the size of the merkle proof. Generally, using digital signatures is cheaper gas-wise compared to merkle proofs.



## [G-35] Bitshifting is cheaper than multiplying or dividing by a power of two
In Solidity, it is often more gas efficient to multiply or divide numbers that are powers of two by shifting their bits, rather than using the multiplication or division operators.

For example, the following two expressions are equivalent
```
10 * 2
10 << 1 # shift 10 left by 1
```


and this is also equivalent
```
8 / 4
8 >> 2 # shift 8 right by 2
```
Bit shifting operations opcodes in the EVM, such as shr (shift right) and shl (shift left), cost 5 gas while multiplication and division operations (mul and div) cost 3 gas each.


Majority of gas savings also comes the fact that solidity does no overflow/underflow or division by check for shr and shl operations. It’s important to have this in mind when using these operators.

```solidity
File: code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol
50   codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L50

```solidity
File: code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol
50  uint256 bitOffset = (_index & 7) * 32;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L50



