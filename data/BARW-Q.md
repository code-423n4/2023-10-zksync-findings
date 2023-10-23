# Missing the business logic checking in circuit 
 
There no business logic check only the constraint check in `demux_log_queue` and `storage_validity_by_grand_product


```
        cs.pad_and_shrink();
        let worker = Worker::new();
        let mut owned_cs = owned_cs.into_assembly();
        owned_cs.print_gate_stats();
        // @audit only checks the constraint here
        assert!(owned_cs.check_if_satisfied(&worker));
```

## Related code:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/storage_validity_by_grand_product/mod.rs#L1167

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/demux_log_queue/mod.rs#L597

# Unused variable in circuit

`_keys_are_equal` is not used anywhere.

```rust
            // @audit _keys_are_equal is not used
            let (_keys_are_equal, previous_key_is_smaller) =
                unpacked_long_comparison(cs, &sorting_key, previous_sorting_key);

```

Related code:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ram_permutation/mod.rs#L301


# Outdated comment in bootloader.yul

Transactions from L1 -> L2 are not free anymore. Remove outdated comment to prevend confusion

```
 // Note, that for now, the L1->L2 transactions are free, i.e. the gasPrice
 // for such transactions is always zero, so the `refundGas` is not used anywhere
 // except for notifications for the operator for API purposes. 
```



Related code:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L929-L931


# Missleading comment in `Mailbox.sol`

The comment states that the `4 + 20 + 32 + 32 + _additionalData.length >= 68 (bytes)` but based on the line above it should be `4 + 20 + 32 + 20 +  _additionalData.length >= 76` (signiture + address + uint256 + address)


```
        // 2. The message that is sent by `withdrawWithMessage(address _l1Receiver, bytes calldata _additionalData)`
        // It should be equal to the length of the following:
        // bytes4 function signature + address l1Receiver + uint256 amount + address l2Sender + bytes _additionalData =
        // = 4 + 20 + 32 + 32 + _additionalData.length >= 68 (bytes).

```


