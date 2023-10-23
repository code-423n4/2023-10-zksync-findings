# Analysis

## Table of Contents

- [Approach](#approach)
- [Architecture Overview](#architecture-overview)
- [Testability](#testability)
- [Centralization Risks](#centralization-risks)
- [Systemic Risks](#systemic-risks)
- [Summary of Reported H/M findings](#summary-of-reported-hm-findings)
- [Summary of Reported QA findings](#summary-of-reported-qa-findings)
- [Learnt About](#other-recommendations)
- [Other Recommendations](#other-recommendations)
- [Security Researcher Logistics](#security-researcher-logistics)
- [Conclusion](#conclusion)

## Approach

The auditing process began even before the contest started, grasping a high-level but brief overview of the context surrounding a `zk rollUp`. This involved exploring articles such as _[overview of Scroll's architecture](https://scroll.mirror.xyz/nDAbJbSIJdQIWqp9kn8J0MVS4s6pYBwHmK7keidQs-k), [this tweet from `portport`](https://twitter.com/portport255/status/1706410965846130929?s=20)_, and several other resources prior to the contest's initiation.

Once the contest began, a significant amount of time was dedicated to examining the smart contracts section of the in-scope codebase, especially since I'm more familiar with `Solidity` as opposed to `Yul` or `Rust`. After an initial 3-days quick review, approximately 8 hours spread around two days were spent on previous contests that were publicly accessible, specifically both the [2022-10](https://github.com/code-423n4/2022-10-zksync-findings) & [2023-03](https://github.com/code-423n4/2023-03-zksync-findings) zksync contests on Code4rena, along with the [OpenZeppelin's zksync Layer 1 audit report](https://blog.openzeppelin.com/zksync-layer-1-audit).

Post-review of available audit reports and ensuring that issues had been correctly mitigated, a thorough, line-by-line examination of the smart contracts section was conducted, with a brief look at the `.yul/.rs` contracts using `chatGPT` to grasp context. The final phase of this process encompassed interactive sessions with the developers on Discord, and also checking out the public discord chat to gain more context from answers provided by devlopers to questions asked by other secuiryt researchers. This not only enhanced comprehension of distinct implementations but also promoted discussions about potential vulnerabilities and observations that, in my opinion, were pivotal and required validation from the team.

## Architecture Overview

### L1 Smart Contracts

**Background**:
zkSync Era uses smart contracts built on Ethereum's Layer 1 (L1) to ensure security and functionality without needing its own consensus mechanism. For a primer on Rollups, refer to [Rollup Introduction](https://era.zksync.io/docs/reference/concepts/rollups.html).

**Components**:

1. **Diamond**:

   - Serves as the bridge between Ethereum (L1) and zkSync (L2).
   - Uses the [EIP-2535](https://eips.ethereum.org/EIPS/eip-2535) diamond proxy pattern, inspired by [mudgen's implementation](https://github.com/mudgen/Diamond). This design is modular, expandable, and includes security features like freezing.

2. **GettersFacet**:

   - Provides `view` and `pure` methods.
   - Uses the [diamond loupe](https://eips.ethereum.org/EIPS/eip-2535#diamond-loupe) to manage facets.

3. **AdminFacet**:

   - Controls privileged addresses and manages the diamond proxy.
   - Governed by two entities: Governance (for protocol upgrades) and Admin (for non-critical changes).

4. **MailboxFacet**:

   - Manages L2 <-> L1 communication.
   - Handles Ether bridging and censorship resistance mechanisms.
   - For detailed operations, refer to [Handling L1->L2](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Handling%20L1→L2%20ops%20on%20zkSync.md) and [Handling pubdata in Boojum](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Handling%20pubdata%20in%20Boojum.md).

5. **ExecutorFacet**:

   - Validates L2 batches, checks zk-proofs, and ensures data availability.

6. **DiamondInit**:

   - Initializes the diamond proxy using an approach outlined in [EIP-1271](https://eips.ethereum.org/EIPS/eip-1271).

7. **Bridges**:

   - Contracts that manage asset transfers between L1 and L2.
   - Types include L1ERC20Bridge, L2ERC20Bridge, L1WethBridge, and L2WethBridge.

8. **Governance**:

   - Oversees zkSync Era contracts on both L1 and L2.
   - Handles upgradability and system parameter changes.
     > NB: - Making the address which has the permission to approve instant upgrades a Gnosis multisig commendable.

9. **ValidatorTimelock**:
   - An interim contract between validator EOA and zkSync.
   - Aims to ensure security by delaying batch execution.

### Elliptic Curve Precompiles

#### Summary

Elliptic curve precompiled contracts are crucial for zkSNARK verification. The operations needed are point addition, scalar multiplication, and pairing on elliptic curves. This documentation covers the precompiles for point addition and scalar multiplication and their design considerations. The full specification can be found [here](https://eips.ethereum.org/EIPS/eip-196).

#### Introduction

- The Ethereum Virtual Machine (EVM) includes precompiled contracts providing advanced functionalities.
- They're available at fixed addresses and have a set gas cost.
- Precompiled contracts are introduced with new hard forks and are called like regular contracts.
- In the zkSync Era, `ecAdd` and `ecMul` precompiles are created as smart contracts due to:
  - A need for zkEVM to prove their execution.
  - The complexity of creating custom circuits for elliptic curve operations.

#### Field Arithmetic

- The BN254 curve (alt-BN128) is defined by \(y^2 = x^3 + 3\) over a finite field.
- Arithmetic on this field uses the Montgomery form to accelerate computations.
- There are disparities in performance between zkSync and EVM because of their different instruction sets.
- The `mulmod` command is notably inefficient in zkEVM.

#### Multiplication & Inversion

- Multiplication uses the Montgomery reduction, providing speed over basic methods.
- Squaring is done by multiplying a number by itself, further optimized with SOS Montgomery squaring.
- Inversion uses the extended binary Euclidean algorithm.

#### Montgomery Form

This section explains the Montgomery form's mathematics, which enhances computation efficiency.

- The Montgomery Reduction Algorithm (REDC) is presented with a Solidity implementation.
- Addition and subtraction in Montgomery form are similar to typical modular operations.
- The Montgomery multiplication and inversion operations are detailed with their Solidity implementations.

#### ECADD

- It's a precompile for elliptic curve point addition.
- Points are in affine form: \( (x,y) \).
- The curve is defined by the equation \( y^2 = x^3 + ax + b \).
- Procedures for point doubling and addition are detailed.

#### ECMUL

- It's a precompile for elliptic curve point scalar multiplication.
- Points are represented in homogeneous projective coordinates: \( (x , y , z) \).
- Projective coordinates defer divisions by introducing a new coordinate, optimizing performance.

**Note**: For in-depth understanding and the complete formulae, one must refer to the full document.

### Bootloader

Try imagining your computer starting up. Before all your software runs, there's a crucial program called the bootloader that starts everything up. The same principle applies here. In typical Ethereum setups, transactions are picked, validated, and executed one by one. But in zkSync, doing this is super slow because they have to prove each transaction separately. So instead, zkSync uses something like a "super transaction" or a batch of many transactions and processes them all together. This is what the bootloader does. It's like a grand entrance where instead of one visitor (transaction) at a time, a whole group of visitors enter together. A similar idea is seen in the EntryPoint of EIP4337. For some technical reasons, this bootloader doesn't really exist in the same way on Layer 2 (L2) of Ethereum, and if someone tries to interact with it, they actually end up talking to a blank or "empty" contract.

### System Contracts

One could think of these like the essential apps or software your computer needs to function. While many basic operations (like adding numbers, managing memory) work just fine in zkSync, some others don't. For these special operations, they have "system contracts". These aren't just any contracts; they're VIPs with special privileges. They live in a unique address space and get set up right at the beginning. Changing them? Only through a significant system update managed from Layer 1 (L1).

> ❗⚠️ Due to time constraints, this section is only covers he _L1 Smart Contract, Elliptic Curve Precompiles and short notes on both the System Contracts & Bootloader _ sections of the zKSync codebase in scope, for additional overview, please refer to the [docs](https://github.com/code-423n4/2023-10-zksync/tree/main/docs) section of the contest, cause unlike other contests this is very understandable and would suffice, or navigate with the references listed below for the Smart Contracts/Circuit and VM Sections

- Smart Contracts Section

  - **[L1 smart contracts](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/L1%20smart%20contracts.md)**
  - **[System Contracts/Bootloader Description](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md)**
  - **[zkSync Era Fee Model](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/zkSync%20fee%20model.md)**
  - **[Handling L1→L2 Ops on zkSync](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Handling%20L1→L2%20ops%20on%20zkSync.md)**
  - **[Batches & L2 Blocks on zkSync](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Batches%20&%20L2%20blocks%20on%20zkSync.md)**
  - **[Elliptic Curve Precompiles](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Elliptic%20curve%20precompiles.md)**
  - **[Handling Pubdata in Boojum](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Handling%20pubdata%20in%20Boojum.md)**

- The VM Section

  - **[ZkSync Era Virtual Machine Primer](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/VM%20Section/ZkSync%20Era%20Virtual%20Machine%20primer.md)**
  - **[zkSync Era: The Equivalence Compiler Documentation](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/VM%20Section/How%20compiler%20works/overview.md)**
    without extending Solidity language with new syntax and why system contracts are needed.
  - **[EraVM Formal specification](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/VM%20Section/EraVM%20Formal%20specification.pdf)**

- Circuits Section

  - **How does ZK work? (high level)**
  - [Intro to zkSync’s ZK](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/Intro%20to%20zkSync%E2%80%99s%20ZK.md)
  - [ZK Terminology](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/ZK%20Terminology.md)
  - [Getting Started](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/Getting%20Started.md)
  - **Examples and Tests**
  - [Circuit Testing](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/Circuit%20testing.md)
  - **Advanced**
  - [Boojum gadgets](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/Boojum%20gadgets.md)
  - [Circuits](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/Circuits.md)
  - [Boojum function: check_if_satisfied](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/Boojum%20function%20check_if_satisfied.md)

### Important Links

- **Documentation:** https://era.zksync.io/docs/
- **Website:** https://zksync.io/
- **Twitter:** https://twitter.com/zksync
- **Discord:** https://join.zksync.dev/
- **Previous Audits:** https://era.zksync.io/docs/reference/troubleshooting/audit-bug-bounty.html

## Testability

The system's testability is very commendable and not less than what was expected, would be unfair not to applaud incorporating both hardhat and foundry for codes in scope, since having different testing schemes is definitely no easy feat and a very good ground for security researchers to get to know protocol more and even help secure protocol more, but just as any good thing... there's always room for further refinement and improvement.

## Centralization Risks

- For diamond proxy Privileged actors/Governors can freeze the diamond (not a specific facet!) by calling the freezeDiamond() and all facets with the marker `isFreezable` should be inaccessible until the governor or admin unfreezes the diamond. It's a dangerous action because freezing the diamond proxy can halt the upgrade system, freezing the diamond indefinitely.

- In the Validator Timelock contract, the owner can set an excessive execution delay. By setting the maximum uint32 value for the execution delay, it implies no requests can be processed until the year 2106. To mitigate this, one can use a smaller uint for the execution delay or apply administrative safeguards.

- Potential issues surround `Allowlist.sol and AllowListed.sol`. The owner can allow only specific users access to specific functions, effectively locking cross-chain requests and user funds. The `senderCanCallFunction` modifier is used extensively, and an owner's intervention can completely cut off a user, by specifying other users access to that function. Although there was a provision for the owner to revoke user access during an Alpha phase, that phase ended in April, but this feature remains accessible.

- Governance can change the validator's address at will, hinting at a risk. There are currently no safeguards against potential malicious changes to these addresses.

- Entrusting multiple entities with decision-making in the AdminFacet, particularly if involving a multisig with renowned crypto contributors, could lead to slower processes, conflicts, and decision paralysis. An established protocol should exist where one entity can override another to avoid potential gridlocks.

- The `executeInstant()` function, while useful for promptly addressing critical protocol issues, also carries centralization risks. The owner can pass a proposal without any waiting period, indicating a degree of unchecked power.

## Systemic Risks

- The current AllowList design doesn't support blacklisting specific addresses. If there's a need to exclude certain users due to issues like malicious activities, the present system isn't equipped to handle this efficiently. Implementing a governance-backed `disallowAddress()` function might improve access management.

- The `ContractDeployer` has potential risks tied to unrestrained force deployments. While standard deployments ensure safety checks, force deployments can bypass these checks, leading to accidental deployments on active addresses. This could result in data loss or misplacement of funds. It's advisable to introduce deployment checks, such as confirming an address resides in the kernel space or ensuring that addresses haven't had prior activity.

- Operating with a single centralized node poses inherent risks. Before making a judgment, it's crucial to ascertain whether Ethereum also functions with just one centralized node. To simplify, zkSync can be visualized as a distinct blockchain with a trustless bridge to Ethereum. Currently, zkSync operates on this centralized node where all transactions take place. System contracts and bootloaders reside on this particular blockchain. Therefore, when users wish to conduct a transaction, they need to invoke the zkSync RPC, which is distinct from Ethereum's RPC instance.

- There has been a notable reduction in memory allocation in `BOOTLOADER_MEMORY_FOR_TXS` from 485,225 to 273,132 in the zkSync system. This change is concerning, especially without clear optimizations. This memory is designated for transaction encoding, and any overflow can lead to transaction failures or other vulnerabilities. Such changes need thorough evaluations, rigorous testing, and transparent documentation to ensure system security. Without clear reasons for this reduction, it's wise to approach it cautiously.

## Summary of Reported H/M findings

The first one I'd like to note is how a user's funds could become trapped within the Bridge for an indefinite period, possibly even indefinitely. This precarious situation stems from the current implementation of `claimFailedDeposit()`, i.e and how it hardcodes the receiver of any failed deposits.

Furthermore, an alarming observation is the absence of a previously recommended fix post the OpenZeppelin's audit. Note that this was added in the codebase for the previous contest on Code4rena but seems to be missing in the current codebase, The missing fix pertained to the merkle verification, which, if left unchecked, could compromise data integrity and network security.

Moreover, multiple issues highlighted the vulnerabilities that could lead the protocolto DOS attacks and the pitfalls of hardcoded parameters. Since `allowList` now points to the public, it doesn't cost much for an attacker to spam the validators queue via `requestL2Transaction()` and in an edge case this could lead to a curb in commiting batches if the attacker successfully gets the validator down for longer than `COMMIT_TIMESTAMP_NOT_OLDER`. A glaring example is the hardcoded `l2ShardId` set to 0, which poses the risk of misidentifying transaction origins.

Diving into the case of hardcodes, a problem was identified in the serialization of transactions where the `paymaster` was hardcoded, rendering the inablity of `ERC4337` accounts to pay for the transaction fees. Another notable hardcoding discrepancy is the configuration of `PRIORITY_EXPIRATION` to `0 days`. This seems to be a residual setting from the Alpha release period which concluded in April, but this is still left as is, and his misconfiguration leads to unintended behaviors, as priority operations invariably set their expiry to the `block.timestamp` upon request creation.

With the ongoing discourse surrounding full EVM compatibility for zk, an issue was submitted pertaining to the `msg.sender != tx.origin` check. This existing mechanism, aimed at identifying contract senders, may fall short in effectively aliasing an address after `EIP-3074`.

Lastly, several issues pinpointed the vulnerabilities associated with funds attached to transactions. A notable mention is the recommendation for making `MsgValueSimulator::fallback` payable, ensuring seamless value transfers. Additionally, the complexities surrounding force deployments were highlighted, emphasizing the inherent risk of irrevocable fund losses under certain circumstances.

## Summary of Reported QA Findings

To highlight a few, the function `extendedAccountVersion()` should not return `AccountAbstractionVersion.Version1` for addresses operating in the kernel space. A notable observation is with the `storeBatchHash` function; it should never return reverted batches in any situation. There's an extra layer of complexity added by an additional check for mainnet in the `proveBatches()` function, which should be reconsidered especially with the post-hard fork scenario, where asserting that `block.chaindId != 1` might not provide valid protection. Concerning instant executions, `executeInstant()` seems to have a failure mechanism for operations scheduled in the future due to the `_checkPredecessorDone()` function.

Also an interesting one is how setting a pending admin in AdminFacets emits an event for setting a governor, causing issues for anyone listening off-chain. It's essential to modify `forceDeployOnAddress` ensuring that the `newAddress` consistently receives the allocated funds, regardless of the `callConstructor`'s value. Surprisingly, after the Alpha period, the Allowlist has not been implemented as initially intended. This is accompanied by concerns surrounding forced deployments which need restrictions in place.

Key to note that processing of L2 to L1 withdrawal messages with additional is not executed efficiently.There's also a strong recommendation to make the `fallback` function `payable` prefixed to safeguard against potential failures from the `bootloader`. Furthermore, to ensure compliance with EIP712, the `transaction.signature` should be added to `_encodeHashEIP712Transaction`. A striking hardcoded value is the `maxFeePerGas` set to 0, which could deter providing any incentives for validators. Also a finding was reported surrounding mismatched interface specifications with the actual functions present another one was regarding multiple documentation typos, and incorrect naming conventions.

A deviation from Solidity's best styling practices in scoped contracts was observed. The function `L2EthToken::transferFromTo` emits dummy events and may lead to unnecessary gas wastage. A potential system hang could occur if validators neglect cross-chain transactions. Concerning signature validations, multiple issues were found with `_Isvalidsignature` as it doesn't align with the EIP specifications. For maintaining data integrity, setters should always be integrated with equality checkers.

The allocation reduction of `BOOTLOADER_MEMORY_FOR_TXS` poses risks. The `OperationState.Waiting` seems not to consider scenarios where the `timestamp` equals `block.timestamp`. Potential compatibility issues might arise for pure ERC20. A recommendation to augment the AllowList with a blacklisting mechanism has been given. Interface discrepancies are evident; the `ILegacyGetters` displays deprecated functions rather than the new ones.

Changes to `L2ContractHelper/Config.sol` demand swift redeployments of facets. Another concern arises from the presence of non-production-ready code. To align with EIP-712 standards, the `verifyingContract` must be added to the TYPEHASH. Lastly, the system currently has a redundant check where `v == 27 & v == 28`.

This was my first attempt to audit a very large codebase on Code4rena, didn't go as bad as i thought it would, but I also didn't have enough time to go through all the contracts in scope unfortunately which means i still have a lot of things to improve in my auditing methodology, but all in all it was a massive learning experience and i definitely have enough courage to hit the next big contest that's going to pop on C4.

## Learnt About

During the course of my security review, I had to research some stuffs to understand the context in which they are currently being used, some of which led to me learning new things and having refreshers on already acquainted topics. Some of these topics are listed below:

- `EIP 1559`
- Some differences between the EVM and zkEVM
- Developed a liking to rust and started a rust course
- `EIP-2535`
- Also had a refresher on multiple previously known things, notably `EIP-1271`, `EIP-2200` & `EIP-4337`
- Zk lite

## Other Recommendations

### **Enhance Documentation Quality**

I'd like to start by commending the quality of the [docs](https://github.com/code-423n4/2023-10-zksync/tree/main/docs). They are generally well-crafted. However, there are some gaps concerning what's within the scope. It's important to remember that code comments also count as documentation, and there have been some inconsistencies in this area. Comprehensive documentation not only provides clarity but also facilitates the onboarding process for new developers. For instance, the instant execution of operations scheduled in the future by `executeInstant()` seems impossible due to the check on the previous operation `_checkPredecessorDone()`. But, after discussing with the team, it was conveyed that this is _somewhat_ intended behavior. Unfortunately, this detail isn't currently documented.

### **Onboard More Developers**

Having multiple eyes scrutinizing a protocol can be invaluable. More contributors can significantly reduce potential risks and oversights. Evidence of where this could come in handy can be gleaned from codebase typos. Drawing from the [broken windows theory](https://en.wikipedia.org/wiki/Broken_windows_theory), such lapses may signify underlying code imperfections.

### **Leverage Additional Auditing Tools**

Many security experts prefer using Visual Studio Code augmented with specific plugins. While the popular [Solidity Visual Developer](https://marketplace.visualstudio.com/items?itemName=tintinweb.solidity-visual-auditor) has already been integrated with the protocol, there's room for incorporating other beneficial tools.

### **Enhance Event Monitoring**

Current implementation subtly suggests that event tracking isn't maximized. Instances where events are missing or seem arbitrarily embedded have been observed. A shift towards a more purposeful utilization of events is recommended.

### **Improve Testability**

As already stated in this report in regards to the current level of testing _there's always room for further refinement and improvement._

### **Refine Naming Conventions**

There's a need to improve the naming conventions for contracts, functions, and variables. In several instances, the names don't resonate with their respective functionalities, leading to potential confusion, an example of this can be seen on the `SystemContext.sol` contract, where `BlockInfo` points to two different contexts which is wrong concepts, namely "The number and the timestamp of the current L1 batch stored packed." & " The number and the timestamp of the current L2 block.", whereas this could be okay for the latter, `BatchInfo` would suit more for the former.

- Add more developers or team members,

## Security Researcher Logistics

My attempt on reviewing the zkSync Era Codebase spanned around 95 hours distributed over 18 days:

- 3 hours dedicated to writing this analysis.
- 8 hours exploring the previous contests on Code4rena and also the audit report by Openzeppelin,viewable from the links below
  - [2022-10-zksync's Code4rena report](https://github.com/code-423n4/2022-10-zksync-findings).
  - [2023-03-zksync's Code4rena report](https://github.com/code-423n4/2023-03-zksync-findings).
  - [OpenZeppelin's zksync Layer 1 audit report](https://blog.openzeppelin.com/zksync-layer-1-audit)
- 2 hours were allocated for discussions with sponsors on the private discord group regarding potential vulnerabilities.
- 10 hours over the course of the 18 days _(~30 minutes per day)_ was spent lurking in the public discord group to get more ideas based on questions other security researchers ask and explanation provided by sponsors.
- The remaining time was on finding issues and writing the report for each of them on the spot, later on _editing a few with more knowledge gained on the protocol as a whole, or downgrading them to QA reports(reason why i had a whooping 28 issues in my QA report)_

## Conclusion

The codebase was a very great learning experience, though it was a pretty hard nut to crack, being that it's like an update contest and most _easy to catch_ bugs have already been spotted and mitigated from the previous contests/audits, This is not surprising, considering there have been three distinct reports on this: two on Code4rena and a comprehensive layer 1 audit with OpenZeppelin, not to mention the ongoing bug bounty program with Immunify.

Nonetheless, during the course of my security review, I encountered multiple issues, exactly 37 in number, as an attempt not to spam the judging repo 28 were submitted as QAs with a request on a few for the judge to upgrade to _med_ findings if they deem fit as some of them easily sit on the fence between med/QA on Code4rena. _>NB: Attached in the two immediate sections above are their summaries_.


### Time spent:
95 hours