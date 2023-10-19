| |Issue|
|-|:-|
| [[L&#x2011;01](#l01-paymaster-can-spend-arbitrary-amounts-of-gas)] | Paymaster can spend arbitrary amounts of gas which are subtracted from user gas limit |
| [[L&#x2011;02](#l02-store-context-paymaster-offset)] | `storePaymasterContextAndCheckMagic()` does not check offest is exactly 64 |
| [[L&#x2011;03](#l03-paymaster-can-spend-arbitrary-amounts-of-gas)] | ChainID is not verified to match the current chain ID in Legacy EIP-155 transactions |
| [[L&#x2011;04](#l04-overflow-in-length-calculations)] | Overflow in `lengthRoundedByWords()` leads in incorrect lengths |
| [[L&#x2011;05](#l05-overflow-pointer-calculations)] | Overflow in pointer calculations may read pointer from outside memory range |
| [[NC&#x2011;01](#nc01-gas-per-pub-data)] | Gas Per PubData is not signed unless using EIP-712 signatures | 
| [[NC&#x2011;02](#nc02-ecrecover-r-s)] | ECRecover does not have constraints for `s < n` and `r < n` | 
| [[NC&#x2011;03](#nc03-ecrecover-x-overflow)] | ECRecover does not allow `x_overflow` to be set | 
| [[NC&#x2011;04](#nc04-bytecode-length-0)] | Check `_bytecode.length != 0` in `publishCompressedBytecode()` | 
| [[NC&#x2011;05](#nc05-bytecode-length-0)] | Calling precompile contracts with `delegatecall` has inconsistent results |


## Low Severity Issues

### [l&#x2011;01] Paymaster can spend arbitrary amounts of gas which are subtracted from user gas limit.

The paymaster receives a call from the bootloader which has the remaining transaction gas limit supplied as gas. The call is made from `validateAndPayForPaymasterTransaction()` which calls `IPaymaster.validateAndPayForPaymasterTransaction()`.

Any gas used during this call is charged to the user as in [l2TxValidation()](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1185-L1219). 

This presents the opportunity for a malicious paymaster to consumer user gas for free.

The issue is rated as low severity as the user must first sign an EIP-712 transaction which contains the paymaster address. Thus, it could be argued that the user is responsible for selecting a paymaster which does not consumer arbitrarily large amounts of gas.


### [L&#x2011;02] `storePaymasterContextAndCheckMagic()` does not check offest is exactly 64

The function [storePaymasterContextAndCheckMagic()](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L771-L854) does not perform sufficient validation on the returned contract offset pointer `returnedContextOffset`.

The correct value is `returnedContextOffset = 64`, which skips words one and two, the magic and the offset. It is possible for a malicious user to return a context which is not `64`.

Some edge cases to consider are if `returnedContextOffset = 32` which would set the offset to itself. A valid ABI encoding could occur with `returnedContextOffset = 32` if `returndatasize() = 96`. This would use the offset as both the offset and length value, followed by one word of data.

Similarly, it's possible to have `returnedContextOffset > 64` and waste bytes. The offset would skip bytes which are never read.

Finally, if `returnedContextOffset = 4` then we will have `returnedContextLen = 0` and the transaction will succeed.

```
            function storePaymasterContextAndCheckMagic()    {
                // The paymaster validation step should return context of type "bytes context"
                // This means that the returndata is encoded the following way:
                // 0x20 || context_len || context_bytes...
                let returnlen := returndatasize()
                // The minimal allowed returndatasize is 64: magicValue || offset
                if lt(returnlen, 64) {
                    revertWithReason(
                        PAYMASTER_RETURNED_INVALID_CONTEXT(),
                        0
                    )
                }

                // Note that it is important to copy the magic even though it is not needed if the
                // `SHOULD_ENSURE_CORRECT_RETURNED_MAGIC` is false. It is never false in production
                // but it is so in fee estimation and we want to preserve as many operations as 
                // in the original operation.
                {
                    returndatacopy(0, 0, 32)
                    let magic := mload(0)

                    let isMagicCorrect := eq(magic, {{SUCCESSFUL_PAYMASTER_VALIDATION_MAGIC_VALUE}})

                    if and(iszero(isMagicCorrect), SHOULD_ENSURE_CORRECT_RETURNED_MAGIC()) {
                        revertWithReason(
                            PAYMASTER_RETURNED_INVALID_MAGIC_ERR_CODE(),
                            0
                        )
                    }
                }

                returndatacopy(0, 32, 32)
                let returnedContextOffset := mload(0) //@audit this value may be 4, 32, >64

                // Ensuring that the returned offset is not greater than the returndata length
                // Note, that we cannot use addition here to prevent an overflow
                if gt(returnedContextOffset, returnlen) {
                    revertWithReason(
                        PAYMASTER_RETURNED_INVALID_CONTEXT(),
                        0
                    )
                }
```

The issue is rated as low severity as the manipulation of data does not overwrites undesirable areas of memory and so poses a low security risk.


## [L&#x2011;03] ChainID is not verified to match the current chain ID in Legacy EIP-155 transactions

From [TransactionHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#L183-L186) we may have `reserved[0]` equal to any non-zero value, even if it doesn't match the current chain ID.

This is low severity since `encodedChainId` uses `block.chainId` rather than `reserved[0]`. The signature is signed over `encodedChainId` thus it is not possible to use signatures from other chains even though `reserved[0]` can be manipulated to any arbitrary non-zero value.

However, we can have multiple transactions for the same signature.

```solidity
        // Encode `chainId` according to EIP-155, but only if the `chainId` is specified in the transaction.
        bytes memory encodedChainId;
        if (_transaction.reserved[0] != 0) {
            encodedChainId = bytes.concat(RLPEncoder.encodeUint256(block.chainid), hex"80_80");
        }
```

**Recommendation**

Require `reserved[0] == block.chainid`.


## [L&#x2011;04 Overflow in `lengthRoundedByWords()` leads in incorrect lengths.

This issue severity is between Low and Medium.

Bug occurs in [bootloader.yul#540](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L539-L542). The function `lengthRoundedByWords()` does not protect against overflow if the length is more than `2^256 - 31`.

In the follow excerpt if `len = 2^256 - 31` then `add(len, 31)` will overflow and the function will return `0` as the length.
```yul
            function lengthRoundedByWords(len) -> ret {
                let neededWords := div(add(len, 31), 32) //@audit overflow if len >= 2^256 - 31
                ret := safeMul(neededWords, 32, "xv")
            }
```

Exploiting this overflow is non-trivial. The following functions will use `lengthRoundedByWords()` to determine the length of arrays.
- `getDataBytesLength()`
- `getSignatureBytesLength()`
- `getFactoryDepsBytesLength()` (would overflow in `validateBytes32Array()` during validation)
- `getPaymasterInputBytesLength()`
- `getReservedDynamicBytesLength()`

However, each of those types are checked in `validateBytes()`. Which also calls `lengthRoundedByWords()`. Now `validateBytes()` will also be vulnerable to the same issue which results in understating `fullLen`. However, this is not able to pass the final check that `and(lastWord, mask)` is zero.

```solidity
            function validateBytes(bytesPtr) -> bytesEnd {
                let length := mload(bytesPtr)
                let lastWordBytes := mod(length, 32)

                switch lastWordBytes
                case 0 { 
                    // If the length is divisible by 32, then 
                    // the bytes occupy whole words, so there is
                    // nothing to validate
                    bytesEnd := safeAdd(bytesPtr, safeAdd(length, 32, "pol"), "aop") 
                }
                default {
                    // If the length is not divisible by 32, then 
                    // the last word is padded with zeroes, i.e.
                    // the last 32 - `lastWordBytes` bytes must be zeroes
                    // The easiest way to check this is to use AND operator

                    let zeroBytes := sub(32, lastWordBytes)
                    // It has its first 32 - `lastWordBytes` bytes set to 255
                    let mask := sub(shl(mul(zeroBytes,8),1),1)

                    let fullLen := lengthRoundedByWords(length) //@audit if length = 2^256-1 this will return 0
                    bytesEnd := safeAdd(bytesPtr, safeAdd(32, fullLen, "dza"), "dzp")

                    let lastWord := mload(sub(bytesEnd, 32)) //@audit lastWord would be bytesPtr which is 2^256 - 1

                    // If last word contains some unintended bits
                    // return 0
                    if and(lastWord, mask) { //@audit since lastWord = 2^256 - 1 this will fail
                        assertionError("bad bytes encoding")
                    }
                }
            }
```

So manipulating `length` to be 2^256 - 1 would not pass `validateBytes()`.

There are still two other calls which this may be reachable but require excessive return value which would exceed gas limits.
- [storePaymasterContextAndCheckMagic()](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L836)
- [ZKSYNC_NEAR_CALL_callPostOp()](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L2108)

**Recommendation**

Use `safeAdd()` in `lengthRoundedByWords()`.

```yul
            function lengthRoundedByWords(len) -> ret {
                let neededWords := div(safeAdd(len, 31), 32)
                ret := safeMul(neededWords, 32, "xv")
            }
```


## [L&#x2011;05] Overflow in pointer calculations may read pointer from outside memory range in `sendCompressedBytecode()`

Again this bug is between low and medium severity.

Within the function [sendCompressedBytecode()](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1535) it is possible to cause an overflow and read from memory before `COMPRESSED_BYTECODES_BEGIN_BYTE()`. The overflow occurs because `mload(afterSelectorPtr)` is reading from untrusted operator input. A malicious operator could set `mload(afterSelectorPtr)` to a large value such as 2^256 - 1 such that `add(mload(afterSelectorPtr), afterSelectorPtr)` overflows. This allows reading from memory before `afterSelectorPtr`.

The severity of this issue is open to debate since it does not seem to drain funds from the protocol, however it is possible for the overflow to occur and read areas of memory anywhere between slot 0 and `dataInfoPtr`. 

```yul
            function sendCompressedBytecode(dataInfoPtr, bytecodeHash) -> ret {
                // Storing the right selector, ensuring that the operator cannot manipulate it
                mstore(add(dataInfoPtr, 32), {{PUBLISH_COMPRESSED_BYTECODE_SELECTOR}})

                let calldataPtr := add(dataInfoPtr, 60)
                let afterSelectorPtr := add(calldataPtr, 4)

                let originalBytecodeOffset := add(mload(afterSelectorPtr), afterSelectorPtr) //@audit this may overflow to read memory before `dataInfoPtr`.
                checkOffset(originalBytecodeOffset)
                let potentialRawCompressedDataOffset := validateBytes(
                    originalBytecodeOffset
                )

                let rawCompressedDataOffset := add(mload(add(afterSelectorPtr, 32)), afterSelectorPtr)
                checkOffset(rawCompressedDataOffset)

                if iszero(eq(potentialRawCompressedDataOffset, rawCompressedDataOffset)) {
                    assertionError("Compression calldata incorrect")
                }

                let nextAfterCalldata := validateBytes(
                    rawCompressedDataOffset
                )
                checkOffset(nextAfterCalldata)

                let totalLen := safeSub(nextAfterCalldata, calldataPtr, "xqwf") //@audit end result must have nextAfterCalldata >= calldataPtr
...
```

**Recommendation**

Use `safeAdd()` when calculating `originalBytecodeOffset`.

```yul
let originalBytecodeOffset := safeAdd(mload(afterSelectorPtr), afterSelectorPtr)
```

## Non-Critical Issues

### [NC&#x2011;01] Gas Per PubData is not signed unless using EIP-712 signatures 

Users doe not sign over the field `gasPerPubdataByteLimit` unless they use EIP-712 signatures. For the remaining transaction types the operator is able to manipulate this value anywhere in the range [0, `MAX_L2_GAS_PER_PUBDATA`].

This is rated as low severity as there is a maximum value the operator may select due to validation in `validateTypedTxStructure()`. The maximum value is `MAX_L2_GAS_PER_PUBDATA`.

### [NC&#x2011;02]: ECRecover does not have constraints for `s < n` and `r < n`

It is possible to trigger a panic in [ecrecover_precompile_inner_routine()](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/mod.rs#L206). The panic occurs when we attempt to take the inverse of zero or specifically `n mod n`.

There are no constraints to ensure `s < n` or `r < n` in `ecrecover_precompile_inner_routine()`. However, these constrains do occur in the yul wrapper [here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/precompiles/Ecrecover.yul#L63-L66) and so it is not exploitable.

PoC: the following test can be run from the tests in `code/era-zkevm_circuits/src/ecrecover/mod.rs`.

```rust
    fn test_signature_for_address_verification_r_as_curve_order() {
        let geometry = CSGeometry {
            num_columns_under_copy_permutation: 100,
            num_witness_columns: 0,
            num_constant_columns: 8,
            max_allowed_constraint_degree: 4,
        };
        let max_variables = 1 << 26;
        let max_trace_len = 1 << 20;

        fn configure<
            F: SmallField,
            T: CsBuilderImpl<F, T>,
            GC: GateConfigurationHolder<F>,
            TB: StaticToolboxHolder,
        >(
            builder: CsBuilder<T, F, GC, TB>,
        ) -> CsBuilder<T, F, impl GateConfigurationHolder<F>, impl StaticToolboxHolder> {
            let builder = builder.allow_lookup(
                LookupParameters::UseSpecializedColumnsWithTableIdAsConstant {
                    width: 3,
                    num_repetitions: 8,
                    share_table_id: true,
                },
            );
            let builder = ConstantsAllocatorGate::configure_builder(
                builder,
                GatePlacementStrategy::UseGeneralPurposeColumns,
            );
            let builder = FmaGateInBaseFieldWithoutConstant::configure_builder(
                builder,
                GatePlacementStrategy::UseGeneralPurposeColumns,
            );
            let builder = ReductionGate::<F, 4>::configure_builder(
                builder,
                GatePlacementStrategy::UseGeneralPurposeColumns,
            );
            // let owned_cs = ReductionGate::<F, 4>::configure_for_cs(owned_cs, GatePlacementStrategy::UseSpecializedColumns { num_repetitions: 8, share_constants: true });
            let builder = BooleanConstraintGate::configure_builder(
                builder,
                GatePlacementStrategy::UseGeneralPurposeColumns,
            );
            let builder = UIntXAddGate::<32>::configure_builder(
                builder,
                GatePlacementStrategy::UseGeneralPurposeColumns,
            );
            let builder = UIntXAddGate::<16>::configure_builder(
                builder,
                GatePlacementStrategy::UseGeneralPurposeColumns,
            );
            let builder = SelectionGate::configure_builder(
                builder,
                GatePlacementStrategy::UseGeneralPurposeColumns,
            );
            let builder = ZeroCheckGate::configure_builder(
                builder,
                GatePlacementStrategy::UseGeneralPurposeColumns,
                false,
            );
            let builder = DotProductGate::<4>::configure_builder(
                builder,
                GatePlacementStrategy::UseGeneralPurposeColumns,
            );
            // let owned_cs = DotProductGate::<4>::configure_for_cs(owned_cs, GatePlacementStrategy::UseSpecializedColumns { num_repetitions: 1, share_constants: true });
            let builder = NopGate::configure_builder(
                builder,
                GatePlacementStrategy::UseGeneralPurposeColumns,
            );

            builder
        }

        let builder_impl = CsReferenceImplementationBuilder::<F, P, DevCSConfig>::new(
            geometry,
            max_variables,
            max_trace_len,
        );
        let builder = new_builder::<_, F>(builder_impl);

        let builder = configure(builder);
        let mut owned_cs = builder.build(());

        // add tables
        let table = create_xor8_table();
        owned_cs.add_lookup_table::<Xor8Table, 3>(table);

        let table = create_and8_table();
        owned_cs.add_lookup_table::<And8Table, 3>(table);

        let table = create_byte_split_table::<F, 1>();
        owned_cs.add_lookup_table::<ByteSplitTable<1>, 3>(table);
        let table = create_byte_split_table::<F, 2>();
        owned_cs.add_lookup_table::<ByteSplitTable<2>, 3>(table);
        let table = create_byte_split_table::<F, 3>();
        owned_cs.add_lookup_table::<ByteSplitTable<3>, 3>(table);
        let table = create_byte_split_table::<F, 4>();
        owned_cs.add_lookup_table::<ByteSplitTable<4>, 3>(table);

        let cs = &mut owned_cs;

        let sk = crate::ff::from_hex::<Secp256Fr>(
            "b5b1870957d373ef0eeffecc6e4812c0fd08f554b37b233526acc331bf1544f7",
        )
        .unwrap();
        let eth_address = hex::decode("12890d2cce102216644c59dae5baed380d84830c").unwrap();
        let (r, s, _pk, digest) = simulate_signature_for_sk(sk);

        let scalar_params = secp256k1_scalar_field_params();
        let base_params = secp256k1_base_field_params();

        let digest_u256 = repr_into_u256(digest.into_repr());

        // @audit changes from `test_signature_for_address_verification()`
        // Set r to be n (curve order of secp256k1
        let r_u256 = U256([
            scalar_params.modulus_u1024.as_ref().as_words()[0],
            scalar_params.modulus_u1024.as_ref().as_words()[1],
            scalar_params.modulus_u1024.as_ref().as_words()[2],
            scalar_params.modulus_u1024.as_ref().as_words()[3],
        ]);

        let s_u256 = repr_into_u256(s.into_repr());

        let rec_id = UInt8::allocate_checked(cs, 0);
        let r = UInt256::allocate(cs, r_u256);
        let s = UInt256::allocate(cs, s_u256);
        let digest = UInt256::allocate(cs, digest_u256);

        let scalar_params = Arc::new(scalar_params);
        let base_params = Arc::new(base_params);

        let valid_x_in_external_field = Secp256BaseNNField::allocated_constant(
            cs,
            Secp256Fq::from_str("9").unwrap(),
            &base_params,
        );
        let valid_t_in_external_field = Secp256BaseNNField::allocated_constant(
            cs,
            Secp256Fq::from_str("16").unwrap(),
            &base_params,
        );
        let valid_y_in_external_field = Secp256BaseNNField::allocated_constant(
            cs,
            Secp256Fq::from_str("4").unwrap(),
            &base_params,
        );

        let (no_error, digest) = ecrecover_precompile_inner_routine(
            cs,
            &rec_id,
            &r,
            &s,
            &digest,
            valid_x_in_external_field.clone(),
            valid_y_in_external_field.clone(),
            valid_t_in_external_field.clone(),
            &base_params,
            &scalar_params,
        );

        assert!(no_error.witness_hook(&*cs)().unwrap() == true);
        let recovered_address = digest.to_be_bytes(cs);
        let recovered_address = recovered_address.witness_hook(cs)().unwrap();
        assert_eq!(&recovered_address[12..], &eth_address[..]);

        dbg!(cs.next_available_row());

        cs.pad_and_shrink();

        let mut cs = owned_cs.into_assembly();
        cs.print_gate_stats();
        let worker = Worker::new();
        assert!(cs.check_if_satisfied(&worker));
    }
```

### [NC&#x2011;03] ECRecover circuits don't allow for `x_overflow` to be set

The [ecrecover](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/precompiles/Ecrecover.yul#L64) precompile requires `v in [27,28]` then subtracts 27 so `v in [0,1]`. It is therefore no possible for the 2nd least significant bit to be set.
Since `x_overflow` is read from the 2nd lsb of `v` (i.e. `recid`) it is not possible for it to ever be true.

```rust
    let [y_is_odd, x_overflow, ..] =
        Num::<F>::from_variable(recid.get_variable()).spread_into_bits::<_, 8>(cs); //@audit read 1st and 2nd lsb bit of `v`

    let (r_plus_n, of) = r.overflowing_add(cs, &secp_n_u256);
    let mut x_as_u256 = UInt256::conditionally_select(cs, x_overflow, &r_plus_n, &r); //@audit x_overflow is always false
    let error = Boolean::multi_and(cs, &[x_overflow, of]);
    exception_flags.push(error);
```

It is therefore not possible to have `r > n`  and `r < p`. It is only about 1^128 chance of having an `r` value in the range `n <= r < p`. However, this signature will always be rejected.

### [NC&#x2011;04]: Check `_bytecode.length != 0` in `publishCompressedBytecode()`

There lacks a check in [publishCompressedBytecode()](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Compressor.sol#L54) to ensure that `_bytecode.length > 0`. This would perform undesirable calculations of hashing empty bytecode and result in a contract with `extcodesize() = 0`.

However, this check is indirectly performed in `hashL2Bytecode()` by check `length % 2 == 1`.

## [NC&#x2011;05]: Calling precompile contracts with `delegatecall` has inconsistent results

It is possible to call the precompile contracts with `delegatecall`. The results for the user vary depending on if an inner `precompileCall()` is made.

The opcode used `precompileCall()` will revert if the current address is not a system contract. Since `msg.sender` in a `delegatecall` will be the user contract it should be that the call to `precompileCall()` reverts.

However, the exact specifics of when `precompileCall()` will revert are not located on the contracts in scope and exist inside the VM. This assumes the desired functionality that `precompileCall()` will check the `msg.sender` rather than the address which contains the bytecode.

So, if we call `EcAdd` or `EcMul` with valid parameters it will succeed since `precompileCall()` is not triggered. However, if we call `EcAdd` or `EcMul` with invalid parameters then the `burnGas()` function is called and the `delegatecall` will revert. All other precompiles will fail since they make use of `precompileCall()`.
