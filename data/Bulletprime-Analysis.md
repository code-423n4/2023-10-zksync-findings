zkSync Protocol Overview 
Before diving into the overview lets first give an idea of what the protocol is before giving a general overview. As we all know The Ethereum network is usually congested, which results in slower transactions and excessive gas prices. A solution came to mind, one that checkmates transaction excessive gas consumption and achieves faster transaction rate while `maintaining` high security. Thus the birth of a `layer 2 solution`, The term Layer 2  is used to describe an overlaying application or network that operates on top of the Layer 1 chain.( an underlying primary chain, such as the `Ethereum network` or Bitcoin. Layer 1 blockchains determine protocol rules and transaction finality, and perform the base-level functions of applications built upon them.) These are most often built to provide `scalability solutions` by taking on a portion of `transaction-based` tasks to lighten the impact on the layer 1 chain, `quickening transaction` times and `lowering` gas fees. With this you can look at the `zkSync Era` as a fully-fledged `Layer-2` scaling solution, combining a set of smart contracts on Ethereum mainnet and zkEVM for enabling Ethereum virtual machine-compatible smart contract execution. 
`General mode of operation:` this protocol increases the scalability of Ethereum by performing `calculations` off-chain, rolling many transactions up into a single batch, and sending it to the main Ethereum chain in a single action. it does this by sending batches of transactions that are verified for correctness on the Ethereum network. After the `verification` passes, the batch of transactions is considered final like any other Ethereum transaction. This is achieved through the power of `cryptographic validity proofs` (commonly called zero-knowledge proofs). With any batch of off-chain transactions, the ZK protocol operator generates a proof of validity for this batch. Once the proof is generated, it is submitted to Ethereum to make the batch final. In zkSync, this is done via a SNARK, `succinct non-interactive argument of knowledge`. The layer 1 (Diamond) Technically, is the smart contract that acts as a connector between Ethereum (L1) and zkSync (L2). It checks the validity proof and data availability, handles `L2 <-> L1` communication and finalizes L2 state `transitions`.

| Vital contracts | Functionality |
| :-| :-|
| ExecutorFacet.sol| A contract that accepts L2 batches, enforces data availability and checks the validity of zk-proofs.commitBatches - check L2, batch timestamp, process the L2 logs.| 
| MailboxFacet.sol | The facet that handles `L2 <-> L1` communication,Bridging native Ether to the L2,Censorship resistance mechanism.
| L2ERC20Bridge.sol | The "standard" implementation of the `ERC20 token bridge`. Works only with regular ERC20 tokens,deposit for both L1 & L2.
| Governance.sol | This contract manages calls for all `governed zkSync Era` contracts on L1 and L2. Mostly, it is used for upgradability and changing critical system parameters. The contract has minimum delay settings for the call execution and upgrade consists of scheduling and upgrade exeution.|
| GettersFacet.sol | A separate facet, whose only function is providing `view` and `pure` methods. It also implements `diamond loupe` which makes managing facets easier. |
| ValidatorTimelock.sol | An intermediate smart contract between the `validator` EOA account and the zkSync smart contract. Its primary purpose is to provide a `trustless` means of delaying batch execution without modifying the main zkSync contract. zkSync actively monitors the chain activity and reacts to any suspicious activity by `freezing` the chain.|

Code base Approach 
A gradual and comprehensive understanding and read of L1s while going through `repository` containing comprehensive `documentation` and codes related to the Smart Contracts, VM, and zk-circuits sections of the zkSync Era Protocol. Special focus was taken to tackle bridges related to the L1 & L2s and its proper implementation, bridges of `tokens` and `WETH` while taking notes of their correct implementation between the `L1 & L2`. Read inadeptly on `Diamond facets` and proxies, upgrades and everything involving its management. Studied security recommendations containing the best possible mechanism from `openzepplein`. Studying of documentation also helped me understand each contracts purpose, functionality, and how it is connected with other contracts, auditing alongside studying to see if purposes aligns.
Read old audits and already known findings from bot races findings, Then I setup my `testing environment`. Running tests to check if all test passed. Used foundry for this protocol.
Finally, started with auditing the code base bearing in mind the overall working of the zkSync protocol while reviewing individual components, threat models and attack ideas paying attention to dangerous logic, access control data availability and gas consumption. In this way I started understanding `line by line` code and took the necessary notes to ask questions both on discord channels or sponsors.

