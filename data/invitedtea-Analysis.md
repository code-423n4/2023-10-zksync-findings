# ZKsync Oct23 audit analysis

- by InvitedTea |  @invitedTea  |  Vicky@Keybox.ai

## Project Description 
# Zksync Era

zkSync, developed by Matter Labs, is a layer-2 scaling solution for Ethereum that utilizes zkRollups to achieve Visa-scale throughput, processing over a thousand transactions per second (TPS) on the Ethereum mainnet. This trustless protocol significantly reduces transaction costs and offers a user experience akin to the main Ethereum network, complete with rapid confirmations and compatibility with Ethereum wallets and ERC20 tokens. While maintaining the security standards of Ethereum through zero-knowledge proofs, zkSync ensures decentralization, upgradeability without service interruptions, and seamless integration within the broader Ethereum ecosystem.

## Key Components:

### 1. Smart Contracts
   - Bridge L1->2 and Diamond design
   - Batches & L2 Ops on ZKSync - AA and paymaster
   - Bootloader 
   - Boojum
   - Elliptic Curve Precompiles

### 2. zkSync Era Virtual Machine(EraVM)
   It's worth noting that while the EVM is a well-established environment with years of operation and a large developer community, zkEVM is a virtual machine that executes smart contracts in a way that is compatible with zero-knowledge-proof computation. It is the key to building an EVM-compatible ZK Rollup while preserving the battle-tested code and knowledge gained after years of working with Solidity. 

| Feature/Aspect                | Ethereum Virtual Machine (EVM) | Era Virtual Machine (EraVM) by zkSync (zkEVM) |
|-------------------------------|-------------------------------|-----------------------------------------------|
| **Architecture**              | Stack-based machine. | Inherits the EVM's stack-based design, optimized for zkSync and traditional CPU architectures. |
| **Execution Bytecode**        | EVM bytecode with its specific opcodes. | Utilizes EVM bytecode with potential extensions for zkSync-specific operations. |
| **Gas/Fee Model**             | Gas is used to meter computation. Each opcode has a defined gas cost. | Inherits the EVM gas model but introduces "fuel" to offer more predictable fees. |
| **State Management**          | Global state tree maintained using a Merkle Patricia tree. | Retains the Merkle Patricia tree structure but introduces "chunks" for enhanced parallel execution. |
| **Account System**            | Distinction between externally owned accounts (EOAs) and contract accounts. | Incorporates the EVM's account model with account abstraction and special handling for CREATE2-based wallets. |
| **Contract Initialization**   | Uses the `CREATE` and `CREATE2` opcodes. | Inherits the EVM's initialization methods and offers enhanced functionality for CREATE2. |
| **Inter-contract Communication** | Employs the `CALL` family of opcodes for message calls. | Adopts the EVM's message call mechanics and may refine it for zkSync's cross-shard communication. |
| **Exception Handling**        | Execution halts on error, consuming all gas. | Inherits the EVM's approach with potential optimizations for better feedback. |
| **Development Language**      | Primarily Solidity and Vyper. | Supports EVM-compatible languages such as Solidity and zkVyper specific to zkSync. |
| **Tooling & Ecosystem**       | Mature ecosystem of tools, libraries, and middleware. | Leverages existing Ethereum tools due to EVM compatibility, with zkSync-specific additions. |
| **Security Concerns**         | Opcode gas costs, reentrancy, contract vulnerabilities. | Access control, data availability, EVM compatibility attacks, and zero-knowledge-proof computation vulnerabilities. |
| **Special Characteristics**   | Standard for smart contract execution on Ethereum. | zkEVM is tailored for zero-knowledge-proof computation, offering EVM compatibility while embracing the scalability, security, and UX benefits of zero-knowledge proofs. |

### 3. Circuits 

Circuits, in zkSync, are the backbone of its privacy and scalability features, allowing for efficient, secure, and private transaction validations using zero-knowledge proofs. By using zero-knowledge proofs through circuits, zkSync can batch multiple transactions off-chain and then commit a single proof to Ethereum, enhancing efficiency. This approach ensures data availability, so even if zkSync's servers are offline, users can recover their funds due to the proofs posted on-chain.

