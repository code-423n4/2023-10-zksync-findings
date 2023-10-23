# Overview of ZKSYNC ERA

zkSync Era is a layer 2 solution on Ethereum powered by ZK-rollups. Previously zkSync Era was called zkSync 2.0 while zkSync Lite was called zkSync 1.0. While zkSync Lite excels in simple asset transfers and limited dApp interaction, it lacks native smart contract capability. zkSync Era addresses this limitation. It empowers developers to deploy smart contracts and interact within a network akin to the Ethereum mainnet. This Contest audit is mainly focus on the ERA, zkSync 2.0.

# Contest Detail

This contest is break the record of Highest prize pool in Code4rena, ($1,100,000 total maximum prize pool).
The scope of it is quite big, consist of 3 parts:

1. L1 Contracts:

   - zkSync
   - Bridges
   - Governance
   - Upgrades
   - Other (helper, libraries, interfaces)

2. L2 Contracts:

   - Bootloader
   - System Contracts
   - Bridges
   - Other

3. ZK Circuits

As I recall, zksync already completed 2 audits in Code4rena, including the 2.0 (ERA) and the system contracts. The newest code being audited here is the ZK Circuits which is based on Rust.

# Audit Methodology

Steps:

1. **Read audit README to understand scope and expectations from the audit:** Familiarize with the provided audit documentation to establish the scope and objectives of the review.

2. **Read Official zkSync docs:** Study the official zkSync documentation to gain a comprehensive understanding of the project's design and functionality.

3. **Review any existing audit reports or similar project assessments:** Examine prior audit reports related to zkSync or similar projects to leverage insights and identify potential areas of focus especially L2 solution based on ZK proof.

4. **Screen through zkSync ERA codebase, emphasizing entry points analysis:** Perform an initial scan of the codebase, focusing on identifying critical entry points where external interactions occur.

5. **Execute provided test cases:** Run the suite of test cases provided by the project team to validate code functionality and detect any obvious bugs or issues.

6. **Conduct detailed logic analysis of zkSync codebase:** Systematically scrutinize the codebase, line by line and function by function, to assess correctness, efficiency, and potential vulnerabilities.

7. **Build diverse edge case scenarios for testing:** Create unconventional scenarios to evaluate the code's performance and behavior under non-standard or extreme conditions.

8. **Compare changes of previous audit codebase:** Sometimes fixing a bug from previous codebase can raise additional little mistakes which overlooked by devs

I seldom rely on audit tools since I presume that most projects have already undergone such analysis, and most auditors typically employ these tools. Therefore, my focus isn't on utilizing well-known automatic analysis tools.

More over, I'm not expertise on Rust, thus I skipped the ZK Circuit part

# Codebase Quality Analysis

I have no comment about the design quality of codebase of this audit. zkSync for sure is a top notch project which brilliant people choose the best methodologies to implement the code design.

In summary, the audited code exhibits a very high standard of quality, characterized by well-documented annotations, modular organization, and thoughtful interface design.

# Architecture Design Analysis & Recommendation

In my opinion, zksync ERA design (also in their proofing methods) which claimed to be 99% EVM compatible with Solidity and Vyper needing to first compile to Yul, an intermediate language, before compiling down to zkEVM bytecode through LLVM is a major benefit of zk rollup protocol compared to for example StarkNet which is far from Zksync EVM compatibility.

My knowledge of ZK technology is very limited, I don't really have anything to recommend about its architecture, but one thing I noticed is due to relies heavily on ZKPs, the math behind it is complex and expensive to implement, and hard to understand (compared to game theory of optimistic rollup)

# Centralization & Systemic Risks

Referencing from https://l2beat.com/scaling/projects/zksync-era#risk-analysis,

the main issue I noticed here is Governance Centralization, the code that secures the system can be changed arbitrarily and without notice by the governor, that currently is a 4 / 8 Multisig. The governor can also change the Verifier contract without notice. This arrangement raises concerns about transparency, as well as the potential for rapid and unilateral changes to the protocol, which could impact the broader zkSync community.

Also there is issue about only the whitelisted proposers can publish state roots on L1, so in the event of failure the withdrawals are frozen.

And about sequencer failure, users can submit transactions to an L1 queue, but can’t force them. The sequencer cannot selectively skip transactions but can stop processing the queue entirely. In other words, if the sequencer censors or is down, it is so for everyone.

# Conclusion, Notes, Additional Information and Suggestions

Currently the L2 rollup tech is still mainly on Optimistic rollup side, honestly ZK is not in a highlight spot yet. While ZK-rollups offer enhanced security and privacy with slightly lower scalability, optimistic rollups provide higher scalability and ease of implementation. Even though ZK-SNARKs are being used in real-world applications, they're still considered a relatively new and untested form of cryptography. Cryptography has been improving a lot lately, but it takes time to make sure new methods are really secure. 

About the audit contest, I still think that the codebase is large enough to be in a single audit contest, it's better to have a segmentation of it, just like previous 2 zksync audit on code4rena. 

Moreover, I'm eager to know finding of any ZK Circuit issue, due to code4rena is well-known for a solidity-based audit contest. 

Thus, I finally suggest to have a separate and dedicated audit for ZK Circuit for a targetted Rust user in maybe different platform or audit company.

### Time spent:
45 hours