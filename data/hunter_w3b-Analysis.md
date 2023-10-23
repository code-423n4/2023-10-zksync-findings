# Analysis - zkSync Era Contest

![zkSync Era](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRoUZV3tciyaoPm2Qhy-wGFL_5rL9v251LeCg&usqp=CAU)

## Description

zkSync Era is a fully-fledged Layer-2 scaling solution that combines smart contracts on the Ethereum mainnet with zkEVM, a technology enabling Ethereum virtual machine-compatible smart contract execution. It's designed to significantly enhance the scalability and reduce transaction costs on the Ethereum blockchain.

This solution is implemented through a combination of smart contracts, a virtual machine (VM), and zk-circuits.

**The key contracts of the protocol for this Audit that I focused on are**:

- **Executor.sol**:
  That contract processes batches of transactions and events in the zkSync protocol, ensuring their integrity and correctness.It commits new batches, verifies batch commitments, and executes batches. It also handles system contract upgrades and priority operations, ensuring the proper functioning of the zkSync network.

- **Mailbox.sol**:
  This contract, part of a Layer 2 solution, enables secure communication between L1 and L2. It offers functions to prove L2 message and log inclusions, verify L1 to L2 transaction statuses, and handle Ether withdrawals. Additionally, it allows users to request and estimate the cost of L2 transactions, playing a crucial role in interchain interactions.

- **Diamond.sol**:
  It allows for dynamic addition, replacement, and removal of functions and facets associated with a diamond proxy contract. The library ensures that functions and facets are properly managed, keeping track of their associations. Additionally, it supports the initialization of new facets after changes have been made. The contract is designed to be a key component in creating upgradeable smart contracts using the diamond standard.

- **TransactionValidator.sol**:
  This library provides functions to validate Layer 1 to Layer 2 transactions in a zkSync system. It checks various properties of these transactions, such as gas limits, and ensures their validity according to the system's requirements. This library also calculates the minimal gas limit required for executing a priority transaction and handles overhead for transaction processing within a batch.

- **ValidatorTimelock.sol**:
  The contract acts as an intermediary between a validator's account and the zkSync smart contract in a zkSync system. It enables a trustless method of delaying batch execution without altering the zkSync contract. This contract allows adjustments to the validator's address and the time delay between committing and executing batches. It records batch commitment timestamps and facilitates batch-related operations while adding an additional layer of security to the zkSync system.

- **L1ERC20Bridge.sol**:
  The contract facilitates the deposit of ERC20 tokens from the Ethereum mainnet to the zkSync layer. It provides functions for users to initiate deposits, claim failed deposits, and finalize withdrawals from zkSync to the Ethereum mainnet. The contract also keeps track of the total deposited amount per user and checks against deposit limits. Additionally, it supports upgrading L2 token implementations and operates with an address aliasing mechanism to handle refunds to L1 contracts on L2.

- **L1WethBridge.sol**:
  The L1WethBridge contract facilitates seamless bridging of WETH tokens between Ethereum Layer 1 and Layer 2, reducing a three-step process to a single transaction. It accepts and unwraps WETH on L1, then facilitates the transfer of ETH to the L2 WETH bridge for wrapping and delivery to the intended L2 recipient.

- **Governance.sol**:
  This contract manages governance tasks, allowing for scheduling, executing, and canceling operations with specific permissions and delays. It facilitates transparent and shadow upgrades for zkSync Era governed contracts, enabling upgrades after predefined delays or with security council approval. The contract is designed for secure and coordinated changes in the system and is inspired by OpenZeppelin's TimelockController and in-house Diamond Proxy upgrade mechanism.

- **SystemContext.sol**:
  The "SystemContext" contract is a component of a L2 Ethereum solution by Matter Labs. It stores critical context variables and handles aspects like block data, virtual L2 blocks, and batch information to facilitate L2 transaction processing on the Ethereum network.

- **L1Messenger.sol**:
  This conract is designed for facilitating communication between L2 and L1 in a ZkSync-like system. It can send both fixed and variable-length messages to L1, maintaining efficient gas cost calculations and state management. The contract handles logs, messages, deployed bytecode, and state differences, ensuring their integrity and then publishes them to L1. Overall, it serves as a crucial component in a Layer 2 scaling solution for Ethereum, managing data transmission and state synchronization between the two layers.

