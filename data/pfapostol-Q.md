## Summary table

<a name="TOP"></a>

The QA Audit Report presents a comprehensive analysis of the smart contract codebase, focusing on critical aspects related to security, functionality, and best practices. The report addresses key issues discovered during the audit, providing detailed descriptions of each issue, its potential impact, and recommended fixes. The goal is to ensure the solidity of the codebase, adherence to standards, and the mitigation of potential risks. The report is structured for clarity, facilitating a deeper understanding of the identified issues and offering precise solutions to enhance the overall robustness of the smart contracts.

|   Index    | Issue                                                                                     | Instances |
| :--------: | :---------------------------------------------------------------------------------------- | :-------: |
| [1](#N-01) | [Check Success of Static Calls in `_getERC20Getters` Function](#N-01)                     |     1     |
| [2](#N-02) | [Wrong comment for `MailboxFacet`:`_parseL2WithdrawalMessage`](#N-02)                     |     2     |
| [3](#N-03) | [Wrong NatSpec tag in comment](#N-03)                                                     |     1     |
| [4](#N-04) | [Missing Implementation of `IBase` Interface in `Base` Contract](#N-04)                   |     1     |
| [5](#N-05) | [Potential Incompatibility with Some ERC-20 Tokens in `_getERC20Getters` Function](#N-05) |     1     |
| [6](#N-06) | [Unenforced Initialization in `L1WethBridge` Contract](#N-06)                             |     3     |
| [7](#N-07) | [Lack of Boundaries in Setter Functions](#N-07)                                           |     2     |
| [8](#N-08) | [Lack of Documentation for Required Approval in `deposit` Function](#N-08)                |     1     |
| [9](#N-09) | [Deprecation of `abi.encodePacked` in Solidity 0.9.0](#N-09)                              |     4     |


16 instances over 9 issues

---

## 1. Check Success of Static Calls in `_getERC20Getters` Function

<a name="N-01"></a>
[To the top](#TOP)

### Description:
In the `_getERC20Getters` function of the `L1ERC20Bridge` contract, the success of static calls to retrieve `name`, `symbol`, and `decimals` from the ERC-20 token is not checked. It is crucial to ensure that these calls are successful to avoid unexpected behavior or errors.

### Recommendation:
Include a check for the success of each static call in the _getERC20Getters function. Verify that the calls return the expected data and handle potential failures gracefully. This helps prevent unintended consequences in cases where the token does not implement these optional functions or encounters an issue during the static call.


### Instance:

 - [L1ERC20Bridge.sol L240-L245](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L240-L245)

### Fix:    

```solidity
function _getERC20Getters(address _token) internal view returns (bytes memory data) {
    (bool success1, bytes memory data1) = _token.staticcall(abi.encodeCall(IERC20Metadata.name, ()));
    require(success1, "Failed to retrieve token name");

    (bool success2, bytes memory data2) = _token.staticcall(abi.encodeCall(IERC20Metadata.symbol, ()));
    require(success2, "Failed to retrieve token symbol");

    (bool success3, bytes memory data3) = _token.staticcall(abi.encodeCall(IERC20Metadata.decimals, ()));
    require(success3, "Failed to retrieve token decimals");

    data = abi.encode(data1, data2, data3);
}
```

---

## 2. Wrong comment for `MailboxFacet`:`_parseL2WithdrawalMessage` and `ExecutorFacet`:`_setBit`

<a name="N-02"></a>
[To the top](#TOP)

### Description:
The comments within the MailboxFacet contract's _parseL2WithdrawalMessage function and the ExecutorFacet contract's _setBit function contain inaccuracies.

### Instances:

 - [Mailbox.sol L418](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L418C36-L418C36)

```solidity
// = 4 + 20 + 32 + 32 + _additionalData.length >= 68 (bytes).
```

The specified value of 68 should be corrected to 88 for accurate representation of the byte length.

 -[Executor.sol L476-L479](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L476-L479)

```solidity
/// @notice Sets the given bit in {_num} at index {_index} to 1.
function _setBit(uint256 _bitMap, uint8 _index) internal pure returns (uint256) {
    return _bitMap | (1 << _index);
}
```

The variable name should be adjusted from {_num} to {_bitMap} for clarity and consistency with the function's parameter names.

---

## 3. Wrong NatSpec tag in comment

<a name="N-03"></a>
[To the top](#TOP)

### Description
The Executor.sol contract, employs an inaccurate NatSpec tag within a comment.

### Instances:

 - [Executor.sol L272-L273](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L272-L273)

```solidity
/// @dev _executedBatchIdx is an index in the array of the batches that we want to execute together
```

It is recommended to replace the @dev tag with @param to accurately reflect variable.

### Fix:

```solidity
/// @param _executedBatchIdx is an index in the array of the batches that we want to execute together
```

---

## 4. Missing Implementation of `IBase` Interface in `Base` Contract

<a name="N-04"></a>
[To the top](#TOP)

### Description:
Although there is a naming convention that might suggest a relationship between the `Base` contract and the `IBase` interface, but it lacks the implementation of the required function `getName()`. This creates a mismatch between the interface and the contract.

### Impact:
If other contracts or external systems rely on the `Base` contract to fulfill the `IBase` interface, it could lead to runtime errors or unexpected behavior due to the missing implementation. (Not required in the current implementation of the contracts, but possible in future use.)

### Instances:

- [Base.sol L14](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L14)

```solidity
contract Base is ReentrancyGuard, AllowListed {
```

### Fix:
Implement the `getName()` function in the `Base` contract to adhere to the `IBase` interface.


---

## 5. Potential Incompatibility with Some ERC-20 Tokens in `_getERC20Getters` Function

<a name="N-05"></a>
[To the top](#TOP)

### Description:
In the context of the `_getERC20Getters` function in the `L1ERC20Bridge` contract, it's important to note that the `name`, `symbol`, and `decimals` functions are not part of the ERC-20 standard. The ERC-20 standard defines a set of mandatory functions, but details such as token `name`, `symbol`, and `decimals` are not included.

The ERC-20 standard, as defined in the Ethereum Improvement Proposal (EIP) 20, specifies the following mandatory functions for ERC-20 tokens:

- totalSupply
- balanceOf
- transfer
- transferFrom
- approve
- allowance
  
Reference: [EIP-20: ERC-20 Token Standard](https://eips.ethereum.org/EIPS/eip-20)

 - [L1ERC20Bridge.sol L240-L245](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L240-L245)

```solidity
function _getERC20Getters(address _token) internal view returns (bytes memory data) {
    // These calls assume that the ERC-20 token implements name, symbol, and decimals functions.
    // However, not all ERC-20 tokens have these functions, which can lead to issues.
    (, bytes memory data1) = _token.staticcall(abi.encodeCall(IERC20Metadata.name, ()));
    (, bytes memory data2) = _token.staticcall(abi.encodeCall(IERC20Metadata.symbol, ()));
    (, bytes memory data3) = _token.staticcall(abi.encodeCall(IERC20Metadata.decimals, ()));
    data = abi.encode(data1, data2, data3);
}
```

### Impact:
This design assumes that all ERC-20 tokens have implementations for `name`, `symbol`, and `decimals`. If a token does not implement these functions, the calls will fail, leading to potential issues such as failed transactions and unnecessary gas consumption.

---

## 6. Unenforced Initialization in `L1WethBridge` Contract

<a name="N-06"></a>
[To the top](#TOP)

### Description:
The `initialize` function, responsible for initializing the `L1WethBridge` contract, is not explicitly enforced to be called before the execution of main logical functions such as `deposit`. The absence of this enforcement might lead to unexpected behavior if the contract is used without proper initialization.

 - [L1ERC20Bridge.sol L144-L152](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L144-L152), [L1ERC20Bridge.sol L176-L183](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L176-L183), [L1WethBridge.sol L159-L166](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L159-L166)

```solidity
function deposit(address _l2Receiver, address _l1Token, uint256 _amount, uint256 _l2TxGasLimit, uint256 _l2TxGasPerPubdataByte, address _refundRecipient) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {
    require(_l1Token == l1WethAddress, "Invalid L1 token address");
    require(_amount != 0, "Amount cannot be zero");

    // Deposit WETH tokens from the depositor address to the smart contract address
    IERC20(l1WethAddress).safeTransferFrom(msg.sender, address(this), _amount);
    // Unwrap WETH tokens (smart contract address receives the equivalent amount of ETH)
    IWETH9(l1WethAddress).withdraw(_amount);

    // Request the finalization of the deposit on the L2 side
    bytes memory l2TxCalldata = _getDepositL2Calldata(msg.sender, _l2Receiver, l1WethAddress, _amount);

    // If the refund recipient is not specified, the refund will be sent to the sender of the transaction.
    // Otherwise, the refund will be sent to the specified address.
    // If the recipient is a contract on L1, the address alias will be applied.
    address refundRecipient = _refundRecipient;
    if (_refundRecipient == address(0)) {
        refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
    }
    txHash = zkSync.requestL2Transaction{value: _amount + msg.value}(
        l2Bridge,
        _amount,
        l2TxCalldata,
        _l2TxGasLimit,
        _l2TxGasPerPubdataByte,
        new bytes[](0),
        refundRecipient
    );

    emit DepositInitiated(txHash, msg.sender, _l2Receiver, _l1Token, _amount);
}
```

### Impact:
If the `initialize` function is not called before executing critical functions such as `deposit`, the contract's essential setup, including deployment of L2 bridge components, may be incomplete. This could lead to unexpected behavior, potential vulnerabilities, or operational failures. Without proper initialization, the contract might lack the required configuration, risking the mismanagement of funds during deposit and withdrawal operations.

### Mitigation:
Enforce a check at the beginning of the deposit function to ensure that the contract has been initialized by calling the `initialize` function. This check should include verifying that the necessary parameters, such as `l2Bridge` and `l2WethAddress`, have been properly set during initialization.

### Fix:

```solidity
function deposit(address _l2Receiver, address _l1Token, uint256 _amount, uint256 _l2TxGasLimit, uint256 _l2TxGasPerPubdataByte, address _refundRecipient) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {
    require(initialized, "Contract not initialized. Call initialize function first.");
    // rest of the deposit function...
}
```

---

## 7. Lack of Boundaries in Setter Functions

<a name="N-07"></a>
[To the top](#TOP)

### Description:
The setter functions `setExecutionDelay` and `setDepositLimit` lack boundary checks on the values they receive. It's important to ensure that the inputs are within appropriate limits to prevent unintended behavior or misuse.

###  Impact:
Without proper boundaries, these functions may allow setting values that could lead to unexpected behavior, misuse, or vulnerabilities.

### Instances:

1. For setExecutionDelay function:

Introduce boundaries to ensure that the _executionDelay value is within acceptable limits.

 -[ValidatorTimelock.sol L59-L62](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L59-L62)

```solidity
function setExecutionDelay(uint32 _executionDelay) external onlyOwner {
    require(_executionDelay <= MAX_EXECUTION_DELAY, "Invalid execution delay");
    executionDelay = _executionDelay;
    emit NewExecutionDelay(_executionDelay);
}
```

1. For setDepositLimit function:

Add proper boundary checks to ensure that _depositCap is within acceptable limits.

 -[AllowList.sol L129-L132](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L129-L132)

```solidity
function setDepositLimit(address _l1Token, bool _depositLimitation, uint256 _depositCap) external onlyOwner {
    require(_depositCap <= MAX_DEPOSIT_CAP, "Invalid deposit cap");
    tokenDeposit[_l1Token].depositLimitation = _depositLimitation;
    tokenDeposit[_l1Token].depositCap = _depositCap;
}
```

---

## 8. Lack of Documentation for Required Approval in `deposit` Function

<a name="N-08"></a>
[To the top](#TOP)

### Description:
The `deposit` function requires users to approve the transfer of tokens to the `L1ERC20Bridge` contract before calling, regardless of the token being used. However, there is no explicit comment or documentation within the function indicating this requirement. This lack of documentation might lead to confusion for developers and users, as they may not be aware of the necessity to pre-approve the token transfer for successful execution of the `deposit` function.

### Impact:
The absence of explicit documentation regarding the need for users to pre-approve the transfer of tokens to the `L1ERC20Bridge` contract might result in developers and users overlooking this requirement. As a result, users attempting to call the `deposit` function without prior approval may encounter transaction failures or unexpected behavior, leading to a suboptimal user experience.

### Mitigation:
Include a `@dev` comment within the deposit function, clearly stating that users must pre-approve the transfer of the specified token to the `L1ERC20Bridge` contract before calling the function. This comment will serve as documentation, providing essential guidance to developers and users to ensure the correct usage of the function.

 -[L1ERC20Bridge.sol L154-L176](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L154-L176)

 ```solidity
 /// @notice Initiates a deposit by locking funds on the contract and sending the request
    /// of processing an L2 transaction where tokens would be minted
    /// @param _l2Receiver The account address that should receive funds on L2
    /// @param _l1Token The L1 token address which is deposited
    /// @param _amount The total amount of tokens to be bridged
    /// @param _l2TxGasLimit The L2 gas limit to be used in the corresponding L2 transaction
    /// @param _l2TxGasPerPubdataByte The gasPerPubdataByteLimit to be used in the corresponding L2 transaction
    /// @param _refundRecipient The address on L2 that will receive the refund for the transaction.
    /// @dev If the L2 deposit finalization transaction fails, the `_refundRecipient` will receive the `_l2Value`.
    /// Please note, the contract may change the refund recipient's address to eliminate sending funds to addresses
    /// out of control.
    /// - If `_refundRecipient` is a contract on L1, the refund will be sent to the aliased `_refundRecipient`.
    /// - If `_refundRecipient` is set to `address(0)` and the sender has NO deployed bytecode on L1, the refund will
    /// be sent to the `msg.sender` address.
    /// - If `_refundRecipient` is set to `address(0)` and the sender has deployed bytecode on L1, the refund will be
    /// sent to the aliased `msg.sender` address.
    /// @dev The address aliasing of L1 contracts as refund recipient on L2 is necessary to guarantee that the funds
    /// are controllable through the Mailbox, since the Mailbox applies address aliasing to the from address for the
    /// L2 tx if the L1 msg.sender is a contract. Without address aliasing for L1 contracts as refund recipients they
    /// would not be able to make proper L2 tx requests through the Mailbox to use or withdraw the funds from L2, and
    /// the funds would be lost.
    /// @return l2TxHash The L2 transaction hash of deposit finalization
    function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        uint256 _l2TxGasLimit,
        uint256 _l2TxGasPerPubdataByte,
        address _refundRecipient
    ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) { // @audit-qa Add comment about approval
 ```

---

## 9. Deprecation of `abi.encodePacked` in Solidity 0.9.0

<a name="N-09"></a>
[To the top](#TOP)

### Description:

The code employs abi.encodePacked, which may face [deprecation in future Solidity versions](https://github.com/ethereum/solidity/issues/11593), including the anticipated 0.9.0 release. Although the release date of Solidity 0.9.0 is uncertain, it's advisable to address potential deprecations proactively.

### Impact:
In future Solidity versions, particularly 0.9.0, the use of abi.encodePacked might become deprecated, leading to compilation issues or unexpected behavior. A preemptive resolution is recommended to ensure continued compatibility and adherence to best practices.

### Fix:
To guard against potential deprecation issues, consider replacing abi.encodePacked with bytes.concat. This adjustment helps future-proof the codebase, ensuring smooth transitions to newer Solidity versions.

### Instances:

 -[Mailbox.sol L132](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L132)

```solidity
     abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBatch, _log.sender, _log.key, _log.value)
```

 -[Executor.sol L384-L390](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L384-L390), [Executor.sol L445](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L445), [Executor.sol L436-L441](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L436-L441)

```solidity
abi.encodePacked(
    _prevBatchCommitment,
    _currentBatchCommitment,
    _verifierParams.recursionNodeLevelVkHash,
    _verifierParams.recursionLeafLevelVkHash
)
...
return abi.encodePacked(s.zkPorterIsAvailable, s.l2BootloaderBytecodeHash, s.l2DefaultAccountBytecodeHash);
...
abi.encodePacked(
    _batch.indexRepeatedStorageChanges,
    _batch.newStateRoot,
    uint64(0), // index repeated storage changes in zkPorter
    bytes32(0) // zkPorter batch hash
);
```