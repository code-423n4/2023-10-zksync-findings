### Low Risk Issues
|Title|Issue|Instances|
|-|:-|:-:|
|[L-1] Zero-value transfers may revert | [Zero-value transfers may revert](#zero-value-transfers-may-revert) | 3 |
|[L-2] Array out of bounds accesses | [Array out of bounds accesses](#array-out-of-bounds-accesses) | 1 |
|[L-3] Casting block.timestamp can reduce the lifespan of a contract | [Casting block.timestamp can reduce the lifespan of a contract](#casting-blocktimestamp-can-reduce-the-lifespan-of-a-contract) | 1 |
|[L-4] Empty Uncontrolled Ether Flow in receive()/payable fallback() | [Empty Uncontrolled Ether Flow in receive()/payable fallback()](#empty-uncontrolled-ether-flow-in-receivepayable-fallback) | 4 |
|[L-5] Local variable shadowing | [Local variable shadowing](#local-variable-shadowing) | 2 |
|[L-6] Upgradeable contract uses non-upgradeable version of the OpenZeppelin libraries/contracts | [Upgradeable contract uses non-upgradeable version of the OpenZeppelin libraries/contracts](#upgradeable-contract-uses-non-upgradeable-version-of-the-openzeppelin-librariescontracts) | 2 |
|[L-7] name() is not a part of the ERC-20 standard | [name() is not a part of the ERC-20 standard](#name-is-not-a-part-of-the-erc-20-standard) | 1 |
|[L-8] symbol() is not a part of the ERC-20 standard | [symbol() is not a part of the ERC-20 standard](#symbol-is-not-a-part-of-the-erc-20-standard) | 1 |
|[L-9] Initialize Front-Run | [Initialize Front-Run](#initialize-front-run) | 2 |
|[L-10] use of pragma solidity >= | [use of pragma solidity >=](#use-of-pragma-solidity) | 1 |

Total: 10 issues


### Non-Critical Issues
|Title|Issue|Instances|
|-|:-|:-:|
|[N-1] Control structures do not follow the Solidity Style Guide | [Control structures do not follow the Solidity Style Guide](#control-structures-do-not-follow-the-solidity-style-guide) | 55 |
|[N-2] Function not called | [function not called](#function-not-called) | 26 |
|[N-3] Events should use parameters to convey information | [Events should use parameters to convey information](#events-should-use-parameters-to-convey-information) | 2 |
|[N-4] Lack of Explicit Visibility Declaration in State Variables | [Lack of Explicit Visibility Declaration in State Variables](#lack-of-explicit-visibility-declaration-in-state-variables) | 9 |
|[N-5] Function names should differ | [Function names should differ](#function-names-should-differ) | 5 |
|[N-6] Inconsistent usage of require/error | [Inconsistent usage of require/error](#inconsistent-usage-of-requireerror) | 10 |
|[N-7] Consider Disable Ownership Renouncement in Ownable Contracts | [Consider Disable Ownership Renouncement in Ownable Contracts](#consider-disable-ownership-renouncement-in-ownable-contracts) | 3 |
|[N-8] Event Emission Preceding External Calls: A Best Practice | [Event Emission Preceding External Calls: A Best Practice](#event-emission-preceding-external-calls-a-best-practice) | 15 |
|[N-9] Variable names too similar | [Variable names too similar](#variable-names-too-similar) | 9 |
|[N-10] Functions that alter state should emit events | [Functions that alter state should emit events](#functions-that-alter-state-should-emit-events) | 26 |
|[N-11] Whitespace in Expressions | [Whitespace in Expressions](#whitespace-in-expressions) | 5 |
|[N-12] Do not calculate constants | [Do not calculate constants](#do-not-calculate-constants) | 2 |
|[N-13] Empty function body | [Empty function body](#empty-function-body) | 2 |
|[N-14] Override function arguments that are unused should have the variable name removed or commented out | [Override function arguments that are unused should have the variable name removed or commented out](#override-function-arguments-that-are-unused-should-have-the-variable-name-removed-or-commented-out) | 1 |
|[N-15] Too many digits | [Too many digits](#too-many-digits) | 5 |
|[N-16] Unused File | [Unused File](#unused-file) | 2 |


Total: 16 issues

#

# Low Issues

## Zero-value transfers may revert
- Severity: Low
- Confidence: High

### Description
This detector identifies instances where zero-valued ERC20 token transfers are made. Even though EIP-20 states that zero-valued transfers must be treated as normal transfers and events must be triggered, some tokens may revert if this is attempted, which can cause transactions that involve other tokens (such as batch operations) to fully revert. Therefore, it may be safer and more gas-efficient to skip the transfer if the amount is zero.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
- 
```
File: contracts/bridge/L1ERC20Bridge.sol

220    _token.safeTransferFrom(_from, address(this), _amount)
```
This variable might be zero: `_amount` 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L220](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L220)


- 
```
File: contracts/bridge/L1ERC20Bridge.sol

319    IERC20(l1Token).safeTransfer(l1Receiver, amount)
```
This variable might be zero: `amount` 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L319](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L319)


- 
```
File: contracts/bridge/L1WethBridge.sol

265    IERC20(l1WethAddress).safeTransfer(l1WethWithdrawReceiver, amount)
```
This variable might be zero: `amount` 

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L265](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L265)


</details>

# 



## Array out of bounds accesses
- Severity: Low
- Confidence: Medium

### Description
If the lengths of arrays are not checked before they're accessed, user operations may not be executed properly due to the potential issue of accessing an array out of its bounds. In Solidity, accessing an array beyond its boundaries can cause a runtime error known as an out-of-bounds exception. This error arises when attempting to read or write to an element of an array that does not exist. Therefore, it is critical to avoid out-of-bounds exceptions while accessing arrays in Solidity code to prevent unexpected halts and possible loss of funds.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: contracts/zksync/facets/Executor.sol

229    CommitBatchInfo[] calldata _newBatchesData
```
 is accessed on 
```
File: contracts/zksync/facets/Executor.sol

241    s.l2SystemContractsUpgradeBatchNumber = _newBatchesData[0].batchNumber
```
 index that might be out-of-bounds

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L229](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L229)


</details>

# 



## Casting block.timestamp can reduce the lifespan of a contract
- Severity: Low
- Confidence: High

### Description
Casting `block.timestamp` to smaller integer types can reduce the effective lifespan of a contract. `block.timestamp` returns the current block timestamp as seconds since the unix epoch as `uint`. When it's cast to a smaller integer type, this effectively reduces the maximum timestamp value the contract can handle. As a result, the contract might stop functioning correctly after a certain point in time, even though `block.timestamp` itself will continue to increase with each block.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: contracts/zksync/ValidatorTimelock.sol

86    uint32 timestamp = uint32(block.timestamp)
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L86](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L86)


</details>

# 


## name() is not a part of the ERC-20 standard
- Severity: Low
- Confidence: High

### Description
The name() function is not a part of the ERC-20 standard, and was added later as an optional extension. As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function. 

There are several ways to address this issue:
1. Check the return value: If the function name() doesn't exist in the contract, the call will return false.
2. Use a try-catch statement: This allows for error handling if the name() function does not exist.
3. Use an interface with optional name(): This provides a way to handle tokens that do not have the name() function.
Among these options, checking the return value is recommended for clarity and simplicity.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

128    return super.name()
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L128](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L128)


</details>

# 


## symbol() is not a part of the ERC-20 standard
- Severity: Low
- Confidence: High

### Description
The symbol() function is not a part of the ERC-20 standard, and was added later as an optional extension. As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function. 

There are several ways to address this issue:
1. Check the return value: If the function symbol() doesn't exist in the contract, the call will return false.
2. Use a try-catch statement: This allows for error handling if the symbol() function does not exist.
3. Use an interface with optional symbol(): This provides a way to handle tokens that do not have the symbol() function.
Among these options, checking the return value is recommended for clarity and simplicity.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

134    return super.symbol()
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L134](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L134)


</details>

# 



## Initialize Front-Run
- Severity: Low
- Confidence: High

### Description
Detects when initialize functions can be front-run.

### Note 
repoted only on instances that was missing in the winning bot. 

<details>

<summary>
There are 2 instances of this issue:

</summary>

###



- 
```
File: contracts/bridge/interfaces/IL2ERC20Bridge.sol

9    function initialize(
10            address _l1Bridge,
11            bytes32 _l2TokenProxyBytecodeHash,
12            address _governor
13        ) external;
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2ERC20Bridge.sol#L9-L13](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2ERC20Bridge.sol#L9-L13)


- 
```
File: contracts/bridge/interfaces/IL2WethBridge.sol

8    function initialize(
9            address _l1Bridge,
10            address _l1WethAddress,
11            address _l2WethAddress
12        ) external;
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol#L8-L12](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol#L8-L12)



</details>


# 



## Local variable shadowing
- Severity: Low
- Confidence: High

### Description
Detection of shadowing using local variables.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol

78    address[] memory facets
```
 shadows:
	- 
```
File: code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol

74    function facets() external view returns (Facet[] memory);
```
 (function)

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L78](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IGetters.sol#L78)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

88    uint256 bytecodeLenInWords = _bytecode.length / 32
```
 shadows:
	- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

46    function bytecodeLenInWords(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) 
```
 (function)

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L88](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L88)


</details>

# 


## Upgradeable contract uses non-upgradeable version of the OpenZeppelin libraries/contracts
- Severity: Low
- Confidence: High

### Description
Detects when upgradeable contracts use non-upgradeable OpenZeppelin libraries/contracts.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

7    import "@openzeppelin/contracts/proxy/beacon/BeaconProxy.sol";
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L7](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L7)


- 
```
File: code/system-contracts/contracts/bridge/L2ERC20Bridge.sol

8    import "@openzeppelin/contracts/proxy/beacon/UpgradeableBeacon.sol";
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L8](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L8)


</details>

# 



## Empty Uncontrolled Ether Flow in receive()/payable fallback()
- Severity: Low
- Confidence: Medium

### Description
This detector flags contracts with empty receive()/payable fallback() functions that do not contain an authorization check. This can potentially lead to loss of funds, as anyone can send Ether to the contract without a way of getting it back out.

<details>

<summary>
There are 4 instances of this issue:

</summary>

###
- 
```
File: contracts/governance/Governance.sol

264    receive() external payable 
```
 don't keep `receive` empty.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L264](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L264)


- 
```
File: contracts/DefaultAccount.sol

231    receive() external payable 
```
 don't keep `receive` empty.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L231-L233](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L231-L233)


- 
```
File: contracts/EmptyContract.sol

14    fallback() external payable 
```
 don't keep `fallback` empty.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L14](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L14)


- 
```
File: contracts/EmptyContract.sol

16    receive() external payable 
```
 don't keep `receive` empty.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L16](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L16)


</details>

# 



## Use of pragma solidity >=
- Severity: Low
- Confidence: High

### Description
This detector checks for instances where pragma solidity is used with >= operator without specifying an upper bound. This could lead to incompatibilities with future versions of Solidity, potentially breaking the code. It's recommended to specify both a lower and an upper bound.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

1    pragma solidity >=0.8.0;
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L1](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L1)


</details>

# 


# Non-Critical Issues 

## Events should use parameters to convey information
- Severity: Non-Critical
- Confidence: High

### Description
Events in smart contracts are crucial for logging various state changes and significant actions. Utilizing parameters within events provides detailed context about the event, enhancing transparency and ease of debugging. This detector ensures all events declared in a contract use parameters, thereby ensuring better context provision for events logged.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- 
```
File: contracts/zksync/facets/Admin.sol

117    emit Freeze()
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L117](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L117)


- 
```
File: contracts/zksync/facets/Admin.sol

128    emit Unfreeze()
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L128](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L128)


</details>

# 




## Event Emission Preceding External Calls: A Best Practice
- Severity: Non-Critical
- Confidence: Medium

### Description

Ensure that events follow the best practice of check-effects-interaction, and are emitted before external calls


<details>

<summary>
There are 15 instances of this issue:

</summary>

###
- Reentrancy in 
```
File: contracts/governance/Governance.sol

169    function execute(Operation calldata _operation) external onlyOwnerOrSecurityCouncil 
```
:
	External calls:
	- 
```
File: contracts/governance/Governance.sol

176    _execute(_operation.calls)
```

		- 
```
File: contracts/governance/Governance.sol

228    (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data)
```

	Event emitted after the call(s):
	- 
```
File: contracts/governance/Governance.sol

182    emit OperationExecuted(id)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L169-L183](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L169-L183)


- Reentrancy in 
```
File: contracts/governance/Governance.sol

188    function executeInstant(Operation calldata _operation) external onlySecurityCouncil 
```
:
	External calls:
	- 
```
File: contracts/governance/Governance.sol

195    _execute(_operation.calls)
```

		- 
```
File: contracts/governance/Governance.sol

228    (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data)
```

	Event emitted after the call(s):
	- 
```
File: contracts/governance/Governance.sol

201    emit OperationExecuted(id)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L188-L202](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L188-L202)


- Reentrancy in 
```
File: contracts/zksync/facets/Admin.sol

100    function executeUpgrade(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor 
```
:
	External calls:
	- 
```
File: contracts/zksync/facets/Admin.sol

101    Diamond.diamondCut(_diamondCut)
```

	Event emitted after the call(s):
	- 
```
File: contracts/zksync/facets/Admin.sol

102    emit ExecuteUpgrade(_diamondCut)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L100-L103](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L100-L103)


- Reentrancy in 
```
File: contracts/bridge/L2Weth.sol

80    function bridgeBurn(address _from, uint256 _amount) external override onlyBridge 
```
:
	External calls:
	- 
```
File: contracts/bridge/L2Weth.sol

83    (bool success, ) = msg.sender.call{value: _amount}("")
```

	Event emitted after the call(s):
	- 
```
File: contracts/bridge/L2Weth.sol

86    emit BridgeBurn(_from, _amount)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L80-L87](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L80-L87)


- Reentrancy in 
```
File: contracts/bridge/L2ERC20Bridge.sol

65    function finalizeDeposit(
66            address _l1Sender,
67            address _l2Receiver,
68            address _l1Token,
69            uint256 _amount,
70            bytes calldata _data
71        ) external payable override 
```
:
	External calls:
	- 
```
File: contracts/bridge/L2ERC20Bridge.sol

80    address deployedToken = _deployL2Token(_l1Token, _data)
```

		- 
```
File: contracts/bridge/L2ERC20Bridge.sol

97    L2StandardERC20(address(l2Token)).bridgeInitialize(_l1Token, _data)
```

	- 
```
File: contracts/bridge/L2ERC20Bridge.sol

87    IL2StandardToken(expectedL2Token).bridgeMint(_l2Receiver, _amount)
```

	Event emitted after the call(s):
	- 
```
File: contracts/bridge/L2ERC20Bridge.sol

89    emit FinalizeDeposit(_l1Sender, _l2Receiver, expectedL2Token, _amount)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L65-L90](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L65-L90)


- Reentrancy in 
```
File: contracts/bridge/L2WethBridge.sol

90    function finalizeDeposit(
91            address _l1Sender,
92            address _l2Receiver,
93            address _l1Token,
94            uint256 _amount,
95            bytes calldata // _data
96        ) external payable override 
```
:
	External calls:
	- 
```
File: contracts/bridge/L2WethBridge.sol

106    IL2Weth(l2WethAddress).depositTo{value: msg.value}(_l2Receiver)
```

	Event emitted after the call(s):
	- 
```
File: contracts/bridge/L2WethBridge.sol

108    emit FinalizeDeposit(_l1Sender, _l2Receiver, l2WethAddress, _amount)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L90-L109](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L90-L109)


- Reentrancy in 
```
File: contracts/bridge/L2ERC20Bridge.sol

107    function withdraw(
108            address _l1Receiver,
109            address _l2Token,
110            uint256 _amount
111        ) external override 
```
:
	External calls:
	- 
```
File: contracts/bridge/L2ERC20Bridge.sol

112    IL2StandardToken(_l2Token).bridgeBurn(msg.sender, _amount)
```

	- 
```
File: contracts/bridge/L2ERC20Bridge.sol

118    L2ContractHelper.sendMessageToL1(message)
```

	Event emitted after the call(s):
	- 
```
File: contracts/bridge/L2ERC20Bridge.sol

120    emit WithdrawalInitiated(msg.sender, _l1Receiver, _l2Token, _amount)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L107-L121](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L107-L121)


- Reentrancy in 
```
File: contracts/bridge/L2WethBridge.sol

65    function withdraw(
66            address _l1Receiver,
67            address _l2Token,
68            uint256 _amount
69        ) external override 
```
:
	External calls:
	- 
```
File: contracts/bridge/L2WethBridge.sol

74    IL2StandardToken(l2WethAddress).bridgeBurn(msg.sender, _amount)
```

	- 
```
File: contracts/bridge/L2WethBridge.sol

80    L2_ETH_ADDRESS.withdrawWithMessage{value: _amount}(l1Bridge, wethMessage)
```

	External calls sending eth:
	- 
```
File: contracts/bridge/L2WethBridge.sol

80    L2_ETH_ADDRESS.withdrawWithMessage{value: _amount}(l1Bridge, wethMessage)
```

	Event emitted after the call(s):
	- 
```
File: contracts/bridge/L2WethBridge.sol

82    emit WithdrawalInitiated(msg.sender, _l1Receiver, l2WethAddress, _amount)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L65-L83](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L65-L83)


- Reentrancy in 
```
File: contracts/KnownCodesStorage.sol

49    function _markBytecodeAsPublished(bytes32 _bytecodeHash, bool _shouldSendToL1) internal 
```
:
	External calls:
	- 
```
File: contracts/KnownCodesStorage.sol

54    L1_MESSENGER_CONTRACT.requestBytecodeL1Publication(_bytecodeHash)
```

	Event emitted after the call(s):
	- 
```
File: contracts/KnownCodesStorage.sol

62    emit MarkedAsKnown(_bytecodeHash, _shouldSendToL1)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol#L49-L64](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/KnownCodesStorage.sol#L49-L64)


- Reentrancy in 
```
File: contracts/ContractDeployer.sol

285    function _performDeployOnAddress(
286            bytes32 _bytecodeHash,
287            address _newAddress,
288            AccountAbstractionVersion _aaVersion,
289            bytes calldata _input
290        ) internal 
```
:
	External calls:
	- 
```
File: contracts/ContractDeployer.sol

299    _constructContract(msg.sender, _newAddress, _bytecodeHash, _input, false, true)
```

		- 
```
File: contracts/ContractDeployer.sol

315    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructingCodeHash(_newAddress, constructingBytecodeHash)
```

		- 
```
File: contracts/ContractDeployer.sol

336    ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo(address(this), _newAddress, value)
```

		- 
```
File: contracts/ContractDeployer.sol

348    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.markAccountCodeHashAsConstructed(_newAddress)
```

		- 
```
File: contracts/ContractDeployer.sol

351    IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT.setImmutables(_newAddress, immutables)
```

		- 
```
File: contracts/ContractDeployer.sol

355    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructedCodeHash(_newAddress, _bytecodeHash)
```

	Event emitted after the call(s):
	- 
```
File: contracts/ContractDeployer.sol

300    emit ContractDeployed(msg.sender, _bytecodeHash, _newAddress)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L285-L301](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L285-L301)


- Reentrancy in 
```
File: contracts/ContractDeployer.sol

164    function create2Account(
165            bytes32 _salt,
166            bytes32 _bytecodeHash,
167            bytes calldata _input,
168            AccountAbstractionVersion _aaVersion
169        ) public payable override onlySystemCall returns (address) 
```
:
	External calls:
	- 
```
File: contracts/ContractDeployer.sol

170    NONCE_HOLDER_SYSTEM_CONTRACT.incrementDeploymentNonce(msg.sender)
```

	- 
```
File: contracts/ContractDeployer.sol

173    _nonSystemDeployOnAddress(_bytecodeHash, newAddress, _aaVersion, _input)
```

		- 
```
File: contracts/ContractDeployer.sol

315    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructingCodeHash(_newAddress, constructingBytecodeHash)
```

		- 
```
File: contracts/ContractDeployer.sol

336    ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo(address(this), _newAddress, value)
```

		- 
```
File: contracts/ContractDeployer.sol

348    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.markAccountCodeHashAsConstructed(_newAddress)
```

		- 
```
File: contracts/ContractDeployer.sol

351    IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT.setImmutables(_newAddress, immutables)
```

		- 
```
File: contracts/ContractDeployer.sol

355    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructedCodeHash(_newAddress, _bytecodeHash)
```

	Event emitted after the call(s):
	- 
```
File: contracts/ContractDeployer.sol

300    emit ContractDeployed(msg.sender, _bytecodeHash, _newAddress)
```

		- 
```
File: contracts/ContractDeployer.sol

173    _nonSystemDeployOnAddress(_bytecodeHash, newAddress, _aaVersion, _input)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L164-L176](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L164-L176)


- Reentrancy in 
```
File: contracts/ContractDeployer.sol

185    function createAccount(
186            bytes32, // salt
187            bytes32 _bytecodeHash,
188            bytes calldata _input,
189            AccountAbstractionVersion _aaVersion
190        ) public payable override onlySystemCall returns (address) 
```
:
	External calls:
	- 
```
File: contracts/ContractDeployer.sol

191    uint256 senderNonce = NONCE_HOLDER_SYSTEM_CONTRACT.incrementDeploymentNonce(msg.sender)
```

	- 
```
File: contracts/ContractDeployer.sol

194    _nonSystemDeployOnAddress(_bytecodeHash, newAddress, _aaVersion, _input)
```

		- 
```
File: contracts/ContractDeployer.sol

315    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructingCodeHash(_newAddress, constructingBytecodeHash)
```

		- 
```
File: contracts/ContractDeployer.sol

336    ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo(address(this), _newAddress, value)
```

		- 
```
File: contracts/ContractDeployer.sol

348    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.markAccountCodeHashAsConstructed(_newAddress)
```

		- 
```
File: contracts/ContractDeployer.sol

351    IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT.setImmutables(_newAddress, immutables)
```

		- 
```
File: contracts/ContractDeployer.sol

355    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructedCodeHash(_newAddress, _bytecodeHash)
```

	Event emitted after the call(s):
	- 
```
File: contracts/ContractDeployer.sol

300    emit ContractDeployed(msg.sender, _bytecodeHash, _newAddress)
```

		- 
```
File: contracts/ContractDeployer.sol

194    _nonSystemDeployOnAddress(_bytecodeHash, newAddress, _aaVersion, _input)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L185-L197](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L185-L197)


- Reentrancy in 
```
File: contracts/ContractDeployer.sol

216    function forceDeployOnAddress(ForceDeployment calldata _deployment, address _sender) external payable onlySelf 
```
:
	External calls:
	- 
```
File: contracts/ContractDeployer.sol

225    _constructContract(
226                _sender,
227                _deployment.newAddress,
228                _deployment.bytecodeHash,
229                _deployment.input,
230                false,
231                _deployment.callConstructor
232            )
```

		- 
```
File: contracts/ContractDeployer.sol

315    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructingCodeHash(_newAddress, constructingBytecodeHash)
```

		- 
```
File: contracts/ContractDeployer.sol

336    ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo(address(this), _newAddress, value)
```

		- 
```
File: contracts/ContractDeployer.sol

348    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.markAccountCodeHashAsConstructed(_newAddress)
```

		- 
```
File: contracts/ContractDeployer.sol

351    IMMUTABLE_SIMULATOR_SYSTEM_CONTRACT.setImmutables(_newAddress, immutables)
```

		- 
```
File: contracts/ContractDeployer.sol

355    ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructedCodeHash(_newAddress, _bytecodeHash)
```

	Event emitted after the call(s):
	- 
```
File: contracts/ContractDeployer.sol

234    emit ContractDeployed(_sender, _deployment.bytecodeHash, _deployment.newAddress)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L216-L235](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L216-L235)


- Reentrancy in 
```
File: contracts/L2EthToken.sol

74    function withdraw(address _l1Receiver) external payable override 
```
:
	External calls:
	- 
```
File: contracts/L2EthToken.sol

79    L1_MESSENGER_CONTRACT.sendToL1(message)
```

	Event emitted after the call(s):
	- 
```
File: contracts/L2EthToken.sol

81    emit Withdrawal(msg.sender, _l1Receiver, amount)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L74-L82](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L74-L82)


- Reentrancy in 
```
File: contracts/L2EthToken.sol

87    function withdrawWithMessage(address _l1Receiver, bytes memory _additionalData) external payable override 
```
:
	External calls:
	- 
```
File: contracts/L2EthToken.sol

92    L1_MESSENGER_CONTRACT.sendToL1(message)
```

	Event emitted after the call(s):
	- 
```
File: contracts/L2EthToken.sol

94    emit WithdrawalWithMessage(msg.sender, _l1Receiver, amount, _additionalData)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L87-L95](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L87-L95)


</details>

# 



## Unused File
- Severity: Non-Critical
- Confidence: High

### Description
files within a codebase that are not imported or used by any other source file. These files may be unnecessary and can potentially clutter the codebase. If such files are intended for tests, it is recommended to move them to a designated test directory.

## Note 
This file is missing in the winning bot. 

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: contracts/interfaces/IL2StandardToken.sol

7    interface IL2StandardToken 
```
 	 contracts/interfaces/IL2StandardToken.sol is never used

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IL2StandardToken.sol#L7-L19](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IL2StandardToken.sol#L7-L19)



</details>

# 




## Whitespace in Expressions
- Severity: Non-Critical
- Confidence: High

### Description
Detects when whitespace usage in expressions does not conform to the Solidity style guide.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
- 
```
File: contracts/zksync/facets/Executor.sol

20    contract ExecutorFacet is Base, IExecutor 
```

```
// @audit: whitespace inside parenthesis
Line: 127                (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET);


// @audit: whitespace inside parenthesis
Line: 128                (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);


// @audit: whitespace inside parenthesis
Line: 129                (bytes32 logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);


```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L20-L473](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L20-L473)


- 
```
File: contracts/bridge/L2Weth.sol

23    contract L2Weth is ERC20PermitUpgradeable, IL2Weth, IL2StandardToken 
```

```
// @audit: whitespace inside parenthesis
Line: 83            (bool success, ) = msg.sender.call{value: _amount}("");


// @audit: whitespace inside parenthesis
Line: 108            (bool success, ) = _to.call{value: _amount}("");


```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L23-L116](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L23-L116)


- 
```
File: contracts/MsgValueSimulator.sol

21    contract MsgValueSimulator is ISystemContract 
```

```
// @audit: whitespace inside parenthesis
Line: 44                (bool success, ) = address(ETH_TOKEN_SYSTEM_CONTRACT).call(


```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L21-L61](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L21-L61)


- 
```
File: contracts/NonceHolder.sol

29    contract NonceHolder is INonceHolder, ISystemContract 
```

```
// @audit: whitespace inside parenthesis
Line: 129            (deploymentNonce, ) = _splitRawNonce(rawNonces[addressAsKey]);


// @audit: whitespace inside parenthesis
Line: 146            (prevDeploymentNonce, ) = _splitRawNonce(oldRawNonce);


```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L29-L178](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L29-L178)


- 
```
File: contracts/openzeppelin/utils/Address.sol

11    library Address 
```

```
// @audit: whitespace inside parenthesis
Line: 28            (bool success, ) = recipient.call{value: amount}("");


```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L11-L310](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L11-L310)


</details>

# 





## Control structures do not follow the Solidity Style Guide
- Severity: Non-Critical
- Confidence: High

### Description
Control structures should follow the One True Brace [OTB style](https://docs.soliditylang.org/en/latest/style-guide.html#control-structures). This means: 1. Opening braces should be on the same line as the declaration. 
2. Closing braces should be on their own line, at the same indentation level as the beginning of the declaration. 
3. The opening brace should be preceded by a single space.

<details>

<summary>
There are 55 instances of this issue:

</summary>

###
- 
```
File: contracts/bridge/L1ERC20Bridge.sol

85    function initialize(
86            bytes[] calldata _factoryDeps,
87            address _l2TokenBeacon,
88            address _governor,
89            uint256 _deployBridgeImplementationFee,
90            uint256 _deployBridgeProxyFee
91        ) external payable reentrancyGuardInitializer 
```
 These control structures in the function `initialize` should follow the One True Brace (OTB) style:

    - Line: 115:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L85-L133](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L85-L133)


- 
```
File: contracts/bridge/L1ERC20Bridge.sol

178    function deposit(
179            address _l2Receiver,
180            address _l1Token,
181            uint256 _amount,
182            uint256 _l2TxGasLimit,
183            uint256 _l2TxGasPerPubdataByte,
184            address _refundRecipient
185        ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) 
```
 These control structures in the function `deposit` should follow the One True Brace (OTB) style:

    - Line: 200:         l2TxHash = zkSync.requestL2Transaction{value: msg.value}(

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L178-L214](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L178-L214)


- 
```
File: contracts/bridge/L1ERC20Bridge.sol

295    function finalizeWithdrawal(
296            uint256 _l2BatchNumber,
297            uint256 _l2MessageIndex,
298            uint16 _l2TxNumberInBatch,
299            bytes calldata _message,
300            bytes32[] calldata _merkleProof
301        ) external nonReentrant senderCanCallFunction(allowList) 
```
 These control structures in the function `finalizeWithdrawal` should follow the One True Brace (OTB) style:

    - Line: 304:         L2Message memory l2ToL1Message = L2Message({
    - Line: 308:         });
    - Line: 312:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L295-L322](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L295-L322)


- 
```
File: contracts/bridge/L1WethBridge.sol

83    function initialize(
84            bytes[] calldata _factoryDeps,
85            address _l2WethAddress,
86            address _governor,
87            uint256 _deployBridgeImplementationFee,
88            uint256 _deployBridgeProxyFee
89        ) external payable reentrancyGuardInitializer 
```
 These control structures in the function `initialize` should follow the One True Brace (OTB) style:

    - Line: 114:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L83-L136](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L83-L136)


- 
```
File: contracts/bridge/L1WethBridge.sol

161    function deposit(
162            address _l2Receiver,
163            address _l1Token,
164            uint256 _amount,
165            uint256 _l2TxGasLimit,
166            uint256 _l2TxGasPerPubdataByte,
167            address _refundRecipient
168        ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) 
```
 These control structures in the function `deposit` should follow the One True Brace (OTB) style:

    - Line: 187:         txHash = zkSync.requestL2Transaction{value: _amount + msg.value}(

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L161-L198](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L161-L198)


- 
```
File: contracts/bridge/L1WethBridge.sol

235    function finalizeWithdrawal(
236            uint256 _l2BatchNumber,
237            uint256 _l2MessageIndex,
238            uint16 _l2TxNumberInBatch,
239            bytes calldata _message,
240            bytes32[] calldata _merkleProof
241        ) external nonReentrant senderCanCallFunction(allowList) 
```
 These control structures in the function `finalizeWithdrawal` should follow the One True Brace (OTB) style:

    - Line: 250:             L2Message memory l2ToL1Message = L2Message({
    - Line: 254:             });
    - Line: 263:         IWETH9(l1WethAddress).deposit{value: amount}();

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L235-L270](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L235-L270)


- 
```
File: contracts/common/AllowListed.sol

12    modifier senderCanCallFunction(IAllowList _allowList) 
```
 These control structures in the function `senderCanCallFunction` should follow the One True Brace (OTB) style:

    - Line: 14:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L12-L18](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowListed.sol#L12-L18)


- 
```
File: contracts/governance/Governance.sol

226    function _execute(Call[] calldata _calls) internal 
```
 These control structures in the function `_execute` should follow the One True Brace (OTB) style:

    - Line: 228:             (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L226-L236](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L226-L236)


- 
```
File: contracts/governance/Governance.sol

264    receive() external payable 
```
 These control structures in the function `receive` should follow the One True Brace (OTB) style:

    - Line: 264: receive() external payable {}

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L264](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L264)


- 
```
File: contracts/upgrades/DefaultUpgrade.sol

17    function _upgradeL1Contract(bytes calldata _customCallDataForUpgrade) internal virtual 
```
 These control structures in the function `_upgradeL1Contract` should follow the One True Brace (OTB) style:

    - Line: 17: function _upgradeL1Contract(bytes calldata _customCallDataForUpgrade) internal virtual {}

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L17](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L17)


- 
```
File: contracts/upgrades/DefaultUpgrade.sol

23    function _postUpgrade(bytes calldata _customCallDataForUpgrade) internal virtual 
```
 These control structures in the function `_postUpgrade` should follow the One True Brace (OTB) style:

    - Line: 23: function _postUpgrade(bytes calldata _customCallDataForUpgrade) internal virtual {}

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L23](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L23)


- 
```
File: contracts/zksync/DiamondInit.sol

52    constructor() reentrancyGuardInitializer 
```
 These control structures in the function `constructor` should follow the One True Brace (OTB) style:

    - Line: 52: constructor() reentrancyGuardInitializer {}

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L52](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L52)


- 
```
File: contracts/zksync/facets/Executor.sol

103    function _processL2Logs(CommitBatchInfo calldata _newBatch, bytes32 _expectedSystemContractUpgradeTxHash)
104            internal
105            pure
106            returns (
107                uint256 numberOfLayer1Txs,
108                bytes32 chainedPriorityTxsHash,
109                bytes32 previousBatchHash,
110                bytes32 stateDiffHash,
111                bytes32 l2LogsTreeRoot,
112                uint256 packedBatchAndL2BlockTimestamp
113            )
114        
```
 These control structures in the function `_processL2Logs` should follow the One True Brace (OTB) style:

    - Line: 114:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L103-L173](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L103-L173)


- 
```
File: contracts/zksync/facets/Executor.sol

179    function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)
180            external
181            override
182            nonReentrant
183            onlyValidator
184        
```
 These control structures in the function `commitBatches` should follow the One True Brace (OTB) style:

    - Line: 184:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L179-L202](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L179-L202)


- 
```
File: contracts/zksync/facets/Executor.sol

415    function _createBatchCommitment(CommitBatchInfo calldata _newBatchData, bytes32 _stateDiffHash)
416            internal
417            view
418            returns (bytes32)
419        
```
 These control structures in the function `_createBatchCommitment` should follow the One True Brace (OTB) style:

    - Line: 419:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L415-L425](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L415-L425)


- 
```
File: contracts/zksync/facets/Executor.sol

441    function _batchAuxiliaryOutput(CommitBatchInfo calldata _batch, bytes32 _stateDiffHash)
442            internal
443            pure
444            returns (bytes memory)
445        
```
 These control structures in the function `_batchAuxiliaryOutput` should follow the One True Brace (OTB) style:

    - Line: 445:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L441-L457](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L441-L457)


- 
```
File: contracts/zksync/facets/Mailbox.sol

84    function proveL1ToL2TransactionStatus(
85            bytes32 _l2TxHash,
86            uint256 _l2BatchNumber,
87            uint256 _l2MessageIndex,
88            uint16 _l2TxNumberInBatch,
89            bytes32[] calldata _merkleProof,
90            TxStatus _status
91        ) public view override returns (bool) 
```
 These control structures in the function `proveL1ToL2TransactionStatus` should follow the One True Brace (OTB) style:

    - Line: 102:         L2Log memory l2Log = L2Log({
    - Line: 109:         });

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L84-L111](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L84-L111)


- 
```
File: contracts/zksync/facets/Mailbox.sol

151    function _L2MessageToLog(L2Message memory _message) internal pure returns (L2Log memory) 
```
 These control structures in the function `_L2MessageToLog` should follow the One True Brace (OTB) style:

    - Line: 153:             L2Log({
    - Line: 160:             });

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L151-L161](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L151-L161)


- 
```
File: contracts/zksync/facets/Mailbox.sol

194    function finalizeEthWithdrawal(
195            uint256 _l2BatchNumber,
196            uint256 _l2MessageIndex,
197            uint16 _l2TxNumberInBatch,
198            bytes calldata _message,
199            bytes32[] calldata _merkleProof
200        ) external override nonReentrant senderCanCallFunction(s.allowList) 
```
 These control structures in the function `finalizeEthWithdrawal` should follow the One True Brace (OTB) style:

    - Line: 203:         L2Message memory l2ToL1Message = L2Message({
    - Line: 207:         });

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L194-L218](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L194-L218)


- 
```
File: contracts/zksync/facets/Mailbox.sol

285    function _requestL2Transaction(
286            address _sender,
287            address _contractAddressL2,
288            uint256 _l2Value,
289            bytes calldata _calldata,
290            uint256 _l2GasLimit,
291            uint256 _l2GasPerPubdataByteLimit,
292            bytes[] calldata _factoryDeps,
293            bool _isFree,
294            address _refundRecipient
295        ) internal returns (bytes32 canonicalTxHash) 
```
 These control structures in the function `_requestL2Transaction` should follow the One True Brace (OTB) style:

    - Line: 305:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L285-L329](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L285-L329)


- 
```
File: contracts/zksync/facets/Mailbox.sol

331    function _serializeL2Transaction(
332            WritePriorityOpParams memory _priorityOpParams,
333            bytes calldata _calldata,
334            bytes[] calldata _factoryDeps
335        ) internal pure returns (L2CanonicalTransaction memory transaction) 
```
 These control structures in the function `_serializeL2Transaction` should follow the One True Brace (OTB) style:

    - Line: 336:         transaction = L2CanonicalTransaction({
    - Line: 354:         });

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L331-L355](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L331-L355)


- 
```
File: contracts/zksync/facets/Mailbox.sol

358    function _writePriorityOp(
359            WritePriorityOpParams memory _priorityOpParams,
360            bytes calldata _calldata,
361            bytes[] calldata _factoryDeps
362        ) internal returns (bytes32 canonicalTxHash) 
```
 These control structures in the function `_writePriorityOp` should follow the One True Brace (OTB) style:

    - Line: 372:             PriorityOperation({
    - Line: 376:             })

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L358-L387](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L358-L387)


- 
```
File: contracts/zksync/facets/Mailbox.sol

390    function _hashFactoryDeps(bytes[] calldata _factoryDeps)
391            internal
392            pure
393            returns (uint256[] memory hashedFactoryDeps)
394        
```
 These control structures in the function `_hashFactoryDeps` should follow the One True Brace (OTB) style:

    - Line: 394:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L390-L405](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L390-L405)


- 
```
File: contracts/zksync/facets/Mailbox.sol

408    function _parseL2WithdrawalMessage(bytes memory _message)
409            internal
410            pure
411            returns (address l1Receiver, uint256 amount)
412        
```
 These control structures in the function `_parseL2WithdrawalMessage` should follow the One True Brace (OTB) style:

    - Line: 412:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L408-L430](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L408-L430)


- 
```
File: contracts/bridge/L2ERC20Bridge.sol

42    function initialize(
43            address _l1Bridge,
44            bytes32 _l2TokenProxyBytecodeHash,
45            address _governor
46        ) external initializer 
```
 These control structures in the function `initialize` should follow the One True Brace (OTB) style:

    - Line: 54:         address l2StandardToken = address(new L2StandardERC20{salt: bytes32(0)}());
    - Line: 55:         l2TokenBeacon = new UpgradeableBeacon{salt: bytes32(0)}(l2StandardToken);

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L42-L57](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L42-L57)


- 
```
File: contracts/bridge/L2StandardERC20.sol

48    function bridgeInitialize(address _l1Address, bytes memory _data) external initializer 
```
 These control structures in the function `bridgeInitialize` should follow the One True Brace (OTB) style:

    - Line: 75:         } catch {
    - Line: 81:         } catch {
    - Line: 94:         } catch {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L48-L100](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L48-L100)


- 
```
File: contracts/bridge/L2Weth.sol

80    function bridgeBurn(address _from, uint256 _amount) external override onlyBridge 
```
 These control structures in the function `bridgeBurn` should follow the One True Brace (OTB) style:

    - Line: 83:         (bool success, ) = msg.sender.call{value: _amount}("");

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L80-L87](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L80-L87)


- 
```
File: contracts/bridge/L2Weth.sol

106    function withdrawTo(address _to, uint256 _amount) public override 
```
 These control structures in the function `withdrawTo` should follow the One True Brace (OTB) style:

    - Line: 108:         (bool success, ) = _to.call{value: _amount}("");

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L106-L110](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L106-L110)


- 
```
File: contracts/bridge/L2WethBridge.sol

65    function withdraw(
66            address _l1Receiver,
67            address _l2Token,
68            uint256 _amount
69        ) external override 
```
 These control structures in the function `withdraw` should follow the One True Brace (OTB) style:

    - Line: 80:         L2_ETH_ADDRESS.withdrawWithMessage{value: _amount}(l1Bridge, wethMessage);

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L65-L83](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L65-L83)


- 
```
File: contracts/bridge/L2WethBridge.sol

90    function finalizeDeposit(
91            address _l1Sender,
92            address _l2Receiver,
93            address _l1Token,
94            uint256 _amount,
95            bytes calldata // _data
96        ) external payable override 
```
 These control structures in the function `finalizeDeposit` should follow the One True Brace (OTB) style:

    - Line: 106:         IL2Weth(l2WethAddress).depositTo{value: msg.value}(_l2Receiver);

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L90-L109](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L90-L109)


- 
```
File: contracts/BootloaderUtilities.sol

46    function encodeLegacyTransactionHash(Transaction calldata _transaction) internal view returns (bytes32 txHash) 
```
 These control structures in the function `encodeLegacyTransactionHash` should follow the One True Brace (OTB) style:

    - Line: 57:         {
    - Line: 68:         {
    - Line: 82:         {
    - Line: 87:         {
    - Line: 92:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L46-L136](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L46-L136)


- 
```
File: contracts/BootloaderUtilities.sol

141    function encodeEIP2930TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) 
```
 These control structures in the function `encodeEIP2930TransactionHash` should follow the One True Brace (OTB) style:

    - Line: 144:         {
    - Line: 164:         {
    - Line: 181:         {
    - Line: 186:         {
    - Line: 191:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L141-L226](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L141-L226)


- 
```
File: contracts/BootloaderUtilities.sol

231    function encodeEIP1559TransactionHash(Transaction calldata _transaction) internal view returns (bytes32) 
```
 These control structures in the function `encodeEIP1559TransactionHash` should follow the One True Brace (OTB) style:

    - Line: 237:         {
    - Line: 259:         {
    - Line: 276:         {
    - Line: 281:         {
    - Line: 286:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L231-L321](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L231-L321)


- 
```
File: contracts/ContractDeployer.sol

240    function forceDeployOnAddresses(ForceDeployment[] calldata _deployments) external payable 
```
 These control structures in the function `forceDeployOnAddresses` should follow the One True Brace (OTB) style:

    - Line: 256:             this.forceDeployOnAddress{value: _deployments[i].value}(_deployments[i], msg.sender);

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L240-L258](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L240-L258)


- 
```
File: contracts/DefaultAccount.sol

163    function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) 
```
 These control structures in the function `_isValidSignature` should follow the One True Brace (OTB) style:

    - Line: 181: b'        // the valid range for s in (301): 0 < s < secp256k1n \xc3\xb7 2 + 1, and for v in (302): v \xe2\x88\x88 {27, 28}. Most'

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L163-L193](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L163-L193)


- 
```
File: contracts/EmptyContract.sol

14    fallback() external payable 
```
 These control structures in the function `fallback` should follow the One True Brace (OTB) style:

    - Line: 14: fallback() external payable {}

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L14](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L14)


- 
```
File: contracts/EmptyContract.sol

16    receive() external payable 
```
 These control structures in the function `receive` should follow the One True Brace (OTB) style:

    - Line: 16: receive() external payable {}

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L16](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L16)


- 
```
File: contracts/L1Messenger.sol

77    function sendL2ToL1Log(
78            bool _isService,
79            bytes32 _key,
80            bytes32 _value
81        ) external onlyCallFromSystemContract returns (uint256 logIdInMerkleTree) 
```
 These control structures in the function `sendL2ToL1Log` should follow the One True Brace (OTB) style:

    - Line: 82:         L2ToL1Log memory l2ToL1Log = L2ToL1Log({
    - Line: 89:         });

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L77-L97](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L77-L97)


- 
```
File: contracts/L1Messenger.sol

121    function sendToL1(bytes calldata _message) external override returns (bytes32 hash) 
```
 These control structures in the function `sendToL1` should follow the One True Brace (OTB) style:

    - Line: 130:         L2ToL1Log memory l2ToL1Log = L2ToL1Log({
    - Line: 137:         });

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L121-L164](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L121-L164)


- 
```
File: contracts/SystemContext.sol

287    function _setNewL2BlockData(uint128 _l2BlockNumber, uint128 _l2BlockTimestamp, bytes32 _prevL2BlockHash) internal 
```
 These control structures in the function `_setNewL2BlockData` should follow the One True Brace (OTB) style:

    - Line: 289:         currentL2BlockInfo = BlockInfo({number: _l2BlockNumber, timestamp: _l2BlockTimestamp});

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L287-L296](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L287-L296)


- 
```
File: contracts/SystemContext.sol

418    function setNewBatch(
419            bytes32 _prevBatchHash,
420            uint128 _newTimestamp,
421            uint128 _expectedNewNumber,
422            uint256 _baseFee
423        ) external onlyCallFromBootloader 
```
 These control structures in the function `setNewBatch` should follow the One True Brace (OTB) style:

    - Line: 433:         BlockInfo memory newBlockInfo = BlockInfo({number: previousBatchNumber + 1, timestamp: _newTimestamp});

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L418-L441](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L418-L441)


- 
```
File: contracts/SystemContext.sol

445    function unsafeOverrideBatch(
446            uint256 _newTimestamp,
447            uint256 _number,
448            uint256 _baseFee
449        ) external onlyCallFromBootloader 
```
 These control structures in the function `unsafeOverrideBatch` should follow the One True Brace (OTB) style:

    - Line: 450:         BlockInfo memory newBlockInfo = BlockInfo({number: uint128(_number), timestamp: uint128(_newTimestamp)});

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L445-L454](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L445-L454)


- 
```
File: contracts/libraries/EfficientCall.sol

126    function rawCall(
127            uint256 _gas,
128            address _address,
129            uint256 _value,
130            bytes calldata _data,
131            bool _isSystem
132        ) internal returns (bool success) 
```
 These control structures in the function `rawCall` should follow the One True Brace (OTB) style:

    - Line: 139:             }

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L126-L154](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L126-L154)


- 
```
File: contracts/libraries/EfficientCall.sol

217    function _verifyCallResult(bool _success) private pure returns (bytes memory returnData) 
```
 These control structures in the function `_verifyCallResult` should follow the One True Brace (OTB) style:

    - Line: 227:             }

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L217-L231](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L217-L231)


- 
```
File: contracts/libraries/SystemContractsCaller.sol

78    function systemCall(uint32 gasLimit, address to, uint256 value, bytes memory data) internal returns (bool success) 
```
 These control structures in the function `systemCall` should follow the One True Brace (OTB) style:

    - Line: 104:             }

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L78-L115](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L78-L115)


- 
```
File: contracts/libraries/TransactionHelper.sol

149    function _encodeHashLegacyTransaction(Transaction calldata _transaction) private view returns (bytes32) 
```
 These control structures in the function `_encodeHashLegacyTransaction` should follow the One True Brace (OTB) style:

    - Line: 160:         {
    - Line: 171:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L149-L217](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L149-L217)


- 
```
File: contracts/libraries/TransactionHelper.sol

221    function _encodeHashEIP2930Transaction(Transaction calldata _transaction) private view returns (bytes32) 
```
 These control structures in the function `_encodeHashEIP2930Transaction` should follow the One True Brace (OTB) style:

    - Line: 229:         {
    - Line: 249:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L221-L287](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L221-L287)


- 
```
File: contracts/libraries/TransactionHelper.sol

291    function _encodeHashEIP1559Transaction(Transaction calldata _transaction) private view returns (bytes32) 
```
 These control structures in the function `_encodeHashEIP1559Transaction` should follow the One True Brace (OTB) style:

    - Line: 299:         {
    - Line: 321:         {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L291-L359](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L291-L359)


- 
```
File: contracts/libraries/TransactionHelper.sol

364    function processPaymasterInput(Transaction calldata _transaction) internal 
```
 These control structures in the function `processPaymasterInput` should follow the One True Brace (OTB) style:

    - Line: 386:             }

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L364-L392](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L364-L392)


- 
```
File: contracts/openzeppelin/utils/Address.sol

62    function sendValue(address payable recipient, uint256 amount) internal 
```
 These control structures in the function `sendValue` should follow the One True Brace (OTB) style:

    - Line: 68:         (bool success, ) = recipient.call{value: amount}("");

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L62-L73](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L62-L73)


- 
```
File: contracts/openzeppelin/utils/Address.sol

93    function functionCall(address target, bytes memory data)
94            internal
95            returns (bytes memory)
96        
```
 These control structures in the function `functionCall` should follow the One True Brace (OTB) style:

    - Line: 96:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L93-L104](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L93-L104)


- 
```
File: contracts/openzeppelin/utils/Address.sol

151    function functionCallWithValue(
152            address target,
153            bytes memory data,
154            uint256 value,
155            string memory errorMessage
156        ) internal returns (bytes memory) 
```
 These control structures in the function `functionCallWithValue` should follow the One True Brace (OTB) style:

    - Line: 161:         (bool success, bytes memory returndata) = target.call{value: value}(

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L151-L171](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L151-L171)


- 
```
File: contracts/openzeppelin/utils/Address.sol

179    function functionStaticCall(address target, bytes memory data)
180            internal
181            view
182            returns (bytes memory)
183        
```
 These control structures in the function `functionStaticCall` should follow the One True Brace (OTB) style:

    - Line: 183:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L179-L190](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L179-L190)


- 
```
File: contracts/openzeppelin/utils/Address.sol

219    function functionDelegateCall(address target, bytes memory data)
220            internal
221            returns (bytes memory)
222        
```
 These control structures in the function `functionDelegateCall` should follow the One True Brace (OTB) style:

    - Line: 222:     {

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L219-L229](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L219-L229)


- 
```
File: contracts/openzeppelin/utils/Address.sol

294    function _revert(bytes memory returndata, string memory errorMessage)
295            private
296            pure
297        
```
 These control structures in the function `_revert` should follow the One True Brace (OTB) style:

    - Line: 297:     {
    - Line: 305:             }

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L294-L309](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L294-L309)


</details>

# 


## Function not called
- Severity: Non-Critical
- Confidence: Medium

### Description
Functions that are not sued.

<details>

<summary>
There are 26 instances of this issue:

</summary>

###
- 
```
File: contracts/vendor/AddressAliasHelper.sol

40    function undoL1ToL2Alias(address l2Address) internal pure returns (address l1Address) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L40-L44](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L40-L44)


- 
```
File: contracts/zksync/facets/Executor.sol

410    function _maxU256(uint256 a, uint256 b) internal pure returns (uint256) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L410-L412](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L410-L412)


- 
```
File: contracts/openzeppelin/utils/Address.sol

93    function functionCall(address target, bytes memory data)
94            internal
95            returns (bytes memory)
96        
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L93-L104](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L93-L104)


- 
```
File: contracts/openzeppelin/utils/Address.sol

131    function functionCallWithValue(
132            address target,
133            bytes memory data,
134            uint256 value
135        ) internal returns (bytes memory) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L131-L143](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L131-L143)


- 
```
File: contracts/openzeppelin/utils/Address.sol

219    function functionDelegateCall(address target, bytes memory data)
220            internal
221            returns (bytes memory)
222        
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L219-L229](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L219-L229)


- 
```
File: contracts/openzeppelin/utils/Address.sol

237    function functionDelegateCall(
238            address target,
239            bytes memory data,
240            string memory errorMessage
241        ) internal returns (bytes memory) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L237-L250](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L237-L250)


- 
```
File: contracts/openzeppelin/utils/Address.sol

179    function functionStaticCall(address target, bytes memory data)
180            internal
181            view
182            returns (bytes memory)
183        
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L179-L190](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L179-L190)


- 
```
File: contracts/openzeppelin/utils/Address.sol

198    function functionStaticCall(
199            address target,
200            bytes memory data,
201            string memory errorMessage
202        ) internal view returns (bytes memory) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L198-L211](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L198-L211)


- 
```
File: contracts/openzeppelin/utils/Address.sol

62    function sendValue(address payable recipient, uint256 amount) internal 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L62-L73](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L62-L73)


- 
```
File: contracts/openzeppelin/utils/Address.sol

282    function verifyCallResult(
283            bool success,
284            bytes memory returndata,
285            string memory errorMessage
286        ) internal pure returns (bytes memory) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L282-L292](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L282-L292)


- 
```
File: contracts/libraries/EfficientCall.sol

60    function call(
61            uint256 _gas,
62            address _address,
63            uint256 _value,
64            bytes calldata _data,
65            bool _isSystem
66        ) internal returns (bytes memory returnData) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L60-L69](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L60-L69)


- 
```
File: contracts/libraries/EfficientCall.sol

90    function delegateCall(
91            uint256 _gas,
92            address _address,
93            bytes calldata _data
94        ) internal returns (bytes memory returnData) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L90-L97](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L90-L97)


- 
```
File: contracts/libraries/EfficientCall.sol

175    function rawDelegateCall(uint256 _gas, address _address, bytes calldata _data) internal returns (bool success) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L175-L182](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L175-L182)


- 
```
File: contracts/libraries/SystemContractHelper.sol

204    function eventInitialize(uint256 initializer, uint256 value1) internal 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L204-L209](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L204-L209)


- 
```
File: contracts/libraries/SystemContractHelper.sol

214    function eventWrite(uint256 value1, uint256 value2) internal 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L214-L219](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L214-L219)


- 
```
File: contracts/libraries/SystemContractHelper.sol

267    function getAuxHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 auxHeapSize) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L267-L269](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L267-L269)


- 
```
File: contracts/libraries/SystemContractHelper.sol

325    function getCalldataPtr() internal view returns (uint256 ptr) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L325-L330](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L325-L330)


- 
```
File: contracts/libraries/SystemContractHelper.sol

284    function getCallerShardIdFromMeta(uint256 meta) internal pure returns (uint8 callerShardId) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L284-L286](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L284-L286)


- 
```
File: contracts/libraries/SystemContractHelper.sol

293    function getCodeShardIdFromMeta(uint256 meta) internal pure returns (uint8 codeShardId) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L293-L295](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L293-L295)


- 
```
File: contracts/libraries/SystemContractHelper.sol

258    function getHeapSizeFromMeta(uint256 meta) internal pure returns (uint32 heapSize) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L258-L260](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L258-L260)


- 
```
File: contracts/libraries/SystemContractHelper.sol

275    function getShardIdFromMeta(uint256 meta) internal pure returns (uint8 shardId) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L275-L277](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L275-L277)


- 
```
File: contracts/libraries/SystemContractHelper.sol

299    function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L299-L305](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L299-L305)


- 
```
File: contracts/libraries/SystemContractHelper.sol

153    function packPrecompileParams(
154            uint32 _inputMemoryOffset,
155            uint32 _inputMemoryLength,
156            uint32 _outputMemoryOffset,
157            uint32 _outputMemoryLength,
158            uint64 _perPrecompileInterpreted
159        ) internal pure returns (uint256 rawParams) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L153-L165](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L153-L165)


