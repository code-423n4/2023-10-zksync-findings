## Comparison after addition can save one addition operation

```solidity
require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
```
It is recommended to modify the above code to

```solidity
totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
require(totalDepositedAmountPerUser[_l1Token][_depositor] <= limitData.depositCap, "d1");
```

Even if it fails, the status will be rolled back without additional deduction of amount.

#### link
https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L347-L348
https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L279-L280