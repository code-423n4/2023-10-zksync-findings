**Introduction**

zkSync Era is a fully-fledged Layer-2 scaling solution, combining a set of smart contracts on Ethereum mainnet and zkEVM for enabling Ethereum virtual machine-compatible smart contract execution.

# Approach taken in evaluating the codebase

> My analysis of zkSync Era Protocol Included understanding the architecture, mechanism, overall codebase, and possible risks associated with the protocol.

- Day 1: I spent time reading the different available documentation particularly the technical documentation in order to have a deep understanding of the protocol. 

- Day 2: I analyzed the codebase for better understanding. 

- Day 3: Performed a Mechanism review of the protocol. 

- Day 4: I researched possible systemic risks and centralization risks.

- Day 5: I spent time coming up with possible Architecture recommendations from identifying possible risks.

- Day 6: I dedicated this day to preparing the final analysis report.



# Codebase quality analysis


The zkSync codebase is well-structured and well-documented. However, there are areas where improvements could be made, particularly in terms of gas efficiency and permission management.

> Analysis of the codebase (What’s unique? What’s using existing patterns?): 

- Unique: This contract carries out specific governance mechanisms that are uniquely designed for its specific use case. 

- Existing Patterns: The contract adheres to common contract management patterns, such as the use of onlyowner, onlyGovernorOrAdmin, and functions for administrative transitions ( setPendingAdmin, setPendingGovernor ). 

**Strengths**

- The zkSync codebase is well-structured and well-documented. 

**Weaknesses**

- Many contract files use floating instead of fixed compiler versions. This is not recommended unless for libraries and packages. 

- A considerable number of files are missing NatSpec 

- The codebase is vulnerable to centralization risks and a single point of failure.


# Centralization risks

Having a single EOA as the only owner of contracts is a large centralization risk and a single point of failure. A single private key may be taken in a hack, or the sole holder of the key may become unable to retrieve the key when necessary, or the single owner may become malicious and perform a rug-pull. Consider changing to a multi-signature setup, and or having a role-based authorization model.

> File: cache/solpp-generated-contracts/common/AllowList.sol

```
51:      function setAccessMode(address _target, AccessMode _accessMode) external onlyOwner {

60:      function setBatchAccessMode(address[] calldata _targets, AccessMode[] calldata _accessModes) external onlyOwner {

85       function setBatchPermissionToCall(
86           address[] calldata _callers,
87           address[] calldata _targets,
88           bytes4[] calldata _functionSigs,
89           bool[] calldata _enables
90:      ) external onlyOwner {

108      function setPermissionToCall(
109          address _caller,
110          address _target,
111          bytes4 _functionSig,
112          bool _enable
113:     ) external onlyOwner {

131:     function setDepositLimit(address _l1Token, bool _depositLimitation, uint256 _depositCap) external onlyOwner {
```


> File: cache/solpp-generated-contracts/governance/Governance.sol

```
131:     function scheduleTransparent(Operation calldata _operation, uint256 _delay) external onlyOwner {

144:     function scheduleShadow(bytes32 _id, uint256 _delay) external onlyOwner {

156:     function cancel(bytes32 _id) external onlyOwnerOrSecurityCouncil {

169:     function execute(Operation calldata _operation) external onlyOwnerOrSecurityCouncil {
```

> File: cache/solpp-generated-contracts/zksync/ValidatorTimelock.sol

```
54:      function setValidator(address _newValidator) external onlyOwner {

61:      function setExecutionDelay(uint32 _executionDelay) external onlyOwner {
```
> File: cache/solpp-generated-contracts/zksync/facets/Admin.sol

```
22:      function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {

46:      function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {

70:      function setValidator(address _validator, bool _active) external onlyGovernorOrAdmin {

77:      function setPorterAvailability(bool _zkPorterIsAvailable) external onlyGovernor {

85:      function setPriorityTxMaxGasLimit(uint256 _newPriorityTxMaxGasLimit) external onlyGovernor {

100:     function executeUpgrade(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {

111:     function freezeDiamond() external onlyGovernor {

122:     function unfreezeDiamond() external onlyGovernorOrAdmin {
```

> File: cache-zk/solpp-generated-contracts/NonceHolder.sol

```
84:      function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall {
```
> File: cache-zk/solpp-generated-contracts/SystemContext.sol
```
89:      function setTxOrigin(address _newOrigin) external onlyCallFromBootloader {

95:      function setGasPrice(uint256 _gasPrice) external onlyCallFromBootloader {

314      function setL2Block(
315          uint128 _l2BlockNumber,
316          uint128 _l2BlockTimestamp,
317          bytes32 _expectedPrevL2BlockHash,
318          bool _isFirstInBatch,
319          uint128 _maxVirtualBlocksToCreate
320:     ) external onlyCallFromBootloader {

418      function setNewBatch(
419          bytes32 _prevBatchHash,
420          uint128 _newTimestamp,
421          uint128 _expectedNewNumber,
422          uint256 _baseFee
423:     ) external onlyCallFromBootloader {
```

# Mechanism review

- The mechanisms implemented in the zkSync ecosystem, including the ERC20 standard and the various modules for ownership management and execution, are well-designed. They provide a comprehensive range of features and capabilities.

- Nonetheless, there are some potential issues and risks associated with these mechanisms. For example, the the centralization risk and single point of failure issue highlighted in this analysis. This issue should be carefully considered and mitigated to ensure the security and reliability of the ecosystem.


# Systemic risks

- Like any smart contract-based system, zkSync is exposed to potential coding bugs or vulnerabilities. Exploiting these issues could result in the loss of funds or manipulation of the protocol. 

- Test Coverage: zkSync did not provide a test coverage for the codebase, none the less I recommend 100% test coverage.

- Governance Mechanism Security: The contract’s governance mechanism is critical for its operation. A poorly implemented governance mechanism could lead to system-wide issues.

- External Contract Dependencies: The zkSync relies on a considerable number of external contracts. If any of these contracts have vulnerabilities, it would affect the protocol. 


# Architecture recommendations

- I recommend adding a test coverage to guide code reviewers and auditors.

- I greatly recommend zkSync to be designed with decentralization as main priority to avoid the huge centralization issue the protocol is currently exposed to.

**Gas Optimizations**

- Review data types: Analyze the data types used in your smart contracts and consider if they can be further optimized. For example, changing uint256 to uint128 or uint94 can save gas and storage slots. 

- Struct packing: Look for opportunities to pack structs into fewer storage slots. By carefully selecting appropriate data types for struct members, you can reduce the overall storage usage. 

- Use constant values: If certain values in your contracts are constant and do not change, declare them as constants rather than storing them as state variables. This can significantly save gas costs. 

- Avoid unnecessary storage: Examine your code and eliminate any unnecessary storage of variables or addresses that are not required for contract functionality. 

- Storage vs. memory usage: When working with arrays or structs, consider whether using storage instead of memory can save gas. Using storage allows direct access to the state variables and avoids unnecessary copying of data.
 
- Replacing the use of memory with calldata for read-only arguments in external functions.

**Other recommendations**

- Regular code reviews and adherence to best practices. 
- Conduct external audits by security experts. 
- Consider open sourcing the contract for community review. 
- Maintain comprehensive security documentation. 
- Establish a responsible disclosure policy for vulnerabilities. 
- Implement continuous monitoring for unusual activity. 
- Educate users about risks and best practices.

**Conclusion**

The zkSync protocol is a well-structured platform. However, there are areas where improvements could be made, particularly in terms of permission management and gas efficiency. By addressing these issues, the zkSync ecosystem could become even more secure, efficient, and reliable.



### Time spent:
48 hours