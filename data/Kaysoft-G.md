## [GAS-1] Repeated `if` block check in a function cause the function to exhaust more gas
Consider adding the same logic in the first if block.
File: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L333-L343

The if `if (value > 0)` is repeated twice in the same function. The two can be refactored to one `if` block to spend less gas.
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