- **ContractDeployer.sol**:
  It is a system contract responsible for deploying other contracts on zkSync. This contract generates addresses for deployed contracts, increments deployment nonces, and ensures that constructors are not called more than once. It is an integral part of the zkSync system, managing contract deployment on Layer 2 of the Ethereum network. The contract also includes functions for deploying contracts with similar address derivation rules to Ethereum's CREATE2 and CREATE opcodes.

## Approach Taken-in Evaluating The zkSync Era Protocol

Accordingly, I analyzed and audited the subject in the following steps;

1.  **Core Protocol Contract Overview**:

    I focused on thoroughly understanding the codebase and providing recommendations to improve its functionality.
    The main goal was to take a close look at the important contracts and how they work together in the zkSync Era Protocol's.

    I structured the audit into three primary segments. Initially, I conducted a thorough examination of the key smart contracts relevant to the contest. Nevertheless, I encountered challenges when dealing with Rust and circuit components, leading to potential gaps in my analysis. These areas require further attention and understanding.

    **Main Contracts I Looked At**

          L1 Contracts - zkSync

            Executor.sol
            Mailbox.sol

          Bridges

            L1ERC20Bridge.sol
            L1WethBridge.sol

          L2 Contracts

            TransactionHelper.sol
            BootloaderUtilities.sol

          Bridges (zkSync)

            L2ERC20Bridge.sol
            L2StandardERC20.sol

    I started my analysis by examining the Executor.sol contract. This contract is a crucial component in the zkSync protocol, responsible for processing events and batch commits in the zkSync layer 2 scaling solution. It plays a vital role in maintaining the integrity and consistency of the zkSync system.Given the central role of the ExecutorFacet in the zkSync protocol, it is essential to conduct a thorough audit to identify any potential vulnerabilities.

    Then, I turned our attention to the Mailbox.sol contract. The contract is responsible for managing interactions between Layer 1 and Layer 2 in a system. Any vulnerabilities in this contract could lead to significant losses, and the contract is quite complex with various libraries, structs, and functions, which could potentially introduce vulnerabilities that might be missed during development.

    L1ERC20Bridge.sol this one is for bridging ERC20 tokens from Ethereum to zkSync Layer 2. It serves as a reference implementation for token bridges and features deposit and withdrawal functionalities. Users can initiate deposits with specified L2 recipients, gas limits, and refund options, with provisions for handling failed deposits. The contract also enforces deposit limits and interacts with zkSync for secure and efficient cross-chain token transfers.so handle the deposit and withdrawal of ERC20 tokens. Any flaw in its logic could result in user funds being lost or stolen.

    The L1WethBridge contract is designed to facilitate the bridging of WETH tokens between L1 and L2 networks, with a focus on Ethereum and zkSync. It streamlines and enhances the user experience by simplifying the token bridging process. It has the capability to initialize its Layer 2 counterpart, the L2WethBridge, along with various factory dependencies and deployment options.

    The BootloaderUtilities.sol as a utility contract for a bootloader. It offers functions to calculate transaction hashes for different Ethereum transaction types, including Legacy, EIP-1559, and EIP-2930. These calculated hashes are crucial for secure transaction processing and verification within the bootloader, abstracting the complexities of Ethereum transaction hashing.

    L2ERC20Bridge.sol is default bridge implementation for ERC20 tokens, connecting Ethereum L1 and L2 networks. It allows users to deposit ERC20 tokens on L1, which are then minted as corresponding L2 tokens. The contract uses a beacon proxy pattern for upgradable L2 token deployment. It also handles withdrawals from L2 to L1, ensuring synchronized token balances between the two layers. The contract is part of a larger bridge infrastructure designed by Matter Labs for Ethereum scalability solutions.

    The L2StandardERC20.sol contract is an implementation of an ERC20 token used within the context of the "default" ERC20 bridge. This contract is designed to work in tandem with an L1 token, enabling users to deposit L1 tokens to mint equivalent L2 tokens and vice versa.