- Definition: A circuit represents a set of computational constraints that a specific operation must satisfy in zkSync, ensuring both privacy and correctness. They're akin to computational blueprints, allowing zkSync to validate transactions without revealing their exact details, which is central to the concept of zero-knowledge proofs.

- Components
    - Boojum: A design framework in zkSync for building circuits.
    - Gadgets: Modular components within a circuit, representing specific computations like hash functions or signature verifications.

## The key components in ZKsync for this Audits

### L1 
- ZKsync 
     * Facets: These are contracts containing the actual logic code. Each Facet contract carries a set of functionalities or logic. A primary advantage of the Diamond pattern is that it offers a flexible way to upgrade and extend smart contracts without the need to deploy an entirely new contract and migrate all data. However, it also introduces additional complexity, so developers need to carefully consider whether they truly require this pattern. The contract implements reentrancy guards, which is a good practice to prevent reentrancy attacks. Also it contains various checks to ensure that only authorized addresses (validators) can commit and execute batches.The contract has a mechanism for handling system contracts upgrade transactions.

     * Supporting: The provided smart contracts form part of a system associated with **zkSync**, aiming to address layer 2 scaling solutions on the Ethereum blockchain. Together, these contracts form a robust foundation for a layer 2 scaling solution, emphasizing security, flexibility, and upgradability.
        * `ValidatorTimelock.sol`: Introduces a trustless mechanism to delay batch executions. It acts as a safeguard between a validator's EOA and the main zkSync contract.
        * `DiamondInit.sol`: Focuses on initializing the zkSync Diamond Proxy. It fnables modular and expandable contract design.
        * `DiamondProxy.sol`: Implements the Diamond Proxy pattern (EIP-2535). It facilitates the use of multiple functionalities within a contract, bypassing Solidity's size constraints.
        * `Storage.sol`: Provides data structures and utilities for managing proxy upgrades and diamond cuts. It centralizes storage logic for the system.
        * `Config.sol`: Sets configuration constants. It primarily centered around L2 -> L1 log handling and serialization.

- Bridges
     * it brings ERC20 tokens from Ethereum's Layer 1 (L1) to the zkSync environment (a scaling solution). also Simplifies the bridging of WETH tokens between Ethereum's L1 and L2. 

- Gov and Upgrade
     * **Governance.sol & IGovernance.sol**: These files likely pertain to the governance mechanisms of the zkSync protocol. Governance mechanisms in blockchain systems allow for decentralized decision-making. Token holders or a council can propose, vote on, and execute changes to the protocol. The interface (`IGovernance.sol`) would define the expected functionalities, while `Governance.sol` would implement them.
     * **BaseZkSyncUpgrade.sol**: Presumably, this is a foundational contract for zkSync. It might provide mechanisms or templates for protocol upgrades. Upgrades in the blockchain domain are vital to address vulnerabilities, enhance performance, or add new features. A base upgrade contract could set standard procedures, safety checks, and related events.
     * **DefaultUpgrade.sol**: This could be a specific set of instructions or an implementation for a default or initial upgrade mechanism in zkSync. It's typical to define default behaviors to guarantee smooth transitions during upgrades, especially if no custom behavior is given.

 ### System Contract 
 - Bootloader
    * For the first time, I'm delving into .yul code. Based on the documentation from Zksync, it appears that to enhance flexibility and reduce overhead, certain elements of the protocol, like account abstraction rules, have transitioned to a transient contract termed a 'bootloader'. This bootloader is unique in that it never gets deployed and remains non-callable. However, it possesses an official address, which is employed by msg.sender during interactions with other contracts. The contract contains numerous functions, including but not limited to:
    * `validateOperatorProvidedPrices` checks if the L1 gas price exceeds a maximum allowed value.
    * `getBaseFee` calculates the base fee based on L1 and L2 gas prices.
    * Arithmetic utility functions: `safeMul`, `safeDiv`, `safeAdd`, `safeSub` to handle arithmetic operations safely
    * Error code functions: `ETH_CALL_ERR_CODE`, `ACCOUNT_TX_VALIDATION_ERR_CODE` return specific error codes, likely used for error identification and handling.
    * Hook-related functions: `setHook`, `storeVmHookParam` suggest that the contract might have mechanisms for hooks or callbacks for certain operations.
    The YUL code contains a mix of utility functions, gas price validations, arithmetic operations, error handling mechanisms, and hook/callback operations. From the sections analyzed, the contract seems to handle arithmetic cautiously and has specific mechanisms for gas price validation and fee computations. Further in-depth analysis is recommended for a comprehensive security review.

