# Low / Informational issues

## contracts
1. The current implementation of `Mailbox` doesn't validate the size of the `uint256 _l2GasLimit` in the `requestL2Transaction()` function. It's essential for this value to be less than or equal to `uint32.max` later on, as the `Bootloader` will revert if this condition is not met. Currently, there's no issue because another parameter, `_l2GasPerPubdataByteLimit`, is fixed at 800. However, this constant is expected to change in the future, as stated in the documentation and by the development team.
If, in the future, `_l2GasPerPubdataByteLimit` is allowed to be set to a value greater than 71635 without additional checks, it would allow someone to input an extremely high `_l2GasLimit` (2^32 and more), causing the transaction to consistently fail when a validator tries to create a new batch. Since L1 transactions are forced to be executed, this will trigger a system-wide DoS scenario, disrupting the system until a fix is applied. It's crucial to address this issue to prevent such disruptions and ensure compatibility with the `Bootloader`.
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L251-L256
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/system-contracts/bootloader/bootloader.yul#L3075-L3078
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L256

2. The documentation underscores the importance of ensuring that neither governance nor the security council can exploit their privileges to bypass the established limitations. While the assumption is that these entities are acting in good faith, it is crucial to prevent any potential misuse of power.
Governance is subject to a timelock mechanism for function calls, which is a commendable measure. However, there is a notable concern related to the `Governance.scheduleShadow()` function, which could be utilized discreetly to perform upgrades without providing a clear description of the changes. Additionally, the absence of a minimum limit on the `Governance.updateDelay()` function could result in a situation where `minDelay` is set to zero. This configuration effectively grants administrators the ability to create and execute transactions, including potentially malicious upgrades, instantaneously within the same block.
https://github.com/code-423n4/2023-10-zksync/blob/0d7a0f6b7770778da83820bc5e338602ca9d9938/code/contracts/ethereum/contracts/governance/Governance.sol#L135-L145
Furthermore, administrators possess the capability to modify the security council to include themselves via the `Governance.updateSecurityCouncil()` function. This action has the potential to introduce conflicts of interest and raise questions about the integrity of the system's governance structure. Addressing these concerns is essential to uphold the principles of transparency and accountability within the system.
https://github.com/code-423n4/2023-10-zksync/blob/0d7a0f6b7770778da83820bc5e338602ca9d9938/code/contracts/ethereum/contracts/governance/Governance.sol#L247-L259

3. `ergs` term (deprecated) is used instead of `gas` in the comments and documentation.
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L159
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/system-contracts/bootloader/bootloader.yul#L1179
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/system-contracts/bootloader/bootloader.yul#L1231
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/system-contracts/bootloader/bootloader.yul#L1233
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/system-contracts/bootloader/bootloader.yul#L1263
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/system-contracts/bootloader/bootloader.yul#L1596

4. There's no ERC721 and ERC1155 support in `Governance`. This can lead to future problems with proposals that include transfer of these tokens. Add `onERC721Received`, `onERC1155Received`, and `onERC1155BatchReceived` to the `Governance` contract. See the original OZ's implementation:
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/fd81a96f01cc42ef1c9a5399364968d0e07e9e90/contracts/governance/TimelockController.sol#L390-L421

5. Unused import of the `Ownable` library in the `Base` facet.
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L9

6. `updateSecurityCouncil()` doesn't implement two-step transfer of privileges, similar to `AdminFacet` and OZ's `Ownable2Step`.
https://github.com/code-423n4/2023-10-zksync/blob/0d7a0f6b7770778da83820bc5e338602ca9d9938/code/contracts/ethereum/contracts/governance/Governance.sol#L254-L259

7. The `ValidatorTimelock` currently has a vulnerability that allows two different validators to bypass its protection. This means that the timelock, which is meant to prevent immediate batch execution in the event of a zero-day vulnerability or stolen private keys, is ineffective. This issue becomes critical in a future scenario where multiple validators can participate, as per the project's goals.
https://github.com/code-423n4/2023-10-zksync/blob/0d7a0f6b7770778da83820bc5e338602ca9d9938/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L75-L91
https://github.com/code-423n4/2023-10-zksync/blob/0d7a0f6b7770778da83820bc5e338602ca9d9938/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L111-L128
The core purpose of the `ValidatorTimelock` is to impose a delay before batch execution. However, if an attacker has access to two validators (one potentially stolen, and the other registered by the attacker), they can call `ValidatorTimelock.commitBatches()` with the first validator and `ValidatorTimelock.executeBatches()` with the second. This allows the batch to be successfully included since the timelock restriction applies only within the context of a single validator.
For future use, a better approach could be to implement a global storage contract that contains all the committed batches. This would prevent batch execution, regardless of the specific validator calling `ValidatorTimelock.executeBatches()`. 
Considering the project's plans to not use multiple validators at the moment and to rework the code during decentralization, this issue may be classified as low-severity or at least informational. It worth to notice there's a possibility in the code to add multiple validators.

