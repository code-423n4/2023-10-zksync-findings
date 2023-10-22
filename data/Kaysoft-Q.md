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
