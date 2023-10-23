


## Phase 1: Documentation and Video Review

A: Start with a comprehensive review of all documentation related to the zkSync Era platform, including the whitepaper, API documentation, developer guides, user guides, and any other available resources.

B: Watch any available walkthrough videos to gain a holistic understanding of the system. Pay close attention to any details about the platform’s architecture, operation, and possible edge cases.

C: Note down any potential areas of concern or unclear aspects for further investigation.

## Phase 2: Manual Code Inspection

I: Once familiarized with the operation of the platform, start the process of manual code inspection.

II: Review the codebase section by section, starting with the core smart contract logic. Pay particular attention to areas related to DiamondProxy,AdminFacet, MailBox,ExecutorFacet,L1ERC20Bridge,Governance,ValidatorTimelock,AccountCodeStorage,BootloaderUtilities,L1Messenger and Compressor Contracts

III: Look for common vulnerabilities such as reentrancy, integer overflow/underflow, improper error handling, etc., while also ensuring the code conforms to best programming practices.

IV: Document any concerns or potential issues identified during this stage.

# L1 Smart contracts Analysis 


### Phase 1 Architecture recommendations:  

    1. DiamondProxy:

    The DiamondProxy contract serves as a key component in the implementation of the EIP-2535 diamond proxy pattern.
    This pattern allows for a modular and extensible smart contract architecture.
    It enables multiple facets (implementations) to be part of a single diamond contract, which can be individually upgraded without affecting the entire contract.

    2. GetterFacet:
    The code implements the Diamond Standard, which allows for contract upgrades and adding new functionality through separate facets.
    The code separates the getter functions into a dedicated facet (GettersFacet), which focuses solely on providing view and pure methods.
    The code follows a modular design by importing external libraries and interfaces, which promotes code reuse and separation of concerns.
    
    3. AdminFacet:
    The code follows a modular architecture by implementing the Diamond Standard and separating different facets for specific functionalities. It distinguishes between governance and admin entities, allowing for different access rights and privileges. This design promotes separation of concerns and facilitates upgradability.

    4. MailBox:
    Documentation: While some comments are present, the codebase could benefit from more comprehensive documentation. Detailed explanations of the contract's functions, data structures, and roles would make it easier for other developers to understand and work with the code.

    5. ExecutorFacet: 
    The code appears to follow the modular design pattern and separates concerns into different functions, which is a good practice for maintainability.
    It relies on a priority queue, cryptographic operations, and various helper functions to manage the execution of L2 batches.

    6. DiamondInit:
    The contract seems to serve its specific purpose of initializing a diamond proxy. It is expected to be called only once during the deployment of the diamond. There are no specific architectural recommendations to make regarding this contract, as its role is straightforward.

    7. L1ERC20Bridge:
    The architecture of the contract is well-defined for its purpose as an ERC20 bridge. It integrates with zkSync for L2 interaction, deploys an L2 bridge counterpart during initialization, and handles deposit, withdrawal, and claim operations. The overall architecture appears sound for a basic ERC20 bridge. Additional functionality could be added based on specific use cases, such as governance features.

    8. L1WethBridge: 
    The contract is part of a larger bridge system, with L2 counterpart contracts. This design is appropriate for bridging assets between different layers of the Ethereum network.
    The use of an allowlist is good for controlling who can interact with the contract.

    9. Governance:
    The contract follows a multi-step approach for upgrade proposals, including scheduling, execution, and cancellation.
    It employs a delay mechanism for scheduled operations to ensure that upgrades can't be executed instantly.
    The contract supports two types of upgrades: fully transparent data and shadow upgrades, offering flexibility based on the use case.

    10. ValidatorTimelock:
    The contract has a clear architecture that serves as an intermediary for delaying and tracking batch execution in zkSync.
    It supports functions for committing, proving, executing, and reverting batches, which align with zkSync's state transition requirements. 