- 
```
File: contracts/libraries/TransactionHelper.sol

96    function isEthToken(uint256 _addr) internal pure returns (bool) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L96-L98](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L96-L98)


- 
```
File: contracts/libraries/UnsafeBytesCalldata.sol

28    function readUint32(bytes calldata _bytes, uint256 _start) internal pure returns (uint32 result) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L28-L33](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L28-L33)


- 
```
File: contracts/libraries/Utils.sol

34    function safeCastToU24(uint256 _x) internal pure returns (uint24) 
```
 is never used and should be removed

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L34-L38](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L34-L38)


</details>

# 



## Lack of Explicit Visibility Declaration in State Variables
- Severity: Non-Critical
- Confidence: High

### Description
This detector identifies state variables that are missing explicit visibility declarations. By default, without explicit declaration, the visibility of such variables is set to 'internal'. While this does not necessarily lead to vulnerabilities, explicitly declaring the visibility of each state variable can improve code readability and maintainability. Therefore, it is recommended to always specify the visibility, even when it's the default 'internal'.

<details>

<summary>
There are 9 instances of this issue:

</summary>

###
- 
```
File: contracts/vendor/AddressAliasHelper.sol

24    uint160 constant offset = uint160(0x1111000000000000000000000000000000001111)
```
State variable lacks explicit visibility.
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L24)


