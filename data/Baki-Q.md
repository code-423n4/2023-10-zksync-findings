# L-01 Publishing to L1 will fail with wrong error in some cases.
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L206
## Impact

Bootloader calls `publishPubdataAndClearState` in L1Messenger. It verifies the encoded data and sends log L2 to L1.

`_totalL2ToL1PubdataAndStateDiffs` stores the number of l2 to L1 logs in the first 4 bytes.

We extract this value using this line.
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L205

This number represents the number of leaves. It is used to reconstruct the merkle hash .
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L211

Problem is the node only supports leaves of constant size of 2048 leaves. But there is no check to ensure that `numberOfL2ToL1Logs` is always <= 2048 .

## Proof of Concept

Suppose Bootloader calls `publishPubdataAndClearState`,  with `_totalL2ToL1PubdataAndStateDiffs` where the first 4 bytes represent 2050 .
So `numberOfL2ToL1Logs` = 2050.

Note, the `l2ToL1LogsTreeArray` array will now be initialized as array of length 2050.
```solidity
bytes32[] memory l2ToL1LogsTreeArray = new bytes32[](L2_TO_L1_LOGS_MERKLE_TREE_LEAVES);
```

After 2048 loops,  

Now i will be 2048, [loop](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L211) .

It will try to set the value of `l2ToL1LogsTreeArray` array at index `2048`. 
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L216

But since the max length of the array is 2048, this call will revert.

Hence , the publishing and verifying of the pubdata for the given L1 batch will revert.

## Tools Used
Manual , Foundry
## Recommended Mitigation Steps

Validate that 
```solidity
numberOfL2ToL1Logs <= L2_TO_L1_LOGS_MERKLE_TREE_LEAVES
```
# L-02 No deposit Limit for Weth , like other ERC20s

L1 ERC20 Bridge contract has [deposit](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L340) limit for all ERC20 tokens which can be enforced by zkSync team, but there is no deposit limit on WETH which is also an ERC20. This limit is enforced as a risk measure as protocol is new right now. 
We recommend to add a similar limit on WETH bridging also until the team is satisfied of the protocol's robustness in terms of security.
