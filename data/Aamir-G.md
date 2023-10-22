## Summary

|                | Issues                               | Instances |
| -------------- | ------------------------------------ | --------- |
| [[G-0](#gas0)] | Check can be reversed for saving gas | 1         |

## Gas

---

### [G-0] Typos <a id="gas0"></a>

_Instance 1:_

Address zero check should be done before comparing address

```Javascript
File: 2023-10-zksync/code/system-contracts/contracts/DefaultAccount.sol

90        /// @dev The function reverts on invalid bytecode hash formam.
```

GitHub: [90](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L190-L190)