- 
```
File: contracts/bridge/L2StandardERC20.sol

24    ERC20Getters availableGetters
```
State variable lacks explicit visibility.
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L24)


- 
```
File: contracts/AccountCodeStorage.sol

25    bytes32 constant EMPTY_STRING_KECCAK = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470
```
State variable lacks explicit visibility.
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L25](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L25)


- 
```
File: contracts/NonceHolder.sol

30    uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128
```
State variable lacks explicit visibility.
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L30](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L30)


- 
```
File: contracts/NonceHolder.sol

33    uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32
```
State variable lacks explicit visibility.
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L33](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L33)


- 
```
File: contracts/libraries/TransactionHelper.sol

84    bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)")
```
State variable lacks explicit visibility.
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L84](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L84)


- 
```
File: contracts/libraries/TransactionHelper.sol

86    bytes32 constant EIP712_TRANSACTION_TYPE_HASH =
87            keccak256(
88                "Transaction(uint256 txType,uint256 from,uint256 to,uint256 gasLimit,uint256 gasPerPubdataByteLimit,uint256 maxFeePerGas,uint256 maxPriorityFeePerGas,uint256 paymaster,uint256 nonce,uint256 value,bytes data,bytes32[] factoryDeps,bytes paymasterInput)"
89            )
```
State variable lacks explicit visibility.
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L86-L89](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L86-L89)


