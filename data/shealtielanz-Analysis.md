# zk Sync Era Codebase Analysis 

This was a time-boxed audit on code4rena   with a contest approach, here wardens are made to compete where they are incentivized to find more issues especially unique bugs, as the more unique issues found by a warden the bigger the wardens reward would be. The audit spanned for about 3weeks.

 zySync Era is a layer 2 scaling solution for Ethereum based on zero-knowledge rollup technology. The protocol aims to provide low transaction fees and high throughput while maintaining full EVM compatibility.

## Architecture recommendations.

- The Architecture of zkSync Era contracts is solid and  with the use of EIP-2535 The Diamond Standard, the upgradeability is all the more fluid.

- The Architecture supports its mechanisms like.
* Bridging of tokens and Weth to Layer 2 and Withdrawals back to Layer 1 was fluid with The Bridges, It’s Libraries, Mailbox and Bootloader contracts.
* Messaging of transactions and requests from Layer 1 to Layer and vice versa are handled smoothly with the Mailbox
* The Commitment, Proving and Execution of Batches are well executed in the Executor, Verifier, Merkle etc Contracts.
* The Governance and Centralization Architecture of the protocol reduces the risk of Centralization with the scheduling and execution of operations, The Governance, Allowlist, security council and Admin  handles the protocol operations and upgrades.
* There is a huge centralization risk in the Allowlist contract as its owner can grant access to critical function of the protocol.

## Codebase Quality 
- The Codebase was well written with the intention to well describe the functionalities and different mechanisms the contracts implement.
- The Documentation well described the core contract of the protocol but did not well describe the interactions between the different contract from Layer 1 to Layer 2.

## Centralization risks

The protocol is under a huge centralization risk where the responsibility of core upgrades and the well functioning of the system will be rested on the shoulders on MatterLabs and The zkSync developers for a while. 
- The scheduling and execution of Operations in zySync Era is well handle by the Governance, Allowlist and Security council  of zySync.

## Mechanism review.
- The Messages sent from Layer 1 to Layer 2 is well handled 
- The commitments and Execution of Batches is well handled.
- Bridging is well Implemented both on Weth and Common ERC 20 tokens.
- verification of batches was out of scope for this audit so I recommend the protocol host an audit for it to.

## Privileged Roles and Security Assumptions

In addition to the privileged roles described in the last audit report, the system contains the following additions and changes:

- An additional Allowlist enables granular access management to the Mailbox facet and the L1 ⇔ L2 bridges built upon it. 

- The Security council changed from a set of individual addresses to a single address that is assumed to be a Gnosis multisig. It continues to be limited to reducing the time a governor must wait between proposing and executing a system upgrade.

- The Governor is no longer forced to publicly reveal any information about an upgrade. It can choose between a transparent upgrade of the system, which reveals the diamond cut to be applied, and a shadow upgrade procedure that only reveals its hash. The execution of an upgrade is bound to a hash commitment during the proposal, either based on the transparent diamond cut data or given through the governor.

## Systemic risks
Issues can arise in
- proving of logs .
- verification of batches.
- failure of transactions sent to Layer 2 from Layer 1.

I recommend more audit and security reviews be done on upgrades and contracts to be added to help improve the efficiency on the protocol also reviews is still highly recommended on the codebase in the current audit as an audit doesn’t prove a code is free from bugs.





### Time spent:
40 hours