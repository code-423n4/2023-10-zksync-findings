# Confusing/Incomplete Function Comment
The NatSpec comment of `@notice` for `_storeConstructingByteCodeHashOnAddress` is incomplete and confusing. I believe there's a word missing after `_newAddress`.

## Proposed Change

**BEFORE:**
```solidity
/// @notice Ensures that the _newAddress and assigns a new contract hash to it
```

**AFTER:**
```solidity
/// @notice Ensures that the _newAddress exists and assigns a new contract hash to it
```

## Source Code

```solidity
307:    /// @notice Ensures that the _newAddress and assigns a new contract hash to it
308     /// @param _newAddress The address of the deployed contract
309     /// @param _bytecodeHash The correctly formatted hash of the bytecode.
310     function _storeConstructingByteCodeHashOnAddress(address _newAddress, bytes32 _bytecodeHash) internal {
311         // Set the "isConstructor" flag to the bytecode hash
312         bytes32 constructingBytecodeHash = Utils.constructingBytecodeHash(_bytecodeHash);
313         ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructingCodeHash(_newAddress, constructingBytecodeHash);
314     }
```
*GitHub*: [307](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L307-L314C6)