- 
```
File: contracts/libraries/Utils.sol

15    bytes32 constant IS_CONSTRUCTOR_BYTECODE_HASH_BIT_MASK =
16            0x00ff000000000000000000000000000000000000000000000000000000000000
```
State variable lacks explicit visibility.
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L15-L16](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L15-L16)


- 
```
File: contracts/libraries/Utils.sol

19    bytes32 constant SET_IS_CONSTRUCTOR_MARKER_BIT_MASK =
20            0x0001000000000000000000000000000000000000000000000000000000000000
```
State variable lacks explicit visibility.
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L19-L20](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L19-L20)


</details>

# 



## Function names should differ
- Severity: Non-Critical
- Confidence: High

### Description
In Solidity, while function overriding allows for functions with the same name to coexist, it is advisable to avoid this practice to enhance code readability and maintainability. Having multiple functions with the same name, even with different parameters or in inherited contracts, can cause confusion and increase the likelihood of errors during development, testing, and debugging.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

178    function deposit(
179            address _l2Receiver,
180            address _l1Token,
181            uint256 _amount,
182            uint256 _l2TxGasLimit,
183            uint256 _l2TxGasPerPubdataByte,
184            address _refundRecipient
185        ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) 
```
```
/// @audit: function used on lines  146
```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L178-L214](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L178-L214)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

112    function functionCall(
113            address target,
114            bytes memory data,
115            string memory errorMessage
116        ) internal returns (bytes memory) 
```
```
/// @audit: function used on lines  93
```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L112-L118](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L112-L118)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

