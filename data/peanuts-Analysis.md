### 1. Summary of the protocol

Zksync era is a new version of the zksync network, which is an L2 that is built on top of Ethereum. In the context of zkSync, an era represents a period where features are implemented or upgraded. The solidity side of the protocol is mostly on the bridge part, where users can bridge their tokens or ETH from L1(Ethereum) to L2(Zksync) and vice versa. During the bridging process, zksync uses zero-knowledge proofs to validate transactions off-chain and submit a SNARK on chain (zero-knowledge Succinct Non-interactive ARgument of Knowledge) to ensure the correctness and security of those transaction. The proofing is done in the circuits part of the protocol using rust.

### 2. Comments and Disclaimer

Looked through the solidity side of the protocol, mostly bridging, minting, sending transactions and gas calculations. Also looked through the bootloader contract

### 3. Flow of protocol functionalities

##### Bridging ERC20 tokens from L1 to L2

- Starts with `deposit()` in L1ERC20Bridge.sol. User must be in the special allowlist or the access mode is set to public in Allowlist.sol.
- User deposit funds via `_depositFunds()`. The fund amount must be the same amount as the parameter.
- The user's deposit limit is checked by `_verifyDepositLimit()`
- `_getDepositL2Calldata()` is called.
- `IL2Bridge.finalizeDeposit` is called (in L2ERC20Bridge.sol).
- L2 Token is deployed through `_deployL2Token()`
- `_deployL2Token()` calls `_deployBeaconProxy` and initializes a bridge in L2StandardERC20.sol with `bridgeInitialize()`
- L2Tokens are minted through `bridgeMint()` in L2StandardToken.
- Back to L1ERC20Bridge contract, `requestL2Transaction()` is called in the zkSync Mailbox contract.
- Mailbox.sol, `_requestL2Transaction()` is called (Take note that \_requestL2Transaction can be called directly by the user)
- `_requestL2Transaction()` calls `_writePriorityOp()`
- `_writePriorityOp()` serializes the L2 transaction through `_serializeL2Transaction()`
- `_serializeL2Transaction()` calls `validateL1ToL2Transaction()` and pushes the transaction into the priority queue.
- The function process ends here. The next continuing function calld will be `commitBatches()` and `executeBatch()` in Executor.sol

##### Bridging native tokens from L1 to L2

- Process is similar to bridging ERC20 tokens. Starts with `deposit()` in L1WethBridge.sol
- The difference is that when `_getDepositL2Calldata` is called which calls `IL2Bridge.finalizeDeposit()`, there is no deploying of proxy and minting of tokens
- Instead, the ETH is converted to WETH via `IL2Weth(l2WethAddress).depositTo`.
- The rest of the process follows a similar pattern as bridging ERC20 tokens

##### Redeeming a failed deposit on L1 Bridge

- If a transaction fails to be transmitted from L1 to L2, the user can call `claimFailedDeposit()` to withdraw their locked tokens.
- `claimFailedDeposit()` calls `proveL1ToL2TransactionStatus()` in Mailbox.sol contract.
- `proveL1ToL2TransactionStatus()` gets the L2Log and calls `_proveL2LogInclusion()`.
- `_proveL2LogInclusion()` calculates the root has and the actual root has and compares them with each other. If both hash are the same, that means the specific L2 log was sent to L2 in a specific L2 batch number.
- Once the transaction is proven, the L1ERC20Bridge `claimFailedDeposit()` function will verify the deposit limit through `_verifyDepositLimit()` and transfer the token back to the user

###### Bridging ERC20 tokens from L2 to L1

- `withdraw()` is called on L2ERC20Bridge
- `bridgeBurn()` is called. The L2 ERC20 token is burned first.
- `_getL1WithdrawMessage()` is called to encode the message
- `L2ContractHelper.sendMessageToL1()` is called with the encoded message as the parameter.
- L2ContractHelper calls `L2_MESSENGER.sendToL1()` to send the message to L1
- Seems like the function stops here to prepare for another call.

###### Bridging native tokens from L2 to L1

- Similar to ERC20 token bridging, `withdraw()` is called on L2WethBridge
- WETH is burned through `IL2StandardToken(l2WethAddress).bridgeBurn`
- Native token is sent through `L2_ETH_ADDRESS.withdrawWithMessage{value: _amount}(l1Bridge, wethMessage);`

### 4. Codebase Analysis and Review

