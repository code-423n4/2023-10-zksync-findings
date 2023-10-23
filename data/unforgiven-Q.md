The format is like this:
ContractName - FunctionName - bug description
LinkToCode

* MailBox - priority queue - zkSync is not censorship resistant because operator is not enforced to perform prioriy queue transactions
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L46-L50

* bootloader/defaultAccount - processL2Tx/execute - legacy transaction will have wrong gas usage, because the execution flow is `bootloader -> defaultAccount -> MsgValueSimulator -> target` which means 63/64 rule will be applied 3 times to user specified gas.
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L2342-L2348
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L149

* L2ERC20Bridge - initialize - L2ERC20Bridge may have wrong governor because code creates a upgradableBeacon and set governor as admin and doesn't add alias
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L40-L55

* L1Messenger - sendL2ToL1Log/sendToL1 - L1->L2 log hashes can be equal. may create issue for external systems and some 3rd party contract logics.
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L80-L87
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L128-L135

* ExecutorFacet/mailbox - l2 transaction - expiration time of priority queue's items not handled in code
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L293-L297

* Diomond - _removeFacet/_saveFacetIfNew - function `_removeFacet()` doesn't delete the facetToSelectors[].index, even so facet is removed from list, this can make facets to have same index and unexpected results
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L260-L279

* bootloader - l1TxPreparation  - code doesn't set tx.origin before marking factory deps for L1 transactions, unlike L2 transactions 
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1003-L1021

* mailbox - requestL2Transaction - code doesn't make sure that aliased address(`from`) is not from kernel space, a random address (the chance is nearly 0) can create upgrade transactions
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236-L273

* Admin Facet - acceptGovernor - when governance address changes in Admin Facet code should enforce change of owner of the L2 bridges too, otherwise there will be governance mismatch and access control loss
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L28-L39

* solidity version - solidity verion 8.0 and 8.13 have some bugs. need to update solidity, https://soliditylang.org/blog/2022/09/08/storage-write-removal-before-conditional-termination/
https://soliditylang.org/blog/2022/08/08/calldata-tuple-reencoding-head-overflow-bug/
https://soliditylang.org/blog/2022/06/15/inline-assembly-memory-side-effects-bug/

* ContractDeployer - forceDeployOnAddresses - code shouldn't allow force deploy on certain addresses like bootloader, ContractDeployer , ....
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L235-L256

* DefaultAccount - fallback - gas usage is not same as EOA
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L222-L227

* Governence/AdminFacet - executeInstant - when changing adminFacets's governece, ready to go operations in Governance will be lost
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L164-L181

* Mailbox - validateL1ToL2Transaction - doesn't make sure that transaction can generate guaranteed pub data, if gasLimit was too low then transaction couldn't do it, this is get checked for L2 transactions
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L18-L44

* bootloader - ensurePayment - user should be able to specify paymaster calls gas, because paymaster can spend user gas too and cause revert
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L652-L732

* L2ERC20Bridge - withdraw - all other bridges in system doesn't let for 0 withdraw or deposit but this bridge allows for 0 withdraw
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L105-L118

* L1WETHBridge - deposit - code assumes external calls for WETH contract works correctly and doesn't double check contract balance
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L170-L174

* bootloader - processL1Tx - wrong comment in code that says L1->L2 are free
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L929-L931

* Dimond - _saveFacetIfNew - if number of facets go higher than 2^16 then it would broke the contract's logics
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L195-L198

* Governence - execute - if ready operation most be done then `execute()` should be callable by everone
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L164-L181

* AdminFacet - setValidator - leakage of admin private keys can break the protocol and all the funds would be lost, because hacker can disable all the validators. there should be check that ensure system have at least one validator
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L65-L71

* DefaultUpgrade/TransactionValidator - _setL2SystemContractUpgrade/validateUpgradeTransaction - admin shouldn't be able to deploy new contract to bootloader address by upgrade, and also not allow _from to be bootloader
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L45-L61

* Executor/Config - MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES - the value should be `4 + 8 * L2_TO_L1_LOG_SERIALIZE_SIZE` not `4 + 512 * L2_TO_L1_LOG_SERIALIZE_SIZE`
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Config.sol#L11-L15

* bootloader - all code - it's possible to generate different hash for batch commitment with different bootloader memory hash, because some slots are overwritten and bootloader code doesn't check to make sure all unused slots are 0x0
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L3709-L3711