151    function functionCallWithValue(
152            address target,
153            bytes memory data,
154            uint256 value,
155            string memory errorMessage
156        ) internal returns (bytes memory) 
```
```
/// @audit: function used on lines  131
```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L151-L171](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L151-L171)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

198    function functionStaticCall(
199            address target,
200            bytes memory data,
201            string memory errorMessage
202        ) internal view returns (bytes memory) 
```
```
/// @audit: function used on lines  179
```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L198-L211](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L198-L211)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

237    function functionDelegateCall(
238            address target,
239            bytes memory data,
240            string memory errorMessage
241        ) internal returns (bytes memory) 
```
```
/// @audit: function used on lines  219
```
[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L237-L250](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L237-L250)


</details>

# 





## Inconsistent usage of require/error
- Severity: Non-Critical
- Confidence: High

### Description
Some parts of the codebase use require statements, while others use custom errors. Consider refactoring the code to use the same approach: the following findings represent the minority of require vs error, and they show one occurrence in each contract, for brevity.

<details>

<summary>
There are 10 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

39    contract L1WethBridge is IL1Bridge, AllowListed, ReentrancyGuard 
```

```
File: code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

225    revert("Method not supported. Failed deposit funds are sent to the L2 refund recipient address.")
```
The contract `L1WethBridge` uses both `require` and custom `revert` for error handling.