## Phase 5: Codebase quality analysis:

    1. DiamondProxy:
    The code appears to be of high quality but if the use solidity Solidity version 0.8.19. instead of version 0.8.13
    It leverages the "Diamond" library to manage the diamond cut operations, which helps keep the contract code modular and easier to maintain.
    Comments are present throughout the codebase, making it understandable for future developers.
    It uses the fallback function as the entry point for all function calls that don't match any defined function in the contract.
    The contract checks the length of the function selector and ensures it is not in the range [1, 3] to avoid conflicts with partial selectors.

    2. GetterFacet:
    Libraries are used to enhance code modularity, making it easier to maintain.
    There are clear and descriptive comments throughout the codebase, improving its readability and understandability.
    The code uses the Diamond library, which suggests that it is implementing the Diamond Standard for upgradeable contracts.

    3. AdminFacet:
    It imports necessary libraries and interfaces to support its functionalities. The code utilizes the Diamond storage pattern and includes various functions for managing access rights, freezing/unfreezing the diamond proxy, and executing upgrades. The code includes event emissions to track state changes. Overall, the code appears to be well-structured and adheres to good coding practices.

    4. MailBox:
    Gas Optimization: Gas optimization is a crucial aspect of Ethereum smart contracts. Ensure that gas usage is optimized, especially in functions like _withdrawFunds, where low-level assembly calls are used. Review gas costs carefully.
    Error Handling: Error handling in smart contracts is critical. The code should revert properly in case of errors or exceptions to avoid potential vulnerabilities.
    Use of Libraries: The contract uses external libraries such as PriorityQueue and Merkle. Ensure that these libraries are well-audited and reliable.
    Constants and Magic Numbers: Consider using named constants for magic numbers and hard-coded values to improve code readability and maintainability.
    Data Storage: Review the data storage design to ensure efficient and secure data management. Consider using data structures like mappings or arrays as needed.

    5. ExecutorFacet:
    The codebase follows some best practices, such as using library functions for mathematical operations and organizing the code into functions with clear responsibilities.
    It leverages keccak256 hashes for data integrity checks and relies on bit manipulation for tracking processed logs.
    Error messages are informative and could help with debugging. 

    6. DiamondInit:
    Variable Initialization: The contract properly initializes key variables during the initialize function, ensuring that essential parameters are set correctly.
    Modifiers: The code uses modifiers, such as reentrancyGuardInitializer, to protect against reentrancy attacks.
    Require Statements: Require statements are used to check that provided addresses and values are valid, which is a good practice for safety.
    Comments: The code includes comments for functions, which improve code readability.
    Data Structures: A struct InitializeData is used to bundle multiple parameters, preventing the "Stack too deep" error.
    Safe Math: The code uses safe math operations, like uncheckedInc, to avoid arithmetic overflows and underflows. 

    7. L1ERC20Bridge:
    Modular Design: The code is modular, separating responsibilities into functions, which enhances readability.
    Use of OpenZeppelin: The contract makes use of OpenZeppelin libraries for safe token handling (SafeERC20) and interfaces (IERC20Metadata), which is a good practice.
    Error Handling: The code includes error handling with require statements to check conditions before executing certain operations.
    Reentrancy Protection: The contract uses the ReentrancyGuard to protect against reentrancy attacks, which is a good security measure.
    Usage of Factory Deployment: The contract deploys the L2 bridge counterpart and token proxies using factory deployment, which allows for controlled setup.
    Address Alias: The contract uses address aliasing to send refunds to contract addresses on L2, which is a good practice to ensure funds are controllable.

    8. L1WethBridge: 
    It relies on various external contracts and libraries, such as OpenZeppelin's SafeERC20, and interfaces like IL1Bridge, IL2WethBridge, IL2Bridge, and others.
    It uses some secure practices like SafeERC20 for handling token transfers.
    The code includes comments explaining the purpose and functionality of various parts of the contract.

    9. Governance:
    The codebase appears to be of high quality and well-structured, utilizing the Solidity version 0.8.13.
    The use of external libraries, such as Ownable2Step from OpenZeppelin, indicates a modular and potentially audited approach to contract development.

    10. ValidatorTimelock:
    It leverages OpenZeppelin's Ownable2Step contract and uses a map data structure for efficient batch timestamp tracking.


