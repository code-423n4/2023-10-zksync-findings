# Analysis report by Ustas

## 1. Overview
zkSync is a L2 blockchain that inherits security features from Ethereum to offer secure and cost-effective transactions to users. It utilizes its custom ZK-proof system called Boojum, which involves a trusted setup and efficiently verifiable proofs that can be later confirmed on the Ethereum mainnet.

The primary functionality of zkSync involves two key actors: L1 contracts and Operators. The fundamental workflow can be summarized as follows:
1. Operators gather transactions from users (from the L2 mempool, and the L1 contracts).
2. These transactions are processed through a Yul contract called `Bootloader`. This execution wraps user and system transactions. The operator then generates a ZK proof based on the output of this execution.
3. The operator subsequently commits, proves, and executes the produced proof on L1, thereby finalizing the batch and user transactions.

## 2. Centralization
In its current form, the protocol places significant reliance on the trustworthiness of the development team. The team utilizes two multisig wallets for critical actions within the system:

1. One multisig is under the control of the team.
2. The other is held by influential actors in the industry.

Despite this arrangement, there remains a potential risk of organized manipulative actions or the theft of private keys from multiple members of these multisig wallets. As a result, the community must actively monitor these multisigs to promptly respond to any undesirable actions or events.

### 2.1 Issues

#### 2.1.1 A Sole Operator
Currently, the zkSync team is the sole operator within the system, and this is expected to remain the case in the near future. This single-operator model introduces a significant risk of failure, as the operator could be susceptible to various traditional attack vectors. The team's control over the operator also means they have the ability to shut it down at any moment, potentially halting the network and withdrawals.

#### 2.1.2 Lack of Emergency Exit
In the event of an unforeseen issue within the system, there is currently no mechanism in place for users to withdraw their balances from the network.

#### 2.1.3 Shadow Upgrades
The development team has access to system upgrades without content restrictions. This means they can make extensive changes, such as withdrawing all ethers, creating backdoors, rendering certain contracts unusable, and more. The only limitation on upgrades is the time lock, which is also controlled by the team and located in the `Governance` contract. Additionally, the upgrade system includes a crucial function, `Governance.scheduleShadow()`, which enables system upgrades without revealing the upgrade's content.

### 2.2 Summary
Collectively, all three issues create a concerning scenario: the team can disable their operator, implement an upgrade, and subsequently withdraw all the funds after the timelock. In the absence of an emergency exit, users would be left in a challenging situation, unable to access their funds. Without a functioning operator, both L1 -> L2 and L2 -> L1 transactions would be impossible.

Given the substantial TVL of $420 million, even the slightest possibility of these issues occurring could potentially lead to a catastrophic outcome.

## 3. Time Spent
Over the course of two weeks of daily work, a total of 74 hours was dedicated to research. This research primarily focused on the system contracts, operator activities, and the interaction between L1 and L2 components.

### Time spent:
74 hours