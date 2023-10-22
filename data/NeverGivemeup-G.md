[G-01] Avoid using Openzepplin :

Description:
Solmate's implementation of the libraries used on the protocol are more optimized saving gas to the user.

Instances: 

https://github.com/code-423n4/2023-10zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L6
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L5

Gas saved per instance:
540

Recommendation:
Change Openzepplin to Solmate on every file.