## Phase 6: Centralization risks:

    1. DiamondProxy:
    The presence of the "access freezability" mechanism introduces centralization risks if not managed carefully.
    The contract could potentially be abused to lock down critical functionality, and thus, control over this mechanism should be tightly restricted.

    3. AdminFacet:
    The code mitigates centralization risks by incorporating a governance mechanism controlled by multiple entities. The governance is managed by two multisig contracts, one controlled by Matter Labs and another by well-respected contributors in the crypto space. These entities collectively control critical changes to the system, such as protocol upgrades. The admin facet, managed by Matter Labs, allows for non-critical changes, such as granting validator permissions. The separation of powers reduces the risk of centralization and promotes decentralization.

    4. L1WethBridge:
    The ownership of this contract is assigned to the Governance contract, which means that key control is in the hands of a central entity. This may introduce centralization risks, as the owner has control over the contract's functions.
    The L2 counterpart contracts are deployed by the L1WethBridge contract, which means that the L2 bridge contracts are initially controlled by this contract. This setup might be intended to facilitate upgrades, but it should be carefully managed to prevent centralization concerns.

    5. Governance: 
    The contract has an "owner," and there is also a "security council" involved in upgrade decisions. Centralization risks may arise if these entities collude or act against the interests of the community.
    The centralization risks are somewhat mitigated by requiring a delay for upgrade execution and allowing both the owner and the security council to cancel scheduled upgrades.

    6. ValidatorTimelock:
    The contract has a validator address that can commit, revert, validate, and execute batches. This presents centralization risks, as a compromise of the validator's private key could lead to unwanted actions.
    The ability to change the validator address is a positive feature, as it allows flexibility in case of key compromise or changing the validator.


## Phase 9: Mechanism Review:

    1. DiamondProxy:
    The contract implements the EIP-2535 standard and provides flexibility in upgrading and replacing facets.
    It uses the Diamond library to perform diamond cuts, which involve adding or replacing functions in separate facets.
    The fallback function determines the appropriate facet contract based on the function selector and delegates the call using delegatecall.
    It checks if the facet exists and if it's frozen, and reverts the call if any of these conditions are met.

    2 GetterFacet:
    The contract includes various getter functions that provide access to contract state variables, such as addresses of smart contracts, batch-related information, priority queue details, and more.
    The contract utilizes the Diamond storage pattern and provides functions to retrieve facet addresses, function selectors, and other information related to the Diamond implementation.
    The contract includes some deprecated getter functions, which suggests that the contract may have undergone updates or changes in its functionality.

    3. AdminFacet:
    The contract provides functions to transfer and accept governor and admin rights, allowing for the transfer of privileged access to specific entities. It also includes functions to set validator status, manage zk porter availability, and change the maximum L2 gas limit for transactions. The code implements upgrade execution through the Diamond Standard, enabling the execution of proposed governor upgrades. Additionally, it includes functions to freeze and unfreeze the diamond proxy, controlling the functionality of freezable facets and their selectors.

    4. MailBox:
    The codebase primarily focuses on implementing mechanisms for L1 to L2 and L2 to L1 communication. These mechanisms are crucial for interoperability between L1 and L2 blockchains in a rollup solution.

    5. ExecutorFacet:
    The mechanism for handling L2 batches and their associated proofs appears to be robust, with verification checks and cryptographic operations to ensure data integrity.

    6. DiamondInit:
    The contract implements the initialization logic for a diamond proxy, following the diamond standard. It sets essential parameters such as the verifier address, governor, admin, and other configuration details. It appears to provide a secure and straightforward way to set up a diamond proxy.

    7. L1ERC20Bridge:
    The contract provides functions for depositing L1 tokens and finalizing withdrawals. It uses proofs and Merkle trees to verify transactions on L2. Funds are locked in the contract and released upon successful withdrawal finalization. The claimFailedDeposit function allows users to reclaim funds if a deposit transaction fails on L2. These mechanisms appear to be well-implemented for their intended purpose.

    8. L1WethBridge:
    The deposit function allows users to deposit WETH on L1. This function unwraps WETH to ETH and sends it to the L2 WETH bridge contract, where it will be wrapped back into WETH on L2. This single transaction process for bridging is user-friendly and minimizes the number of transactions required.
    The finalizeWithdrawal function is responsible for finalizing the withdrawal process of WETH from L2 to L1. It performs wrapping ETH to WETH and transfers it to the L1 recipient.
    The system uses a messaging mechanism (zkSync) to ensure that the operations on L2 correspond with the actions on L1, maintaining a two-way bridge between the networks.

    9. Governance: 
    The contract employs the use of a two-step process, including the owner's proposal and the security council's approval for instant upgrades.
    It's possible to cancel scheduled operations, which adds flexibility in case of issues or changes in plans.

    10. ValidatorTimelock:
    The contract serves its intended purpose well by providing a delay mechanism for batch execution.
    It relies on the validator's actions for batch operations, which might require careful management of the validator's private key.
        
