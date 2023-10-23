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


