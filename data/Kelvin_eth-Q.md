##  Dangerous shadowing

Local variable **successVerifyProof' shadow (i.e. override) itself**
Contract: 
First declaration: Line 355
Second declaration: 363
(https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol)

The If block is ran and successVerifyProof is declared, but the following code overrides it. 

 ## Dangerous usage of tx.origin

Contract: Mailbox.sol 
Code line: 248 
(https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol)
Use of tx.origin for authorization may be abused by a MITM malicious contract forwarding calls from the legitimate user who interacts with it.