## Phase 7: Systemic Risks: 
    
    1. DiamondProxy:
    The security and integrity of the DiamondProxy contract heavily depend on the underlying implementation and security of the Diamond library.
    Any vulnerabilities or weaknesses in the Diamond library could potentially impact the security of the entire system.
    Thorough security reviews and audits of both the DiamondProxy contract and the Diamond library are crucial to identify and mitigate potential risks.
    While GettersFacet primarily provides read-only access and is not subject to freezing, it plays a crucial role in providing information to external users. If it contains incorrect or outdated data, external actors may make decisions based on inaccurate information, potentially causing systemic issues.

    2. GetterFacet:
    While GettersFacet primarily provides read-only access and is not subject to freezing, it plays a crucial role in providing information to external users. If it contains incorrect or outdated data, external actors may make decisions based on inaccurate information, potentially causing systemic issues.

    3. ExecutorFacet:
    The code handles batching, execution, and proof verification for L2 transactions. Systemic risks may include vulnerabilities in the cryptographic primitives or unexpected behavior in the priority queue. 

    4. DiamondInit:
    The contract seems relatively low-risk in terms of systemic vulnerabilities. The primary role is to initialize variables, and it is intended to be called once during deployment. The risks related to the diamond proxy as a whole would depend on the overall architecture and the functions provided by other facets.

    5. L1WethBridge:
    The claimFailedDeposit function is mentioned but marked as unsupported. The comment states that funds from failed deposits are sent to the L2 refund recipient address, but there is no implementation of this function in the provided code. This needs further clarification and implementation.
    There's a check to prevent withdrawal finalization from happening multiple times, reducing the risk of double-spending on L1.

    6. Governance:
    The delay mechanism and the requirement for security council approval for instant upgrades help mitigate potential systemic risks by providing a safeguard against rushed decisions.
    However, the effectiveness of risk mitigation largely depends on the composition of the security council and the wisdom of the delay period chosen.
    In conclusion, the Governance contract seems well-designed to manage upgrades and changes in zkSync Era contracts. However, like any governance mechanism, its effectiveness depends on the trustworthiness of the owner and the security council. While the contract has several safeguards in place, its security ultimately relies on the integrity of these parties. It's also important to choose appropriate values for the delay period and be vigilant against centralization risks. Considerations should be made for the choice of the security council to maintain decentralization and security. Additionally, thorough security audits are recommended to ensure the robustness of the contract.

    7. ValidatorTimelock:
    The contract enforces a delay mechanism between committing and executing batches. This mechanism helps mitigate potential systemic risks, allowing time for validation and security checks.
    It's essential to set an appropriate execution delay value, balancing the need for security and network efficiency
    One potential systemic risk is the reliance on the centralization of the owner's multisig wallet (Matter Labs multisig) for critical operations, such as changing the validator or modifying the execution delay. If the multisig is compromised or experiences issues, it could disrupt the entire system's functionality.

## Phase 8: Recommendations:

1. ValidatorTimelock:
   1. Owner Multisig Security: Ensure the security of the owner's multisig wallet (Matter Labs multisig) by following best practices for multisignature wallets. Implement strong access controls, regular security audits, and keep the multisig wallet's private keys in a secure and offline environment.
   2. Timely Updates: Regularly review and update the contract as necessary to maintain security and address any identified vulnerabilities.

2. DiamondProxy:
   1. Access Freezability: Use the access freezability feature judiciously and consider implementing additional safeguards to prevent misuse or unauthorized freezing of critical facets. Include mechanisms for timely unfreezing and emergency recovery in case of misuse.
   2. Security Audits: Conduct thorough security audits of the DiamondProxy contract and its facets, including testing for potential issues related to access control and freezing.

3. GettersFacet:
   1. Data Accuracy: Ensure that data exposed by the GettersFacet remains accurate and up-to-date. Implement mechanisms to regularly update this facet with the most recent information from the underlying smart contract system.
   
