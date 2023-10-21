# Various Typos or Confusing/Incomplete Comments

## `ContractDeployer.sol`
The NatSpec comment of `@notice` for `_storeConstructingByteCodeHashOnAddress` is incomplete and confusing. I believe there's a word missing after `_newAddress`.

### Proposed Change

**BEFORE:**
```solidity
/// @notice Ensures that the _newAddress and assigns a new contract hash to it
```

**AFTER:**
```solidity
/// @notice Ensures that the _newAddress exists and assigns a new contract hash to it
```

### Source Code

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

## `booyloader.yul`
There are a number of typos/misspellings in `bootloader.yul`, and I've comprised a list of suggested changes for better readability and correctness.

Proposed changes below:

## 1. Comments within ZKSYNC_NEAR_CALL_markFactoryDepsL2

**BEFORE:**
```rust
// Here we are making sure that the bytecode is indeed not yet know and needs to be published,
// preveting users from being overcharged by the operator.
```

**AFTER:**
```rust
// Here we are making sure that the bytecode is indeed not yet known and needs to be published,
// preventing users from being overcharged by the operator.
```

*GitHub* [1357-1358](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1357-L1358)

### 2. Misspelling of `innerTxDataOffset`

**BEFORE:**
```rust
let innerTxDataOffst := add(txDataOffset, 32)
```

**AFTER:**
```rust
let innerTxDataOffset := add(txDataOffset, 32)
```

*GitHub* [2023](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L2023), [2025](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#2025), [2047](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#2047), [2049](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#2049), [2196](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#2196), [2197](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#2197), [2201](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#2201)

### 3. Misspelling of `gas`

**BEFORE:**
```rust
/// @param txTotalGasLimit The total gass limit of the transaction (including the overhead).
```

**AFTER:**
```rust
/// @param txTotalGasLimit The total gas limit of the transaction (including the overhead).
```

*GitHub* [1595](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1595)

### 4. Misspelling of `accommodate`

**BEFORE:**
```rust
/// it needs to also accomodate uncompressed state diffs that are required for the state diff
```

**AFTER:**
```rust
/// it needs to also accommodate uncompressed state diffs that are required for the state diff
```

*GitHub* [344](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L344)

### 5. Misspelling of `Scratch`

**BEFORE:**
```rust
/// Scatch space is used for various temporary values
```

**AFTER:**
```rust
/// Scratch space is used for various temporary values
```

*GitHub* [109](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L109)

### 6. Misspelling of `maintenance`

**BEFORE:**
```rust
/// the common parts of the bootloader as well as general maintainance of the system.
```

**AFTER:**
```rust
/// the common parts of the bootloader as well as general maintenance of the system.
```

*GitHub* [66](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L66)

### 7. Misspelling of `exhausted`

**BEFORE:**
```rust
// L2 block. In case a batch is sealed by timeout (i.e. the resources of the batch have not been exhaused, but we need
```

**AFTER:**
```rust
// L2 block. In case a batch is sealed by timeout (i.e. the resources of the batch have not been exhausted, but we need
```

*GitHub* [3806](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L3806)

### 8. Misspelling of `Transferring`

**BEFORE:**
```rust
// Transfering all the ETH received in the block to the operator
```

**AFTER:**
```rust
// Transferring all the ETH received in the block to the operator
```

*GitHub* [3793](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L3793)

### 9. Misspelling of `@notice`

**BEFORE:**
```rust
/// @norice The id of the VM hook that use used to notify the operator that it needs to insert the pubdata.
```

**AFTER:**
```rust
/// @notice The id of the VM hook that use used to notify the operator that it needs to insert the pubdata.
```

*GitHub* [3581](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L3581)

### 10. Misspelling of `trigger`

**BEFORE:**
```rust
// For L2 transactions, we use near call panic, it will triger the validation 
```

**AFTER:**
```rust
// For L2 transactions, we use near call panic, it will trigger the validation 
```

*GitHub* [2681](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L2681)

