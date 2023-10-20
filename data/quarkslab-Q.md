# List of issues with LOW-level severity

## L1 Diamond deviates from the ERC-2535 standard

The `Diamond`'s governor can replace a selector's facet address with the same address.

When replacing a selector's facet with the same facet, the smart contract does not revert and emits
the `DiamondCut(FaceCut[] _diamondCut, address _init, bytes calldata)` event.
This does not conform to the [ERC-2535](https://eips.ethereum.org/EIPS/eip-2535)'s
[`IDiamondCut`](https://eips.ethereum.org/EIPS/eip-2535#idiamondcut-interface) interface:

> If the `action` is `Replace`, update the function selector mapping for each `functionSelectors`
> item to the `facetAddress`.
> If any of the `functionSelectors` had a value equal to `facetAddress` or the selector was unset,
> revert instead.

The [original mudgen reference implementation](https://github.com/mudgen/Diamond)
([diamond-3](https://github.com/mudgen/diamond-3-hardhat) version)
[does not suffer](https://github.com/mudgen/diamond-3-hardhat/blob/1bd4aef810b2e97c9b438a2289036a3f693099af/contracts/libraries/LibDiamond.sol#L121)
from this issue.

Standard-adhering tools listening to `DiamondCut` events may get confused in this situation.

The impact is limited by the restricted access to this function (`onlyGovernor`). However,
developers chose to implement other similar rules (like the case when the selector was unset)
demonstrating they do not consider the governor as fully trusted.

### Classification

- [CWE-1068](https://cwe.mitre.org/data/definitions/1068.html):
Inconsistency Between Implementation and Documented Design

### Files

- `ethereum/contracts/zksync/libraries/Diamond.sol`

### Recommendation

Either document and motivate clearly why and how the Smart Contract deviates from the
[ERC-2535](https://eips.ethereum.org/EIPS/eip-2535) standard,
or revert when `action` is `Replace` and the `facetAddress` does not change,
as the standard requires.



## Empty contracts hash doesn't respect zkSync's bytecode hash format

The code hash returned by the `getCodeHash` function does not comply with zkSync's bytecode hash format when querying an empty account, a constructing contract or a precompiled contract.

The `getCodeHash` function is supposed to simulate the behavior of the `EXTCODEHASH` EVM opcode, as per the EIP-1052 specification.
However, zkSync [redefined](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md#bytecode-hashes) how bytecode hashes are defined:

"""
*On zkSync the bytecode hashes are stored in the following format:*

* *The 0th byte denotes the version of the format. Currently the only version that is used is “1”.*
* *The 1st byte is 0 for deployed contracts’ code and 1 for the contract code that is being constructed.*
* *The 2nd and 3rd bytes denote the length of the contract in 32-byte words as big-endian 2-byte number.*
* *The next 28 bytes are the last 28 bytes of the sha256 hash of the contract’s bytecode.*
* *The bytes are ordered in little-endian order (i.e. the same way as for bytes32 ).*
"""

`getCodeHash` returns the bytecode hash as stored in the `AccountCodeStorage` system contract
(i.e. complying with zkSync's definition of a bytecode hash),
except for empty accounts, constructing contracts and precompiled contracts,
for which it returns `keccak256("") == 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470`.

The valid value would be either `0x0100000086f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470` or
`0x0101000086f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470` depending on whether the contract
is constructing or not.

The impact is that smart contracts can't rely on bytecode hash to know the bytecode size of an account. As the size in the hash is messed up for impacted accounts, smart contract will not consider those accounts as zero bytecode size ones. The decoded size from the hash would `0x4601 = 17921` instead of `0x0000`.

*Note: impacted accounts are empty, constructing and precompiled contracts.*


### Classification

- [CWE-1068](https://cwe.mitre.org/data/definitions/1068.html):
Inconsistency Between Implementation and Documented Design

### Files

Affected code:
* https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L23
* https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L94
* https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#LL103
* https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L108

Affected docs:
* https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md#bytecode-hashes

### Recommendation

`EMPTY_STRING_KECCAK` should be equal to `0x0100000086f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470` to respect the bytecode hash format.






## Unused code in bootloader

In `ZKSYNC_NEAR_CALL_validateTx` function from bootloader's Yul code, the local variable `innerTxDataOffset` is created by adding 32 (0x20) to `txDataOffset`. But this local variable is never used in the rest of the function.

### Classification

- [CWE-563](https://cwe.mitre.org/data/definitions/563.html):
Assignment to a Variable without Use

### Files
* https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1296

### Recommendation

Delete [the line of code](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1296) that create `innerTxDataOffset`.
