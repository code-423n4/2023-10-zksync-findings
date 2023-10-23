### [Low-01] While updating mapping `s.totalDepositedAmountPerUser` for a `depositor`, the deposited amount includes `l2GasPrice`

While requesting a L2 Transaction via `requestL2Transaction()` caller includes `msg.value` with Tx,
Where technically `msg.sender = _l2Value + baseCost`
where baseCost = l2GasPrice * _l2GasLimit
```solidity
 {
            params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);
            uint256 baseCost = params.l2GasPrice * _l2GasLimit; 
            require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost
        }
```

In function `_verifyDepositLimit()` mapping `s.totalDepositedAmountPerUser` get updated where it update mapping with value `_amount` which is equal to `msg.value`

i.e here L2GasCost not substaracted while updating depositor deposit balance, which lead to an accounting error while L2 tx fail and depositor request for refund.

```solidity
    function requestL2Transaction(
        address _contractL2,
        uint256 _l2Value,
        bytes calldata _calldata,
        uint256 _l2GasLimit,
        uint256 _l2GasPerPubdataByteLimit,
        bytes[] calldata _factoryDeps,
        address _refundRecipient
    ) external payable nonReentrant senderCanCallFunction(s.allowList) returns (bytes32 canonicalTxHash) {
        // Change the sender address if it is a smart contract to prevent address collision between L1 and L2.
        // Please note, currently zkSync address derivation is different from Ethereum one, but it may be changed in the future.
        address sender = msg.sender; //@audit
        if (sender != tx.origin) {
            sender = AddressAliasHelper.applyL1ToL2Alias(msg.sender);
        }
        _verifyDepositLimit(msg.sender, msg.value);
        .....
        .....
    }
```
```solidity
    function _verifyDepositLimit(address _depositor, uint256 _amount) internal {
        IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH
        if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH

        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2"); // @audit
        s.totalDepositedAmountPerUser[_depositor] += _amount; // @audit-issue here _amount includes l2GasPrie as well
    }
```

```
file:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L261

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L275-L281
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L306
```