2.  **Documentation Review**:

    Then went to Review the comprehensive [documentation](https://era.zksync.io/docs/) and learn alot of new things for a more detailed and technical explanation of zksync era.

    The main documentation that I looked at:

    1. [L1 smart contracts](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/L1%20smart%20contracts.md)
    2. [System Contracts](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md)
    3. [zkSync Era Fee Model](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/zkSync%20fee%20model.md)
    4. [Handling L1→L2 Ops on zkSync](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Batches%20&%20L2%20blocks%20on%20zkSync.md)
    5. [Batches & L2 Blocks on zkSync](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Batches%20&%20L2%20blocks%20on%20zkSync.md)

3.  **Test Coverage Evaluation**: During this phase, I play with the tests, initially encountering challenges when attempting to run Hardhat tests them first. However, after resolving the issues, I found the well-written tests to be quite interesting.

4.  **Manuel Code Review** In this phase, I initially conducted a line-by-line analysis, following that, I engaged in a comparison mode.

    - **Line by Line Analysis**: Pay close attention to the contract's intended functionality and compare it with its actual behavior on a line-by-line basis.

    - **Comparison Mode**: Compare the implementation of each function with established standards or existing implementations, focusing on the function names to identify any deviations.

## Codebase Quality

Overall, I consider the quality of the zksync era codebase to be excellent. The code appears to be very mature and well-developed. We have noticed the implementation of various standards. Details are explained below:

| Codebase Quality Categories       | Comments                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Code Clarity and Readability**  | The codebase exhibits relatively good code clarity and readability. The codebase codes are well-structured, uses meaningful variables and functions names, and includes explanatory comments, which help developers anditor understand its purpose and functionality.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| **Code Comments**                 | I explain the comments in a example of one contract's,Comments at the beginning of the contract describe its purpose, authorship, and contact information:Describes the contract as an "zkSync Executor contract capable of processing events emitted in the zkSync protocol."Mentions the author as "Matter Labs."Provides a contact email for security inquiries.Comments within functions and sections of code explain their functionality, such as:In the \_commitOneBatch function, there's a comment explaining the function's behavior, including checks for batch number, timestamp, and priority operations.In the \_verifyBatchTimestamp function, the comments clarify that it checks the correctness of batch timestamps.In the \_processL2Logs function, there's an explanation of the log processing logic, ensuring that specific logs are processed as expected.In the commitBatches function, comments highlight that it checks the order of batch execution and handles system contract upgrades.In the executeBatches function, there's a comment describing its role in executing batches, completing priority operations, and processing withdrawals.The proveBatches function is documented to explain the process of verifying batch commitments.Additional comments explain various helper functions and calculations throughout the contract. |
| **Documentation**                 | These documentation is very well explain the protocol 1. [L1 smart contracts](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/L1%20smart%20contracts.md) 2. [System Contracts](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md) 3. [zkSync Era Fee Model](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/zkSync%20fee%20model.md) 4. [Handling L1→L2 Ops on zkSync](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Batches%20&%20L2%20blocks%20on%20zkSync.md) 5. [Batches & L2 Blocks on zkSync](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Batches%20&%20L2%20blocks%20on%20zkSync.md) instead of this one [documentation](https://era.zksync.io/docs/)                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| **Custom Error Types**            | In the codebase of zksync era there don't appear to be custom error messages defined. However, it do include require statements that are used to check various conditions and revert the transaction if the conditions are not met. These require statements are used for error checking and to ensure that certain conditions are satisfied before allowing a function to proceed. It's a best practice to sues Custome Error instead of Require statements.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **Testing**                       | Test is not complete and have bad documentation of test it's recommendated to provide the best documentation that how to run tests and No having, fuzzing and invariant tests could open the door to future vulnerabilities                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| **Code Structure and Formatting** | The codebase contracts adheres to typical Solidity code conventions with a clear structure. It incorporates several imports for libraries and other contracts. The codebase code is well-organized with logical grouping of functions, consistent naming, and event emissions to provide transparency. Additionally, it employs comments for code explanation, bitwise operations for managing bit flags, and custom data structures for data organization. .                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

## Systemic & Centralization Risks

The analysis provided highlights several significant systemic and centralization risks present in the zksync era protocol. These risks encompass concentration risk in Executor, Base, AllowList risk and more contracts, third-party dependency risk, and centralization risks arising from the existence of an “owner” role in specific contracts. However, the documentation lacks clarity on whether this address represents an externally owned account (EOA) or a contract, warranting the need for clarification. Additionally, the absence of fuzzing and invariant tests could also pose risks to the protocol’s security.

1. The Executor.sol contract uses a priority queue, which might have implications for centralization of transaction ordering.

2. In the Mailbox.sol contract the finalizeEthWithdrawal function allows the contract owner or operator to control the withdrawal of funds from the contract. If this entity is centralized and can be compromised, it poses a significant centralization risk. Users might not have full control over their funds.

3. The Diamond.sol contract appears to manage diamond proxy facets and their functions. Any changes to these facets could impact the entire system, and If there's no mechanism to pause or shut down the contract in the event of unforeseen issues, such as a critical vulnerability or exploit, systemic risk is present.

4. This Getters.sol contract tracks the number of batches committed, verified, and executed. Risks related to incorrect batch processing, data inconsistency, or malicious manipulation could lead to systemic issues, and the contract provides information about non-finalized (non-executed) batches. If the logic for finalizing batches is flawed, it might lead to systemic issues.

5. The ability to change the validator status in Admin.sol contract could lead to systemic risks. If validators are not properly selected or their status is changed maliciously, it might impact the security of the system.

6. The L1ERC20Bridge performs Merkle proof validation in the finalizeWithdrawal function. Incorrect or insufficient validation of these proofs could lead to vulnerabilities, also the contract enforces withdrawal limits, but the method of determining and updating these limits is not provided in the contract. Depending on how the withdrawal limits are managed, they could introduce centralization risks and the contract deploys the L2 bridge and L2 token proxies during initialization. The deployment process should be transparent, and control over these deployments could lead to centralization risks.

7. The L1WethBridge.sol has a mechanism for finalizing withdrawals. If this mechanism is not properly designed or secure, it could result in failed withdrawals or funds getting locked and contract interacts with external contracts, such as SafeERC20 and IWETH9, which may introduce risks if these external contracts behave unexpectedly or have vulnerabilities.

8. The ability to change the minimum timelock duration for future operations in Governance.sol contract is restricted to self-upgrades. However, this self-upgrade capability could introduce risks if the contract owner can upgrade the contract to their advantage.

9. The AllowList.sol contract uses an AccessMode to control access to functions. It can be set to "Closed" (no one can call the contract), "SpecialAccessOnly" (only some addresses can call the contract), or "Public" (anyone can call the contract). The change of access mode can introduce systemic risks, especially if switched from "SpecialAccessOnly" to "Public," allowing anyone to call the contract and The owner can set permissions to call specific functions on specific contracts for specific callers. If these permissions are changed incorrectly or maliciously, it could lead to disruptions and unauthorized access.

10. The getTransactionHashes function in BootloaderUtilities contract reverts with "Unsupported tx type" if an unsupported transaction type is provided. This might result in unexpected behavior or reverts if new transaction types are introduced in the future. It's a good practice to provide more informative error messages and handle unsupported types gracefully and the contract does not have an owner or administrator who can modify its behavior. However, centralization risks might arise if other contracts or systems rely heavily on the utility functions provided by BootloaderUtilities. If these utility functions are used across different contracts, they might introduce centralization risks into the broader system.

11. The SystemContext contract depends on certain data (such as timestamps and batch hashes) provided by external systems (like the bootloader) without strong verification. Any discrepancies in this data could lead to systemic risks. Robust data verification mechanisms are crucial and the contract uses a rolling hash of transactions in the current L2 block. Any errors or vulnerabilities in how this hash is calculated and managed could impact the integrity of the system, Some parameters like MINIBLOCK_HASHES_TO_STORE are hard-coded. This inflexibility could be a risk if future changes are required and The contract contains deprecated methods, which may cause confusion or introduce risks if not handled properly.

12. The contract L1Messenger.sol manages multiple internal state variables (e.g., chainedLogsHash, numberOfLogsToProcess, chainedMessagesHash, etc.), which can be prone to errors. Complex state management can lead to unexpected behavior, security issues, and unintended consequences and The contract references l2ShardId with a fixed value of 0 in sendL2ToL1Log. If the contract is designed to work within a multi-shard environment, the centralization risk arises from its limitation to a specific shard.

13. The L2EthToken.sol uses the `_burnMsgValue` function to effectively burn the msg.value. This can be a risk if it's not clear why this value is being burned and if it's not handled properly. It might lead to unintended loss of funds.

14. The contract L2StandardERC20.sol allows the L1 token to define which getters it implements. If the L1 token does not provide certain getters, the L2 token will revert when queried for these properties. This lack of uniformity can lead to inconsistencies in user interfaces and may affect dApps built on top of the token, and The bridge contract can potentially decide which getters the L2 token implements by providing data during initialization. This could allow centralized control over the token's public interface, which may not be in line with decentralized principles.

15. The SystemContractsCaller depends on the address of the L2 bridge (l2Bridge) for executing system calls. If this bridge contract becomes centralized or controlled by a single entity, it introduces centralization risks, as decisions regarding system calls and their parameters are concentrated in one place, The contract allows for specifying system calls using the MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT. If this control over what constitutes a system call is not adequately governed or audited, it may introduce centralization risks, as the criteria for system calls are determined by this contract and The CalldataForwardingMode enum defines the options for forwarding calldata. If these modes are not configurable or if control over them becomes centralized, it may limit the flexibility and decentralization of the system.

16. **No having, fuzzing and invariant tests could open the door to future vulnerabilities**.

**Properly managing these risks and implementing best practices in security and decentralization will contribute to the sustainability and long-term success of the zksync era protocol.**

## Gas Optimization

zksync era L1 demonstrates a strong commitment to gas optimization, incorporating many widely accepted techniques. In addition to the major optimizations mentioned automatic finding, I have compiled a list of **40** gas optimization suggestions.

To improve code clarity in the provided code snippets, we have occasionally used function abbreviations to highlight specific sections. It's important for developers to exercise caution when integrating these proposed changes to prevent potential vulnerabilities. Even though we have conducted prior testing on these optimizations, developers should take on the responsibility of conducting a thorough reevaluation.

### summary of gas optimization suggestions

| Finding                                                                                     | Occurrences |
| :------------------------------------------------------------------------------------------ | :---------: |
| Use ECDSA signatures instead of merkle trees                                                |      1      |
| Private functions used once can be inlined                                                  |     11      |
| Mappings used within a function more than once should be cached to save gas                 |      9      |
| Use assembly to write address/contract type storage values                                  |      7      |
| Use assembly to validate msg.sender                                                         |     11      |
| Invert if-else statements that have a negation                                              |      4      |
| Assigning state variables directly with named struct constructors wastes gas                |      7      |
| Using constants directly, rather than caching the value, saves gas                          |      1      |
| Use selfdestruct in the constructor if the contract is one-time use                         |      5      |
| Understand the trade-offs when choosing between internal functions and modifiers            |     10      |
| Use fallback or receive instead of deposit() when transferring Ether                        |      5      |
| Consider using alternatives to OpenZeppelin                                                 |      -      |
| Using assembly to revert with an error message                                              |     151     |
| Test if a number is even or odd by checking the last bit instead of using a modulo operator |      1      |
| Don’t make variables public unless it is necessary to do so                                 |     27      |
| It is cheaper to use vanity addresses with leading zeros, this saves calldata gas cost.     |      1      |
| Using private rather than public for constants, saves Gas                                   |      9      |
| Using fixex bytes is cheaper than using string                                              |      5      |
| Always use Named Returns                                                                    |     66      |
| Can make the variable outside the loop to save gas                                          |      6      |
| Emitting memory values instead of the storage one.                                          |      5      |
| It is sometimes cheaper to cache calldata                                                   |     37      |
| Empty blocks should be removed or emit something                                            |      4      |
| Use storage pointers instead of memory where appropriate                                    |     14      |
| Use constants instead of type(uintx).max                                                    |      3      |
| abi.encode() is less efficient than abi.encodePacked()                                      |     12      |
| Do not calculate constants                                                                  |     14      |
| Do-While loops are cheaper than for loops                                                   |     19      |
| Use assembly to perform efficient back-to-back calls                                        |     11      |
| Short-circuit booleans                                                                      |     15      |
| Pre-increment and pre-decrement are cheaper than +1 ,-1                                     |      9      |
| Use Clones for Cheap Contract Deployments                                                   |      7      |
| Use assembly to hash instead of solidity                                                    |     14      |
| Use hardcode address instead address(this)                                                  |     10      |
| Use bytes.concat() instead of abi.encodePacked(), since this is preferred since 0.8.4       |      4      |
| Admin functions no uses of the nonReentrant modifier                                        |     11      |
| Use Mappings instead of arrays                                                              |     10      |
| array[index] += amount is cheaper than array[index] = array[index] + amount                 |      1      |
| Expression `` is cheaper than new bytes(0)                                                  |      4      |
| Shorten the array rather than copying to a new one                                          |      3      |


### Time spent:
18 hours