There are 13 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L39-L316](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L39-L316)


- 
```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

20    contract ExecutorFacet is Base, IExecutor 
```

```
File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol

161    revert("ul")
```
The contract `ExecutorFacet` uses both `require` and custom `revert` for error handling.

There are 36 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L20-L473](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L20-L473)


- 
```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

13    contract L2StandardERC20 is ERC20PermitUpgradeable, IL2StandardToken 
```

```
File: code/system-contracts/contracts/bridge/L2StandardERC20.sol

49    require(_l1Address != address(0), "in6")
```
The contract `L2StandardERC20` uses both `require` and custom `revert` for error handling.

There are 2 `require` statement(s) and 3 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L13-L152](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L13-L152)


- 
```
File: code/system-contracts/contracts/bridge/L2Weth.sol

23    contract L2Weth is ERC20PermitUpgradeable, IL2Weth, IL2StandardToken 
```

```
File: code/system-contracts/contracts/bridge/L2Weth.sol

73    revert("bridgeMint is not implemented! Use deposit/depositTo methods instead.")
```
The contract `L2Weth` uses both `require` and custom `revert` for error handling.

There are 5 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L23-L116](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L23-L116)


- 
```
File: code/system-contracts/contracts/BootloaderUtilities.sol

18    contract BootloaderUtilities is IBootloaderUtilities 
```

