# Missing Documentation for Some Functions
#### Summary 
***Docstrings and inline comments are not well *detailed* in several parts of the codebase with sensitive functionality.***

### Impact
***[_serializeL2Transaction()](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L275), [_wrietPriortyOp()](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L356), [_hashFactoryDeps()](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L283),[_requestL2Transaction](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L283), [_verifyDepositLimit](). It is possible that these methods overestimate/underestimate, or are implemented incorrectly. However, 
 relevant documentation was insufficient to validate these***

##### Recommendation
>Consider including thorough docstrings and inline explanations with references to relevant sources files or documentation, allowing maintainers to verify the implementation and their correct usage.


# Revert messages are not informative enough

#### Summary
***For instance, instead of "*pm*" and "*dm*," you could use messages like "Permission Denied" and "Data Mismatch" to provide more context. This not only enhances the user experience but also assists developers in quickly identifying and resolving issues.***

### Impact 
***This poor practice affects all the contracts in the scope of zksync if not all but most contracts. I wish I could still get to know why the revert message is that short, an auditor can have a hard time understanding what the revert messages are for different contracts***

##### Recommendation
>Since you want your revert functions to be short or optimized why not use a custom error to solve that? Consider using informative error messages or custom errors throughout the contracts.


# No emission in SetDepositLimit

##### Summary 
***This is not truly reflective of happenings when an operator sets a limit of deposits so when he wants to 
 make any withdrawal he can check the limit he has deposited before to avoid ignorance***

### Impact
***This shortchanges various off-chain tooling, monitoring, reporting, and frontend services that may rely on events to adequately capture real-time activities of the contracts. It may even be critical for security monitoring so the project can respond adequately if events are sufficiently detailed and informative. Any suspicious emissions can allow protocol to react quickly***

#### Recommendation
>Recommendation to add an event for the case detailed above [_setDeposiitLimit()](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L129)


# Poor Visibility in L1ERC20Bridge.sol

### Impact
***[deposit()](https://github.com/code-423n4/2023-10-zksync/blame/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L144) and [deposit()](https://github.com/code-423n4/2023-10-zksync/blame/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L176): This difference in visibility might not be a problem per se, but it's worth noting that the external function acts as a "wrapper" that then calls the public function. This design may introduce some complexity without clear benefits.***

##### Recommendation
>*Remove The External Function:*
***If the external function serves no specific function other than forwarding the call to the public function, consider removing it. Directly calling the public function can simplify the code.***
*Combine functionality:*
***Since there are legitimate use cases for both the external and public functions, consider consolidating their functionality into a single function with optional parameters. This can make the code more concise and easier to understand.
Here is an example of how you can consolidate the functionality into a single transaction with optional parameters:***

```
function deposit(
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
    _verifyDepositLimit(_l1Token, msg.sender, _amount, false);

    bytes memory l2TxCalldata = _getDepositL2Calldata(msg.sender, _l2Receiver, _l1Token, amount);
    // If the refund recipient is not specified, the refund will be sent to the sender of the transaction.
    // Otherwise, the refund will be sent to the specified address.
    // If the recipient is a contract on L1, the address alias will be applied.
    address refundRecipient = _refundRecipient;
    if (_refundRecipient == address(0)) {
        refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
    }
    l2TxHash = zkSync.requestL2Transaction{value: msg.value}(
        l2Bridge,
        0, // L2 msg.value
        l2TxCalldata,
        _l2TxGasLimit,
        _l2TxGasPerPubdataByte,
        new bytes[](0),
        refundRecipient
    );

    // Save the deposited amount to claim funds on L1 if the deposit failed on L2
    depositAmount[msg.sender][_l1Token][l2TxHash] = amount;

    emit DepositInitiated(l2TxHash, msg.sender, _l2Receiver, _l1Token, amount);
}
```

# Eth Tokens at Risk Dues to lack of address(0) Chech in deposit()
### Summary
***The `deposit` function does not check if the L2Reciever is address(0). For some tokens like USDC and USDT, it does check if the sender and receiver are not address(0) and revert it( so it is not necessary for the function to check it), but ETH token does not check for that, and will not revert to 0 address.

# Vulnerability Details
Since the dev described that the ETH token would be present in the contract somewhat the _deposit should check if any of the L2 receivers are address(0).



# Unessecary Function in L2WETHBridge

## Summary
***The [proveL2LogInclusion](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L64) and [proveL2MessageInclusion](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L49C14-L49C37) functions appear to have very similar functionality. In the second function, `proveL2MessageInclusion` there is a call to `_proveL2LogInclusion`  with a message argument, and it returns a boolean value. However, the function name suggests that it's about proving message inclusion, not log inclusion. This can be a source of confusion***

### Impact
***I must say that the [proveL2LogInclusion](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L64) function and [proveL2MessageInclusion](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L49C14-L49C37) function is performing the same mechanism in the https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L49C14-L49C37 which should not be necessary. Just one function can do it instead of a separate function performing the same mechanism.***

# Tools Used
Manual Review

##### Recommendation

>***One function should be used since you want to check if the return value is valid proof you can use the [proveL2MessageInclusion](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L49C14-L49C37).***








