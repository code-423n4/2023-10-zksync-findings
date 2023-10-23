## [L-1] The local variable `prevBatchHash` of SystemContext.sol#publishTimestampDataToL1() function is unused

File: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L385

Consider removing unused variables.

## [L-2] Disable the `renounceOwnership()` function of the Allowlist function.
file: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L18

Consider disabling the `renounceOwnership()` function of the Allowlist contract by overriding it since it inherits from Ownable2step so that `onlyOwner` functions will not be inaccessible if through human error the `renounceOwnership()` function of the Allowlist is mistakenly called by the owner.

## [L-3] Consider removing commented code
commented code may be an indication of unfinished work. Consider removing commented code
File: https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L150-L157

## [L-4] The publishCompressedBytecode(...) function of Compressor.sol should not be payable. 
The payable publishCompressedBytecode(...) functon does not have any ETH transfer logic so users that send ETH with this function call will lose their ETH to Compressor.sol contract.

FILE: https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L54-L57

## [L-5] The local variable `bytecodeLenInWords` of Utils.sol#hashL2Bytecode() functions shadows the Utils.sol#bytecodeLenInWords(...) function name.
File: 
- `bytecodeLenInWords` local variable:  https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/Utils.sol#L86
- `bytecodeLenInWords(...)` function: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/Utils.sol#L44

Consider renaming either the local variable or the function.

## [L-6] Function DefaultAccount._validateTransaction() shouln’t check trx.value for required balance, maybe user wanted the transaction to fail. also maybe paymaster is going to transfer required balance later.

```
// The fact there is are enough balance for the account
        // should be checked explicitly to prevent user paying for fee for a
        // transaction that wouldn't be included on Ethereum.
        uint256 totalRequiredBalance = _transaction.totalRequiredBalance();
        require(totalRequiredBalance <= address(this).balance, "Not enough balance for fee + value");
```