8. There is another concern regarding the presence of multiple validators in the system. It's the possibility to call `ValidatorTimelock.revertBatches()` or `ExecutorFacet.revertBatches()` without any restrictions (if the caller is a validator, of course). In a scenario where multiple validators exist, one of them could hinder the others from executing batches by front-running their `.executeBatches()` with the attacker's `.revertBatches()`.
https://github.com/code-423n4/2023-10-zksync/blob/0d7a0f6b7770778da83820bc5e338602ca9d9938/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L394-L414
https://github.com/code-423n4/2023-10-zksync/blob/0d7a0f6b7770778da83820bc5e338602ca9d9938/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L93-L98
This situation has several implications:
- Blocked validators would incur additional costs because they would need to perform two transactions: `.commitBatches()` and `.proveBatches`, whereas the attacker only requires a single (and small) transaction, which is cheaper. This kind of behavior can be considered a griefing attack. That will make validation of batches unprofitable for the victims.
- It may lead to a temporary DoS of the whole network if the attacker successfully front-runs every validator. This could result in substantial and unpredictable losses for dApps operating on the zkSync network.
It's essential to note that this attack only works when multiple validators are in play. Given the project's plans and constraints, this issue is classified as low-severity or informational, as was the case with the previous issue.

## system-contracts
1. The comment states "return 0", but instead reverts the execution.
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/system-contracts/bootloader/bootloader.yul#L3204-L3208

2. Function `Bootloader.lengthRoundedByWords()` can overflow and return 0 if `len` > `type(uint).max - 30`. It is not a problem right now, because the overflow is checked indirectly in other functions. But since `Bootloader` is considered as upgradeable, this can lead to an issue in the future.
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/system-contracts/bootloader/bootloader.yul#L539-L542

3. Misleading `_SLOT` is used in the name of the function, but it refers to the bytes instead (slot 523260, bytes 16744320, it's the end of the actual transaction’s descriptions). Other functions that refer to bytes use `_BYTES` in the end.
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/system-contracts/bootloader/bootloader.yul#L434

4. Abstract contract with logic treated like an interface. It's inside the `interfaces` folder and has a confusing `I*` name, which are conventionally used to mark interfaces, not abstract contracts.
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/system-contracts/contracts/interfaces/ISystemContract.sol

5. Typo in `Bootloader`. Some functions use `innerTxDataOffst` instead of `innerTxDataOffset`.
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/system-contracts/bootloader/bootloader.yul#L2023-L2025
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/system-contracts/bootloader/bootloader.yul#L2047-L2049
https://github.com/code-423n4/2023-10-zksync/blob/ebec640786cf3cb791fa77a856ad9d2a554dad3f/code/system-contracts/bootloader/bootloader.yul#L2196-L2201

6. `Keccak256` precompile contract uses `lt(bytesSize, sub(BLOCK_SIZE(), 1))` condition to optimize execution. However, this means that in the case when `bytesSize` equals 135, it will take the more resource-intensive path, even though the result is the same (40 gas). The correct condition should be `lt(bytesSize, BLOCK_SIZE())`. This correction ensures that the optimization behaves as intended and avoids the costly path when `bytesSize` is 135.
https://github.com/code-423n4/2023-10-zksync/blob/0d7a0f6b7770778da83820bc5e338602ca9d9938/code/system-contracts/contracts/precompiles/Keccak256.yul#L70

## era-zkevm_circuits
1. Typo in the name of a public constant `CIRCUIT_VERSOBE`. The correct name is `CIRCUIT_VERBOSE`.
https://github.com/code-423n4/2023-10-zksync/blob/9c4aa015c755f4599db2256299ba02af4d21070c/code/era-zkevm_circuits/src/config.rs#L2
https://github.com/code-423n4/2023-10-zksync/blob/9c4aa015c755f4599db2256299ba02af4d21070c/code/era-zkevm_circuits/src/config.rs#L5