4. Security Audits: Perform comprehensive security audits of the entire smart contract system, including all facets, to identify and mitigate vulnerabilities. Engage with reputable third-party security audit firms to ensure a thorough evaluation.
5. Governance and Emergency Protocols: Establish well-defined governance procedures for making critical changes to the system, such as upgrades or freezing. Create emergency response plans to address any potential issues promptly.
6. Operational Best Practices: Implement robust operational practices, including monitoring and alerting, to detect and respond to any suspicious or malicious activity in a timely manner.
7. Documentation: Maintain clear and up-to-date documentation for all smart contracts, facets, and critical functions to facilitate system management and audits.
8. Regular Updates: Stay vigilant about software and contract updates, especially when new security threats or vulnerabilities are discovered in the Ethereum ecosystem.
9. User Education: Educate users and stakeholders about the system's features, risks, and appropriate usage, especially in the context of access freezing.
10. External Dependencies: Continuously assess and manage external dependencies, including libraries and interfaces used by the contracts, to ensure they are secure and well-maintained.
11. Community Involvement: Encourage community involvement and responsible disclosure of security vulnerabilities, offering bug bounties to incentivize responsible reporting.

## System contracts Analysis 

### Phase 1 Architecture recommendations:  
   
   1. AccountCodeStorage:
   The architecture of this contract appears to be designed with a clear separation of responsibilities. It relies on other system contracts, such as the Deployer and KnownCodesStorage, to validate and manage code hashes. This separation of concerns is a good architectural choice.

   2. BootloaderUtilities:
   The architecture of this contract is straightforward and doesn't introduce complexity. It provides essential utility functions that are needed for the bootloader but are better suited for implementation in Solidity rather than Yul. This separation of concerns is a good design choice.

   3.  DefaultAccount:
   The architecture of the contract is clear and well-structured. It focuses on maintaining EOA-like behavior and doesn't introduce unnecessary complexity.

   4. L1Messenger:
   The contract's architecture is designed to allow zkSync (Layer 2) to send arbitrary length messages to Ethereum Layer 1.
   It leverages merkle trees for efficient validation and consistency checks, ensuring the accuracy of data sent to Layer 1.
   The separation of responsibilities for logs, messages, and bytecode publications is clear, contributing to the contract's overall structure.

   5. NonceHolder:
   Code Separation: The codebase separates the functionality into multiple functions, making it modular and easy to understand. This is a good practice for code maintainability.
   Conditional Checks: The contract has conditional checks to ensure that nonces are managed correctly based on the chosen ordering (sequential or arbitrary). It is recommended to provide clear documentation on how these checks work and why they are essential.
   Usability: The contract can be used for various applications due to the flexibility in ordering nonces. Documentation should provide guidance on when to use this contract and how to interact with it.

   6. Compressor:
   Modular Design: The code is divided into functions with specific responsibilities, making it modular and easy to maintain.
   Well-Defined Purpose: The contract serves a well-defined purpose, which is the compression and validation of data, ensuring data can be correctly restored on L1.
   Use of Interfaces: The codebase makes use of interfaces, which promotes flexibility and allows for contract interactions.

