| List | Head | Details |
| --- | --- | --- |
| 1   | introduction to zkSync Era Protocol | introduction to the zkSync Era |
| 2   | Audit approach | Process and steps i followed |
| 3   | L1 and L2 Layers | what's the difference? |
| 4   | L1 to L2 communication | L1 to L2 communication is governed by ? |
| 5   | Fee mechanism | fee mechanism of the zkSync Era |
| 6   | Architecture recommendations | some recommendations related to the architecture |
| 7   | Codebase Analysis | Analysis of the codebase |
| 8   | Codebase Quality | how was the quality of the codebase |
| 9   | How could they have done it better? | Some best code practice suggestions |
| 10  | Possible Systemic Risks | The possible systemic risks based on my analysis |
| 11  | Centralization risks | Concerns associated with centralized systems |
| 12  | Security Approach | Security Approach of the Project |
| 13  | Gas Optimizations | Details about my gas optimizations findings and gas savings |
| 14  | Documents | how was the Documents that they provided for us |
| 15  | Recommendations | some recommendations to the zkSync Era teams |
| 16  | Conclusion | Conclusion |
| 17  | Time spent on analysis | The Over all time spend for this reports |

## Introduction

**zkSync Era** is a [ZK rollup](https://era.zksync.io/docs/reference/concepts/rollups.html#what-are-zk-rollups), a trustless protocol that uses cryptographic validity proofs to provide scalable and low-cost transactions on Ethereum.

zkSync Era is made to look and feel like Ethereum, but with lower fees. Just like on Ethereum, smart contracts are written in Solidity/Vyper and can be called using the same clients as the other EVM-compatible chains.

At this time, zkSync is solely run and operated by the zkSync team's servers and is therefore centralized. However, this will be transitioned to a decentralized system shortly.

zkSync Era repository contains three section

1\. Smart Contracts Section

2\. VM Section

3\. Circuits Section

## Audit Approach

1.  **Initial Scope and Documentation Review**: Thoroughly went through the Contest Readme File and project documentation to understand the protocol's objectives and functionalities.
    
2.  **High-level Architecture Understanding**: Performed an initial architecture review of the codebase by going through all files without going into function details.
    
3.  **Test Environment Setup and Analysis**: Set up a test environment and execute all tests. Additionally, use Static Analyzer tools like Slither to identify potential vulnerabilities.
    
4.  **Comprehensive Code Review**: Conducted a line-by-line code review focusing on understanding code functionalities.
    
    - **Understand Codebase Functionalities**: Began by going through the functionalities of the codebase to gain a clear understanding of its operations.
    - **Identify Access Control Issues**: Thoroughly examine the codebase for any access control problems that might allow unauthorized users to execute critical functions.
    - **Evaluate Function Execution Order**: Checked random sequence of function executions to ensure the protocol's logic cannot be disrupted by changing the order of calls.
    - **Assess State Variable Handling**: Identify state variables and assess the possibility of breaking assumptions to manipulate them into exploitable states, leading to unintended exploitation of the protocol's functionality.
5.  **Report Writing**: Write a Report by compiling all the insights I gained throughout the line-by-line code review.
    

## L1 and L2 Layers: what's the difference?

The term **Layer 1** (or **L1**) is used to refer to the underlying primary chain, such as the Ethereum network or Bitcoin. Layer 1 blockchains determine protocol rules and transaction finality, and perform the base-level functions of applications built upon them.

The term **Layer 2** (or **L2**) is used to describe an overlaying application or network that operates on top of the Layer 1 chain. These are most often built to provide further scalability solutions by taking on a portion of transaction-based tasks to lighten the impact on the layer 1 chain, quickening transaction times and lowering gas fees.

**zkSync Era is an L2, where L1 is the main Ethereum blockchain.**

## L1 to L2 communication

L1 to L2 communication is governed by the [`IZkSync.sol`open in new window](https://github.com/matter-labs/v2-testnet-contracts/blob/b8449bf9c819098cc8bfee0549ff5094456be51d/l1/contracts/zksync/interfaces/IZkSync.sol#L4) inherited interfaces.

## **Fee mechanism**

**zkSync Era's fee model is similar to Ethereum’s where `gas` is charged for computational cost, cost of publishing data on-chain and storage effects. However, zkSync Era includes additional costs for publishing to L1 and for proof generation.**

**Because the L1 gas price for publishing data (on L1) is so volatile, the amount of required L2 `gas` is variable. Therefore, for each block, the zkSync Era sequencer defines the following dynamic parameters:**

- `gasPrice`: the price, in gwei, of a unit of gas.
- `gasPerPubdata`: the amount of `gas` for publishing one byte of data on Ethereum.

## Architecture recommendations

I suggest having the contract under audit equipped with a complete set of NatSpec detailing all input and output parameters pertaining to each functions although much has been done in specifying each @dev. Additionally, a thorough set of flowcharts, and if possible a video walkthrough, could mean a lot to the developers/auditors/users in all areas of interests.

### Codebase Analysis

- The codebase is well-structured and follows Solidity best practices. The contracts are well-documented, which aids in understanding the functionality and purpose of each contract. The use of libraries for common functions helps to reduce code duplication and increase readability. However, the complexity of the system could potentially lead to unexpected behaviour or vulnerabilities.

## Codebase Quality

- **Modular Design:** The code is organized into different contracts, each responsible for specific functionality. This makes the codebase easier to understand and maintain.
    
- **Clear Comments:** There are comments throughout the code that explain the purpose of functions, variables, and sections. This enhances readability and comprehension.
    
- **Use of Interfaces:** The code leverages interfaces to interact with other contracts. This is a good practice for decoupling and reusability.
    
- **Access Control:** The contracts implement access control using modifiers like onlyOwner. This ensures that certain functions can only be called by authorized parties.
    
- **Events:** Events are used to provide transparency and enable easier tracking of important contract interactions.
    

## How could they have done it better?

While the provided code seems to be functional, there are always areas for improvement to ensure better security, efficiency, and readability in the contract and the protocol.

- Here are some potential areas for improvement:
    
- **Modularization**: Break down the code into smaller, well-defined functions and contracts. This makes the code easier to understand, test, and maintain.
    
- **Comments and Documentation**: Provide thorough comments and documentation throughout the code to explain the purpose, functionality, and any potential gotchas of each component.
    
- **Input Validation**: Validate and sanitize all user inputs to prevent unexpected behavior or attacks. For example, checking that input amounts are positive, non-zero, and within reasonable bounds.
    
- **Consistent Naming Conventions**: Use consistent and descriptive variable and function names to make the code more understandable.
    
- **Gas Efficiency**: Optimize the code for gas efficiency. This involves minimizing unnecessary computations, storage, and external calls to save on transaction costs.
    
- **Avoid Reentrancy Vulnerabilities**: Implement safeguards to prevent reentrancy attacks, such as using the "Checks-Effects-Interactions" pattern and using the reentrancyGuard modifier to prevent multiple calls to the same function.
    
- **Use Latest Solidity Version**: Keep up-to-date with the latest Solidity version and use its latest features and improvements.
    

## Possible Systemic Risks:

- **Algorithmic Complexity**: The contract involves complex mathematical calculations and interactions. Errors in these calculations could lead to incorrect outcomes, affecting the functioning of the contract and potentially leading to unexpected losses for users.
    
- **Rounding Errors**: Rounding errors can accumulate over time due to frequent calculations involving floating-point arithmetic. These errors could lead to discrepancies between expected and actual token balances, causing loss of funds.
    
- **Front-Running and Manipulation**: Complex financial systems can be susceptible to front-running attacks where malicious actors place transactions ahead of others to gain an advantage. The evolving nature of the bonding curve could provide opportunities for manipulation.
    
- **Unintended Consequences**: The interactions between different parts of the contract could lead to unintended consequences, negatively impacting users. For instance, changes in the curve parameters over time could lead to unforeseen behaviors.
    

## Centralization Risk

the owner is a single point of failure and a centralization risk.

**NOTE**: bot finds all the centralization risks and mentioned  as Medium Risk Issue.

## Security Approach of the Project

- **Minimize Complexity**: Keep the smart contract logic as simple as possible to reduce the attack surface. Complex systems are more prone to bugs and vulnerabilities.
    
- **Separation of Concerns**: Divide the contract's functionalities into smaller, modular components. This can help isolate vulnerabilities and make the contract easier to audit.
    
- **Secure Coding Guidelines**: Enforce secure coding practices among developers. Follow best practices for Solidity development to minimize the risk of introducing vulnerabilities.
    

Remember that security is an ongoing process, and regular reviews, updates, and improvements are essential to stay ahead of emerging threats. Collaborating with experienced blockchain security professionals and conducting frequent security assessments can significantly enhance the security posture of the project.

## Gas Optimization

zkSync Era is generally efficient in terms of gas optimizations, many generally accepted gas optimizations have been implemented, gas optimizations with minor effects are already mentioned in automatic finding, but gas optimizations will not be a priority considering code readability and code base size.

## Documents

The documentation provided for the zkSync Era protocol is quite comprehensive and detailed in terms of explaining its functionality, parameter usage, purpose, and overall architecture. However, here are some suggestions that could further enhance the clarity and understanding of the documentation:

- **Visual Examples**: Include diagrams or visual aids to help readers understand concepts better.
    
- **Practical Examples**: Provide specific numerical examples to help readers apply calculations and parameters in real-world scenarios.
    
- **Detailed Use Cases**: Expand use cases to demonstrate how the contract applies in real life.
    
- **Highlighted Key Terms**: Boldly highlight technical terms when first mentioned to help readers identify important concepts.
    

## Recommendations:

To enhance the security posture of the zkSync Era, several measures can be considered. Implementing extra security mechanisms like timelock delay consensus or requiring multisig approvals for critical changes can provide an extra layer of protection. Introducing a decentralized validation process for significant upgrades could help ensure that changes are authorized by a broader consensus. Additionally, documentation should be improved to provide comprehensive insights into roles, responsibilities, and interactions within the system. Thorough testing, particularly focused on upgrade and timelock processes, is essential for identifying and mitigating potential vulnerabilities. For further assurance, a formal audit by a reputable smart contract security firm is highly recommended and zkSync should be transitioned to a decentralized system as soon as possible.

## Conclusion

In general, the `zkSync Era project` exhibits an interesting and well-developed architecture we believe the team has done a good job regarding the code, but the identified risks need to be addressed, and measures should be implemented to protect the protocol from potential malicious use cases. Additionally, it is recommended to improve the documentation and comments in the code to enhance understanding and collaboration among developers. It is also highly recommended that the team continues to invest in security measures such as mitigation reviews, audits, and bug bounty programs to maintain the security and reliability of the project.

## Time Spent on the Audit

- I spent around 6 days on this audit, each day I put in around 7-10 hours of work. In total that would be around 50 - 60 hours.

### Time spent:
55 hours