## 1. **Simplify Code Structure**: 
To enhance code readability and comprehension, it is recommended to reduce nesting and complexity. In the file `knownCodesStorage.sol`, specifically in the `_markBytecodeAsPublished` function, we propose streamlining the code as follows:

   - Check if `_bytecodeHash` is not already marked.
   - Validate the bytecode.
   - If required, send the bytecode to the L1 chain.
   - Save it as a known code to prevent redundant logging to L1.
   - Emit an event to indicate the bytecode has been marked.

```solidity
if (getMarker(_bytecodeHash) != 0) {
    return;
}

_validateBytecode(_bytecodeHash);

if (_shouldSendToL1) {
    _sendBytecodeToL1(_bytecodeHash, _l1PreimageHash, _l1PreimageBytesLen);
}

// Save as known, to not resend the log to L1
assembly {
    sstore(_bytecodeHash, 1)
}

emit MarkedAsKnown(_bytecodeHash, _shouldSendToL1);
```
## 2. Utilize SafeCast for Type Casting

In Solidity 0.8 and later versions, automatic overflow protection is available, but this protection does not extend to casting operations. For instance, if you cast a large number like `uint32(4294967300)`, it will result in `4` without triggering an exception. To mitigate potential casting issues, it is advisable to employ OpenZeppelin's SafeCast library. Even when it appears that variables cannot lead to overflow, it is considered a best practice to implement SafeCast for type casting operations. This extra layer of safety can help prevent unexpected issues related to data type conversions.

*/contracts/ImmutableSimulator.sol*

```solidity
27:	return immutableDataStorage[uint256(uint160(_dest))][_index];
40:	immutableDataStorage[uint256(uint160(_dest))][index] = value;
```

*/contracts/MsgValueSimulator.sol*

```solidity
29:	to = address(uint160(addressAsUint));
60:	SystemContractHelper.setValueForNextFarCall(uint128(value));
```

*/contracts/AccountCodeStorage.sol*

```solidity
38:	uint256 addressAsKey = uint256(uint160(_address));
54:	uint256 addressAsKey = uint256(uint160(_address));
64:	uint256 addressAsKey = uint256(uint160(_address));
77:	address account = address(uint160(_input));
78:	if (uint160(account) <= CURRENT_MAX_PRECOMPILE_ADDRESS) {
105:	address account = address(uint160(_input));
117:	uint160(account) > CURRENT_MAX_PRECOMPILE_ADDRESS &&
```

*/contracts/L2EthToken.sol*

```solidity
65:	return balance[address(uint160(_account))];
```

*/contracts/KnownCodesStorage.sol*

```solidity
127:	uint8 version = uint8(_bytecodeHash[0]);
```

*/contracts/DefaultAccount.sol*

```solidity
83:	uint32(gasleft()),
141:	address to = address(uint160(_transaction.to));
```

*/contracts/ContractDeployer.sol*

```solidity
101:	bytes.concat(CREATE2_PREFIX, bytes32(uint256(uint160(_sender))), _salt, _bytecodeHash, constructorInputHash)
104:	newAddress = address(uint160(uint256(hash)));
120:	newAddress = address(uint160(uint256(hash)));
256:	require(uint160(_newAddress) > MAX_SYSTEM_CONTRACT_ADDRESS, "Can not deploy contracts in kernel space");
260:	ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getCodeHash(uint256(uint160(_newAddress))) == 0x0,
320:	SystemContractHelper.setValueForNextFarCall(uint128(value));
```

*/contracts/BootloaderUtilities.sol*

```solidity
60:	bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));
67:	uint64 txDataLen = uint64(_transaction.data.length);
90:	uint256 vInt = uint256(uint8(_transaction.signature[64]));
115:	encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
146:	bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));
163:	uint64 txDataLen = uint64(_transaction.data.length);
189:	uint256 vInt = uint256(uint8(_transaction.signature[64]));
206:	encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
240:	bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));
258:	uint64 txDataLen = uint64(_transaction.data.length);
301:	encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
```

*/contracts/libraries/SystemContractHelper.sol*

