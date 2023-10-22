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



### Time spent:
250 hours