```
File: code/system-contracts/contracts/BootloaderUtilities.sol

39    revert("Unsupported tx type")
```
The contract `BootloaderUtilities` uses both `require` and custom `revert` for error handling.

There are 3 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L18-L322](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L18-L322)


- 
```
File: code/system-contracts/contracts/Compressor.sol

36    contract Compressor is ICompressor, ISystemContract 
```

```
File: code/system-contracts/contracts/Compressor.sol

243    revert("unsupported operation")
```
The contract `Compressor` uses both `require` and custom `revert` for error handling.

There are 13 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L36-L256](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L36-L256)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

29    contract NonceHolder is INonceHolder, ISystemContract 
```

```
File: code/system-contracts/contracts/NonceHolder.sol

165    revert("Reusing the same nonce twice")
```
The contract `NonceHolder` uses both `require` and custom `revert` for error handling.

There are 5 `require` statement(s) and 2 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L29-L178](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L29-L178)


- 
```
File: code/system-contracts/contracts/SystemContext.sol

19    contract SystemContext is ISystemContext, ISystemContextDeprecated, ISystemContract 
```

```
File: code/system-contracts/contracts/SystemContext.sol

367    revert("Invalid new L2 block number")
```
The contract `SystemContext` uses both `require` and custom `revert` for error handling.

There are 16 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L19-L486](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L19-L486)


- 
```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

80    library TransactionHelper 
```

```
File: code/system-contracts/contracts/libraries/TransactionHelper.sol

114    revert("Encoding unsupported tx")
```
The contract `TransactionHelper` uses both `require` and custom `revert` for error handling.

There are 2 `require` statement(s) and 2 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L80-L416](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L80-L416)


- 
```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

11    library Address 
```

```
File: code/system-contracts/contracts/openzeppelin/utils/Address.sol

307    revert(errorMessage)
```
The contract `Address` uses both `require` and custom `revert` for error handling.

There are 4 `require` statement(s) and 1 custom `revert` statement(s).

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L11-L310](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L11-L310)


</details>

# 



## Consider Disable Ownership Renouncement in Ownable Contracts
- Severity: Non-Critical
- Confidence: High

### Description
Ownership renouncement is a feature provided by the Ownable contract in various smart contract frameworks, such as OpenZeppelin. It allows the contract owner to transfer ownership to another address, relinquishing their control over the contract. However, in certain cases, it may be undesirable or unnecessary to allow ownership renouncement. 

It is important to carefully consider the implications of allowing ownership renouncement. Disabling renouncement can provide additional security and prevent potential risks associated with unintended ownership transfers.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
- 
```
File: code/contracts/ethereum/contracts/common/AllowList.sol

20    contract AllowList is IAllowList, Ownable2Step 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L20-L141](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L20-L141)


- 
```
File: code/contracts/ethereum/contracts/governance/Governance.sol

22    contract Governance is IGovernance, Ownable2Step 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L22-L265](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L22-L265)


- 
```
File: code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

22    contract ValidatorTimelock is IExecutor, Ownable2Step 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L22-L157](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L22-L157)


</details>

# 



## Variable names too similar
- Severity: Non-Critical
- Confidence: Medium

### Description
Detect variables with names that are too similar.

<details>

<summary>
There are 9 instances of this issue:

</summary>

###
- Variable 
```
File: contracts/bridge/L1WethBridge.sol

66    address payable _l1WethAddress
```
 is too similar to 
```
File: contracts/bridge/L1WethBridge.sol

85    address _l2WethAddress
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L66](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L66)


- Variable 
```
File: contracts/bridge/L1WethBridge.sol

46    address payable public immutable l1WethAddress
```
 is too similar to 
```
File: contracts/bridge/L1WethBridge.sol

58    address public l2WethAddress
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L46](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L46)


- Variable 
```
File: contracts/bridge/interfaces/IL2Bridge.sol

18    address _l1Receiver
```
 is too similar to 
```
File: contracts/bridge/interfaces/IL2Bridge.sol

11    address _l2Receiver
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol#L18](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2Bridge.sol#L18)


- Variable 
```
File: contracts/bridge/interfaces/IL2WethBridge.sol

10    address _l1WethAddress
```
 is too similar to 
```
File: contracts/bridge/interfaces/IL2WethBridge.sol

11    address _l2WethAddress
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol#L10](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/interfaces/IL2WethBridge.sol#L10)


- Variable 
```
File: contracts/zksync/facets/Executor.sol

230    bytes32 _systemContractUpgradeTxHash
```
 is too similar to 
```
File: contracts/zksync/facets/Executor.sol

189    bytes32 systemContractsUpgradeTxHash = s.l2SystemContractsUpgradeTxHash
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L230](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L230)


- Variable 
```
File: contracts/bridge/L2ERC20Bridge.sol

108    address _l1Receiver
```
 is too similar to 
```
File: contracts/bridge/L2ERC20Bridge.sol

67    address _l2Receiver
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L108](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L108)


- Variable 
```
File: contracts/bridge/L2WethBridge.sol

66    address _l1Receiver
```
 is too similar to 
```
File: contracts/bridge/L2WethBridge.sol

92    address _l2Receiver
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L66](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L66)


- Variable 
```
File: contracts/bridge/L2WethBridge.sol

49    address _l1WethAddress
```
 is too similar to 
```
File: contracts/bridge/L2WethBridge.sol

50    address _l2WethAddress
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L49](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L49)


- Variable 
```
File: contracts/bridge/L2WethBridge.sol

31    address public l1WethAddress
```
 is too similar to 