```solidity
222:	gasPerPubdataByte = uint32(extractNumberFromMeta(meta, META_GAS_PER_PUBDATA_BYTE_OFFSET, 32));
232:	heapSize = uint32(extractNumberFromMeta(meta, META_HEAP_SIZE_OFFSET, 32));
241:	auxHeapSize = uint32(extractNumberFromMeta(meta, META_AUX_HEAP_SIZE_OFFSET, 32));
249:	shardId = uint8(extractNumberFromMeta(meta, META_SHARD_ID_OFFSET, 8));
258:	callerShardId = uint8(extractNumberFromMeta(meta, META_CALLER_SHARD_ID_OFFSET, 8));
267:	codeShardId = uint8(extractNumberFromMeta(meta, META_CODE_SHARD_ID_OFFSET, 8));
330:	return uint160(_address) <= uint160(MAX_SYSTEM_CONTRACT_ADDRESS);
```

*/contracts/libraries/Utils.sol*

```solidity
22:	return uint128(_x);
28:	return uint32(_x);
34:	return uint24(_x);
45:	codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
```

*/contracts/libraries/RLPEncoder.sol*

```solidity
64:	encoded[0] = bytes1(uint8(_offset + hbs + 56));
```

*/contracts/libraries/SystemContractsCaller.sol*

```solidity
82:	uint32 dataLength = uint32(Utils.safeCastToU32(data.length));
```

*/contracts/libraries/EfficientCall.sol*

```solidity
252:	SystemContractHelper.ptrShrinkIntoActive(uint32(msg.data.length));
260:	SystemContractHelper.ptrAddIntoActive(uint32(dataOffset));
262:	uint32 shrinkTo = uint32(msg.data.length - (_data.length + dataOffset));
```

*/contracts/libraries/TransactionHelper.sol*

```solidity
94:	return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0;
163:	bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));
170:	uint64 txDataLen = uint64(_transaction.data.length);
198:	encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
231:	bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));
248:	uint64 txDataLen = uint64(_transaction.data.length);
270:	encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
302:	bytes memory encodedTo = RLPEncoder.encodeAddress(address(uint160(_transaction.to)));
320:	uint64 txDataLen = uint64(_transaction.data.length);
342:	encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
374:	address paymaster = address(uint160(_transaction.paymaster));
405:	if (address(uint160(_transaction.paymaster)) != address(0)) {
```

## 3. **Use `revert` for Safety**
In the `fallback` function of `MsgValueSimulator`, there is a safety condition meant to protect against situations that should not occur. If a user sends an amount greater than `MAX_MSG_VALUE`, it's recommended to use `revert` instead of `return` on line 55. Alternatively, the condition could be moved to the beginning of the function to handle it early.

```solidity
if (value > MAX_MSG_VALUE) {
    // The if above should never be true, since noone should be able to have
    // MAX_MSG_VALUE wei of ether. However, if it does happen for some reason,
    // we will revert(0,0).
    // Note, that we use raw revert here instead of `panic` to emulate behaviour close to
    // the EVM's one, i.e. returndata should be empty.
    assembly {
        return(0, 0)
    }
}
```

