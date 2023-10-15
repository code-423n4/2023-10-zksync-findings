QA1. The constructor of AllowList fails to pass arguments to the base contract ``Ownable``.
Similarly, the constructor of ValidatorTimeLock  fails to pass arguments to the base contract ``Ownable``.

[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L31-L33](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L31-L33)

Mitigation:

```diff
- constructor(address _initialOwner) {
+ constructor(address _initialOwner) Ownable(_initialOwner) {

-        _transferOwnership(_initialOwner);
    }
```
