	
# These issues are not listed in the automated findings report.
#### Multiply operator has more gas than Shift operator


https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L23-L27

```diff
	...
--	require(_bytecode.length % 32 == 0, "pq");
++	require((_bytecode.length & 0x1F) == 0, "pq");
	...
--	uint256 bytecodeLenInWords = _bytecode.length / 32;
++	uint256 bytecodeLenInWords = (_bytecode.length >> 5);
	...
--	require(bytecodeLenInWords < 2**16, "pp"); 
++	require((bytecodeLenInWords >> 16) == 0, "pp");
	...
--	require(bytecodeLenInWords % 2 == 1, "ps"); 
++	require(bytecodeLenInWords & 1 == 1, "ps"); 
	...
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L50

```diff
--	codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
++	codeLengthInWords = uint256(uint8(_bytecodeHash[2])) << 8 + uint256(uint8(_bytecodeHash[3]));
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L16-L30

```diff
	...
--	uint256 mapValue = _map.map[_index / 8];
++	uint256 mapValue = _map.map[_index >> 3];
	...
--	uint256 bitOffset = (_index & 7) * 32;
++	uint256 bitOffset = (_index & 7) << 5;
	...
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L36-L65

```diff
	...
--	uint256 mapIndex = _index / 8;
++	uint256 mapIndex = _index >> 3;
	...
--	uint256 bitOffset = (_index & 7) * 32;
++	uint256 bitOffset = (_index & 7) << 5;
	...
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L37

```diff
	...
--	uint256 shiftedVal = _val << (lbs * 8);
++	uint256 shiftedVal = _val << (lbs << 3);
	...
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L72

```diff
	...
--	uint256 shiftedVal = uint256(_len) << (lbs * 8);
++	uint256 shiftedVal = uint256(_len) << (lbs << 3);
	...
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L46

```diff
	...
--	codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
++	codeLengthInWords = uint256(uint8(_bytecodeHash[2])) << 8 + uint256(uint8(_bytecodeHash[3]));
	...
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L82-L96

```diff
	...
--	require(_bytecode.length % 32 == 0, "po");
++	require((_bytecode.length & 0x1F) == 0, "po");
	...
--	uint256 bytecodeLenInWords = _bytecode.length / 32;
++	uint256 bytecodeLenInWords = (_bytecode.length >> 5);
	...
--	require(bytecodeLenInWords < 2**16, "pp"); 
++	require((bytecodeLenInWords >> 16) == 0, "pp");
	...
--	require(bytecodeLenInWords % 2 == 1, "pr"); 
++	require(bytecodeLenInWords & 1 == 1, "pr"); 
	...
```



