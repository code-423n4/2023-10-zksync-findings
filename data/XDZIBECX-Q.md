## ISSUE N°1: 
in this contract https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/EventWriter.yul#L1C1-L170C2
it's  contains several functions that perform various operations related to event decoding and writing, 
These functions are used to perform various operations. For example, getExtraAbiData_0(), getExtraAbiData_1(), getExtraAbiData_2(), getExtraAbiData_3(), getExtraAbiData_4(), getCallFlags(), eventInitialize(), eventWrite(), writeFirstTopicWithDataChunk(), writeFirstTwoTopics(), writeThirdTopicWithDataChunk(), writeSecondTwoTopics(), and onlySystemCall() are all helper functions that perform various operations, so the use of low-level calls such as verbatim_0i_1o and verbatim_2i_0o. These calls do not automatically propagate errors. If the called contract throws an exception, the current contract will not be notified, which can lead to unexpected behavior. This is present in lines 24, 30, 36, 42, 48, 57, 64, 71, and others, this need to solve with  adding error handling to the contract to catch any exceptions that might be thrown by the low-level calls.
## ISSUE N°2: 
here in this part https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L28C8-L32C1  If any of the functions called within this block of code do not check their return values, this could lead to unexpected behavior. For example, if a function is supposed to return a boolean indicating success or failure, and this return value is not checked, the contract may continue to execute even if the function failed.
```solidity
_setNewProtocolVersion(_proposedUpgrade.newProtocolVersion);
 _upgradeL1Contract(_proposedUpgrade.l1ContractsUpgradeCalldata);
_upgradeVerifier(_proposedUpgrade.verifier, _proposedUpgrade.verifierParams);        _setBaseSystemContracts(_proposedUpgrade.bootloaderHash, _proposedUpgrade.defaultAccountHash);
```