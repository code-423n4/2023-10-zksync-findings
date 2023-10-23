0. Gas Inefficiency - Throughout the codebase, there is a recurring pattern that involves the declaration and explicit initialization of a loop counter variable.

```solidity
for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
```

Summary: 

In the code, a loop counter variable `i` is declared and explicitly initialized to zero (`uint256 i = 0;`). This approach is inefficient from a gas consumption perspective because it results in additional gas costs associated with variable initialization, which may not be necessary.

Recommendation: 

To optimize gas usage and improve code efficiency, it is advisable to implicitly initialize the loop counter variable `i` to its default value of zero, as shown below:

```solidity
for (uint256 i; i < facetsLen; i.uncheckedInc()) {
```

=======
=======

1. GAS: might save on gas by caching the storage `s.totalDepositedAmountPerUser[_depositor]` for the computation here

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L279

Summary:

Within this function, the total deposited amount per user, denoted as s.totalDepositedAmountPerUser[_depositor], is computed twice. It is first calculated in the require statement to check the deposit limit, and then it is incremented by _amount if the condition is met. This results in two separate operations that read from and write to storage for the same value, potentially incurring additional gas costs.

```solidity
    function _verifyDepositLimit(address _depositor, uint256 _amount) internal {
        IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH
        if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH

        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
        s.totalDepositedAmountPerUser[_depositor] += _amount;
    }
```

Recommendation:

To optimize gas usage, it is suggested to cache the value of `s.totalDepositedAmountPerUser[_depositor]` before the require statement. This way, the value is read from storage once and can be used for the require() check. The reason for caching s.totalDepositedAmountPerUser[_depositor] before the calculation is to avoid performing the same storage read operation twice, as it is used both in the require statement for a comparison and in the subsequent addition of _amount. Caching the value reduces the need for redundant storage reads and improves gas efficiency.
