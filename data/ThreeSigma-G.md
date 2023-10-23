### [GAS-1] No need to set `depositCap` if the `depositLimitation` is false, thus avoiding saving a variable in memory

[Here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L130-L131), even if the value of `depositLimitation` is `false`, the variable `depositCap` is still being assigned. Adding an `if` statement that only assigns a `depositCap` if `depositLimitation` is `true` would mitigate this issue. Additionally, although now every time `depositCap` is used, `depositLimitation` is checked first, in the future if code is written where only the value of `depositCap` is used and `depositCap` has a non-zero value, it may cause confusion and a wrongly assumption that `depositLimitation` is true.

### [GAS-2] No need to write to memory an unused variable

In the function `_parseL2WithdrawalMessage()` in `Mailbox.sol` the [last line of code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L427) returns and copies the variables `amount` and the `offset`. Since `offset` isn't used after this point there is no need to spend gas in copying it to memory.

### [GAS-3] Gas spent on an unnecessary `Math.max()` usage

In `TransactionValidator.sol` in [line 142](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L142) a max operation is done between the variables `batchOverheadForTransaction` and `txSlotOverhead`. But at this point the variable `batchOverheadForTransaction` is always going to be zero since it was just initialized. So you can just do `batchOverheadForTransaction = txSlotOverhead;` to save on gas.