## Phase 5: Codebase quality analysis: 
   
    1. AccountCodeStorage:
    The codebase is written in Solidity 0.8.0, a relatively recent version that benefits from improved readability and security.
    It employs constants and modifiers effectively to enhance code clarity and maintainability.
    The use of assembly for low-level storage operations is efficient and transparent, but it should be used with care to ensure that storage slots are managed correctly.
    The contract trusts external system contracts (ContractDeployer and KnownCodesStorage) to enforce certain invariants, which is a typical design choice to separate concerns and maintain simplicity.
    Code quality appears high, with clear and meaningful function and variable names, as well as inline comments that describe the purpose of each function.

    2. BootloaderUtilities:
    The use of libraries, such as TransactionHelper, RLPEncoder, and EfficientCall, promotes code reusability and maintainability.
    The code uses modular functions to avoid stack depth errors and efficiently encodes various parameters.
    Error handling is present with meaningful error messages.
    The codebase is well-documented, enhancing readability and understandability.

    3. DefaultAccount:
    It makes use of various library functions and utility functions (from TransactionHelper, SystemContractHelper, and EfficientCall) to enhance code reusability and maintainability.
    The ignoreNonBootloader and ignoreInDelegateCall modifiers, in combination with other logic, ensure that the contract behaves like an EOA when called.
    Error handling is implemented using require statements, which provides informative error messages.
    The codebase includes fallback and receive functions to further mimic EOA behavior.

    4. L1Messenger:
    It employs efficient gas management, considering the gas costs associated with hashing operations.
    The keccakGasCost and sha256GasCost functions are used to estimate gas costs for specific operations, which is a good practice for optimizing gas usage.

    5. NonceHolder:
    Readability: The code is well-structured and readable, which is crucial for maintaining and auditing smart contracts.
    Modularity: The contract is divided into functions with specific responsibilities, promoting code reusability and maintainability.
    Security Checks: The contract includes checks to prevent incorrect nonce usage, which is important for maintaining the integrity of transactions.
    Efficiency: Using a mapping structure for nonces and values is efficient in terms of storage usage.
    Error Messages: The error messages in the contract are informative, which helps in understanding why a transaction might fail.

    6. Compressor:
    Readability: The code is well-structured and readable, making it easier to review, maintain, and audit.
    Efficiency: The code aims to be efficient, especially considering that zkEVM systems need to optimize data availability.
    Safety Checks: Several checks are in place to ensure that data compression and validation are done correctly. This is crucial for maintaining the integrity of the zkEVM system.

## Phase 6: Centralization risks:
   
    1. DefaultAccount:
    The contract doesn't inherently introduce centralization risks. Centralization risks might be associated with how this contract is integrated into the broader system and who has control over the bootloader address.


## Phase 9: Mechanism Review:
    
    1. AccountCodeStorage:
    The primary mechanism of this contract is to store and manage code hashes. It doesn't implement complex business logic, but rather focuses on maintaining the integrity and security of these code hashes.

    2. BootloaderUtilities:
    The primary mechanism of this contract is to calculate transaction hashes. It doesn't implement complex business logic but provides crucial utility functions for the bootloader.
   
    3. DefaultAccount: 
    The primary mechanism is to mimic EOA behavior, especially for non-bootloader callers. This is achieved through modifiers and validation logic.

    4. L1Messenger:
    The contract effectively manages the state of logs and messages sent to Layer 1.
    It maintains sequential hashes for logs and messages, making it possible to verify the integrity and consistency of data sent to Layer 1.
    The use of merkle trees to calculate and validate these hashes adds an extra layer of security to the process.
    It also handles state diffs to account for changes in the state, ensuring that these changes are verifiable.

    5. NonceHolder: 
    The mechanism for managing nonces is well thought out, allowing for both sequential and arbitrary nonce ordering. It also prevents nonces from being set back to an "unused" state once they have been marked as used. These mechanisms are essential for maintaining transaction integrity.

    6. Compressor:
    The mechanisms for verifying data compression, both for bytecode and state diffs, appear sound. The contract enforces that compressed data can correctly reconstruct the original data on L1, which is a critical requirement for zkEVM systems.

## Phase 7: Systemic Risks: 



1. Compressor:

  1. Dependency Risks:
  The Compressor contract relies on other contracts, such as L1_MESSENGER_CONTRACT and KNOWN_CODE_STORAGE_CONTRACT, for certain operations. If these dependencies fail or are compromised, it could impact the entire zkEVM system. Ensuring the reliability and security of these dependencies is crucial to mitigating this risk.

  2. Data Availability Risks: Since the contract is involved in data availability, any failure to correctly compress or decompress data could lead to data unavailability issues on L1. If important data is not correctly restored, it could disrupt the functioning of the zkEVM system.
   
  3. Interaction Risks: The Compressor contract interacts with other system components, including the sequencer and data storage contracts. If there are flaws in how it interacts with these components, it could lead to vulnerabilities and systemic risks.
   
  4. Protocol Changes: If the zkEVM system undergoes protocol changes, the Compressor contract may need to be updated to remain compatible. Failure to adapt to changes in the system's architecture could lead to systemic issues.

  5. Validation and Verification Risks: The correctness of data compression and validation is crucial. If there are errors or vulnerabilities in the validation process, it could lead to systemic risks, especially in terms of data consistency and integrity.





## Phase 9: Time Spent
    
    99:30 hours

### Time spent:
99.5 hours