```
File: contracts/bridge/L2WethBridge.sol

34    address public l2WethAddress
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L31](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L31)


</details>

# 


## Functions that alter state should emit events
- Severity: Non-Critical
- Confidence: Medium

### Description
Functions that alter the state of the contract should emit an event to inform external observers of the change.

<details>

<summary>
There are 26 instances of this issue:

</summary>

###
- 
```
File: contracts/bridge/L1ERC20Bridge.sol

85    function initialize(
86            bytes[] calldata _factoryDeps,
87            address _l2TokenBeacon,
88            address _governor,
89            uint256 _deployBridgeImplementationFee,
90            uint256 _deployBridgeProxyFee
91        ) external payable reentrancyGuardInitializer 
```
 The function `initialize` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L85-L133](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L85-L133)


- 
```
File: contracts/bridge/L1ERC20Bridge.sol

342    function _verifyDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal 
```
 The function `_verifyDepositLimit` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L342-L352](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L342-L352)


- 
```
File: contracts/bridge/L1WethBridge.sol

83    function initialize(
84            bytes[] calldata _factoryDeps,
85            address _l2WethAddress,
86            address _governor,
87            uint256 _deployBridgeImplementationFee,
88            uint256 _deployBridgeProxyFee
89        ) external payable reentrancyGuardInitializer 
```
 The function `initialize` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L83-L136](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L83-L136)


- 
```
File: contracts/common/AllowList.sol

131    function setDepositLimit(address _l1Token, bool _depositLimitation, uint256 _depositCap) external onlyOwner 
```
 The function `setDepositLimit` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L131-L134](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L131-L134)


- 
```
File: contracts/upgrades/BaseZkSyncUpgrade.sol

163    function _setL2SystemContractUpgrade(
164            IMailbox.L2CanonicalTransaction calldata _l2ProtocolUpgradeTx,
165            bytes[] calldata _factoryDeps,
166            uint256 _newProtocolVersion
167        ) internal returns (bytes32) 
```
 The function `_setL2SystemContractUpgrade` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L163-L197](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L163-L197)


- 
```
File: contracts/governance/Governance.sol

217    function _schedule(bytes32 _id, uint256 _delay) internal 
```
 The function `_schedule` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L217-L222](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L217-L222)


- 
```
File: contracts/zksync/DiamondInit.sol

57    function initialize(InitializeData calldata _initalizeData) external reentrancyGuardInitializer returns (bytes32) 
```
 The function `initialize` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L57-L92](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L57-L92)


- 
```
File: contracts/zksync/facets/Executor.sol

275    function _executeOneBatch(StoredBatchInfo memory _storedBatch, uint256 _executedBatchIdx) internal 
```
 The function `_executeOneBatch` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L275-L288](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L275-L288)


- 
```
File: contracts/zksync/facets/Mailbox.sol

277    function _verifyDepositLimit(address _depositor, uint256 _amount) internal 
```
 The function `_verifyDepositLimit` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L277-L283](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L277-L283)


- 
```
File: contracts/ContractDeployer.sol

57    function _storeAccountInfo(address _address, AccountInfo memory _newInfo) internal 
```
 The function `_storeAccountInfo` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L57-L59](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L57-L59)


- 
```
File: contracts/ImmutableSimulator.sol

36    function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override 
```
 The function `setImmutables` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L36-L46](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L36-L46)


- 
```
File: contracts/L1Messenger.sol

201    function publishPubdataAndClearState(
202            bytes calldata _totalL2ToL1PubdataAndStateDiffs
203        ) external onlyCallFromBootloader 
```
 The function `publishPubdataAndClearState` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L201-L338](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L201-L338)


- 
```
File: contracts/L2EthToken.sol

101    function _burnMsgValue() internal returns (uint256 amount) 
```
 The function `_burnMsgValue` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L101-L111](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L101-L111)


- 
```
File: contracts/NonceHolder.sol

67    function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce) 
```
 The function `increaseMinNonce` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L67-L78](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L67-L78)


- 
```
File: contracts/NonceHolder.sol

112    function incrementMinNonceIfEquals(uint256 _expectedNonce) external onlySystemCall 
```
 The function `incrementMinNonceIfEquals` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L112-L122](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L112-L122)


- 
```
File: contracts/NonceHolder.sol

137    function incrementDeploymentNonce(address _address) external onlySystemCall returns (uint256 prevDeploymentNonce) 
```
 The function `incrementDeploymentNonce` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L137-L147](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L137-L147)


- 
```
File: contracts/SystemContext.sol

89    function setTxOrigin(address _newOrigin) external onlyCallFromBootloader 
```
 The function `setTxOrigin` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L89-L91](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L89-L91)


- 
```
File: contracts/SystemContext.sol

95    function setGasPrice(uint256 _gasPrice) external onlyCallFromBootloader 
```
 The function `setGasPrice` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L95-L97](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L95-L97)


- 
```
File: contracts/SystemContext.sol

185    function _setL2BlockHash(uint256 _block, bytes32 _hash) internal 
```
 The function `_setL2BlockHash` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L185-L187](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L185-L187)


- 
```
File: contracts/SystemContext.sol

236    function _setVirtualBlock(
237            uint128 _l2BlockNumber,
238            uint128 _maxVirtualBlocksToCreate,
239            uint128 _newTimestamp
240        ) internal 
```
 The function `_setVirtualBlock` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L236-L281](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L236-L281)


- 
```
File: contracts/SystemContext.sol

287    function _setNewL2BlockData(uint128 _l2BlockNumber, uint128 _l2BlockTimestamp, bytes32 _prevL2BlockHash) internal 
```
 The function `_setNewL2BlockData` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L287-L296](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L287-L296)


- 
```
File: contracts/SystemContext.sol

375    function appendTransactionToCurrentL2Block(bytes32 _txHash) external onlyCallFromBootloader 
```
 The function `appendTransactionToCurrentL2Block` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L375-L377](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L375-L377)


- 
```
File: contracts/SystemContext.sol

418    function setNewBatch(
419            bytes32 _prevBatchHash,
420            uint128 _newTimestamp,
421            uint128 _expectedNewNumber,
422            uint256 _baseFee
423        ) external onlyCallFromBootloader 
```
 The function `setNewBatch` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L418-L441](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L418-L441)


- 
```
File: contracts/SystemContext.sol

445    function unsafeOverrideBatch(
446            uint256 _newTimestamp,
447            uint256 _number,
448            uint256 _baseFee
449        ) external onlyCallFromBootloader 
```
 The function `unsafeOverrideBatch` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L445-L454](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L445-L454)


- 
```
File: contracts/SystemContext.sol

456    function incrementTxNumberInBatch() external onlyCallFromBootloader 
```
 The function `incrementTxNumberInBatch` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L456-L458](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L456-L458)


- 
```
File: contracts/SystemContext.sol

460    function resetTxNumberInBatch() external onlyCallFromBootloader 
```
 The function `resetTxNumberInBatch` changes state but does not emit an event.

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L460-L462](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L460-L462)


</details>

# 




## Do not calculate constants
- Severity: Non-Critical
- Confidence: High

### Description
Due to how constant variables are implemented in Solidity (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas. While in most cases, the compiler will optimize these computations away, it is considered a best practice to write code that does not rely on the compiler optimization.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/NonceHolder.sol

30    uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L30](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L30)


- 
```
File: code/system-contracts/contracts/NonceHolder.sol

33    uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L33](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L33)


</details>

# 


## Empty function body
- Severity: Non-Critical
- Confidence: High

### Description
Functions with empty bodies should include comments describing their purpose.

<details>

<summary>
There are 2 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/EmptyContract.sol

14    fallback() external payable 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L14](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L14)


- 
```
File: code/system-contracts/contracts/EmptyContract.sol

16    receive() external payable 
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L16](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L16)


</details>

# 


## Override function arguments that are unused should have the variable name removed or commented out
- Severity: Non-Critical
- Confidence: High

### Description
Unused arguments in overridden functions can lead to compiler warnings andcan make the code less readable. It is a good practice to remove or comment outunused arguments in these cases.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/DefaultAccount.sol

129    Transaction calldata _transaction
```

[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L129](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L129)


</details>

# 


## Too many digits
- Severity: Non-Critical
- Confidence: Medium

### Description

Literals with many digits are difficult to read and review.


<details>

<summary>
There are 5 instances of this issue:

</summary>

###
- 
```
File: code/system-contracts/contracts/SystemContext.sol

19    contract SystemContext is ISystemContext, ISystemContextDeprecated, ISystemContract 
```
 uses literals with too many digits:
	- 
```
File: code/system-contracts/contracts/SystemContext.sol

46    uint256 public difficulty = 2500000000000000
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L19-L486](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L19-L486)


- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

13    function encodeAddress(address _val) internal pure returns (bytes memory encoded) 
```
 uses literals with too many digits:
	- 
```
File: code/system-contracts/contracts/libraries/RLPEncoder.sol

20    mstore(add(encoded, 0x20), 0x9400000000000000000000000000000000000000000000000000000000000000)
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L13-L24](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L13-L24)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

84    function hashL2Bytecode(bytes calldata _bytecode) internal view returns (bytes32 hashedBytecode) 
```
 uses literals with too many digits:
	- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

91    hashedBytecode =
92                EfficientCall.sha(_bytecode) &
93                0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L84-L98](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L84-L98)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

13    library Utils 
```
 uses literals with too many digits:
	- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

15    bytes32 constant IS_CONSTRUCTOR_BYTECODE_HASH_BIT_MASK =
16            0x00ff000000000000000000000000000000000000000000000000000000000000
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L13-L99](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L13-L99)


- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

13    library Utils 
```
 uses literals with too many digits:
	- 
```
File: code/system-contracts/contracts/libraries/Utils.sol

19    bytes32 constant SET_IS_CONSTRUCTOR_MARKER_BIT_MASK =
20            0x0001000000000000000000000000000000000000000000000000000000000000
```


[https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L13-L99](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L13-L99)


</details>

# 