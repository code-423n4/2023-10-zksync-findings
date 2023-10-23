## Approach

After gaining a high level understanding of the system by reading the docs provided in the C4 repo, I decided to focus on the smart contracts section. 
Did couple of passes through the codebase to associate the code with its functionalities followed by analysing each section in depth.  

## Mechanism Review

The system stores value in a L1 contract and manages state by transactions in L2 where the rules for state updates is enforced using zk-proofs. User's submit their transactions to an operator to perform actions. The operator batches multiple transactions and commits it to L1 where the batch can only be executed if the operator is able to provide a proof to a contract named Verifier in L1. This contract was out-of-scope of in the audit. 

Although the system maintains the trustability and scalability heads using zk-proofs and batching/compression, it falls behind in decentralization. As of now, the operator is a centralized entity which processes all transactions in L2. Hence censorship is not guarenteed. But the system is designed in such a way that a malicious operator can be replaced with another one at any time and the transactions submitted to L1 by the operator can be used to reconstructed the current state of L2.

The implementation of the L1 part is a diamond proxy which allows for contractwise-functionwise freezability to required functions. This allows to pause a certain portion of the system if needed while still being able to perform other operations. The Ether in L2 is minted by locking Ether in this contract. The logic regarding movement of tokens and eth across L1 and L2 is implemented in the Mailbox facet. To withdraw funds from L2, users need to produce a merkle proof showing the inclusion of their withdrawal/transaction which the Mailbox facet will verify with root hash stored in it. User's need to be aware that address aliasing takes place and that the deposits are capped. Also to note for a user is that once deposited, withdrawals doesn't give additional power to deposit.  

## Centralization risks

Security Council, Admin and Operator/Validator are the 3 categories of entities having special permissions in the system.

1. Operator: The transactions submitted by users are executed and commited to L1 by the operator. As of now the operator is trusted in providing the gasPrices, but it is not of concern for the user's as their max spending is guaranteed by the contracts and zk-proof. But the operator can decide to push user's spending to the maximum permitted without providing an accurate refund. The power of the operator is controlled with the use of timelock which only allows updates from the operator with a fixed delay. This allows the other more trusted roles in the system to monitor and act accordingly.

2. Admin: The admin is a multisig of the matter labs team. It has the ability update the operator, schedule delayed upgrades and schedule shadow upgrades. Shadow upgrades only publish the hash of the upgrade and not the full data. But the ability to do a malicious upgrade via such a mechanism is also controlled by the Security Council which can cancel any upgrade. Hence as the user's need not worry on trusting only the matter labs team.

3. Security Council: The security Council is supposed to be a multisig of a trustable members in the crypto community. When both Security council and the admin agree, it has the ability to execute and instant upgrade which if malicious can rig the entire system and is the action that user's need to be most concerned of.

### Time spent:
100 hours