### NC-1 Inconsistent use of license Identifier 

Majority of contracts use 
```javascript
// SPDX-License-Identifier: MIT 
```
However the following have different licenses e.g // SPDX-License-Identifier: UNLICENSED
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IBase.sol 

While there are instances where using other code e.g IWETH.sol or AddressAliasHelper.sol using // SPDX-License-Identifier: Apache-2.0; its expected for own contracts like IBase.sol an interface for own contract Base.sol which uses MIT there is no need for the two to differ, they must use same licence with all others using MIT

Impact:--> The above leads to inconsistencies on the license for the project and may even have legal, usage, open source, and reputation issues on the project 

Recommendation: --> It is recommended to make use of same license identifier in allowed cases. Where not possible it is ideal to comment or document this

### NC-2 Missing or Broken Links to Contracts.
The audit scope [lists some contracts to be in scope](https://code4rena.com/contests/2023-10-zksync-era#top).

However these links in audit description are broken. It seems system-contracts/contracts/interfaces/IKnownCodesStorage.sol and zksync/contracts/vendor/AddressAliasHelper.sol not available or a broken links 

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/IKnownCodesStorage.sol%5D

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/vendor/AddressAliasHelper.sol%5D 

Impact:--> The above affects the auditability contracts as contracts may be thought to be needed, omitted or take time to find, or may indicate missing logic or incomplete contracts .

Recommendation: --> Recommended links be corrected and files be moved to libs folder

### NC-3 Inconsistent use of _ (underscore) for function, event arguments etc,.

The contracts seem to all make use of underscore _ for function arguments e.g 
```
function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {

```
However there are functions not following this best practise as in cases below 

https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/governance/IGovernance.sol#L65
https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/governance/IGovernance.sol#L68
In above contracts in events uint256 delay is not underscored although all other event params are 

https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L28
address l1Address not underscored in the 2 functions in contract 

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol
In above contract some function arguments are underscored whereas others are not 
e.g 
```
function eventInitialize(uint256 initializer, uint256 value1) internal {
// vs
function setValueForNextFarCall(uint128 _value) internal returns (bool success) {
```

https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/interfaces/ISystemContextDeprecated.sol#L12
One function uses underscore for params/arguments the other does not 


https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L147
In above contracts all other function use underscore expect for this one **bytes32 salt**
```
function _deployBeaconProxy(bytes32 salt) internal returns (BeaconProxy proxy) {
```

https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/zksync/contracts/bridge/interfaces/IL2StandardToken.sol#L6
event does not underscore arguments/params like the other event in same contract 

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/SystemContractsCaller.sol
Does not underscore function arguments/params at all 

Impact:--> The above leads to inconsistencies, affects code readability, maintanability

Recommendation: --> It is recommended to make use of consistency in applying (underscor) _ to arguments and fix all cases in all contracts in scope

### NC-4 Lack of checks for empty arrays 

https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L291
StoredBatchInfo[] calldata _batchesData not checked if empty results in uncessaray updates that don't update or execute anything 

https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L105
https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L113
Above when StoredBatchInfo[] calldata _newBatchesData or StoredBatchInfo[] calldata are empty results in unnecessary call to _propagateToZkSync() with empty data. 

The above has examples arrays used without checking that they are not empty e.g [] The above can lead to unexpected errors, unnecessary calls, unnecessary updates, gas wastage, etc. 

It is important to ensure that arrays in cases where empty array results in code logic proceeding necessarily have enforcements that require(arrayInput.length != 0) 

### NC-5 - Inconsistent spacing in comments or empty comments 

https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/zksync/contracts/bridge/L2Weth.sol#L18
https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/libraries/TransactionHelper.sol#L292
https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/governance/Governance.sol#L15
Above are all example of empty comments e.g \\\ or \\


https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/L1Messenger.sol#L189
Above link shows problems with spacing 
```
 /// @notice Verifies that the {_totalL2ToL1PubdataAndStateDiffs} reflects what occurred within the L1Batch and that
    ///         the compressed statediffs are equivalent to the full state diffs.
    /// @param _totalL2ToL1PubdataAndStateDiffs The total pubdata and uncompressed state diffs of transactions that were
    ///        processed in the current L1 Batch. Pubdata consists of L2 to L1 Logs, messages, deployed bytecode, and state diffs.
    /// @dev Function that should be called exactly once per L1 Batch by the bootloader.
    /// @dev Checks that totalL2ToL1Pubdata is strictly packed data that should to be published to L1.
    /// @dev The data passed in also contains the encoded state diffs to be checked again, however this is aux data that is not
    ///      part of the committed pubdata.
    /// @dev Performs calculation of L2ToL1Logs merkle tree root, "sends" such root and keccak256(totalL2ToL1Pubdata)
    /// to L1 using low-level (VM) L2Log.
```
The last @dev its continuation sentence "to L1 using..." part starts //// to whereas the others e.g "processed" or "part" start more indented within but at different indents 

https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L150
Link above comments would be neater and cleaner with multi-line comments /* */ vs // 

It is important to check all comments to ensure they are formatted and spaced correctly or there are no empty comment lines 