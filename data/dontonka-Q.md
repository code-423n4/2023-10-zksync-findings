**[[1]]** 
## Description
Bootloader is essentially the zkEra VM that is processing transactions in L2. So for every L2 block (aka batch), it does process all the L2 transactions (also L1 --> L2 txs), bundle them into an L2 block, and publish the result to L1.

The problem is that it is expected that all transactions are in a [continious array](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L3728-L3729), but if such assumption is ever broken somehow, the code will `break` the loop and move on with was processed. Which means if this happen in real life and there were some transactions after this empty pointer, they will be `lost` forever and not added to the L2 block.

## Impact
Transaction processed by the `bootloader` have the potential to be `lost` and not included in the corresponding L2 block.

## Recommended Mitigation Steps
If this developer assumtion is ever broken (and loop ends early instead of as expected by `lt(txPtr, TXS_IN_BATCH_LAST_PTR()`), this should `revert` instead of breaking the loop, such that no transaction can be lost.

```diff
diff --git a/code/system-contracts/bootloader/bootloader.yul b/code/system-contracts/bootloader/bootloader.yul
index be77edc..5e34254 100644
--- a/code/system-contracts/bootloader/bootloader.yul
+++ b/code/system-contracts/bootloader/bootloader.yul
@@ -3727,8 +3727,8 @@ object "Bootloader" {
                 if iszero(execute) {
                     // We expect that all transactions that are executed
                     // are continuous in the array.
-                    break
-                }
+                    assertionError("Tx data is not in contious array")
+                }

                 let txDataOffset := mload(add(txPtr, 32))
```


**[[2]]** 
The require should be numberOfL2ToL1Logs <= `L2_TO_L1_LOGS_MERKLE_TREE_LEAVES`. 
```solidity
        uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
        require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L205-L206


**[[3]]** 
There should be an additional require as follow: `require(_deployBridgeImplementationFee != 0 && _deployBridgeProxyFee != 0);`, as even if the sum does match msg.value, if one of these is 0, it will revert later on.
```solidity
    function initialize(
        bytes[] calldata _factoryDeps,
        address _l2WethAddress,
        address _governor,
        uint256 _deployBridgeImplementationFee,
        uint256 _deployBridgeProxyFee
    ) external payable reentrancyGuardInitializer {
        require(_l2WethAddress != address(0), "L2 WETH address cannot be zero");
        require(_governor != address(0), "Governor address cannot be zero");
        require(_factoryDeps.length == 2, "Invalid factory deps length provided");
        require(
            msg.value == _deployBridgeImplementationFee + _deployBridgeProxyFee,
            "Miscalculated deploy transactions fees"
        );
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L88-L94


**[[4]]** 
There should be an additional requires as follow: 
- `require(_l2TxGasLimit != 0 && _l2TxGasPerPubdataByte != 0);`
- `require(msg.value != 0);`
```solidity
    function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        uint256 _l2TxGasLimit,
        uint256 _l2TxGasPerPubdataByte,
        address _refundRecipient
    ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {
        require(_l1Token == l1WethAddress, "Invalid L1 token address");
        require(_amount != 0, "Amount cannot be zero");
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L159-L168


**[[5]]** 
If by user mistake `l1WethWithdrawReceiver` is a smart contract that can't operate with ERC20 tokens, those ETH will be stuck in the bridge forever. Maybe it could be interesting to have a function to recover those funds to the benefits of zkSync.
```solidity
        // Transfer WETH tokens from the smart contract address to the withdrawal receiver
        IERC20(l1WethAddress).safeTransfer(l1WethWithdrawReceiver, amount);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L262-L263


**[[6]]** 
This comment is not accurate. That is true for L1WethBridge, but for L1ERC20Brige, only the excess gas will be refunded, `_l2Value is always zero`. 
```solidity
		/// @notice Initiates a deposit by locking funds on the contract and sending the request
		/// of processing an L2 transaction where tokens would be minted
		/// @param _l2Receiver The account address that should receive funds on L2
		/// @param _l1Token The L1 token address which is deposited
		/// @param _amount The total amount of tokens to be bridged
		/// @param _l2TxGasLimit The L2 gas limit to be used in the corresponding L2 transaction
		/// @param _l2TxGasPerPubdataByte The gasPerPubdataByteLimit to be used in the corresponding L2 transaction
		/// @param _refundRecipient The address on L2 that will receive the refund for the transaction.
		/// @dev If the L2 deposit finalization transaction fails, the `_refundRecipient` will receive the `_l2Value`.
		/// Please note, the contract may change the refund recipient's address to eliminate sending funds to addresses
		/// out of control.
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L162


**[[7]]** 
There is a typo in the comment, it should mention `the transaction's execution has ended`.
```solidity
		/// @notice The id of the VM hook that notifies the operator that the transaction's execution has started.
		function VM_HOOK_TX_HAS_ENDED() -> ret {
			ret := 4
		}
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L3538-L3541


**[[8]]** 
There is a typo in the comment `2 bytes total len of compressed`, it should mention 3 bytes instead.
```solidity
        /// Check State Diffs
        /// encoding is as follows:
        /// header (1 byte version, 2 bytes total len of compressed, 1 byte enumeration index size, 2 bytes number of initial writes)
        /// body (N bytes of initial writes [32 byte derived key || compressed value], M bytes repeated writes [enumeration index || compressed value])
        /// encoded state diffs: [20bytes address][32bytes key][32bytes derived key][8bytes enum index][32bytes initial value][32bytes final value]
        require(
            uint256(uint8(bytes1(_totalL2ToL1PubdataAndStateDiffs[calldataPtr]))) ==
                STATE_DIFF_COMPRESSION_VERSION_NUMBER,
            "state diff compression version mismatch"
        );
        calldataPtr++;

        uint24 compressedStateDiffSize = uint24(bytes3(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 3]));
        calldataPtr += 3;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L281C37-L281C68
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L291-L292



**[[9]]** 
The constant `MAX_MSG_VALUE` is now unused, please remove. 
```solidity
		/// @dev The maximal msg.value that context can have
		uint256 constant MAX_MSG_VALUE = 2 ** 128 - 1;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Constants.sol#L72-L73





