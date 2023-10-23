# ZK Sync

## Overview:

ZK sync employs the use of circuits to generate zero knowledge proofs for the bootloader.

## **Circuits**

**Main VM:**

The MainVM is the main instruction handler in the circuit logic. It is responsible for the following:

- Apply the opcode (e.g., perform an addition).
- Apply the accumulated state differences.
- Update memory and registers.
- Push data to queues for other circuits.
- Handle exceptions.

the MainVM processes instructions in cycles, taking care of core operations directly while delegating certain proofs to other specialized circuits such as ecrecover, storage sorter, Ram permutations etc.

**Ecrecover:**

leverages zk-SNARKs to recreate the Ethereum **`ecrecover`** functionality in a privacy-preserving manner. It allows one to verify the authenticity of a signature without revealing the actual signature or message, which can have applications in private transactions and voting systems, among others

**Log Sorter:**

- The circuit's primary responsibility is to sort logs, ensure uniqueness, and handle rollbacks.
- The sorting is predominantly dependent on the timestamp of logs.
- The logic also deals with potential rollbacks by looking for matching events and nullifying them.
- The **`inner`** part is crucial as it processes each log, ensuring they're sorted correctly and rollbacks are addressed.
- Various flags and conditions are used throughout to guide the logic and handle different scenarios.

**Demuxer_log_queue:**

- Log_Demuxer takes in a **`log_queue`** with different types of requests like storage requests, events, L1 messages, and requests to specific precompiles (ecrecover, sha256, keccak256).
- The circuit then splits this input queue into six different queues.
- Before any operations, it verifies that no elements from the queue have been previously retrieved.

**CodeDecommiter:** 

This circuit processes a queue of decommit requests intended for the DecommitSorter circuit. Its primary functionality includes:

1. **Decommitment Checking**: For every decommit request, the circuit ensures the linear hash of all opcodes matches the hash stored within the decommit request.
2. **Memory Writing**: It writes code to a corresponding memory page.
3. **Queue Unpacking**: The circuit extracts the queue from the opcode, then updates the memory queue and validates its correctness.

