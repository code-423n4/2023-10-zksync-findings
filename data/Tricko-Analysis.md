# Overview of ZKSync Era

zkSync Era is a Layer-2 scaling solution on Ethereum that utilizes ZK rollup technology. It employs cryptographic proofs to enable off-chain computation and data storage, resulting in cost-effective and scalable transactions while maintaining Ethereum's security. 

Rollups enhance Ethereum's scalability by consolidating multiple transactions off-chain and submitting them to the main chain as a single batch, reducing processing times and gas fees. Unlike Optimistic rollups, which assume off-chain transactions are valid, ZK rollups use cryptographic proofs to verify correctness on the Ethereum network. Zero-knowledge proofs confirm the validity of a batch of transactions, with the operator generating a proof for each batch before submitting it to Ethereum for finalization.

zkSync Era implements standard Web3 API and it preserves key EVM features such as smart contract composability while introducing some new concept such as native account abstraction. While not entirely EVM-compatible, users can seamlessly interact with smart contracts written in Solidity or Vyper on zkSync Era, requiring minimal to no modifications compared to their mainnet versions. This allows them to experience the familiarity of Ethereum, benefit from reduced fees, and maintain the same high level of security.

# Architecture Overview
The zkSync Era codebase is remarkably extensive, encompassing three primary domains: L1 contracts, L2 system contracts, and ZK circuits. This codebase includes implementations for both on-chain execution and off-chain processing (sequencing and proving), involving a variety of programming languages, including Solidity, Yul, and Rust.

## L1 contracts
#### Bridges
Main entrypoint for users trying to bridge non-native assets. ETH bridging is done through the Diamond contract. Enable users to lock tokens on one layer, resulting in minting the same token on the other layer. Currently there are two bridges, both owned by Governance.
- `L1ERC20Bridge` / `L2ERC20Bridge`: Works for standard ERC20 tokens
- `L1WethBridge` / `L2WethBridge`: Custom bridge to exclusively transfer WETH tokens

#### Diamond
The L1 contracts use the diamond proxy pattern based on the [EIP-2535](https://eips.ethereum.org/EIPS/eip-2535), which are modular smart contract systems that can be upgraded or extended after deployment, it also allows separating specific contract functionality on different contracts (facets). The contract has four main facets.

- ***Admin***: Manages priviledged addresses and also allows priviledged addresses to freeze/unfreeze the diamond proxy, disabling most of the contract functionality until reverted.

- ***Getters***: Provides `view` methods to access values stored on the L1, like bootloader bytecode hash, batch hashes, protocol version. 

- ***Mailbox***: Responsible for the L1 <-> L2 messaging and also allows bridging of ETH.

- ***Executors***: Handles L2 batches, verify the proof validity and updates the L2 state root.

#### Governance
This contract manages governance and upgrades for zkSync Era contracts on both L1 and L2. It facilitates upgradability and parameter changes with timelock.

## L2 Contracts
#### Bootloader
Due to performance reasons Instead of processing transactions one by one, like in Ethereum, the bootloader runs a batch of transactions as a single program. This batch includes a set of blocks and associated metadata, and it processes them inside a single significant "transaction". Notably, the bootloader's code is not stored on L2, but its code hash is stored on the L1 and can only be changed during system upgrades.

#### System Contracts
Not all EVM opcodes are directly supported by ZkSync VM. Those opcodes are instead implemented through system contracts deployed in the L2. Also allows the introduction of new opcodes not present in the EVM, like `near_call`, `far_call`, `getCodeAddress`, `getMeta` and many others.

## VM and ZK Circuits
In this section of the codebase, mostly Rust code, we have a dedicated modules that handles the off-chain processing of the VM and ZK circuits. This off-chain processing occurs during batch execution and proof generation.

#### VM
VM code executed in the sequencer. It comprises various modules responsible for a range of tasks, including fetching transactions from the mempool, executing these transactions, aggregating them into batches, and preparing them for submission to the prover instances. Essentially, the off-chain VM code initializes all the necessary modules, loads the bootloader address into the VM, and feeds it with batches of transactions to be processed.

#### Circuits
Code responsible for arithmetizing the execution of the VM, so it can be converted into constrainst to be proved. All the VM operations like individual opcodes execution, storage reads/writes, precompiles, logs emition, and many others are converted into circuits that are used to generate proofs of the correct execution of the VM. All this computation is done off-chain using the L2 transaction batches info and only the respective proofs and state diffs are published on the L1.

# Codebase Quality
The Solidity codebase is excellent, with thorough comments, explanatory specs, and descriptive function names. However, the Rust codebase lacks crucial comments in significant sections and documentation.

| Codebase Quality Categories  | Comments |
| --- | --- |
| *Testing*  | Both Solidity and Rust codebases are well-tested, although most circuit integration tests are located in a [separate repository](https://github.com/matter-labs/era-zkevm_test_harness).|
| *Code Comments*  | Overall comments in Rust codebase were not enough, as there is no other documentation about it. Solidity codebase is thoroughly commented. |
| *Documentation* | Lacking for circuit code. |

# EVM compatibility
The ZkSync Era VM does not exhibit complete compatibility with the EVM. Consequently, in certain rare scenarios, modifications to smart contracts may be necessary prior to deployment. Failure to detect these differences during compile-time could potentially lead to significant consequences.

## Deprecated EVM instructions
Some EVM opcodes are deprecated in ZkSync's VM, like `PC`, `SELFDESTRUCT`, `CALLCODE`, `EXTCODECOPY`.

## Unavailiable EVM precompiles
Some EVM cryptographic precompiles, for example `0x05 Modexp` or `0x08 ecPairing`, are not currently available.

## `CREATE`/`CREATE2`
To ensure the proper operation of `create` or `create2`, the compiler must possess prior knowledge of the bytecode of the deployed contract. Consequently, deploying new contracts using `create` or `create2` from calldata, as demonstrated below, will not function correctly and cannot be identified during compile time.

```solidity
function deploy(bytes memory contractBytecode) public {
   assembly {
      addr := create(0, add(contractBytecode, 0x20), mload(contractBytecode))
   }
}
```

## Different gas logic
ZkSync's zkEVM exhibits unique computational trade-offs in comparison to the EVM, resulting in a distinct gas logic when compared to Ethereum. Consequently, any attempt to calculate gas costs, whether on-chain or off-chain, is likely to yield inaccurate estimations. Also avoid relying on hardcoded gas values, as they are susceptible to modifications.

# Centralization Risks
## Governance
All the main contracts, the Diamond, both bridges, have the Governance contract as its admin. Allowing it to upgrade zkSync Era, upgrade bridge, change rollup parameters albeit with a delay. The governance contract is controlled by a 4/8 Gnosis Safe Multisig.

## Frozen/Delayed withdraws
In order to withdraw, after requesting the withdraw on the L2, the state roots of the finalized batch has to be verified and updated on the L1. If by any reason, the validator (either by being offline or malicious behaviour) stops publishing new state roots, withdraws will be frozen.

## MEV and Censorship-resistance
In the current stage, the sequencer is centralized, giving rise to two potential issues: MEV and censorship.

The sequencer holds exclusive authority to propose new batches and has complete control over the transaction order within each batch. Consequently, the sequencer operator could exploit this power to manipulate the transaction sequence to their advantage, facilitating actions like frontrunning transactions and other forms of MEV extraction. Furthermore, the sequencer can ignore transactions from specific addresses. While users have the option to submit transactions to the L1 queue, there is no mechanism to compel the inclusion of these transactions, as the sequencer possesses the ability to halt the processing of the L1 queue entirely.

### Time spent:
22 hours