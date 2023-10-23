## Summary

### Low-Risk Issues

|  | Issue | Instances |
| --- | --- | --- |
| [L‑01] | L1 transaction gas cost should not be constant as it may be changed in the future | 1 |
| [L‑02] | Canonical tx hash in bootloader may not be canonical | 1 |
| [L‑03] | Better to divide the gas_per_pubdata considering the essence of the value | 1 |

Total: 3 instances over 3 issues

### Non-critical Issues

|  | Issue | Instances |
| --- | --- | --- |
| [N‑01] | Wrong natspec descriptions | 1 |

Total: 1 instance over 1 issue

Note: The above tables were created, considering the automatic findings and thus, those are not included.

---

## Low-Risk Issues

### [L‑01] **L1 transaction gas cost should not be constant as it may be changed in the future**

Summary:

The procedure of zkSync rollup starts from the bootloader program. It handles and manages many operations to send the validated transactions back to layer 1. During its work cycle, the Bootloader calculates and sets the block gas fee, based on the L1 gas and a fair L2 gas price. This calculation uses `L1_GAS_PER_PUBDATA_BYTE()` pure function to get the publishing cost which returns the number `17` statically.
Currently, this cost is `16` for a single byte and considering the additional fees, it has been set as `17` which is correct. However, many proposals are suggested to alter this number. One of those proposals is EIP-4488 which suggests reducing this number from `16` to `3`, and thus the maximum block size increases to 10M bytes. Although this proposal is stagnant, with recent advances and proposals, it is likely to be applied. As a consequence, the base fee would be calculated wrongly using the bootloader program. The function `L1_GAS_PER_PUBDATA_BYTE()` should consider these kind of changes and be flexible to handle these in the near future.
Consider making the `L1_GAS_PER_PUBDATA_BYTE()` dynamic in which the admin can change its value over the time.

EIP-4488:
https://eips.ethereum.org/EIPS/eip-4488

```Solidity
            /// @dev The number of L1 gas needed to be spent for
            /// L1 byte. While a single pubdata byte costs `16` gas, 
            /// we demand at least 17 to cover up for the costs of additional
            /// hashing of it, etc.
            function L1_GAS_PER_PUBDATA_BYTE() -> ret {
                ret := 17
            }
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L88

### [L‑02] **Canonical tx hash in bootloader may not be canonical**

Summary:

Although the transaction parameters differ and the return value of the function `getCanonicalL1TxHash()` function seems to be consistent and unique, the ignorance of some tx-uniqueness parameters such as `nonce` may reproduce the same L1 tx hash inside the bootloader.

*There is an instance of this issue:*

```solidity
            /// @dev Calculates the canonical hash of the L1->L2 transaction that will be
            /// sent to L1 as a message to the L1 contract that a certain operation has been processed.
            function getCanonicalL1TxHash(txDataOffset) -> ret {
                // Putting the correct value at the `txDataOffset` just in case, since 
                // the correctness of this value is not part of the system invariants.
                // Note, that the correct ABI encoding of the Transaction structure starts with 0x20
                mstore(txDataOffset, 32)

                let innerTxDataOffset := add(txDataOffset, 32)
                let dataLength := safeAdd(32, getDataLength(innerTxDataOffset), "qev")

                debugLog("HASH_OFFSET", innerTxDataOffset)
                debugLog("DATA_LENGTH", dataLength)

                ret := keccak256(txDataOffset, dataLength)
            }
```

Link(s): https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L637


### [L‑03] **Better to divide the `gas_per_pubdata` considering the essence of the value**

Summary:

As it is written in the yellowpaper of the Ethereum:

> $G_{txdatazero}$     4 Paid for every zero byte of data or code for a transaction.
> $G_{txdatanonzero}$ 16 Paid for every non-zero byte of data or code for a transaction.

Splitting the essence of zero/non-zero of a value may charge the user fairly amount of gas

*There is an instance of this issue:*

```solidity
            /// @dev The number of L1 gas needed to be spent for
            /// L1 byte. While a single pubdata byte costs `16` gas, 
            /// we demand at least 17 to cover up for the costs of additional
            /// hashing of it, etc.
            function L1_GAS_PER_PUBDATA_BYTE() -> ret {
                ret := 17
            }
```

Link(s): https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L88

---

## Non-critical Issues

### [N‑01] Wrong natspec descriptions

Summary: There is a wrong duplicative description which is wrong and can mislead the end-user or developer which should be corrected.

*There are instances of this issue:*

```solidity
/// @dev The type id of legacy transactions.
uint8 constant LEGACY_TX_TYPE = 0x0;
/// @dev The type id of legacy transactions.
uint8 constant EIP_2930_TX_TYPE = 0x01;
```
As it is seen from the mentioned code snippet, the second one should be re-written as `EIP2930` transaction instead of the `legacy` transaction.

Link(s): https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L19
