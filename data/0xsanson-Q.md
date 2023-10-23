## (LOW) `heap_upper_bound` not updated after NearCall
This is a known bug according the the VM docs, however it must all be pointed out here.
Since NearCall keeps the same heap memory, returning from it should forward the `heap_upper_bound` if it was increased. The only impact is that this transaction might pay more gas in the case the bound gets increased again.

## (LOW) Wrong enforcement on bytecode version
In the circuits, this enforcement is inverted
```
        state
            .state_get_from_queue
            .conditionally_enforce_true(cs, version_hash_matches);
```
It should be:
```
        version_hash_matches
            .conditionally_enforce_true(cs, state.state_get_from_queue);
```
In a future version, contracts with old version may be executed incorrectly.
Line: https://github.com/code-423n4/2023-10-zksync/blob/main/code/era-zkevm_circuits/src/code_unpacker_sha256/mod.rs#L208-L210
