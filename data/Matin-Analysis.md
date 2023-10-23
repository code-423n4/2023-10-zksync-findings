# Analysis Report

## Overview
zkSync Era, is the game changer in the field of ZK-proof-based Ethereum rollups. Unlike sidechains such as Polygon, zkSync inherits security from Ethereum. With its powerful ZkEVM and it's rust-based ZK circuits, zkSync aims to provide secure and high throughput transaction solutions for the users.

## Approach taken in evaluating the codebase

### Steps:
First I went through the documents. Starting from the L1 contracts, then I switched into L2 and system contracts.
After this stage, I read the in scope codes to understood where should I concentrate on.

The test coverage in both sides (L1 and L2) were high. The L1 side contract tests were both provided using Hardhat and Foundry.
Protocol uses a diamond upgradeable proxy and controls and handles four contracts into one contract in L1 side. These contracts are aimed to manage these tasks:

1. L1 to L2 transaction requests, ETH transfer into ZK, and message sending tasks
2. Government related tasks
3. Final executions of batched transactions. It is worth to mention that transactions should be first commited (timestamp and other checks), proved, and then executed.
4. Some public getters.

After looking at and understanding the L1 side, I switched into L2 and system contracts. The core of the zkSync which bundles the txs, applies fees and gases, and lots of works is called Bootloader.

### Auditing process
I spent my most hours of studying dedicated to understand the protocol. It is hard to get a full dominance over the codebase in 21 days but tried hard and read the documents to understand it.

## Advantages of zkSync Era
Considering the procedure of proving the transactions in another environment, it accelerates and facilitates the transaction validation process in L1 blocks. It also helps people setup their DApps in zkSync Era and be sure that their states are fully compatible with the Ethereum global state. Overall advantages in my opinion are:

1 - Supporting contract deployment in zkSync Era
2 - Commiting, proving and executing tons of transaction in just a single bundle
3 - Fully compatible state changes in L1
4 - Circuits are written in Rust and are more powerful in contrast to other adversaries

## Centralization Risks
A single entity as the only owner of L1 contracts is a large centralization risk. A single private key may be compromised, or the single owner can become malicious and perform a rug-pull. Also a single sequencer which handles all the vital works is vulnerable to centralization risk.

## Mechanism Review
The mechanism of the protocol is complex and it is a kind of a combination of DApp and infrastructure. It has three levels of general mechanism:

a. L1 side and L1 contracts
b. L2 side and system contracts
c. ZK-Proof circuits

The main concept is, users can work, send transactions, deploy contracts and interact with DApps in another environment with pretty low fees, finally zkSync sends this transactions (multiple txs in a batch) and syncs all the states with the Ethereum global states. Also it ensures that all the opcodes and entire code works as participated in L1 due to its powerful circuits written in Rust.

## Systemic Risks
The protocol is designed to be a ZK-proof-based rollup with its own zkEVM and powerful proving circuits. I think these are the possible risks in zkSync:

1. An L1 -> L2 transaction which uses heavy operations (e.g. keccak, ecrecover, ...) many times, if being the first one in a L2 batch, may prevent other txs included in the batch.
2. Hash collisions may occur in circuits which can potentially damage the opcode proving.
3. L1 pubdata gas can be changed as the Ethereum faces updates. (Such as EIP-4488)

## Recommendations
1. Consider reducing the times between commitments, proving, and execution on L1.
2. Implement node-based decentralized sequencer rather than lying on a central one.
3. Remove L2-L1 Merkle tree calculation as I think is redundant
4. Consider replacing EIP1559 mechanism for l2 gas price

## Conclusion
zkSync era proves the validity of the transaction rather than promises and being optimistic. With its powerful technology, protocols and users would prefer to deploy and handle their works in zkSync. This process will reduce the volatile scalability of Ethereum.

### Time spent:
70 hours

### Time spent:
70 hours