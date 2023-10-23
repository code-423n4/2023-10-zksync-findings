## Advanced Analysis Report for [ZkSync](https://github.com/code-423n4/2023-10-zksync) by K42

### Overview
This Analysis Report is structured for maximum efficiency, to cover specifically the L1 contracts.

**L1 Contracts:**
- [Section 1: ZkSync](#section-1-zksync)
- [Section 2: Bridges](#section-2-bridges)
- [Section 3: Governance](#section-3-governance)
- [Section 4: Upgrades](#section-4-upgrades)
- [Section 5: Other](#section-5-other)

### Understanding the Ecosystem
#### Data Structures:
- `OperationState`: Enum used in [Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol) to manage the state of governance proposals. Each state is mapped to a specific function, reducing the need for multiple `if-else` conditions.
- `Storage`: Struct in [Storage.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol) that holds the state variables.
- `PriorityQueue`: Custom data structure in [PriorityQueue.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol) for managing transaction priorities.
- `ProposedUpgrade`: Struct in [BaseZkSyncUpgrade.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol) that holds the details of a proposed upgrade, including the new protocol version and the timestamp for the upgrade.
- `AccessMode`: Enum in [AllowList.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol) that specifies the access level of an address. It can be `Public`, `SpecialAccessOnly`, or `NoAccess`.

#### Key Contracts:
- `DiamondProxy`: Delegates calls, risk of incorrect delegation.
- `Executor`: Manages transaction logic, risk of state corruption.
- `Governance`: Manages governance, risk of centralization.
- `Mailbox`: Handles cross-chain messages, risk of message replay.
- `L1ERC20Bridge`: Manages L1 token deposits, risk of front-running.
- `L1EthBridge`: Manages L1 Ether deposits, risk of reentrancy.
- `AllowList`: Manages permissions, risk of unauthorized access.

#### Inter-Contract Communication:
- [Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol) can trigger upgrades in [BaseZkSyncUpgrade.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol) through the `upgrade()` function.
- [DiamondProxy.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol) delegates calls to [Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol), [Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol), and other facets, which can be manipulated if not properly validated.
- `Executor` interacts with `Storage` for state management, which can be corrupted if not properly isolated.

### Codebase Quality Analysis
#### Data Structures:
- `OperationState`: The enum in [Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol) could be vulnerable to incorrect state changes if not properly managed. Ensure that only authorized addresses can change states.
- `ProposedUpgrade`: The struct in [BaseZkSyncUpgrade.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol) should be tightly controlled to prevent unauthorized modifications.
#### Modifiers:
- `onlyOwner`: Used in multiple contracts, including [Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol). This poses a centralization risk and should be replaced with a more decentralized access control mechanism in the future.
#### Security: Refer to [This Section](#specific-risks-and-mitigations-in-key-contracts)

### Architecture Recommendations
- Implement a DAO for [Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol) to replace the `onlyOwner` modifier, reducing centralization.

### Centralization Risks
- [Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol) uses a single `owner` address for critical functionalities, posing a significant centralization risk.

### Mechanism Review
- [Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol) allows instant upgrades without a time lock, posing a risk of rapid, potentially malicious upgrades.

### Systemic Risks
-  [L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol)'s `withdrawTo()` function could be exploited to drain all wrapped Ether if not properly secured.

### Specific Risks and Mitigations in Key Contracts
### Section 1: ZkSync:
**[DiamondProxy.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol):**
- **Risks**: Delegation to incorrect facets.
- **Mitigations**: Strict validation of facet addresses during upgrades.

**[Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol):**
- **Risks**: Incorrect state transition due to bugs in execution logic.
- **Mitigations**: Comprehensive unit tests and formal verification.

**[Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol):**
- **Risks**: Centralization due to a single entity's control over proposals. Incorrect state transitions due to unchecked ``_schedule`` and ``_execute`` calls.
- **Mitigations**: Transition to a DAO-based model. Implement a multi-signature mechanism for proposal initiation and execution. 

### Section 2: Bridges
**[L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol) & [L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol):**
Key Data Structures: 
1. `msg.sender`: Initiator of the deposit.
2. `deposit()`: Main deposit function.
3. `_depositFunds()`: Internal function for transferring tokens.
4. `IERC20(_l1Token).safeTransferFrom()`: ERC20 token transfer.
5. `zkSync.requestL2Transaction()`: Request for L2 transaction.
6. `_refundRecipient`: Address for refund on L2.

### Doable Attack Vectors
### 1. Front-Running on L2 Transaction Request
- **Path**: `msg.sender` -> `deposit()` -> `zkSync.requestL2Transaction()`
- **Specific Risk**: An attacker can observe the pending transaction pool and front-run the user's L2 transaction request. This can allow the attacker to manipulate the state on L2 before the user's transaction gets processed.
- **Mitigation**: Implement a commit-reveal scheme to obscure the transaction details until they are confirmed on-chain. This would require a two-step process where the user first commits to the transaction and later reveals it.

### 2. Gas Price Manipulation in L2 Transaction
- **Path**: `msg.sender` -> `deposit()` -> `zkSync.requestL2Transaction()`
- **Specific Risk**: An attacker can manipulate the `_l2TxGasLimit` and `_l2TxGasPerPubdataByte` parameters to either make the transaction fail due to out-of-gas errors or make it excessively expensive for the user.
- **Mitigation**: Implement server-side checks to ensure that the gas parameters are within a reasonable range. This could be done by setting upper and lower bounds for `_l2TxGasLimit` and `_l2TxGasPerPubdataByte`.

### 3. Refund Address Hijacking
- **Path**: `msg.sender` -> `deposit()` -> `_refundRecipient`
- **Specific Risk**: If `_refundRecipient` is not properly validated or is set to a malicious address, the attacker could receive the refund meant for the user.
- **Mitigation**: Implement rigorous server-side validation for `_refundRecipient` and consider using a whitelist of approved refund addresses.

### Section 3: Governance
**[Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol):**
- The contract uses ``onlyOwner`` and ``onlySecurityCouncil`` modifiers for access control which is good.
- Consider adding events for state changes like ``_schedule`` and ``_execute``.

### Section 4: Upgrades
**[BaseZkSyncUpgrade.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol):**
- **Risks**: Incorrect upgrades due to unchecked ``_setVerifier`` and ``_setAllowList`` calls. State corruption due to unchecked ``_upgradeVerifier`` and ``_setBaseSystemContracts`` calls.
- **Mitigations**: Use a mapping to validate new verifiers against a list of approved addresses. Implement a circuit breaker for emergency stops. Consider adding events for state changes like ``_setVerifier`` and ``_setAllowList``.

### Section 5: Other
**[AllowList.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol):**
- **Risks**: Unauthorized access due to incorrect permissioning.
- **Mitigations**: Rigorous server-side validation and whitelisting. The contract uses ``onlyOwner`` for access control which is good. Consider adding events for state changes like ``_setPermissionToCall`` and ``setDepositLimit``.

### Areas of Concern
- Centralization in [Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol) due to the `onlyOwner` modifier.
- Above mentioned bridge risks. 

### Recommendations
- Implement rate limiters in [L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol) and [L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol).
- Transition [Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol) to a DAO-based model.

### L1 Contract Details
- Function interaction graphs I made for each contract for better visualization, I made these unique and more  precise to function interaction, therefore they differ to the graphs provided in the docs. These are made for providing a visual representation of the contracts architecture:

**Section 1: ZkSync:**

- Link to [Graph](https://svgshare.com/i/yoB.svg) for [Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol).

- Link to [Graph](https://svgshare.com/i/ymy.svg) for [Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol).

- Link to [Graph](https://svgshare.com/i/ynW.svg) for [Diamond.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol).

- Link to [Graph](https://svgshare.com/i/yn8.svg) for [Getters.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol).

- Link to [Graph](https://svgshare.com/i/yoU.svg) for [TransactionValidator.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol).

- Link to [Graph](https://svgshare.com/i/ym_.svg) for [ValidatorTimelock.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol).

- Link to [Graph](https://svgshare.com/i/yoV.svg) for [Admin.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol).

**Section 1: ZkSync: End**

**Section 2: Bridges:**

- Link to [Graph](https://svgshare.com/i/ynn.svg) for [L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol).

- Link to [Graph](https://svgshare.com/i/yo1.svg) for [L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol).

**Section 2: Bridges: End**

**Section 3: Governance:**

- Link to [Graph](https://svgshare.com/i/ynJ.svg) for [Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol).

**Section 3: Governance: End**

**Section 4: Upgrades:**

- Link to [Graph](https://svgshare.com/i/yma.svg) for [BaseZkSyncUpgrade.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol).

- Link to [Graph](https://svgshare.com/i/yng.svg) for [DefaultUpgrade.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol).

**Section 4: Upgrades: End**

**Section 5: Other:**

- Link to [Graph](https://svgshare.com/i/ynK.svg) for [AllowList.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol).

**Section 5: Other: End**

### Conclusion
- The [ZkSync](https://github.com/code-423n4/2023-10-zksync) contracts are robust but have areas that could be improved for both security and decentralization. Specific recommendations and mitigations have been provided for each identified risk. 

### Time spent:
42 hours