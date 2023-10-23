# Gas Optimization zkSync Era L1 contracts

I have conducted a thorough audit of zkSync Era contest, focusing on optimizing gas saving for efficient and cost-effective operations. After rigorous analysis, I am pleased to present the following key findings and recommendations to help improve the gas optmization of zkSync Era:

When doing the refactoring, we’ve avoided including any changes that were reported by the bot and we encourage you to go through the automated findings to maximum the amount of gas being saved.

| Finding                                                                                     | Occurrences |
| :------------------------------------------------------------------------------------------ | :---------: |
| Use ECDSA signatures instead of merkle trees                                                |      1      |
| Private functions used once can be inlined                                                  |     11      |
| Mappings used within a function more than once should be cached to save gas                 |      9      |
| Use assembly to write address/contract type storage values                                  |      7      |
| Use assembly to validate msg.sender                                                         |     11      |
| Invert if-else statements that have a negation                                              |      4      |
| Assigning state variables directly with named struct constructors wastes gas                |      7      |
| Using constants directly, rather than caching the value, saves gas                          |      1      |
| Use selfdestruct in the constructor if the contract is one-time use                         |      5      |
| Understand the trade-offs when choosing between internal functions and modifiers            |     10      |
| Use fallback or receive instead of deposit() when transferring Ether                        |      5      |
| Consider using alternatives to OpenZeppelin                                                 |      -      |
| Using assembly to revert with an error message                                              |     151     |
| Test if a number is even or odd by checking the last bit instead of using a modulo operator |      1      |
| Don’t make variables public unless it is necessary to do so                                 |     27      |
| It is cheaper to use vanity addresses with leading zeros, this saves calldata gas cost.     |      1      |
| Using private rather than public for constants, saves Gas                                   |      9      |
| Using fixex bytes is cheaper than using string                                              |      5      |
| Always use Named Returns                                                                    |     66      |
| Can make the variable outside the loop to save gas                                          |      6      |
| Emitting memory values instead of the storage one.                                          |      5      |
| It is sometimes cheaper to cache calldata                                                   |     37      |
| Empty blocks should be removed or emit something                                            |      4      |
| Use storage pointers instead of memory where appropriate                                    |     14      |
| Use constants instead of type(uintx).max                                                    |      3      |
| abi.encode() is less efficient than abi.encodePacked()                                      |     12      |
| Do not calculate constants                                                                  |     14      |
| Do-While loops are cheaper than for loops                                                   |     19      |
| Use assembly to perform efficient back-to-back calls                                        |     11      |
| Short-circuit booleans                                                                      |     15      |
| Pre-increment and pre-decrement are cheaper than +1 ,-1                                     |      9      |
| Use Clones for Cheap Contract Deployments                                                   |      7      |
| Use assembly to hash instead of solidity                                                    |     14      |
| Use hardcode address instead address(this)                                                  |     10      |
| Use bytes.concat() instead of abi.encodePacked(), since this is preferred since 0.8.4       |      4      |
| Admin functions no uses of the nonReentrant modifier                                        |     11      |
| Use Mappings instead of arrays                                                              |     10      |
| array[index] += amount is cheaper than array[index] = array[index] + amount                 |      1      |
| Expression `` is cheaper than new bytes(0)                                                  |      4      |
| Shorten the array rather than copying to a new one                                          |      3      |

## [G-01] Use ECDSA signatures instead of merkle trees

Merkle trees use a considerable amount of calldata and increase in cost with the size of the merkle proof. Generally, using digital signatures is cheaper gas-wise compared to merkle proofs.

Although the current implementation of the calculateRoot function in the Merkle contract is correct, it can be more gas-efficient by making use of the following optimizations:

### 1. In-place Computation

The `calculateRoot` function can be optimized by using in-place computation to store intermediate hashes at each level of the Merkle tree. This approach eliminates the need to create new arrays, reducing memory usage and gas costs.

Note: this optimization requires the leaves array not to be used again after it is modified. Based on the current implementation, this optimization is safe because the leaves array is not used again after it is modified.

### 2. Assembly

The use of assembly code to load the left and right siblings into memory is more gas-efficient

```solidity
File: contracts/zksync/libraries/Merkle.sol

18    function calculateRoot(
19        bytes32[] calldata _path,
20        uint256 _index,
21        bytes32 _itemHash
22    ) internal pure returns (bytes32) {
23        uint256 pathLength = _path.length;
24        require(pathLength > 0, "xc");
25        require(pathLength < 256, "bt");
26        require(_index < (1 << pathLength), "px");
27
28        bytes32 currentHash = _itemHash;
29        for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
30            currentHash = (_index % 2 == 0)
31                ? _efficientHash(currentHash, _path[i])
32                : _efficientHash(_path[i], currentHash);
33            _index /= 2;
34        }
35
36        return currentHash;
37    }
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L9

## [G-02] Private functions used once can be inlined to save gas

Inlining private functions that are used only once can be an effective optimization technique in smart contract development, especially on L1 .

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

## [G-03] Mappings used within a function more than once should be cached to save gas

Cache such mappings and perform operations on them, if operations include modifications to the mapping(s) then remember to equate the mapping to it's cached counterpart at the end

```solidity
File: contracts/common/AllowList.sol

// @audit getAccessMode[] --> should cached then used more than once in a function
69        AccessMode accessMode = getAccessMode[_target];
72        AccessMode accessMode = getAccessMode[_target];


// @audit hasSpecialAccessToCall[] --> should cached then used more than once in a function
117        bool currentPermission = hasSpecialAccessToCall[_caller][_target][_functionSig];
120            hasSpecialAccessToCall[_caller][_target][_functionSig] = _enable;


// @audit tokenDeposit[] --> should cached then used more than once in a function
130        tokenDeposit[_l1Token].depositLimitation = _depositLimitation;
131        tokenDeposit[_l1Token].depositCap = _depositCap;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L69

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