Architecture Recommendation
The protocol indeed has a strong root and supports a seamless implementation, the development team really took security measures to the next level, while countering lapse and covering loopholes. some potential areas of improvements. `Update Risk` If the smart contract needs to be updated, there is a risk that updates may be implemented incorrectly or that new versions introduce `vulnerabilities`. Additionally, updates can lead to divisions within the user community causing unintended behaviors. So there should be regular audit of contract code to ensure upgrades do not introduce unintended behavior or vulnerabilities. Teams should also be transparent about the upgrade process. Inform the community about planned upgrades and provide them with enough time to review the changes.
Also the `Allowlisted.sol` modifier, recommended be checked if it can be fixed somewhere else for a more standard code.

Quality Analysis
Code stood seamless while maintaining Code quality. And the documentation for this protocol is very mature, giving proper information, considering the vastness of the components. The test suite is pretty comprehensive. 
1. A suggestion should be to add integration tests to verify behavior of the system as a whole, rather than all its specific sub-components. Interacting with these contracts by calling their functions and observing the results. Verifying  that the contracts interact correctly, for example, by checking the state of one contract after a function call on another contract. `Integration tests` are valuable because they catch issues that arise from the interaction of different components, which unit tests (tests that check individual components in isolation) might miss.
   
2. Code Formatting: The code could benefit from consistent indentation and spacing. This improves readability and makes the code easier to understand.
   
3. Use of Modifiers: The contract could use modifiers to reduce code duplication and improve readability. For example, the `_commitOneBatch function` could use a modifier to check that the `_newBatch` is the next batch to be committed.

4. Error Messages: The error messages could be more informative. Instead of single letters like '`f'`, `'l'`, `t'`, etc., use meaningful names that describe the error.

5. Comments: While the code is well-structured, some comments could be more descriptive. For example, the purpose of the `packedBatchAndL2BlockTimestamp` could be explained in more detail.

6.Code Reusability: Some parts of the code could be extracted into separate functions to improve code `reusability` and readability.

7. Contract uses old and inconsistent solidity version (the version used is also prone to assembly optimizer bugs.)

Centralization Risk
The `owner` can be a single point of failure and a centralization risk, if it has power over one mechanism. The single owner can become malicious and perform a rug-pull. Moreover, the owner role can also pose a centralization risk. If the owner is a `single entity` or a small group of entities, `(security council & admin )` it `concentrates power` in their hands. This goes against the principles of `decentralization` and `trustlessness` that are at the core of blockchain technology. Consider changing to a multi-signature setup, or having a role-based authorization model mechanism for critical operations. This requires a `predefined` number of signatures to perform these operations, distributing the authority among multiple entities and reducing the risk of misuse or compromise.

```solidity
File: cache/generated-contracts/zksync/ValidatorTimelock.sol

54:      function setValidator(address _newValidator) external onlyOwner {

61:      function setExecutionDelay(uint32 _executionDelay) external onlyOwner {

```

Mechanism review
Potential areas for mechanism review from the `Executor.sol` contract

Batch Commitment: The `commitBatches` function commits the provided batches, but it doesn't check if the batches are already committed. This could potentially allow a malicious user to commit the same batches multiple times, leading to incorrect state transitions.

Batch Execution: The `executeBatches` function executes the `committed` batches, but it doesn't check if the batches are already executed. This could potentially allow a malicious user to execute the same batches multiple times, leading to incorrect state transitions.

Data Availability: The `proveBatches` function checks the data availability of the committed batches, but it doesn't handle the case when the data is not available. If the data is not available, the function should revert the state transition to maintain the contract's integrity.

Systemic risks
`Failure` to prove certain blocks due to the verifier (being out of scope), main systemic risk that and could be risky depending on potocols `configuration`, possible risk also can arise from batch commitments.

As the part of the platform is `upgradeable`, migration in the event of a security hole might be `challenging`. The team should prepare `multiple` recovery scenarios and set up adequate action channels to prepare for such eventualities.

Conclusion 
Overall, the `zkSync era` Protocol presents an interesting `architecture` aimed at establishing `decentralized` and `sacalable` transaton while maintaining `high security standards`. The development team has done a good job with the code. However, as its a common in blockchain projects, the protocol risks related to the security of its smart contracts and the centralization of key actors such as the governors and admin is lurking. `Ultimately`, the continued success of the protocol will depend on its ability to address these `challenges`, especially after `potential security` issues are disclosed by C4 wardens. This will involve maintaining security and decentralization and remaining an attractive choice for `n users`

Time Spent

A total of 4 days was dedicated to completing this audit.

### Time spent:
23 hours