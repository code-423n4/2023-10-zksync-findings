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
FILE: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L96-L100
```
// The fact there is are enough balance for the account
        // should be checked explicitly to prevent user paying for fee for a
        // transaction that wouldn't be included on Ethereum.
        uint256 totalRequiredBalance = _transaction.totalRequiredBalance();
        require(totalRequiredBalance <= address(this).balance, "Not enough balance for fee + value");
```
## [L-7] Repeated `if` block check in a function
Consider adding the same logic in the first if block.
File: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L333-L343

The if `if (value > 0)` is repeated twice in the same function. The two can be refactored to 1.
```solidity
 if (value > 0) {
                ETH_TOKEN_SYSTEM_CONTRACT.transferFromTo(address(this), _newAddress, value);
            }//@audit-issue there is a similar if block below.
            // 2. Set the constructed code hash on the account
            _storeConstructingByteCodeHashOnAddress(_newAddress, _bytecodeHash);

            // 3. Call the constructor on behalf of the account
            if (value > 0) {
                // Safe to cast value, because `msg.value` <= `uint128.max` due to `MessageValueSimulator` invariant
                SystemContractHelper.setValueForNextFarCall(uint128(value));
            }
```
## [L-8] finalizeDeposit(...) function is payable and also requires `msg.value == 0`.
consider removing the payable keyword from the L2ERC20Bridge.sol#finalizeDeposit(...) function since it is not expected to receive ETH and has `require(msg.value == 0)` statement which is contradicting.
File: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L69-L73

```solidity
 function finalizeDeposit(
        address _l1Sender,
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        bytes calldata _data
    ) external payable override {//@audit-info remove payable since you expect msg.valu ==0
        // Only the L1 bridge counterpart can initiate and finalize the deposit.
        require(AddressAliasHelper.undoL1ToL2Alias(msg.sender) == l1Bridge, "mq");
        // The passed value should be 0 for ERC20 bridge.
        require(msg.value == 0, "Value should be 0 for ERC20 bridge");
```