**[L1MessagesHasher](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/Circuits/L1MessagesHasher.md#l1messageshasher):**

This circuit processes a queue of L1 messages, hashing them using the keccak function. Initially, it allocates the input part of PI and sets up an empty hash state. Through a loop, it pops messages from the queue, buffering and hashing them as required. Once all messages are processed, it verifies the queue's emptiness and computes the final hash. Lastly, it establishes a commitment to the Public Input.

**[SortDecommitments](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/Circuits/SortDecommitments.md#sortdecommitments):** 

this circuit is designed for sorting and deduplicating code cancellation requests. In the initialization phase, inputs are allocated and decisions based on the **`start_flag`** determine the state and accumulation method. During the main logic, elements from queues are processed, checked for order, and deduplicated, with updates to internal variables as needed. The final phase ensures that the permutation accumulators match when queues are empty and then updates the public input.

StorageSorter:

This circuit primarily focuses on sorting and deduplicating storage requests from the **`unsorted_log_queue_state`**, pushing the results to **`final_sorted_queue_state`**. The first part involves allocating input fields and making decisions about which state queues to utilize. The main segment pops elements from the queue, accumulates encodings for a permutation argument, enforces sorting, and updates relevant variables. An additional loop iteration is executed if the last query was taken during the prior cycle. Lastly, the final section verifies permutation argument accumulators' equality and updates Public Input (PI) outputs.

**[KeccakRoundFunction](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/Circuits/KeccakRoundFunction.md#keccakroundfunction):**

The main logic for the Keccak circuit involves processing the Keccak hash function, specifically for contract executions. Here's a summarized breakdown:

1. The circuit receives metadata about queued up precompile calls and verifies that the call at the front of the queue is intended for the Keccak precompile.
2. Metadata collected about the call specifies memory positions for input and output data, and other related data such as the hash timestamp. It then gathers data from a queue containing memory queries, giving witnesses for the Keccak buffer.
3. The main processing loop begins by obtaining a new precompile request from the queue, checks its validity, and gathers additional information for the call.
4. After preparing the required data, the circuit forms a full input and then executes the Keccak round function. Upon completion of the last round, it will write the memory query of the result.
5. Finally, the circuit updates public input (PI) output sections, computes a commitment, and allocates it as public variables.

The entire process ensures the proper functioning and execution of the Keccak hash function in a secure and reliable manner.

## **Approach**

As the codebase was very large and complex, I decided to focus solely on the circuit section of the codebase which was written in rust due to a lack of time, dedicating about 6 hours a day for the audit. I belived that from a competitive and personal growth standpoint it would be better for me to focus my efforts there; I believed that this section would have less eyes on it due to it being written in rust and I also believed that this could also pay dividends in the future when auditing other zk circuits.

My main tactics was to go through each circuit and make sure that each circuit has the correct constraints. I mostly did this through comparison, asking myself questions such as “Why does this circuit have x constraint and this one did not?” 

I also asked myself whether there were too many constraints, where honest pubdata could be  rendered invalid by the circuits 

And finally I asked myself whether there was a way for bad actors to intentionally make bad transactions in order to invalidate other pubdata in a grieving attack.

## **Observations and o**bstacles during the review

One of the biggest obstacles for the audit, was the lack of knowledge not only regarding rust but also the math behind the circuits. This made the contest very challenging as I was simutaneously was learning rust, zk and doing the audit at the same time. In the future, I would have preferred if this contest was announced a month in advance as most auditors in the space did not have the experience and skillset to audit this contest fully 

**Awkward scoping** 

While the zk circuit codebase was huge, the important elements that make it work are not in scope i.e. the verifier and the boojum system. These 2 elements verfies the pubdata and also computes the the proofs needed for zero knowledge. This made it very difficult to understand how to audit the circuits as many of the important elements that creates the zero knowledge proofs was abstracted away.

It is also important to note that in order to manipulate some tests, the user needed to have some 

**Circuit tests in scope**

In several zk circuits, I noticed that there were tests located within contracts that are in scope. (insert example). This created an awkward situation where I was not sure how to approach them as an auditor. I knew that they were only tests and did not directly affect the codebase, but they were also technically within scope. This posed internal questions such as “If this test is written improperly, is it in scope?”. I recommend that they are separated from the main codebase to avoid confusion for the auditors 

**No unit tests for the main VM circuit**

While all of the smaller circuits all had unit tests available, it was very strange to see that the main vm circuits, which was the biggest circuit in the codebase, not have a single unit test. This created a disadvantage for those who are not rust experts as creating a unit test for the main vm circuit from scratch is a very difficult and time consuming task that may have been better spent auditing the codebase 

**Confusing naming conventions** 

Throughout the codebase there is a usage of args used, which is meant to be gas 

Some circuits in the docs diagram had a different name than they did in the codebase making the diagrams confusing to dicipher at times. One example is the logSorter circuit which is really 2 circuits in one. More examples include:

| Diagram Circuit Name | Circuit Name in Code |
| --- | --- |
| CodeDecommiter |  https://github.com/matter-labs/era-zkevm_circuits/tree/main/src/code_unpacker_sha256 |
| https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/Circuits/L1MessagesHasher.md#l1messageshasher | https://github.com/matter-labs/era-zkevm_circuits/tree/main/src/linear_hasher |
| https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/Circuits/SortDecommitments.md#sortdecommitments |  https://github.com/matter-labs/era-zkevm_circuits/tree/main/src/sort_decommittment_requests |
| StorageSorter | https://github.com/matter-labs/era-zkevm_circuits/tree/main/src/storage_validity_by_grand_product |
| https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/Circuits/KeccakRoundFunction.md#keccakroundfunction | https://github.com/matter-labs/era-zkevm_circuits/tree/main/src/keccak256_round_function |

Also, it is worth mentioning that the ecrecover circuit is not shown at all in the diagram  

**Some parts of the Circuits Docs are Incorrect**

The docs state that their are 13 base layer circuits. In reality there are 12 circuits as the logSorter circuit that is used as both `EventsSorter` and `L1MessagesSorter`

## Architecture

![Flowchart](https://user-images.githubusercontent.com/29849840/277199424-1df2fe62-c86f-4774-a483-695d817d8809.png)

**Circuit With Cycle Limit Values**

![Cycle-Limit](https://user-images.githubusercontent.com/29849840/277236707-af4dfed6-3b4f-4086-a156-3c4504014c0f.png)



## **Codebase Quality**

| Codebase Quality Categories  | Comments  |
| --- | --- |
| Documentation | The documentation overall was very extensive and explained many parts of the codebase very well. This is very impressive considering the sheer size and complexity of the codebase. However there was little to no documentation on the circuit tests which made testing very difficult. There was also no documentation on the tables portion of the circuits. |
| Comments  | The comments were very good in the L1 and L2 sections. However in many circuits files there were almost no comments such as the tables section of the circuits |
| Testing  | Overall the testing was pretty good. However, it is important to note that in the circuit files that were listed as being in scope there were test functions. This created some very confusing situations as an auditor.                                    Also there was a noticeable lack of unit tests in the main vm circuit which was strange considering that it was by far the largest circuit. I recommend more sufficient testing for this section as the chances for vulnerabilities are higher due to its sheer size  |
| Organization | The organization was decent. However, as stated earlier, the verifier and the the core boojum was in scope and therefore separated into a different repo. This made the codebase tricky to navigate  |

## **Systemic Risks**

**Scalability Issues**: zk-SNARKs, while efficient in terms of proof size, can be computationally intensive to generate. This can pose scalability challenges in systems with high transaction throughput. If this problem is not addressed properly there could be situation where the circuits will not be able to function. There could also be a possibility where a malicious actor could intentionally overload the system, causing it to fail.

**Lack of constraints or too many constraints:**

At its core, the circuit system allows a prover to convince a verifier that they've executed a particular computation correctly without revealing specific inputs to that computation. Constraints define this computation. They set the "rules" that the computation must follow, ensuring the resulting proof corresponds to the desired computation. However, there could be situation where the circuits does not have enough constraints in the system, possibly resulting in validating an invalid proof, compromising the security and soundness of the circuit system. There could also be a situation where there are too many constraints, where valid proofs are rendered invalid.

**Misbehaving prover:** 

There is also a chance that the prover can misbehave and corrupt the system by submitting invalid proofs. 

If the prover has knowledge of the initial setup parameters (due to a compromised trusted setup), they can potentially create fake proofs. These fake proofs would wrongly convince a verifier that a computation was performed correctly, even when it wasn't. This could undermine the integrity of the entire system.

If a prover can find ways to manipulate inputs without violating constraints, they could produce proofs that, while technically valid, represent incorrect or misleading computations. This would essentially defeat the purpose of having a verification system.

## **Learnings**

I definitely learned a lot during this contest as it was easily my most challenging contest yet. I was introduced to rust and very math heavy concepts.

**Main Concepts:**

zk-SNARK:

zk-SNARK stands for "Zero-Knowledge Succinct Non-Interactive Argument of Knowledge." It’s a cryptographic method that allows one party to prove to another that a statement is true, without revealing any specific information about the statement itself, other than the fact that it's true. This concept was previously alien to me, but I learned a lot about how zk-SNARK can be used in various settings, including blockchain and how it can change the world that we live in

Rust:

Developing cryptographic operations, especially something as nuanced and intricate as ZKPs, demands a language that prioritizes both performance and safety. Rust, with its focus on memory safety without sacrificing performance, becomes a prime candidate for implementing ZKPs. Its type system and borrowing mechanism ensure that common pitfalls in cryptographic implementations, such as buffer overflows or race conditions, are minimized.

Plonk math:

PLONK is a type of zero-knowledge proof system, which allows one party to prove the truth of a statement without revealing specific information about it. The acronym PLONK stands for "Permutations over Lagrange-bases for Oecumenical Noninteractive arguments of Knowledge." A key feature of PLONK is its use of a Universal Reference String (URS), allowing the same setup to be used for multiple circuits or computations. Compared to other systems, PLONK is valued for its simplicity and efficiency.

Deep Dive into L2 chains: 

During my exploration, I learned a lot about how Layer 2 (L2) chains operate and their significance in the realm of blockchain and decentralized applications. These chains act as a secondary layer on top of the main blockchain, commonly referred to as Layer 1 or L1. The primary objective of L2 chains is to scale the throughput of the network, reducing transaction fees and latency. This is achieved by offloading a significant portion of transactions from the main chain, processing them off-chain, and then batching and settling them back on the main chain in a single or a few transactions.

### Time spent:
120 hours