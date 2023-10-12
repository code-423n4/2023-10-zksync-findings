**PS:** Some of these issues contains automated findings that weren't referenced in the bot report.

1. Check for overflow before using [UncheckedMath library](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UncheckedMath.sol), as the `unchecked` keyword can set the default overflow and underflow checks to off. This might optimize gas but can cause silent over/underflow.

***
2. Call flags in `getCallFlags` has an inconsistent descriptions.

    According to [SystemContract](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L307) and [EventWriter](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/EventWriter.yul#L52C4-L52C93), `Call flags are the value of the first register at the start of the call.`  But according to the readme, call flags are in the second registry
`r2 — the pointer with flags of the call.`
***
3. `baseFee` description is inconsistent.
   
    According to [SystemContext](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/SystemContext.sol#L47C6-L47C41), `baseFee` `is currently a constant.`. 
However, in the [readme](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md), the baseFee `For now, it is provided by the operator, but it in the future it may become hardcoded.`

    Also in `bootloader.yul`, `baseFee` here is not a constant, [1](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/bootloader/bootloader.yul#L38C17-L40C71) and [2](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/bootloader/bootloader.yul#L3667).
***

4. Code for [testing](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/SystemContext.sol#L443) not removed. Ensure is removed from code before deployment.
   
    According to the comment:
   ```
   /// @notice A testing method that manually sets the current blocks' number and timestamp.
   /// @dev Should be used only for testing / ethCalls and should never be used in production.
   ```
***
5. [`setImmutables`](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/ImmutableSimulator.sol#L34C4-L42C14) in ImmutableSimulator can receive 0x0 address, consider adding a check for 0x0 address in input `_dest`.
***
6.  It's recommended to use [OpenZeppelin](https://github.com/code-423n4/2023-10-zksync/tree/main/code/system-contracts/contracts/openzeppelin) through a build system/smart contract framework rather than just copying the files
***
7. The [ZkSyncMeta](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L42) struct in SystemContractHelper includes the following fields:

```
struct ZkSyncMeta {
    uint32 gasPerPubdataByte;
    uint32 heapSize;
    uint32 auxHeapSize;
    uint8 shardId;
    uint8 callerShardId;
    uint8 codeShardId;
}
   ```

However, the [`getZkSyncMeta`](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L297C1-L303C6)  does not include `heapSize` and `auxHeapSize` in its retrieval of the struct.
    

***
8. Typo in [comment](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/SystemContext.sol#L91)
        ```
        /// @notice Set the the current gas price.
        ```
***
9. Possible centralization risks as owner is a point of failure.

   NonceHolder.sol - [65](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L65), [110](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L110), [L135](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L135);
    
    MSGValuesimulator.sol - [35](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/MsgValueSimulator.sol#L35);
    
    Contractdeployer.sol - [62](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L62), [71](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L71), [167](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L162), [188](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L183);
    
    KnownCodeStorage.sol - [28](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/KnownCodesStorage.sol#L28);
    
    L1messanger.sol - [201](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L199);
    
    L2ethtoken.sol - [64](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L2EthToken.sol#L64);
    
    Systemcontext - [373](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L373), [379](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L379),    [443](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L443); 443 should be removed before deployment. See issue 4.

***
10. Funds may become trapped in the contract due to `payable` property

    ContractDeployer.sol [130](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/ContractDeployer.sol#L130), [146](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/ContractDeployer.sol#L146), [72](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/L2EthToken.sol#L72); 
    
    Compressor.sol [54](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/Compressor.sol#L54);
***

11. Add a deadline argument to [_encodeHashEIP712Transaction](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/libraries/TransactionHelper.sol#L118C14-L118C42)` function in TransferHelper contract since nonce also used. This is for transactions occur in the same block.
***
12. [forceDeployOnAddress](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/ContractDeployer.sol#L214) in ContractDeployer.sol can deploy any address. Recommend a whitelist for needed addresses.
***
13. [`isEthToken`](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/libraries/TransactionHelper.sol#L94C4-L96C6) in TransactionHelper.sol returns true when `_addr` == 0. But it might be safer to let it return false instead. Because, a zero aadreess going through is likely to cause further issues with withdrawal down the line.
