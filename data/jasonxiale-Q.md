# L-01 constant value 1 is used as chainId in `Executor.proveBatches`
File:https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L351
In [Executor.proveBatches](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L351), **1** is used as block.chainid, I think it's better not to using **constant 1** as chainId, and use some variable instead.

# L-02 ETH might be locked in `ComplexUpgrader` contact
File: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ComplexUpgrader.sol#L21-L31
Function [ComplexUpgrader.upgrade](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ComplexUpgrader.sol#L21-L31) is defined as **payable**, but the  contract doesn't provide function to withdraw the ETH it receives, if any ETH is send to the contract, the ETH is locked.

# L-03 address in yul doesn't clean remaining bits
File:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L136
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L162
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L178
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L208

According to [UnsafeBytesCalldata.sol#L15-L16], the remaining bits should be cleaned in yul.

# L-04 `gasleft()` in `DefaultAccount._validateTransaction` should be casted by `Utils.safeCastToU32`
File: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L84
In [DefaultAccount.sol#L84](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L84), the `gasleft()'s` return value is casted by `uint32`, but I think it's better wrapped by `Utils.safeCastToU32`

# L-05 `gasleft()` in `L2ERC20Bridge._deployBeaconProxy` should be casted by `Utils.safeCastToU32`
File: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L149
In [L2ERC20Bridge.sol#L149](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L149), the `gasleft()'s` return value is casted by `uint32`, but I think it's better wrapped by `Utils.safeCastToU32`

# L-06 in `s.verifier.verify` is called twice in `Executor.proveBatches`
File:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L367
In [Executor.sol#L354-L367](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L367), `s.verifier.verify` is called twice, and since the function doesn't change any state/memory, we can delete one `s.verifier.verify` call

# L-07 `Executor._batchAuxiliaryOutput` check `_batch.systemLogs.length <= MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES` which is not accurate
File:
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L453
In [Executor.sol#L453](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L453), the function check if `_batch.systemLogs.length <= MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES`
According to [Config.sol#L14-L19](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Config.sol#L14-L19), **MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES** should be `4 + 88 * 512`.
In fact, `_batch.systemLogs.length` can be 88*8 at most because of [_processL2Logs._processL2Logs](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L123-L169)