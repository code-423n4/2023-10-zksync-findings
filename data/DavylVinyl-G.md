## Title
Absence of access control for the publication of compressed bytecode

## Impact

## Proof of Concept
```
function publishCompressedBytecode(
        bytes calldata _bytecode,
        bytes calldata _rawCompressedData
    ) external payable returns (bytes32 bytecodeHash) {
        unchecked {
            (bytes calldata dictionary, bytes calldata encodedData) = _decodeRawBytecode(_rawCompressedData);

            require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8");
            require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");
            require(
                encodedData.length * 4 == _bytecode.length,
                "Encoded data length should be 4 times shorter than the original bytecode"
            );

            for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {
                uint256 indexOfEncodedChunk = uint256(encodedData.readUint16(encodedDataPointer)) * 8;
                require(indexOfEncodedChunk < dictionary.length, "Encoded chunk index is out of bounds");

                uint64 encodedChunk = dictionary.readUint64(indexOfEncodedChunk);
                uint64 realChunk = _bytecode.readUint64(encodedDataPointer * 4);

                require(encodedChunk == realChunk, "Encoded chunk does not match the original bytecode");
            }
        }

        bytecodeHash = Utils.hashL2Bytecode(_bytecode);

        bytes32 rawCompressedDataHash = L1_MESSENGER_CONTRACT.sendToL1(_rawCompressedData);
        KNOWN_CODE_STORAGE_CONTRACT.markBytecodeAsPublished(
            bytecodeHash,
            rawCompressedDataHash,
            _rawCompressedData.length
        );
    }
```
Marking bytecode hash as 'known' is exclusively performed by the Bootloader using the 'markFactoryDeps' function in KnownCodeStorage.sol.
```
  function markFactoryDeps(bool _shouldSendToL1, bytes32[] calldata _hashes) external onlyBootloader {
        unchecked {
            uint256 hashesLen = _hashes.length;
            for (uint256 i = 0; i < hashesLen; ++i) {
                uint256 codeLengthInBytes = Utils.bytecodeLenInBytes(_hashes[i]);
                _markBytecodeAsPublished(_hashes[i], 0, codeLengthInBytes, _shouldSendToL1);
            }
        }
    }
```
However, it's worth noting that the 'publishCompressedBytecode' function in BytecodeCompressor allows the publishing of any bytecode, provided it has a valid bytecode hash and a valid compressed version. An important observation is that the 'publishCompressedBytecode' function lacks an access control check by the Bootloader, which could be considered as a potential oversight.

```
 function _markBytecodeAsPublished(
        bytes32 _bytecodeHash,
        bytes32 _l1PreimageHash,
        uint256 _l1PreimageBytesLen,
        bool _shouldSendToL1
    ) internal {
        if (getMarker(_bytecodeHash) == 0) {
            _validateBytecode(_bytecodeHash);

            if (_shouldSendToL1) {
                _sendBytecodeToL1(_bytecodeHash, _l1PreimageHash, _l1PreimageBytesLen);
            }

            // Save as known, to not resend the log to L1
            assembly {
                sstore(_bytecodeHash, 1)
            }

            emit MarkedAsKnown(_bytecodeHash, _shouldSendToL1);
        }
    }
```
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/BytecodeCompressor.sol#L35
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L31
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L59

## Tool Used
VsCode

## Recommended Mitigation Steps
It is advisable to introduce an 'OnlyBootloader' modifier to the 'publishCompressedBytecode' function.