// @audit totalDepositedAmountPerUser
344        if (_claiming) {
345            totalDepositedAmountPerUser[_l1Token][_depositor] -= _amount;
346        } else {
347            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
348            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L344C1-L348C74

## [G-04] Use assembly to write address/contract type storage values

Using assembly { sstore(state.slot, addr) instead of state = addr can save gas.

```solidity
File: contracts/governance/Governance.sol

26    address public securityCouncil;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L26-L26

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

46    address public l2Bridge;

49    address public l2TokenBeacon;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L46

```solidity
File: contracts/bridge/L1WethBridge.sol

53    address public l2Bridge;

56    address public l2WethAddress;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L53

```solidity
File: contracts/zksync/ValidatorTimelock.sol

33    address public immutable zkSyncContract;

39    address public validator;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L33

## [G-05] Use assembly to validate msg.sender

Here's an example of how you can use assembly to perform this validation:

Let's assume you have a simple contract that only allows a specific address to call a function:

```solidity
pragma solidity ^0.8.0;

contract AuthorizedCaller {
    address public authorizedAddress;

    constructor(address _authorizedAddress) {
        authorizedAddress = _authorizedAddress;
    }

    function doSomething() public {
        require(msg.sender == authorizedAddress, "Not authorized");
        // Your function logic here
    }
}

```

In this contract, the doSomething function checks if msg.sender is equal to authorizedAddress using the require statement. However, you can save gas by using assembly to perform this check. Here's how you can do it:

```solidity
pragma solidity ^0.8.0;

contract AuthorizedCaller {
    address public authorizedAddress;

    constructor(address _authorizedAddress) {
        authorizedAddress = _authorizedAddress;
    }

    function doSomething() public {
        address sender;
        assembly {
            sender := sload(msg.sender.slot)
        }
        require(sender == authorizedAddress, "Not authorized");
        // Your function logic here
    }
}

```

In this updated version of the contract, we use assembly to load the msg.sender value directly, which is more gas-efficient than using the msg.sender global variable. By loading the msg.sender value into a local variable, we save some gas compared to the standard msg.sender usage.

```solidity
File: contracts/zksync/facets/Admin.sol

31        require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights

55        require(msg.sender == pendingAdmin, "n4"); // Only proposed by current admin address can claim the admin rights
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L31

```solidity
File: contracts/common/AllowListed.sol

13            require(_allowList.canCall(msg.sender, address(this), msg.sig), "nr");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L13

```solidity
File: contracts/zksync/facets/Base.sol

19        require(msg.sender == s.governor, "1g"); // only by governor

25        require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");

31        require(s.validators[msg.sender], "1h"); // validator is not active
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L19

```solidity
File: contracts/governance/Governance.sol

59        require(msg.sender == address(this), "Only governance contract itself allowed to call this function");

65        require(msg.sender == securityCouncil, "Only security council allowed to call this function");

72            msg.sender == owner() || msg.sender == securityCouncil,
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L59

```solidity
File: contracts/bridge/L1WethBridge.sol

311        require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L311

```solidity
File: contracts/zksync/ValidatorTimelock.sol

66        require(msg.sender == validator, "8h");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L66

## [G-06] Invert if-else statements that have a negation

This is the same example we gave at the beginning of the article. In the code snippet below, the second function avoids an unnecessary negation. In theory, the extra ! increases the computational cost. But as we noted at the top of the article, you should benchmark both methods because the compiler is can sometimes optimize this.

```solidity
function cond() public {
    if (!condition) {
        action1();
    }
    else {
        action2();
    }
}

function cond() public {
    if (condition) {
        action2();
    }
    else {
        action1();
    }
}
```

```solidity
File: contracts/zksync/libraries/Diamond.sol

289            if (!success) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L289

```solidity
File: contracts/governance/Governance.sol

227            if (!success) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L227

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

342        if (!limitData.depositLimitation) return; // no deposit limitation is placed for this token
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L342

```solidity
File: contracts/zksync/facets/Mailbox.sol

277        if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L277

## [G-07] Assigning state variables directly with named struct constructors wastes gas

Using named arguments for struct means that the compiler needs to organize the fields in memory before doing the assignment, which wastes gas. Set each field directly in storage (use dot-notation), or use the unnamed version of the constructor.

```solidity
File: contracts/zksync/libraries/Diamond.sol

223        ds.selectorToFacet[_selector] = SelectorToFacet({
224            facetAddress: _facet,
225            selectorPosition: selectorPosition,
226            isFreezable: _isSelectorFreezable
227        });
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L223-L227

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

302        L2Message memory l2ToL1Message = L2Message({
303            txNumberInBatch: _l2TxNumberInBatch,
304            sender: l2Bridge,
305            data: _message
306        });
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L302C1-L306C12

```solidity
File: contracts/bridge/L1WethBridge.sol

248            L2Message memory l2ToL1Message = L2Message({
249                txNumberInBatch: _l2TxNumberInBatch,
250                sender: L2_ETH_TOKEN_SYSTEM_CONTRACT_ADDR,
251                data: _message
252            });
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L248-L252

```solidity
File: contracts/zksync/facets/Mailbox.sol

100        L2Log memory l2Log = L2Log({
101            l2ShardId: 0,
102            isService: true,
103            txNumberInBatch: _l2TxNumberInBatch,
104            sender: L2_BOOTLOADER_ADDRESS,
105            key: _l2TxHash,
106            value: bytes32(uint256(_status))
107        });


151            L2Log({
152                l2ShardId: 0,
153                isService: true,
154                txNumberInBatch: _message.txNumberInBatch,
155                sender: L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR,
156                key: bytes32(uint256(uint160(_message.sender))),
157                value: keccak256(_message.data)
158           });



201        L2Message memory l2ToL1Message = L2Message({
202            txNumberInBatch: _l2TxNumberInBatch,
203            sender: L2_ETH_TOKEN_SYSTEM_CONTRACT_ADDR,
204            data: _message
205        });


370            PriorityOperation({
371                canonicalTxHash: canonicalTxHash,
372                expirationTimestamp: _priorityOpParams.expirationTimestamp,
373                layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
374            })
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L100C1-L107C12

## [G-08] Using constants directly, rather than caching the value, saves gas

using constants directly rather than caching their values can be a more efficient and cost-effective approach in terms of gas consumption.When you cache a value in a state variable, it consumes storage space on the Ethereum blockchain. Storing data in smart contract storage is expensive in terms of gas. If a value is known to be a constant and won't change, there's no need to consume storage for it.

```solidity
File: contracts/zksync/libraries/Diamond.sol

87        bytes32 position = DIAMOND_STORAGE_POSITION;
88        assembly {
89            diamondStorage.slot := position
90        }
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L87C1-L90C10

## [G-09] Use selfdestruct in the constructor if the contract is one-time use

Sometimes, contracts are used to deploy several contracts in one transaction, which necessitates doing it in the constructor.

If the contract’s only use is the code in the constructor, then selfdestructing at the end of the operation will save gas.

Although selfdestruct is set for removal in an upcoming hardfork, it will still be supported in the constructor per EIP 6780

```solidity
File: contracts/common/AllowList.sol

31    constructor(address _initialOwner) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L31

```solidity
File: contracts/zksync/DiamondProxy.sol

11    constructor(uint256 _chainId, Diamond.DiamondCutData memory _diamondCut) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L11

```solidity
File: contracts/governance/Governance.sol

41    constructor(address _admin, address _securityCouncil, uint256 _minDelay) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L41

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

65    constructor(IZkSync _zkSync, IAllowList _allowList) reentrancyGuardInitializer {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L65

```solidity
File: contracts/bridge/L1WethBridge.sol

64    constructor(address payable _l1WethAddress, IZkSync _zkSync, IAllowList _allowList) reentrancyGuardInitializer {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L64

## [G-10] Understand the trade-offs when choosing between internal functions and modifiers

Modifiers inject its implementation bytecode where it is used while internal functions jump to the location in the runtime code where the its implementation is. This brings certain trade-offs to both options.

Using modifiers more than once means repetitiveness and increase in size of the runtime code but reduces gas cost because of the absence of jumping to the internal function execution offset and jumping back to continue. This means that if runtime gas cost matter most to you, then modifiers should be your choice but if deployment gas cost and/or reducing the size of the creation code is most important to you then using internal functions will be best.

However, modifiers have the tradeoff that they can only be executed at the start or end of a functon. This means executing it at the middle of a function wouldn’t be directly possible, at least not without internal functions which kill the original purpose. This affects it’s flexibility. Internal functions however can be called at any point in a function.

Example showing difference in gas cost using modifiers and an internal function

Example showing difference in gas cost using modifiers and an internal function

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

//  deployment gas cost: 195435
//  gas per call:
//  restrictedAction1: 28367
//  restrictedAction2: 28377
//  restrictedAction3: 28411

contract Modifier {
address owner;
uint256 val;

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner);
        _;
    }

    function restrictedAction1() external onlyOwner {
        val = 1;
    }

    function restrictedAction2() external onlyOwner {
        val = 2;
    }

    function restrictedAction3() external onlyOwner {
        val = 3;
    }

}

//  deployment gas cost: 159309
//  gas per call:
//  restrictedAction1: 28391
//  restrictedAction2: 28401
//  restrictedAction3: 28435

contract InternalFunction {
address owner;
uint256 val;

    constructor() {
        owner = msg.sender;
    }

    function onlyOwner() internal view {
        require(msg.sender == owner);
    }

    function restrictedAction1() external {
        onlyOwner();
        val = 1;
    }

    function restrictedAction2() external {
        onlyOwner();
        val = 2;
    }

    function restrictedAction3() external {
        onlyOwner();
        val = 3;
    }

}
```

| Operation          | Deployment | restrictedAction1 | restrictedAction2 | restrictedAction3 |
| ------------------ | ---------- | ----------------- | ----------------- | ----------------- |
| Modifiers          | 195,435    | 28,367            | 28,377            | 28,411            |
| Internal Functions | 159,309    | 28,391            | 28,401            | 28,435            |

From the table above, we can see that the contract that uses modifiers cost more than 35k gas more than the contract using internal functions when deploying it due to repetition of the onlyOwner functionality in 3 functions.

During runtime, we can see that each function using modifiers cost a fixed 24 gas less than the functions using internal functions.

```solidity
File: contracts/common/AllowListed.sol

10    modifier senderCanCallFunction(IAllowList _allowList) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L10

```solidity
File: contracts/zksync/facets/Base.sol

18    modifier onlyGovernor() {

24    modifier onlyGovernorOrAdmin() {

30    modifier onlyValidator() {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L18

```solidity
File: contracts/governance/Governance.sol

58    modifier onlySelf() {

64    modifier onlySecurityCouncil() {

70    modifier onlyOwnerOrSecurityCouncil() {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L58

```solidity
File: contracts/common/ReentrancyGuard.sol

41    modifier reentrancyGuardInitializer() {

68    modifier nonReentrant() {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L41

```solidity
File: contracts/zksync/ValidatorTimelock.sol

65    modifier onlyValidator() {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L65

## [G-11] Use fallback or receive instead of deposit() when transferring Ether

Similar to above, you can “just transfer” ether to a contract and have it respond to the transfer instead of using a payable function. This of course, depends on the rest of the contract’s architecture.

Example Deposit in AAVE

```solidity
contract AddLiquidity{

    receive() external payable {
      IWETH(weth).deposit{msg.value}();
      AAVE.deposit(weth, msg.value, msg.sender, REFERRAL_CODE)
    }

}
```

The fallback function is capable of receiving bytes data which can be parsed with abi.decode. This servers as an alternative to supplying arguments to a deposit function.

```solidity
File: contracts/bridge/interfaces/IL1Bridge.sol

21    function deposit(
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L21

```solidity
File: bridge/interfaces/IL1BridgeLegacy.sol

7    function deposit(
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL1BridgeLegacy.sol#L7

```solidity
File:

5    function deposit() external payable;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IWETH9.sol#L5C1-L6C1

```solidity
File: bridge/L1ERC20Bridge.sol

144    function deposit(
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L144

```solidity
File: contracts/bridge/L1WethBridge.sol

159    function deposit(
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L159

## [G-12] Consider using alternatives to OpenZeppelin

OpenZeppelin is a great and popular smart contract library, but there are other alternatives that are worth considering. These alternatives offer better gas efficiency and have been tested and recommended by developers.

Two examples of such alternatives are [Solmate](https://github.com/transmissions11/solmate) and [Solady](https://github.com/Vectorized/solady).

Solmate is a library that provides a number of gas-efficient implementations of common smart contract patterns. Solady is another gas-efficient library that places a strong emphasis on using assembly.

```javascript
File: code/contracts/zksync/package.json

10    "@openzeppelin/contracts": "4.9.2",
```

## [G-13] Using assembly to revert with an error message

When reverting in solidity code, it is common practice to use a require or revert statement to revert execution with an error message. This can in most cases be further optimized by using assembly to revert with the error message.

Here’s an example;

```solidity
/// calling restrictedAction(2) with a non-owner address: 24042
contract SolidityRevert {
    address owner;
    uint256 specialNumber = 1;

    constructor() {
        owner = msg.sender;
    }

    function restrictedAction(uint256 num) external {
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

    function restrictedAction(uint256 num) external {
        assembly {
            if sub(caller(), sload(owner.slot)) {
                mstore(0x00, 0x20) // store offset to where length of revert message is stored
                mstore(0x20, 0x13) // store length (19)
                mstore(
                    0x40,
                    0x63616c6c6572206973206e6f74206f776e657200000000000000000000000000
                ) // store hex representation of message
                revert(0x00, 0x60) // revert with data
            }
        }
        specialNumber = num;
    }
}

```

From the example above we can see that we get a gas saving of over 300 gas when reverting wth the same error message with assembly against doing so in solidity. This gas savings come from the memory expansion costs and extra type checks the solidity compiler does under the hood.

```solidity
File: contracts/zksync/facets/Admin.sol

31        require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights

55        require(msg.sender == pendingAdmin, "n4"); // Only proposed by current admin address can claim the admin rights

84        require(_newPriorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "n5");

112        require(!diamondStorage.isFrozen, "a9"); // diamond proxy is frozen already

123        require(diamondStorage.isFrozen, "a7"); // diamond proxy is not frozen

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L31

```solidity
File: contracts/common/AllowList.sol

60        require(targetsLength == _accessModes.length, "yg"); // The size of arrays should be equal

92        require(callersLength == _targets.length, "yw");

93        require(callersLength == _functionSigs.length, "yx");

94        require(callersLength == _enables.length, "yy");

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L60

```solidity
File: contracts/common/AllowListed.sol

13            require(_allowList.canCall(msg.sender, address(this), msg.sig), "nr");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L13

```solidity
File: contracts/zksync/facets/Base.sol

19        require(msg.sender == s.governor, "1g"); // only by governor

25        require(msg.sender == s.governor || msg.sender == s.admin, "Only by governor or admin");

31        require(s.validators[msg.sender], "1h"); // validator is not active

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L19

```solidity
File: contracts/upgrades/BaseZkSyncUpgrade.sol

72        require(block.timestamp >= _proposedUpgrade.upgradeTimestamp, "Upgrade is not ready yet");

171        require(_l2ProtocolUpgradeTx.txType == SYSTEM_UPGRADE_L2_TX_TYPE, "L2 system upgrade tx type is wrong");

185        require(

201        require(_factoryDeps.length == _expectedHashes.length, "Wrong number of factory deps");

202        require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32");

205            require(

216        require(

222        require(s.l2SystemContractsUpgradeTxHash == bytes32(0), "Previous upgrade has not been finalized");

223        require(
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L72

```solidity
File: contracts/zksync/libraries/Diamond.sol

106            require(selectors.length > 0, "B"); // no functions for diamond cut

132        require(_facet != address(0), "G"); // facet with zero address cannot be added

141            require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists

156        require(_facet != address(0), "K"); // cannot replace facet with zero address

162            require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address

176        require(_facet == address(0), "a1"); // facet address must be zero

182            require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet

220            require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");

285            require(_calldata.length == 0, "H"); // Non-empty calldata for zero address

302            require(data.length == 32, "lp");

303            require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L106

```solidity
File: contracts/zksync/DiamondInit.sol

56        require(address(_initalizeData.verifier) != address(0), "vt");

57        require(_initalizeData.governor != address(0), "vy");

58        require(_initalizeData.admin != address(0), "hc");

59       require(_initalizeData.priorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "vu");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L56

```solidity
File: contracts/zksync/DiamondProxy.sol

14        require(_chainId == block.chainid, "pr");

25        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");

30        require(facetAddress != address(0), "F"); // Proxy has no facet for this selector

31        require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L14

```solidity
File: contracts/zksync/facets/Executor.sol

32        require(_newBatch.batchNumber == _previousBatch.batchNumber + 1, "f"); // only commit next batch

45        require(_previousBatch.batchHash == previousBatchHash, "l");

47        require(expectedPriorityOperationsHash == _newBatch.priorityOperationsHash, "t");

49        require(expectedNumberOfLayer1Txs == _newBatch.numberOfLayer1Txs, "ta");

81        require(batchTimestamp == _expectedBatchTimestamp, "tb");

85        require(_previousBatchTimestamp < batchTimestamp, "h3");

93        require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small

94        require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big

130            require(!_checkBit(processedLogs, uint8(logKey)), "kp");

135                require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lm");

138                require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "ln");

139                require(providedL2ToL1PubdataHash == logValue, "wp");

141                require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lb");

144                require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sc");

147                require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sv");

150                require(logSender == L2_BOOTLOADER_ADDRESS, "bl");

153                require(logSender == L2_BOOTLOADER_ADDRESS, "bk");

156                require(logSender == L2_BOOTLOADER_ADDRESS, "bu");

157                require(_expectedSystemContractUpgradeTxHash == logValue, "ut");

167            require(processedLogs == 127, "b7");

169            require(processedLogs == 255, "b8");

184        require(s.storedBatchHashes[s.totalBatchesCommitted] == _hashStoredBatchInfo(_lastCommittedBatchData), "i"); // incorrect previous batch data

185        require(_newBatchesData.length > 0, "No batches to commit");


236        require(s.l2SystemContractsUpgradeBatchNumber == 0, "ik");

275        require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k"); // Execute batches in order

276        require(

282        require(priorityOperationsHash == _storedBatch.priorityOperationsHash, "x"); // priority operations hash does not match to expected

300        require(newTotalBatchesExecuted <= s.totalBatchesVerified, "n"); // Can't execute batches more than committed and proven currently.

327        require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");

332            require(

346        require(currentTotalBatchesVerified <= s.totalBatchesCommitted, "q");

360            require(successVerifyProof, "p"); // Proof verification fail

368        require(successVerifyProof, "p"); // Proof verification fail

399        require(s.totalBatchesCommitted > _newLastBatch, "v1"); // The last committed batch is less than new last batch

400        require(_newLastBatch >= s.totalBatchesExecuted, "v2"); // Already executed batches cannot be reverted

453        require(_batch.systemLogs.length <= MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES, "pu");

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L32

```solidity
File: contracts/zksync/facets/Getters.sol

160        require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L160

```solidity
File: contracts/governance/Governance.sol

42        require(_admin != address(0), "Admin should be non zero address");

59        require(msg.sender == address(this), "Only governance contract itself allowed to call this function");

65        require(msg.sender == securityCouncil, "Only security council allowed to call this function");

71        require(

155        require(isOperationPending(_id), "Operation must be pending");

172        require(isOperationReady(id), "Operation must be ready before execution");

177        require(isOperationReady(id), "Operation must be ready after execution");

191        require(isOperationPending(id), "Operation must be pending before execution");

196        require(isOperationPending(id), "Operation must be pending after execution");

216        require(!isOperation(_id), "Operation with this proposal id already exists");

217        require(_delay >= minDelay, "Proposed delay is less than minimum delay");

240        require(_predecessorId == bytes32(0) || isOperationDone(_predecessorId), "Predecessor operation not completed");

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L42

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

90        require(_l2TokenBeacon != address(0), "nf");

91        require(_governor != address(0), "nh");

93        require(_factoryDeps.length == 3, "mk");

95        require(msg.value == _deployBridgeImplementationFee + _deployBridgeProxyFee, "fee");

184        require(_amount != 0, "2T"); // empty deposit amount

186        require(amount == _amount, "1T"); // The token has non-standard transfer logic

272        require(proofValid, "yn");

275        require(amount > 0, "y1");

300        require(!isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "pw");

312            require(success, "nq");

329        require(_l2ToL1message.length == 76, "kk");

332        require(bytes4(functionSignature) == this.finalizeWithdrawal.selector, "nt");

347            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L90

```solidity
File: contracts/bridge/L1WethBridge.sol

88        require(_l2WethAddress != address(0), "L2 WETH address cannot be zero");

89        require(_governor != address(0), "Governor address cannot be zero");

90        require(_factoryDeps.length == 2, "Invalid factory deps length provided");

91        require(

167        require(_l1Token == l1WethAddress, "Invalid L1 token address");

168        require(_amount != 0, "Amount cannot be zero");

240        require(!isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "Withdrawal is already finalized");

254            require(success, "vq");

279        require(_message.length == 96, "Incorrect ETH message with additional data length");

282        require(

289        require(l1EthReceiver == address(this), "Wrong L1 ETH withdraw receiver");

295        require(l2Sender == l2Bridge, "The withdrawal was not initiated by L2 bridge");

311        require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L88

```solidity
File: contracts/zksync/facets/Mailbox.sol

119        require(callSuccess, "pz");

129        require(_batchNumber <= s.totalBatchesExecuted, "xx");

136        require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");

199        require(!s.isEthWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "jj");

210        require(proofValid, "pi"); // Failed to verify that withdrawal was actually initialized on L2

257        require(_l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp");

279        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");

294        require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "uj");

306            require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost

421        require(_message.length >= 56, "pm");

424        require(bytes4(functionSignature) == this.finalizeEthWithdrawal.selector, "is");

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L119

```solidity
File: contracts/zksync/libraries/Merkle.sol

24        require(pathLength > 0, "xc");

25        require(pathLength < 256, "bt");

26        require(_index < (1 << pathLength), "px");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L24

```solidity
File: contracts/zksync/libraries/PriorityQueue.sol

65        require(!_queue.isEmpty(), "D"); // priority queue is empty

73        require(!_queue.isEmpty(), "s"); // priority queue is empty

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L65

```solidity
File: contracts/common/ReentrancyGuard.sol

58        require(lockSlotOldValue == 0, "1B");

75        require(_status == _NOT_ENTERED, "r1");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L58

```solidity
File: contracts/zksync/libraries/TransactionValidator.sol

30        require(l2GasForTxBody <= _priorityTxMaxGasLimit, "ui");

32        require(l2GasForTxBody / _transaction.gasPerPubdataByteLimit <= PRIORITY_TX_MAX_PUBDATA, "uk");

36        require(

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

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L30

```solidity
File: contracts/zksync/ValidatorTimelock.sol

66        require(msg.sender == validator, "8h");

123                require(block.timestamp >= commitBatchTimestamp + delay, "5c"); // The delay is not passed
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L66

## [G-14] Test if a number is even or odd by checking the last bit instead of using a modulo operator

The conventional way to check if a number is even or odd is to do x % 2 == 0 where x is the number in question. You can instead check if x & uint256(1) == 0. where x is assumed to be a uint256. Bitwise and is cheaper than the modulo op code. In binary, the rightmost bit represents "1" whereas all the bits to the are multiples of 2, which are even. Adding "1" to an even number causes it to be odd.

```solidity
file:  contracts/zksync/libraries/Merkle.sol

30            currentHash = (_index % 2 == 0)
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L30

## [G-15] Don’t make variables public unless it is necessary to do so

A public storage variable has an implicit public function of the same name. A public function increases the size of the jump table and adds bytecode to read the variable in question. That makes the contract larger.

Remember, private variables aren’t private, it’s not difficult to extract the variable value using web3.js.

This is especially true for constants which are meant to be read by humans rather than smart contracts.

```solidity
File: contracts/zksync/facets/Admin.sol

15    string public constant override getName = "AdminFacet";

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15

```solidity
File: contracts/common/AllowList.sol

22    mapping(address => AccessMode) public getAccessMode;

26    mapping(address => mapping(address => mapping(bytes4 => bool))) public hasSpecialAccessToCall;

29    mapping(address => Deposit) public tokenDeposit;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L22

```solidity
File: contracts/zksync/facets/Executor.sol

22    string public constant override getName = "ExecutorFacet";
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22

```solidity
File: contracts/zksync/facets/Getters.sol

19    string public constant override getName = "GettersFacet";
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19

```solidity
File: contracts/governance/Governance.sol


26    address public securityCouncil;

32    mapping(bytes32 => uint256) public timestamps;

35    uint256 public minDelay;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L26

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

39    mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;

46    address public l2Bridge;

49    address public l2TokenBeacon;

52    bytes32 public l2TokenProxyBytecodeHash;

54    mapping(address => uint256) public __DEPRECATED_lastWithdrawalLimitReset;

57    mapping(address => uint256) public __DEPRECATED_withdrawnAmountInWindow;

61    mapping(address => mapping(address => uint256)) public totalDepositedAmountPerUser;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L39

```solidity
File: contracts/bridge/L1WethBridge.sol


44    address payable public immutable l1WethAddress;

47    IAllowList public immutable allowList;

50    IZkSync public immutable zkSync;

53    address public l2Bridge;

56    address public l2WethAddress;

60    mapping(uint256 => mapping(uint256 => bool)) public isWithdrawalFinalized;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L44

```solidity
File: contracts/zksync/facets/Mailbox.sol

41    string public constant override getName = "MailboxFacet";

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41

```solidity
File: contracts/zksync/ValidatorTimelock.sol

24    string public constant override getName = "ValidatorTimelock";

33    address public immutable zkSyncContract;

39    address public validator;

42    uint32 public executionDelay;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24

## [G-16] It is cheaper to use vanity addresses with leading zeros, this saves calldata gas cost.

A good example is OpenSea Seaport contract with this address: 0x00000000000000ADc04C56Bf30aC9d3c0aAF14dC.

This will not save gas when calling the address directly. However, if that contract’s address is used as an argument to a function, that function call will cost less gas due to having more zeros in the calldata.

This is also true of passing EOAs with a lot of zeros as a function argument – it saves gas for the same reason.

Just be aware that there have been hacks from generating vanity addresses for wallets with insufficiently random private keys. This is not a concert for smart contracts vanity addresses created with finding a salt for create2, because smart contracts do not have private keys.

```solidity
File: contracts/vendor/AddressAliasHelper.sol

22    uint160 constant offset = uint160(0x1111000000000000000000000000000000001111);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L22

## [G-17] Using private rather than public for constants, saves Gas

```solidity
File: zksync/facets/Executor.sol

22    string public constant override getName = "ExecutorFacet";

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22

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

## [G-18] Using fixex bytes is cheaper than using string

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

## [G-19] Always use Named Returns

The solidity compiler outputs more efficient code when the variable is declared in the return statement. There seem to be very few exceptions to this in practice, so if you see an anonymous return, you should test it with a named return instead to determine which case is most efficient.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.20;

contract NamedReturn {
    function myFunc1(uint256 x, uint256 y) external pure returns (uint256) {
        require(x > 0);
        require(y > 0);

        return x * y;
    }
}

contract NamedReturn2 {
    function myFunc2(uint256 x, uint256 y) external pure returns (uint256 z) {
        require(x > 0);
        require(y > 0);

        z = x * y;
    }
}
```

```solidity
File: contracts/upgrades/BaseZkSyncUpgrade.sol

168            return bytes32(0);

194        return l2ProtocolUpgradeTxHash;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L168

```solidity
File: contracts/upgrades/DefaultUpgrade.sol

45        return Diamond.DIAMOND_INIT_SUCCESS_RETURN_VALUE;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L45

```solidity
File: contracts/common/libraries/L2ContractHelper.sol

71        return address(uint160(uint256(data)));
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L71

```solidity
File: contracts/common/libraries/UncheckedMath.sol

13            return _number + 1;

19            return _lhs + _rhs;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UncheckedMath.sol#L13

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

## [G-20] Can make the variable outside the loop to save gas

```solidity
File: contracts/zksync/facets/Executor.sol

212            s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData);

250            s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData);

264            PriorityOperation memory priorityOp = s.priorityQueue.popFront();

333                _hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L212

```solidity
File: contracts/zksync/ValidatorTimelock.sol

86                committedBatchTimestamp.set(_newBatchesData[i].batchNumber, timestamp);

117                uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L86C1-L87C1

## [G-21] Emitting memory values instead of the storage one.

state variables should not used in emit

State variables should not be used directly within emit statements in Solidity when emitting events. Using state variables directly in emit statements can lead to unexpected behavior and gas inefficiencies.

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

```solidity
File: contracts/bridge/L1WethBridge.sol

267        emit WithdrawalFinalized(l1WethWithdrawReceiver, l1WethAddress, amount);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L267

## [G-22] It is sometimes cheaper to cache calldata

Although the calldataload instruction is a cheap opcode, the solidity compiler will sometimes output cheaper code if you cache calldataload. This will not always be the case, so you should test both possibilities.

```solidity
contract LoopSum {
    function sumArr(uint256[] calldata arr) public pure returns (uint256 sum) {
        uint256 len = arr.length;
        for (uint256 i = 0; i < len; ) {
            sum += arr[i];
            unchecked {
                ++i;
            }
        }
    }
}
```

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

```solidity
File: contracts/zksync/facets/Mailbox.sol

53        bytes32[] calldata _proof

68        bytes32[] calldata _proof

87        bytes32[] calldata _merkleProof,

127        bytes32[] calldata _proof

197        bytes32[] calldata _merkleProof

242        bytes[] calldata _factoryDeps,

290        bytes[] calldata _factoryDeps,

332        bytes[] calldata _factoryDeps

359        bytes[] calldata _factoryDeps
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L53

```solidity
File: contracts/zksync/ValidatorTimelock.sol

79        CommitBatchInfo[] calldata _newBatchesData

105        StoredBatchInfo[] calldata,

113    function executeBatches(StoredBatchInfo[] calldata _newBatchesData) external onlyValidator {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L79

## [G-23] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.
If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation.

If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be
nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when
the code is later modified (if( x ) {}else if(y){ . . . }else{ . . . } => if ( !x) {if(y) { . . . }else{ . . .}}) .
Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

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

## [G-24] Use storage pointers instead of memory where appropriate

In Solidity, storage pointers are variables that reference a location in storage of a contract. It is helpful to know how to use storage pointers efficiently to avoid unnecessary storage reads and perform gas-efficient storage updates.

Here’s an example showing where storage pointers can be helpful.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.20;

contract StoragePointerUnOptimized {
    struct User {
        uint256 id;
        string name;
        uint256 lastSeen;
    }

    constructor() {
        users[0] = User(0, "John Doe", block.timestamp);
    }

    mapping(uint256 => User) public users;

    function returnLastSeenSecondsAgo(
        uint256 _id
    ) public view returns (uint256) {
        User memory _user = users[_id];
        uint256 lastSeen = block.timestamp - _user.lastSeen;
        return lastSeen;
    }
}
```

Above, we have a function that returns the last seen of a user at a given index. It gets the lastSeen value and subtracts that from the current block.timestamp. Then we copy the whole struct into memory and get the lastSeen which we use in calculating the last seen in seconds ago. This method works well but is not so efficient, this is because we are copying all of the struct from storage into memory including variables we don’t need. Only if there was a way to only read from the lastSeen storage slot (without assembly). That’s where storage pointers come in.

`This results in approximately 5,000 gas savings compared to the previous version.`

```solidity
contract StoragePointerOptimized {
    struct User {
        uint256 id;
        string name;
        uint256 lastSeen;
    }

    constructor() {
        users[0] = User(0, "John Doe", block.timestamp);
    }

    mapping(uint256 => User) public users;

    function returnLastSeenSecondsAgoOptimized(
        uint256 _id
    ) public view returns (uint256) {
        User storage _user = users[_id];
        uint256 lastSeen = block.timestamp - _user.lastSeen;
        return lastSeen;
    }
}

```

“The above implementation results in approximately 5,000 gas savings compared to the first version”. Why so, the only change here was changing memory to storage and we were told that anything storage is expensive and should be avoided?

Here we store the storage pointer for users[_id] in a fixed sized variable on the stack (the pointer of a struct is basically the storage slot of the start of the struct, in this case, this will be the storage slot of user[_id].id). Since storage pointers are lazy (meaning they only act(read or write) when called or referenced). Next we only access the lastSeen key of the struct. This way we make a single storage load then store it on the stack, instead of 3 or possibly more storage loads and a memory store before taking a small chunk from memory unto the stack.

Note: When using storage pointers, it’s important to be careful not to reference dangling pointers.

```solidity
File: contracts/upgrades/BaseZkSyncUpgrade.sol

136        VerifierParams memory oldVerifierParams = s.verifierParams;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L136

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

## [G-25] Use constants instead of type(uintx).max

type(uint16).max or type(uint160).max, etc. it uses more gas in the distribution process and also for each transaction than constant usage.

```solidity
File: contracts/zksync/libraries/TransactionValidator.sol

50        require(_transaction.from <= type(uint16).max, "ua");

51        require(_transaction.to <= type(uint160).max, "ub");

55        require(_transaction.reserved[1] <= type(uint160).max, "uf");

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L50

## [G-26] abi.encode() is less efficient than abi.encodePacked()

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

## [G-27] Do not calculate constants

```solidity
File: contracts/vendor/AddressAliasHelper.sol

22    uint160 constant offset = uint160(0x1111000000000000000000000000000000001111);

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L22

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
File: contracts/L2ContractHelper.sol

74  address constant BOOTLOADER_ADDRESS = address(SYSTEM_CONTRACTS_OFFSET + 0x01);

75  address constant MSG_VALUE_SYSTEM_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x09);

76  address constant DEPLOYER_SYSTEM_CONTRACT = address(SYSTEM_CONTRACTS_OFFSET + 0x06);

78  IL2Messenger constant L2_MESSENGER = IL2Messenger(address(SYSTEM_CONTRACTS_OFFSET + 0x08));

80  IEthToken constant L2_ETH_ADDRESS = IEthToken(address(SYSTEM_CONTRACTS_OFFSET + 0x0a));

89    bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/L2ContractHelper.sol#L74

## [G-28] Do-While loops are cheaper than for loops

If you want to push optimization at the expense of creating slightly unconventional code, Solidity do-while loops are more gas efficient than for loops, even if you add an if-condition check for the case where the loop doesn’t execute at all.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.20;

// times == 10 in both tests
contract Loop1 {
    function loop(uint256 times) public pure {
        for (uint256 i; i < times; ) {
            unchecked {
                ++i;
            }
        }
    }
}

contract Loop2 {
    function loop(uint256 times) public pure {
        if (times == 0) {
            return;
        }

        uint256 i;

        do {
            unchecked {
                ++i;
            }
        } while (i < times);
    }
}
```

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

## [G-29] Use assembly to perform efficient back-to-back calls

Use assembly to reuse memory space when making more than one external call.
An operation that causes the solidity compiler to expand memory is making external calls. When making external calls the compiler has to encode the function signature of the function it wishes to call on the external contract alongside it’s arguments in memory. As we know, solidity does not clear or reuse memory memory so it’ll have to store these data in the next free memory pointer which expands memory further.

With inline assembly, we can either use the scratch space and free memory pointer offset to store this data (as above) if the function arguments do not take up more than 96 bytes in memory. Better still, if we are making more than one external call we can reuse the same memory space as the first calls to store the new arguments in memory without expanding memory unnecessarily. Solidity in this scenario would expand memory by as much as the returned data length is. This is because the returned data is stored in memory (in most cases). If the return data is less than 96 bytes, we can use the scratch space to store it to prevent expanding memory.

See the example below;

```solidity

contract Called {
    function add(uint256 a, uint256 b) external pure returns (uint256) {
        return a + b;
    }
}

contract Solidity {
    // cost: 7262
    function call(address calledAddress) external pure returns (uint256) {
        Called called = Called(calledAddress);
        uint256 res1 = called.add(1, 2);
        uint256 res2 = called.add(3, 4);

        uint256 res = res1 + res2;
        return res;
    }
}

contract Assembly {
    // cost: 5281
    function call(address calledAddress) external view returns (uint256) {
        assembly {
            // check that calledAddress has code deployed to it
            if iszero(extcodesize(calledAddress)) {
                revert(0x00, 0x00)
            }

            // first call
            mstore(0x00, hex"771602f7")
            mstore(0x04, 0x01)
            mstore(0x24, 0x02)
            let success := staticcall(
                gas(),
                calledAddress,
                0x00,
                0x44,
                0x60,
                0x20
            )
            if iszero(success) {
                revert(0x00, 0x00)
            }
            let res1 := mload(0x60)

            // second call
            mstore(0x04, 0x03)
            mstore(0x24, 0x4)
            success := staticcall(gas(), calledAddress, 0x00, 0x44, 0x60, 0x20)
            if iszero(success) {
                revert(0x00, 0x00)
            }
            let res2 := mload(0x60)

            // add results
            let res := add(res1, res2)

            // return data
            mstore(0x60, res)
            return(0x60, 0x20)
        }
    }
}

```

We save approximately 2,000 gas by using the scratch space to store the function selector and it’s arguments and also reusing the same memory space for the second call while storing the returned data in the zero slot thus not expanding memory.

If the arguments of the external function you wish to call is above 64 bytes and if you are making one external call, it wouldn’t save any significant gas writing it in assembly. However, if making more than one call. You can still save gas by reusing the same memory slot for the 2 calls using inline assembly.

Note: Always remember to update the free memory pointer if the offset it points to is already used, to avoid solidity overriding the data stored there or using the value stored there in an unexpected way.

Also note to avoid overwriting the zero slot (0x60 memory offset) if you have undefined dynamic memory values within that call stack. An alternative is to explicitly define dynamic memory values or if used, to set the slot back to 0x00 before exiting the assembly block.

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

## [G-30] Short-circuit booleans

In Solidity, when you evaluate a boolean expression (e.g the || (logical or) or && (logical and) operators), in the case of || the second expression will only be evaluated if the first expression evaluates to false and in the case of && the second expression will only be evaluated if the first expression evaluates to true. This is called short-circuiting.

For example, the expression require(msg.sender == owner || msg.sender == manager) will pass if the first expression msg.sender == owner evaluates to true. The second expression msg.sender == manager will not be evaluated at all.

However, if the first expression msg.sender == owner evaluates to false, the second expression msg.sender == manager will be evaluated to determine whether the overall expression is true or false. Here, by checking the condition that is most likely to pass firstly, we can avoid checking the second condition thereby saving gas in majority of successful calls.

This is similar for the expression require(msg.sender == owner && msg.sender == manager). If the first expression msg.sender == owner evaluates to false, the second expression msg.sender == manager will not be evaluated because the overall expression cannot be true. For the overall statement to be true, both side of the expression must evaluate to true. Here, by checking the condition that is most likely to fail firstly, we can avoid checking the second condition thereby saving gas in majority of call reverts.

Short-circuiting is useful and it’s recommended to place the less expensive expression first, as the more costly one might be bypassed. If the second expression is more important than the first, it might be worth reversing their order so that the cheaper one gets evaluated first.

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

## [G-31] Pre-increment and pre-decrement are cheaper than +1 ,-1

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

## [G-32] Use Clones for Cheap Contract Deployments

Use clones or metaproxies when deploying very similar smart contracts that are not called frequently
When deploying multiple similar smart contracts, the gas costs can be high. To reduce these costs, you can use minimal clones or metaproxies which store the address of the implementation contract in their bytecode and interact with it as a proxy.

However, there is a trade-off between the runtime cost and deployment cost of clones. Clones are more expensive to interact with than normal contracts due to the delegatecall they use, so they should only be used when you don’t need to interact with them frequently. For example, the Gnosis Safe contract uses clones to reduce deployment costs.

Learn more about how to use clones and metaproxies to reduce the gas costs of deploying smart contracts:

EIP-1167: Minimal Proxy Standard
EIP-3448 Metaproxy Clone

```solidity
File: contracts/bridge/libraries/BridgeInitializationHelper.sol

14  library BridgeInitializationHelper {

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol#L14

```solidity
File:

10  contract DiamondProxy {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L10

```solidity
File: interfaces/IL2ContractDeployer.sol

9   interface IL2ContractDeployer {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IL2ContractDeployer.sol#L9C1-L10C1

```solidity
File: contracts/bridge/L1ERC20Bridge.sol

103        address bridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(

123        l2Bridge = BridgeInitializationHelper.requestDeployTransaction(

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L103

```solidity
File: contracts/bridge/L1WethBridge.sol

102        address wethBridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(

126        l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L102

## [G-33] Use assembly to hash instead of solidity

Use assembly for small keccak256 hashes, in order to save gas

Solidity always writes to memory by expanding it which sometimes is not efficient. We can optimize memory operations on data that are 96 bytes in size or less by utilizing inline-assembly.

Solidity reserves it’s first 64 bytes of memory (mem[0x00:0x40]) as scratch space that devs can use to perform any operation with guarantees that it won’t be overwritten or read from unexpectedly. The next 32 bytes of memory (mem[0x40:0x60]) is where solidity stores, reads and updates the free memory pointer from. This is how solidity keeps track of the next memory offset to write new data to. The next 32 bytes of memory (mem[0x60:0x80]) is called the zero slot. It is where uninitialized dynamic memory data (bytes memory, string memory, T[] memory(where T is any valid type)) points to. Since these values are uninitialized, solidity advances that the slot they point to (0x60) remain 0x00.

Note: Structs stored in memory even when dynamic (i.e have a dynamic value within themselves), when uninitialized do not point to the zero slot.

Note: Uninitialized dynamic memory data still point to the zero slot even if they’re nested within a struct.

If we can utilize the scratch space in performing operations in memory that the compiler would usually expand memory to perform if it did so itself, then we can optimize our code. So we have 64 bytes of cheaper memory to work with now.

The free memory pointer space can also be used as long as we update it before exiting the assembly block too. We can store it on the stack temporarily for this.

Let’s see some examples.

Using assembly to log up to 96 bytes of unindexed data

```solidity
contract ExpensiveLogger {
    event BlockData(uint256 blockTimestamp, uint256 blockNumber, uint256 blockGasLimit);

    // cost: 26145
    function returnBlockData() external {
        emit BlockData(block.timestamp, block.number, block.gaslimit);
    }
}


contract CheapLogger {
    event BlockData(uint256 blockTimestamp, uint256 blockNumber, uint256 blockGasLimit);

    // cost: 22790
    function returnBlockData() external {
        assembly {
            mstore(0x00, timestamp())
            mstore(0x20, number())
            mstore(0x40, gaslimit())

            log1(0x00,
                0x60,
                0x9ae98f1999f57fc58c1850d34a78f15d31bee81788521909bea49d7f53ed270b // event hash of BlockData
                )
        }
    }
}
```

The example above shows how we can save almost 2,000 gas by using memory to store the data we wish to emit in the BlockData event.

There is no need to update our free memory pointer here because execution ends right after we emit our event and we never step back into solidity code.

Let’s take another example where we would need to update the free memory pointer

Using assembly to hash up to 96 bytes of data

```solidity
contract ExpensiveHasher {
    bytes32 public hash;
    struct Values {
        uint256 a;
        uint256 b;
        uint256 c;
    }
    Values values;

    // cost: 113155function setOnchainHash(Values calldata _values) external {
        hash = keccak256(abi.encode(_values));
        values = _values;
    }
}


contract CheapHasher {
    bytes32 public hash;
    struct Values {
        uint256 a;
        uint256 b;
        uint256 c;
    }
    Values values;

    // cost: 112107
    function setOnchainHash(Values calldata _values) external {
        assembly {
            // cache the free memory pointer because we are about to override it
            let fmp := mload(0x40)

            // use 0x00 to 0x60
            calldatacopy(0x00, 0x04, 0x60)
            sstore(hash.slot, keccak256(0x00, 0x60))

            // restore the cache value of free memory pointer
            mstore(0x40, fmp)
        }

        values = _values;
    }
}

```

In the above example, similar to the first one, we use assembly to store values in the first 96 bytes of memory which saves us 1,000+ gas. Also notice that in this instance, because we still break back into solidity code, we cached and updated our free memory pointer at the start and end of our assembly block. This is to make sure that the solidity compiler’s assumptions on what is stored in memory remains compatible.

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

## [G-34] Use hardcode address instead address(this)

It can be more gas-efficient to use a hardcoded address instead of the address(this) expression, especially if you need to use the same address multiple times in your contract.

The reason for this is that using address(this) requires an additional EXTCODESIZE operation to retrieve the contract's address from its bytecode, which can increase the gas cost of your contract. By pre-calculating and using a hardcoded address, you can avoid this additional operation and reduce the overall gas cost of your contract.

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

## [G-35] Use bytes.concat() instead of abi.encodePacked(), since this is preferred since 0.8.4

Starting from Solidity version 0.8.4, the bytes.concat() function has been introduced, which provides a preferred and more explicit way to concatenate byte arrays compared to abi.encodePacked(). bytes.concat() is designed to make byte array concatenation in smart contracts more readable and easier to use.

```solidity
File: contracts/zksync/facets/Executor.sol

384                    abi.encodePacked(

436            abi.encodePacked(

445        return abi.encodePacked(s.zkPorterIsAvailable, s.l2BootloaderBytecodeHash, s.l2DefaultAccountBytecodeHash);

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L384

```solidity
File: contracts/zksync/facets/Mailbox.sol

132            abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBatch, _log.sender, _log.key, _log.value)

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L132

## [G-36] Admin functions no uses of the nonReentrant modifier

Admin functions are trusted there is no use of nonReentrant modifier to save significant amount of Gas.

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

```solidity
File: contracts/bridge/L1WethBridge.sol

166    ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {

239    ) external nonReentrant senderCanCallFunction(allowList) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L166

## [G-37] Use Mappings instead of arrays

Using mappings instead of arrays to avoid length checks
When storing a list or group of items that you wish to organize in a specific order and fetch with a fixed key/index, it’s common practice to use an array data structure. This works well, but did you know that a trick can be implemented to save 2,000+ gas on each read using a mapping?

See the example below

```solidity
/// get(0) gas cost: 4860
contract Array {
    uint256[] a;

    constructor() {
        a.push() = 1;
        a.push() = 2;
        a.push() = 3;
    }

    function get(uint256 index) external view returns (uint256) {
        return a[index];
    }
}

/// get(0) gas cost: 2758
contract Mapping {
    mapping(uint256 => uint256) a;

    constructor() {
        a[0] = 1;
        a[1] = 2;
        a[2] = 3;
    }

    function get(uint256 index) external view returns (uint256) {
        return a[index];
    }
}
```

Just by using a mapping, we get a gas saving of 2102 gas. Why? Under the hood when you read the value of an index of an array, solidity adds bytecode that checks that you are reading from a valid index (i.e an index strictly less than the length of the array) else it reverts with a panic error (Panic(0x32) to be precise). This prevents from reading unallocated or worse, allocated storage/memory locations.

Due to the way mappings are (simply a key => value pair), no check like that exists and we are able to read from the a storage slot directly. It’s important to note that when using mappings in this manner, your code should ensure that you are not reading an out of bound index of your canonical array.

```solidity
File: contracts/upgrades/BaseZkSyncUpgrade.sol

33        bytes[] factoryDeps;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L33

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

## [G-38] array[index] += amount is cheaper than array[index] = array[index] + amount

```solidity
File:  contracts/governance/Governance.sol

219        timestamps[_id] = block.timestamp + _delay;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L219

## [G-39] Expression `` is cheaper than new bytes(0)

```solidity
File: contracts/bridge/L1WethBridge.sol

207            (_l1Sender, _l2Receiver, _l1Token, _amount, new bytes(0))
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L207

```solidity
File: contracts/zksync/facets/Mailbox.sol

348            signature: new bytes(0),

349            paymasterInput: new bytes(0),

350            reservedDynamic: new bytes(0)
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L348C1-L351C42

## [G-40] Shorten the array rather than copying to a new one

```solidity
File: contracts/zksync/facets/Executor.sol

324        uint256[] memory proofPublicInput = new uint256[](committedBatchesLength);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L324

```solidity
File: contracts/zksync/facets/Getters.sol

180        result = new Facet[](facetsLen);

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L180

```solidity
File: contracts/zksync/facets/Mailbox.sol

394        hashedFactoryDeps = new uint256[](factoryDepsLen);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L394
