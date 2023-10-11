## using unchecked blocks to save Gas
Solidity always checks for under/overflow and uses more gas.
If you use uncheck blocks, you can save some gas.

[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L223-L225](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L223-L225)
```
        for (uint256 i = numberOfL2ToL1Logs; i < L2_TO_L1_LOGS_MERKLE_TREE_LEAVES; ++i) {
            l2ToL1LogsTreeArray[i] = L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH;
        }
```
[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L229-L233](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L229-L233)
```
            for (uint256 i = 0; i < nodesOnCurrentLevel; ++i) {
                l2ToL1LogsTreeArray[i] = keccak256(
                    abi.encode(l2ToL1LogsTreeArray[2 * i], l2ToL1LogsTreeArray[2 * i + 1])
                );
            }
```