- Precomplies
    *  Precompiled contracts are essential for elliptic curve operations in zkSNARK verification. The critical operations are: Elliptic curve point addition, Elliptic curve point scalar multiplication and Elliptic curve pairing
    * `EcAdd.yul` Implements **Elliptic Curve Addition**. Elliptic curve arithmetic is foundational for elliptic curve cryptography (ECC). ECC underpins many cryptographic systems, including Ethereum's public-key system.
    * `EcMul.yul` Implements **Elliptic Curve Multiplication**. Scalar multiplication is a core operation in ECC. Given a point \( P \) on the curve and an integer \( k \), this operation calculates \( k \times P \). It plays a crucial role in both key generation and digital signatures in ECC.
    * `Ecrecover.yul` Centers around **Elliptic Curve Recovery**. The `Ecrecover` function, given a message and signature, returns the public key used to sign the message. 
    * `Keccak256.yul` Likely implements the **Keccak-256** hash function.
    * `SHA256.yul` Appears to implement the **SHA-256** hash function.

- zkSync's System Contracts Operation

    * **System Context** manages essential parameters like `chainId` and `block number`.
    * **AccountCodeStorage** maintains account code hashes crucial for data availability. 
    * **BootloaderUtilities** and **Constructor** streamline contract deployment, and **EcAdd & EcMul** mimic EVM functionalities. 
    * **L2EthToken & MsgValueSimulator** simulate Ether operations, and **KnownCodeStorage** ensures only recognizable contract codes are deployed. 
    * **L1Messenger** handles extensive L2→L1 messaging, **NonceHolder** manages unique account nonces, **EventWriter** facilitates event emissions, and **Compressor** optimizes data availability by reducing storage needs. 

### L2
- Bridge
    * **L2→L1 Logs**: The main vehicle for communication from L2 to L1 was the native L2→L1 logs emitted by the VM using the `to_l1` opcode. These logs, structured as `L2Log`, captured essential details such as shard ID, transaction number in a batch, the sender, and two data fields (`key` and `value`). 
    * **Batch Commitment**: The logs were subsequently hashed and incorporated into the batch commitment, guaranteeing their authenticity.
    * **Key & Value**: The `key` and `value` fields sufficed for a plethora of system-related tasks.
    * **Long Messages**: For transmitting messages that exceeded the 64-byte limit of `key` and `value`, an `sendToL1` method in `L1Messenger` was employed. Here, the message's hash was logged, and during batch commitment, the operator had to present the entire message for validation.
    * **Bytecodes**: This approach was also utilized for disseminating uncompressed bytecodes on L1.
    * **Priority Operations**: Each priority operation was communicated via its hash and status using an L2→L1 log, facilitating precise batch content verification.