| Contract        | Function                     | Explanation                                                          | Review                                                                 |
| --------------- | ---------------------------- | -------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| Allowlist       | canCall                      | Whether the caller can call the specific function on target contract | Used in AllowListed senderCanCallFunction modifer in Bridges Contracts |
| Allowlist       | setAccessMode                | Set the permission mode to call the target contract                  | onlyOwner, changes AccessMode (public, special access)                 |
| Allowlist       | setBatchAccessMode           | Same as setAccessMode(), just with batches                           | Loops and calls \_setAccessMode()                                      |
| Allowlist       | setPermissionToCall          | Give permission to call if access is restricted                      | onlyOwner, gives special access permission                             |
| Allowlist       | setBatchPermissionToCall     | Same as setPermissionToCall, just with batches                       | Loops and calls \_setPermissionToCall()                                |
| Allowlist       | setDepositLimit              | Set deposit limit data for a L1 token                                | onlyOwner, checks if limit is active and sets limit                    |
| Allowlist       | getTokenDepositLimitData     | Get deposit limit data of a token                                    | view, returns deposit limit data                                       |
| Governance      | getOperationState            | Checks the operation state of the given id                           | view, returns operation state                                          |
| Governance      | scheduleTransparent          | Propose a fully transparent upgrade                                  | onlyOwner, schedules operation with a delay, delay must be < minDelay  |
| Governance      | scheduleShadow               | Propose "shadow" upgrade                                             | onlyOwner, gets \_id instead of \_operation,                           |
| Governance      | cancel                       | Cancel the scheduled operation                                       | onlyOwnerOrSecurityCouncil, cancels the operation                      |
| Governance      | execute                      | Executes the scheduled operation                                     | onlyOwnerOrSecurityCouncil, make sure time delay has passed            |
| Governance      | executeInstant               | Executes the scheduled operation instantly                           | onlySecurityCouncil , no need for time delay                           |
| Governance      | updateDelay                  | Changes the minimum timelock duration for future operations          | onlySelf , must be called through execute (only contract can call)     |
| Governance      | updateSecurityCouncil        | Updates the address of the security council                          | onlySelf , must be called through execute (only contract can call)     |
| L1ERC20Bridge   | deposit                      | Lock funds in the contract and sends the request                     | calls finalizeDeposit on L2 Bridge and requestL2Transaction in Mailbox |
| L1ERC20Bridge   | claimFailedDeposit           | Withdraw funds that failed when finalizing on L2                     | proveL1ToL2TransactionStatus, call proveL2MessageInclusion             |
| L1ERC20Bridge   | finalizeWithdrawal           | Finalize the withdrawal and release funds                            | called from L2 bridge, call proveL2MessageInclusion                    |
| L1WethBridge    | deposit                      | Deposits WETH in contract, contract unwraps WETH to ETH              | different from normal bridge, directly call requestL2Transaction       |
| L1WethBridge    | claimFailedDeposit           | Withdraw funds that failed when finalizing on L2                     | **Not Supported**                                                      |
| L1WethBridge    | finalizeWithdrawal           | Finalize the withdrawal from L2 and release funds to L1              | called from L2 bridge, call extra isEthWithdrawalFinalized             |
| L2ERC20Bridge   | finalizeDeposit              | Finalize the deposit from L1 and mint funds on L2                    | called in deposit in L1ERC20 Bridge, creates a beacon proxy L2 Token   |
| L2ERC20Bridge   | withdraw                     | Burns funds on L2 and send message to L1                             | Call L2ContractHelper.sendMessageToL1                                  |
| L2StandardERC20 | bridgeMint                   | Mint tokens to a given account                                       | onlyBridge, onlyL2Bridge can call                                      |
| L2StandardERC20 | bridgeBurn                   | Burn tokens from a given account                                     | onlyBridge, onlyL2Bridge can call                                      |
| L2WethBridge    | finalizeDeposit              | Finalize the deposit from L1 and mint WETH on L2                     | onlyL1Bridge can call                                                  |
| L2WethBridge    | withdraw                     | Withdraw WETH on L2 and send to L1                                   | must be WETH token, not native ETH (no payable modifier)               |
| L2Weth          | bridgeMint                   | Mint tokens on L2                                                    | **Not Supported**                                                      |
| L2Weth          | bridgeBurn                   | Burn tokens from a given account and send to L2 Bridge               | onlyL2WethBridge can call, burns WETH, transfer ETH to bridge          |
| L2Weth          | deposit                      | Deposit Ether to mint WETH                                           | Special L2 Weth minted from native ETH                                 |
| L2Weth          | withdraw                     | Burn WETH to get Ether back                                          | Special L2 Weth burned for native ETH                                  |
| Mailbox         | proveL2MessageInclusion      | Prove that message was sent in a specific L2 batch number            | view, used to finalize L1 withdrawals                                  |
| Mailbox         | proveL2LogInclusion          | Prove that log was sent in a specific L2 batch                       | view, Returns true or false, (success or failure)                      |
| Mailbox         | proveL1ToL2TransactionStatus | Prove that the L1 -> L2 transaction was processed                    | pure, used to claim failed deposits                                    |
| Mailbox         | l2TransactionBaseCost        | Estimate ETH cost of requestion execution of L2 from L1              | derives the L2 Gas price                                               |
| Mailbox         | finalizeEthWithdrawal        | Finalize ETH withdrawal and release funds                            | sets isEthWithdrawalFinalized to true, calls \_withdrawFunds           |
| Mailbox         | requestL2Transaction         | Prove that L1->L2 transaction was processed                          | payable, gets the canonicalTxhash (txId is the nonce)                  |