## 4. **Comment Corrections**
 Some comments in the code need correction or clarification:

   - In `knownCodesStorage.sol`, remove the word "words" at the end of line 14.
   - In `NonceHolder.sol`, replace "marked" with "mark" in the comment on line 17.
   - In `SystemContext.sol`, change "will" to "set it to" in the comment on line 36.
   - Correct an inaccurate comment on line 64 in `SystemContext.sol`.
   - Remove the word "when" from the comment on line 71 in `SystemContext.sol.

## 5. **Unused Test Function**
 The function `unsafeOverrideBlock` in `SystemContext.sol` was initially intended for testing purposes but was not removed from the codebase.

## 6. Spelling Mistake

*contracts/libraries/SystemContractHelper.sol*

* The word `returns` is misspelled as [`retuns`](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/libraries/SystemContractHelper.sol#L318).

## 7. Underscore Notation Not Used or Not Used Consistently

Consider using underscore notation to help with contract readability (Ex. `23453` -> `23_453`).

*/contracts/SystemContext.sol*

```solidity
40:	uint256 public difficulty = 2500000000000000;
```

## 8. bytes.concat() Can Be Used Over abi.encodePacked()

Consider using `bytes.concat()` instead of `abi.encodePacked()` in contracts with Solidity version >= 0.8.4.

*/contracts/L2EthToken.sol*

```solidity
98:	return abi.encodePacked(IMailbox.finalizeEthWithdrawal.selector, _to, _amount);
```

*/contracts/libraries/TransactionHelper.sol*

```solidity
132:	keccak256(abi.encodePacked(_transaction.factoryDeps)),
141:	return keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```

## 9. Space Between License and Pragma

Almost all contracts in scope have a space between the license statement and pragma ([ex](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/SystemContext.sol#L2)). Although it does not void the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html), all example contracts in the Style Guide do not have a space between the license statement and pragma ([ex](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#blank-lines)). This format can also be seen throughout the Solidy documentation ([ex](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html)). Consider removing the needless space.

It is also good to note the following [contract](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/libraries/Utils.sol#L2) does not have a space between the license and pragma. At the very least all contracts should have a consistant style. 

## 10. Inconsistent Trailing Period

Some comments like [this](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L59) have a trailing `.` but other lines like [this](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/AccountCodeStorage.sol#L49) do not.

Consider sticking to a single comment style for a less distracting developer experience.

## 8. ERC20 Token Recovery

Consider adding a recovery function for Tokens that are accidently sent to the core contracts of the protocol. 

## 11. Gas Greif If Relayed

Although it may not be possible in the current system design, it is good to note a possible gas greif if transactions are relayed. 

Even if the `returnData` is left of of the following statement `(bool success, ) = target.call()`,  `returnData` is still copied to memory. This can lead to a gas greif if relayed. The code [here](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L66), and [here](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/MsgValueSimulator.sol#L36) use `(bool success, ) = target.call()`.

For more information, see [this](https://twitter.com/pashovkrum/status/1607024043718316032?t=xs30iD6ORWtE2bTTYsCFIQ&s=19) post.

## 12. Lack of Interface NatSpec

The [Solidity documentation](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html#natspec-format) states: 

> [I]t is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI).

[Some](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IEthToken.sol) interfaces have no NatSpec documentation, [some](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/IL1Messenger.sol) have partial documentation, and [some](https://github.com/code-423n4/2023-03-zksync/blob/main/contracts/interfaces/INonceHolder.sol) have full documentation (still missing `@title`). Consider adding full NatSpec documentation to all interfaces.

## 13. Inconsistent NatSpec Style

There is a general inconsistency with the way NatSpec comments are formatted. A NatSpec comment [here](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/INonceHolder.sol#L23) is single line and uses `//`. [This](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/IERC20.sol#L25) NatSpec comment is a single line, but uses the multi-line `/**`. Although both `//` and `/**` are valid NatSpec notation, it is best to keep to a single comment style.

## 14. NatSpec Comments Without Tag

The [Solidity documentation](https://docs.soliditylang.org/en/v0.8.19/natspec-format.html#tags) states: 

> "[I]f no tags are used then the Solidity compiler will interpret a /// or /** comment in the same way as if it were tagged with @notice".

There are some instances ([ex1](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IContractDeployer.sol#L66), [ex2](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IContractDeployer.sol#L67), [ex3](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/interfaces/IContractDeployer.sol#L75), ...) where no NatSpec tag is specified. It is best to be explicit with NatSpec tags. 

## 15. Function Declaration Style

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#function-declaration) states that "[f]or short function declarations, it is recommended for the opening brace of the function body to be kept on the same line as the function declaration". It is not clear what length is exactly meant by "short" or "long". The [maximum line length](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) of 120 characters may be an indication, and in that case any expanded function declaration under 120 characters should be on a single line.

The following functions in their respective contracts are not compliant by this standard: 

*contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol*
* [`safeTransfer`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L22), [`safeTransferFrom`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L33), [`safeApprove`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L52), [`safeIncreaseAllowance`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L70), [`safeDecreaseAllowance`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/token/ERC20/utils/SafeERC20.sol#L86). 

*contracts/openzeppelin/utils/Address.sol*
* [`functionCall`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L91), [`functionCallWithValue`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L129), [`functionStaticCall`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L177), [`functionDelegateCall`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L217), [`_revert`](https://github.com/code-423n4/2023-03-zksync//blob/main/contracts/openzeppelin/utils/Address.sol#L292). 

## 16. Order of Layout

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout) suggests the following contract layout order: Type declarations, State variables, Events, Modifiers, Functions.

The following contracts are not compliant (examples are only to prove the layout are out of order NOT a full description): 

* [SystemContext.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/SystemContext.sol): State is positioned after Modifier.
* [ContractDeployer.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/ContractDeployer.sol): State is positioned after Functions.
* [IEthToken.sol](https://github.com/code-423n4/2023-03-zksync/tree/main/contracts/interfaces/IEthToken.sol): Events are positioned after Functions.






### Time spent:
250 hours