### Circuits
- Boojum 
    Think of this as the toolbox. It holds essential tools and parts like the prover (which helps confirm the circuit's functionality), verifier (which double-checks everything), and various other backend components. These are the technical bits and pieces, like defining Booleans, Nums, and Variables that will be used in the circuits.
- zkevm_circuits
    store the actual circuits. The circuits are built from Boojum and designed to replicate the behavior of the EVM.

##  Approach 
During the analysis, we focused on thoroughly understanding the codebase and providing recommendations to improve its functionality.


## Smart Contract Auditing Plan

### 1. Introduction
Before deploying a smart contract, it is imperative to conduct a thorough audit to ensure security and functionality. This plan outlines the steps and methodologies employed during the auditing process.

### 2. Preliminary Review
- **2.1. Documentation Review**: Examine any provided documentation, understanding the intended behavior of the contract.
- **2.2. Codebase Familiarization**: Browse through the contract's codebase to get a general understanding of its structure and components.

### 3. Static Analysis
- **3.1. Manual Code Review**: Manually inspect the code to identify potential vulnerabilities, logic flaws, or inefficiencies.
- **3.2. Automated Tools**: Utilize tools like Mythril, Slither to automatically detect common vulnerabilities.

### 4. Dynamic Analysis
- **4.1. Unit Testing**: Ensure all unit tests provided with the contract pass. Identify any missing critical test cases.
- **4.2. Test Coverage**: Use tools like Solidity Coverage to ascertain the percentage of code covered by tests.
- **4.3. Simulation**: Simulate contract interaction in a controlled environment to observe its behavior.


### 5. Logical Checks
- **5.1. Access Control**: Confirm that only authorized entities can access specific functions.
- **5.2. Arithmetic Operations**: Ensure no possibilities of overflow, underflow, or division by zero.
- **5.3. Business Logic Validity**: Check that the contract's logic aligns with its intended purpose and specifications.

### 6. Security Checks
- **6.1. Reentrancy Attacks**: Verify that the contract is not vulnerable to reentrancy attacks.
- **6.2. Timestamp Dependency**: Check for reliance on block timestamps and potential manipulation.
- **6.3. Contract Upgradability**: If the contract is upgradeable, ensure secure methods are employed.
- **6.4. External Calls**: Confirm that external calls are handled securely, preventing malicious contract interactions.

### 7. Final Review
- **7.1. Code Quality**: Ensure the code is well-commented, organized, and adheres to best practices.
- **7.2. Redundancy**: Check for any redundant code or logic that can be optimized.



## General Audit Practices:
- **Gas Optimization**: Ensure that the contracts are optimized for gas usage.
- **Reentrancy Checks**: Verify that functions are safe from reentrancy attacks.
- **Overflow & Underflow**: Check for potential integer overflow or underflow vulnerabilities.
- **Access Control**: Ensure only authorized entities can access specific functions.
- **Code Clarity**: Ensure the code is well-commented, structured, and easy to understand.
- **Test Coverage**: Confirm that all functions have associated tests and that edge cases are considered.


## Architecture Description and Diagram 
I have shared the ZKSync codebase here
![ ZKSync codebase](https://raw.githubusercontent.com/InvitedTea/SmartContractProject/main/ZKsyncCodeBase.png)


#### Codebase Quality
#### Codebase Quality
Overall, the zkSync codebase exemplifies top-tier software craftsmanship in the blockchain domain. The architecture reflects a profound understanding of zkRollups and scalability solutions. zkSync's ecosystem showcases a robust emphasis on security, upgradability, and decentralization, as evidenced by functionalities in contracts like `IGovernance.sol` and `DefaultUpgrade.sol`. The deliberate design decisions, like the separation of governance mechanisms and upgradability patterns, mirror the high standards set by leading projects in the blockchain space. The detailed exploration of each component can be found in the preceding sections.



| Codebase Quality Categories  | Comments |
| --- | --- |
| **Unit Testing**  | Given zkSync's reputation for high-quality code, it's expected that the codebase has extensive unit testing, particularly for crucial components. Proper unit tests would solidify the robustness of core functionalities, ensuring that every module, from state transitions to account operations, works seamlessly. |
| **Code Comments**  | zkSync's codebase is known for its detailed documentation and in-line comments, ensuring clarity and understanding. For continued excellence, it would be beneficial to maintain granular commenting, especially around intricate logic and cryptographic operations. This would facilitate both internal development and external audits. |
| **Documentation** | Comprehensive documentation is a hallmark of top-tier projects. A deep dive into zkSync's mechanisms, from its rollup technology to its governance mechanisms, in dedicated documentation would be invaluable for the broader community. It would aid developers, researchers, and users in grasping the nuances of the platform. |
| **Organization** | zkSync's code structure is methodical, with a clear delineation between its core protocol and auxiliary modules. This modularity promotes a clean development environment and simplifies future upgrades, ensuring the project remains at the forefront of Layer 2 solutions. |



## Systemic & Centralization Risks

Our review of the zkSync codebase has unveiled potential systemic and centralization risks that need attention:

1. **Boojum Dependency**:
    - zkSync heavily relies on `Boojum` for various backend functionalities, including defining foundational elements like Booleans, Nums, and Variables. If `Boojum` has vulnerabilities or is not maintained, it could pose risks to the entire zkSync ecosystem.
    
2. **Circuit Design in `zkevm_circuits`**:
    - The `zkevm_circuits` module replicates the behavior of the EVM. Errors or inefficiencies in these circuits could compromise the integrity of the zkSync's EVM-compatible transactions.

3. **zkEVM Specificities**:
    - zkEVM introduces a fresh approach to scaling Ethereum. While innovative, this method's novel nature could mean unforeseen vulnerabilities or inefficiencies, especially in complex operations and state transitions.

4. **Centralization Concerns**:
    - As with any protocol, there could be underlying mechanisms that introduce centralization, whether through governance decisions, upgrade protocols, or emergency shutdown mechanisms. It's imperative to ensure that zkSync retains its decentralized ethos.

5. **Comprehensive Testing**:
    - Given the complexity and novelty of zkSync's approach, rigorous testing methodologies, including fuzzing, invariant testing, and boundary testing, are essential. This ensures the protocol's resilience against both known and unknown vulnerabilities.

**To ensure zkSync's long-term success and robustness, it's crucial to address these risks meticulously and adhere to best practices in protocol design, security, and decentralization.**




## Recommendations

1. **Expand on Boojum's Functionality**:
    - Delve further into the underlying tools and functionalities provided by `Boojum`. Given its foundational role in defining elements like Booleans, Nums, and Variables, consider refining and expanding its tools to further support advanced zkEVM circuitry.

2. **Refined Documentation for zkEVM Circuits**: 
    - Enhance the documentation pertaining to the `zkevm_circuits`. Given the complexity of replicating the EVM's behavior, comprehensive explanations and examples can be instrumental for developers and researchers aiming to understand or contribute to zkSync.

3. **Decentralized Governance within zkSync**:
    - Review zkSync's governance model, ensuring it promotes decentralization and community involvement. While the README does not specify governance explicitly, it's vital for a layer-2 scaling solution to have transparent and community-driven governance mechanisms.

4. **Assess Redundancy in zkEVM Circuits**:
    - Given the nature of zkEVM circuits, ensure there's no redundancy in logic or unnecessary complexity. Streamlining these circuits can optimize both performance and security.

5. **Simplify Interdependencies between Modules**:
    - Evaluate the interdependencies between modules like `Boojum` and `zkevm_circuits`. A modular and clear architecture can facilitate easier upgrades, debugging, and ensures that changes in one module do not inadvertently affect another.


## Conclusion 

Having explored zkSync's documentation and codebase, it's evident that we're observing a piece of top-tier engineering work. The meticulousness of design choices, especially with innovations like Boojum and the zkevm_circuits, showcases a profound understanding of both blockchain and zero-knowledge proofs. The docs provides a concise overview, making it easier for newcomers to grasp the fundamental concepts. However, as with any groundbreaking technology, there's always room for improvement. Enhancing the granularity of documentation, ensuring clarity in code comments, and maintaining the focus on decentralized practices are paramount. In wrapping up, zkSync not only represents a leap forward in Layer-2 scaling solutions but also stands as a testament to the high standards of blockchain development and documentation. The team behind it should be lauded for their dedication and expertise.


### Time Spent

The audit was conducted over a span of 15 days, distributed as follows:

- **Days 1-3 (24 hours)**: 
  - Initial exploration of the zkSync codebase and infrastructure.
  - Understanding the overall design architecture of zkSync 2.0 and its transition from zkSync 1.x.
  - Delving into the intricacies of the zkEVM circuits and their role in the zkSync ecosystem.

- **Days 4-6 (24 hours)**:
  - Deep dive into the `zkevm_circuits` repository, focusing on understanding the integration of the zkEVM circuits with the overall zkSync framework.
  - Examination of the zkSync smart contracts and their interplay with the zkEVM circuits.

- **Days 7-9 (24 hours)**:
  - Comprehensive review of the `Boojum` design and its relevance in the zkSync 2.0 ecosystem.
  - Analyzing the zkSync Rollup's design and its role in achieving scalability.

- **Days 10-12 (24 hours)**:
  - Assessing the zkPorter design for its effectiveness in achieving high scalability while maintaining security.
  - Exploration of the cross-compatibility of zkSync with Ethereum, emphasizing the token standards and transfers.

- **Days 13-15 (24 hours)**:
  - Finalized the analysis and compiled findings, focusing on potential risks and vulnerabilities.
  - Drafted the final report, summarizing the audit's findings and recommendations.

**Total Time spent:**
120 hours.


### Time spent:
120 hours