#### In-depth Review

###### Allowlist

- There is quite a bit of centralization risk going on. Most importantly, the owner can set a deposit limit on any ERC20 tokens. If the owner is malicious, he can enable the deposit limit and set it to a really low value to grief the bridging process (no one can bridge any ERC20 tokens).

```
File: AllowList.sol
129:     function setDepositLimit(address _l1Token, bool _depositLimitation, uint256 _depositCap) external onlyOwner {
130:         tokenDeposit[_l1Token].depositLimitation = _depositLimitation;
131:         tokenDeposit[_l1Token].depositCap = _depositCap;
132:     }
```

###### Governance

- The owner can schedule and execute an operation. In every operation, the owner can set a delay before the operation is executable.
- There is also a minDelay variable, which is set in the constructor.
- The security council can execute any operation. The security council can overrule the delay and execute the operation instantly.
- There was an issue in the OpenZeppelin's Timelock Contract whereby an operator can overrule an operation and update the delay and the security council address. This reentrancy issue is mitigated here by checking the state of the operation before and after the execution of the operation
- Reference: https://medium.com/immunefi/openzeppelin-bug-fix-postmortem-66d8c89ed166
- The issue in this contract is that the security council can override a delay but not the owner, which is pretty confusing. If the security council can override, the owner should be able to override too, otherwise there is too much power given to the security council.

##### L1ERC20Bridge

- Fee-on-transfer token is not supported, which is understandable but may be an issue in the future when more tokens are created with a transfer / burn fee.
- The bridge bridges the exact amount of tokens everytime, so there should not be any leftover amounts in the contract or extra amounts bridged

##### Mailbox

- Proving transaction / message / logs and also requesting transaction from L1 -> L2
- Apparently `requestL2Transaction()` can be called directly without going through the L1ERC20 Bridge.
- If called directly, `IL2Bridge.finalizeDeposit()` is not called, which means that calling  `requestL2Transaction()` directly does not entail bridging tokens.

### 5. Centralization Risk

| Contract      | Actors            | Purpose                                                          | Risk |
| ------------- | ----------------- | ---------------------------------------------------------------- | ---- |
| Allowlist     | Owner             | Set access mode, deposit limit                                   | Yes  |
| Governance    | Owner             | Schedule and execute operations                                  | Yes  |
| Governance    | Security Council  | Execute operations, bypass operation time limit                  | Yes  |
| Governance    | Contract          | UpdateDelay, update security council                             | Nil  |
| L1ERC20Bridge | User              | Call deposit functions, depending on availability of access mode | No   |
| L1WethBridge  | User              | Call deposit functions, depending on availability of access mode | No   |
| L2ERC20Bridge | User              | Withdraw tokens from L2 to L1                                    | No   |
| L2ERC20Bridge | L1Bridge Contract | Call finalizeDeposit and mint tokens                             | Nil  |
| L2WethBridge  | L1Bridge Contract | Call finalizeDeposit and deposit native ETH tokens               | Nil  |
| L2Weth        | L2Bridge Contract | Burns L2 WETH token and send ETH to the bridge                   | Nil  |

### 6. Learnings

1. In Governance.sol, the operation status must be checked before and after execute, otherwise the check can be bypassed. There was a similar issue with the TimelockController contract. Before the fix, the check was only done after the execute function, which allows the executor to hijack the contract and set the delay to zero and set themselves as the proposer

- https://medium.com/immunefi/openzeppelin-bug-fix-postmortem-66d8c89ed166
- https://github.com/OpenZeppelin/openzeppelin-contracts/commit/cec4f2ef57495d8b1742d62846da212515d99dd5#diff-8229f9027848871a1706845a5a84fa3e6591445cfac6e16cfb7d652e91e8d395R307

2. In Governance.sol, there is an onlySelf modifier, which means that only the Governance contract itself can call. I find it rather interesting and had to understand how the contract can call its own function. Turns out, the function is called through an operation which calls `execute()` and subsequently calls the contract itself, so the msg.sender calling the contract is the Governance contract itself.

```
File: Governance.sol
58:     modifier onlySelf() {
59:         require(msg.sender == address(this), "Only governance contract itself allowed to call this function");
60:         _;
61:     }
```

3. I learned about the beacon proxy creation when transferring ERC20 tokens from L1-L2. A new proxy contract is created for any amount of token bridged.

```
File: L2ERC20Bridge.sol
90:     /// @dev Deploy and initialize the L2 token for the L1 counterpart
91:     function _deployL2Token(address _l1Token, bytes calldata _data) internal returns (address) {
92:         bytes32 salt = _getCreate2Salt(_l1Token);
93:
94:         BeaconProxy l2Token = _deployBeaconProxy(salt);
95:         L2StandardERC20(address(l2Token)).bridgeInitialize(_l1Token, _data);
96:
97:         return address(l2Token);
98:     }
```

### Time spent:
60 hours