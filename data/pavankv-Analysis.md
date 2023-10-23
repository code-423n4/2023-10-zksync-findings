Analysis Report
**Index of table**
| Sl.no  | Particulars  |
|---|---|
| 1   | Overview  |
| 2  | Architecture view(Diagram)  |
| 3  | Approach taken in evaluating the codebase  |
| 4 | Recommendation  |
| 5  | Hours spend   |
|   |   |

## 1. Overview :-
Zksync Era is the one of the scaling solution for ethereum ecosystem aims to improve scalibity and efficiency of the Ethereum network. It based on zk-rollups concept which means aggregation and validation of the transaction in Layer 2 and after some limit reached it will be off-to Layer 1 network means Ethereum mainnet. This will reduce the burden of fee for users by bundling hundreds of transaction into a single zkRollup, then sent to mainnet.
We observed the zksync adpoted the concept of zkevm it is a virtual machine that executes smart contracts in a way that is compatible with zero-knowledge-proof computatiuon.

Key Difference between Zk-evm and Zk-sync era :-

**Zk-evm** is a typr of layer-2 scaling solution for ethereum that uses zero-knowledge proofs to verify the state of a large number of transaction off-chain and then batch them up and submit them to the main ethereum chain as a single transaction.

The key components of zk-evm are :-

Sequencers :-
The smart contract which propose the transaction batches to the network i.e they roll-up the transaction requests in batches & add them to the consensus contract.Every squencer must pay a fee to earn the right to create and propose batches . A sequencer that propse valid batches (which consist of valid transaction) is incentivised with the fee paid by transaction requestors or the users of the network.

Aggregators :-
The smart contract check the validity of the transaction batched & provide validity proofs. Any permission less aggregator can submit the proof to demonstrate the correctness of the state transaction computations.

**Zk-sync Era**
Zksync Era is the one of the scaling solution for ethereum ecosystem aims to improve scalibity and efficiency of the Ethereum network. It based on zk-rollups concept which means aggregation and validation of the transaction in Layer 2 and after some limit reached it will be off-to Layer 1 network means Ethereum mainnet.

The key components of zkSync Era :-
The mechanisms used by zksync era but uses a new type of sequencer called a Hyperchain. Hyperchains are more scalable than traditional sequencers and can support a higher transaction throughput.zkSync Era also uses a new type of aggregator called a Layered Aggregator. Layered Aggregators combine the benefits of L3 rollups with the benefits of simple aggregation.The combination of Hyperchains and Layered Aggregators allows zkSync Era to achieve very high scalability and low gas costs.


## 2. Architecture view(Diagram)

![Diagram of architecture](https://user-images.githubusercontent.com/69415766/277394956-d0895be8-f7a5-45d0-a1af-088f14ac2021.png)


Full view of diagram :-
https://lucid.app/lucidchart/9e767253-3fcd-4f12-aefa-3156b9e0da10/edit?page=0_0&invitationId=inv_c091b12c-fa7d-49b9-9170-98434ee3066b#

Note:-
Due to paid software with limited feature we added only core concept which define the architecture.


## 3.Approach taken in evaluating the codebase :-
We approached manual code review by looking into each space in the scoped contract and decided to explain some core functional contracts.it is important to note that manual code reviews can be very time-consuming.

**The core contracts in L1 are:-**
1. Executore.sol
2. Mailbox.sol
3. L1ERC20Bridge.sol
4. L1Wethbridge.sol

**The core contracts in L2 are**

1.L1Messenger.sol
2.ContractDepolyer.sol
3.L2ERC20Bridge.sol
4.L2StandardERC20.sol
5.L2WethBridge.sol


## 4. Recommendations :-
There is no recommendation for zksync era due it is a mechainsism which has to be followed by users to experience the robustness, privacy and security process while interacting between the layer 1 and layer 2.And already matterlab team conscientiously examined and build well secured concept to interoperability between layer 1and layer 2.
Add some input validation corresponding to function name:-
Add the zero amount check to bridgeBurn() in L2ERC20Bridge.sol contract.
Add check before bridgeBurn() in mailbox.sol

## 5. Hours spend
We spend 120 hours for gas finding , Quality Assurance and Analysis Report.

### Time spent:
120 hours