# [G-01] `_bytecodeLen` can be put in an unchecked block

As seen in [Utils, function bytecodeLenInWords](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/Utils.sol#L44C1-L48C6):

```diff
    function _bytecodeLen(bytes32 _bytecodeHash) private pure returns (uint256 codeLengthInWords) {
+       unchecked {
            codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
+       }
    }
```

(without taking into account my QA submission `L-15`)
