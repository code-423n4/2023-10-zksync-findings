Description 

Zksync is a layer2 scaling solution protocol which uses a technology known as Zk-Rollups. 

A rollup is an L1 scaling solution, it scales (increases the throughput) of the L1 chain by bundling Multiple Transactions into a batch and uses an off chain mechanism to compute state, if the new state validity is proven then the current state is updated to a new state. In zksync the transactions are bundled and using an off chain mechanism gets the new state as a result of processing the bundled transactions off chain and a cryptographic proof of the new state, the new state(root Hash) and the proof are submitted to smart contracts which verifies the new state using the submitted proof if valid the new state is updated. 

The Scope of the Analysis are categorized 

-Overview 

-System Architecture  

-Audit Approach and Techniques  

-Codebase Quality and Analysis 

-Systemic Risk Based on architecture & development pattern 

-Recommendations 

-Conclusions 

-Judging Recommendation 

 

 

Overview of the Audit 

The codebase for the audit is divided into 

a). The Smart Contract Section : This section relates to the smart contract component of the system either implemented in the Yul or solidity.These smart contracts are further divided into two segments based on the deployment network i.e. (Eth Mainnet and Zksync network). 

 

b).The Vm Section: This section of the audit is mostly based on the EraVm.The EraVm is an Evm compatible(similar functionalities to the Ethereum virtual machine) , it also extends its functionality by with the aid of a LLVM compiler(a collection of compilers used for compiling a high-level language to a standard intermediate representation irrespective of the language ) to allow developers implement smart contracts using high-level languages. Though not all evm based opcodes are present on the virtual Machine ,it serves as the ideal virtual Machine for generating zkproofs and executing smart contracts. 

c). Circuits Section: This section of the audit has to do with arithmetic circuits which are used to verify zero knowledge proofs. Arithmetic circuits within the context of ZkSync play a critical role in ensuring the privacy and integrity of transactions and computations. These circuits enable secure and efficient verification of complex mathematical operations without exposing sensitive information. With the use of arithmetic circuits, ZkSync ensures that transactions are validated without revealing underlying data, thus safeguarding the confidentiality of user activities. This feature is especially significant in maintaining the security and privacy of financial transactions and data exchanges on the ZkSync network, fostering trust and reliability in the decentralized ecosystem. 

System Architecture(Smart Contract Section) 

From a simplified perspective, the call flow for L2 execution proceeds as follows: 

  

1. User sends a transaction. 

2. The transaction is added to a batch, verified off-chain, and a validity proof is generated if the transaction is valid. 

3. The validity proof is verified in the L1 system contract and finalized, updating the new root state. 

  

Zksync's architecture facilitates L2 scaling through transaction bundling and off-chain state computation. Transactions are batched, verified off-chain, and upon validation, the new state is updated via cryptographic proofs in L1 system contracts. Key components, including DiamondProxy.sol, Executor.sol, Mailbox.sol, Getters.sol, and Admin.sol, collectively manage the protocol's functionalities. 

  

Smart Contract Section: 

  

Comprising smart contracts deployed on either the Ethereum or Zksync network, implemented in Yul or Solidity. 

  

L1-Based Contracts: 

 - DiamondProxy: Manages the storage of the zksync infrastructure. 

- Facets:  

  - Executor: Validates and commits batches, implements the Only Validator modifier which allows only the initialized validator to call some functions. 

  - Mailbox: Handles L1-L2 interactions, bridging Native ETH to L2. 

  - Getters: Serves as a getter for crucial system parameters, implements the diamond Loupe contract. 

  - Admin: Manages privileged addresses and system parameters, responsible for pausing/unpausing the diamond Proxy. 

  

L1 Bridge: Implementation contract of the L1Bridge Proxy, bridging ERC20 Tokens to Zksync's network, Has an alias on the zksync chain.  

Governance: Manages zksync contracts on both L1 and L2, used for critical system parameter changes and protocol upgrades. 

  

ValidatorTimeLock: This contract serves as an security measure incase of a swan event.  

L2-Based Contracts: 

  

- L2ERC20Bridge: Bridges ERC20 tokens between L1 and L2 chains. 

- L2EthTokens:An alias of the Native ETH on the L1,serves all the functionalites of the Native ETH on the zksync. 

- BootLoader: This Contract is a key component that manages the execution of layer 2 transactions. 

Bundles Transactions: The bootloader bundles transactions and executes them by delegating core functionality to the system-contracts deployed on the layer 2. 

Manages Execution Environment : The bootloader runs within a node as part of the execution environment. The validator modifies transactions and stores them in an array, which is then written to the bootloader memory and executed. 

- L2Weth.sol: An upgrade of the WETH contract on the L1 Chain  it includes functionalities like facilitating bridging of the WETH token from the L1->L2. 

 

 

 

AUDIT APPROACH AND TECHNIQUE 

In conducting the contract audit, we employed various techniques. A brief overview of our approach includes: 

  

1) Utilizing automated tools such as Slither and Echidna to scan the codebase. 

  

2) Implementing a top-down approach, which involved immersing ourselves in the developer's perspective to grasp the codebase and comprehend the rationale behind specific design patterns. 

  

3) Crafting tests to stress the system's invariants and subject the design patterns to rigorous scrutiny subsequent to understanding the codebase. 

  

4) Engaging in manual analysis of the codebase to identify common coding errors. 

  

5) Concluding with a manual comparison between the codebase and the corresponding documentation. 

 

 CODEBASE QUALITY AND ANALYSIS 

  

Regarding the quality of the codebase, it adheres to the most recent industry standards: 

  

1) The Codebase is well-documented and easily readable, facilitating smooth navigation and comprehension for auditors. 

  

2) The Codebase design is customized to prioritize minimizing transaction gas costs. 

  

3) The Codebase incorporates widely recognized libraries and utilities to uphold the security of the protocol.				 

SYSTEMIC RISK 

1. The system's reliance on multiple dependencies could introduce a single point of failure for the entire system. Should one component fail, it might cause a cascade effect leading to the malfunction of other related components. 

  

2. The system is susceptible to centralization risks, given that power distribution is concentrated among a limited number of privileged addresses. 

  

3. Smart contracts may harbor vulnerabilities that are exploitable by malicious entities. Critical security flaws in a smart contract, such as access errors or logic problems, could result in asset losses or manipulation of the system. We strongly advise that, following the protocol audit, necessary measures be implemented to address any issues highlighted by C4 Wardens. 

RECOMMENDATIONS  

1. It is advisable for the protocol to transition towards a decentralized governance system to facilitate more effective decision-making concerning actions impacting the entire protocol. 

  

2. Reworking contracts, such as transforming helpers into libraries, can effectively counteract high deployment costs. 

  

3. The protocol ought to lessen reliance on individual components within the system. Relying heavily on a single component implies that the entire functionality of the protocol is at risk should that component cease to operate. 

CONCLUSIONS 

Overall, the zksync protocol showcases an intriguing structure and design for an optimal L2 solution. The efforts of the Matter's lab team in developing the codebase are commendable. Nevertheless, it is a commonplace reality that web3 projects encounter substantial risks pertaining to smart contracts, along with the vulnerability of privileged address wallets to potential malevolent actors. In essence, the future success of the zksync protocol hinges upon its capacity to effectively mitigate and manage threats to the system. The zksync team must continuously stay abreast of the latest risks within the evolving realm of web3. 

JUDGING RECOMMENDATIONS 

  

It is widely acknowledged that a significant portion of auditors prioritize the smart contract segment. Hence, it is advisable for the judge to allocate additional attention to the smart contract section. ￼	

### Time spent:
30 hours