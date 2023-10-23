# Smart contract and Yul QA

| # | Issues                                                                       |
|---|------------------------------------------------------------------------------|
| 1 | Should remove all debug statement in bootloader                              |
| 2 | Upgrade transaction via mailbox may missing gas amount validation            |
| 3 | Governance contract cannot handle ERC721 and ERC1155 NFT token transfer      |
| 4 | Governance execute function missing reentrancy protection                    |
| 5 | execute and executeInstant missing payable                                   |
| 6 | owner of the governance contract can grief security council instant execution|
| 7 | Governance contract cannot batch transaction                                 |
| 8 | ISystemContract should only contains interface                               |
| 9 | The Diamond Getter contract should expose a few crucial state                |
|10 | Rebase token such stETH is not supported for L1ERC20Bridge.sol               |
|11 | L1ERC20Bridge.sol is not capable of handling airdrop                         |
|12 | Bridge contract can be used before the bridge contract initialization         |
|13 | staticcall does not enforce cap the gas limit in _getERC20Getters            |
|14 | Priority queue does not pop operation by priority of expiration time and layer2 tips |
|15 | set deposit limit in allowlist can be frontrun                               |
|16 | no free L2 Transaction via mailbox                                           |


# Should remove all debug statement in bootloader

bootloader contains a lot of debugLog, while it is understandable that such code is ok for debugging purpose, 

these debug statement consume additional opcode and gas and should definitely be removed before production,

otherwise, the gas that should be refunded to the refund recipient address is wasted to log debug statement instead, so these debug statement should be removed for both readability and gas saving perspective.

# Upgrade transaction via mailbox may missing gas amount validation

An upgrade transaction can start via mailbox on l2

but in the current implementation, the validation is implemented as follow

```solidity
/// @dev Used to validate upgrade transactions
/// @param _transaction The transaction to validate
function validateUpgradeTransaction(IMailbox.L2CanonicalTransaction memory _transaction) internal pure {
	// Restrict from to be within system contract range (0...2^16 - 1)
	require(_transaction.from <= type(uint16).max, "ua");
	require(_transaction.to <= type(uint160).max, "ub");
	require(_transaction.paymaster == 0, "uc");
	require(_transaction.value == 0, "ud");
	require(_transaction.reserved[0] == 0, "ue");
	require(_transaction.reserved[1] <= type(uint160).max, "uf");
	require(_transaction.reserved[2] == 0, "ug");
	require(_transaction.reserved[3] == 0, "uo");
	require(_transaction.signature.length == 0, "uh");
	require(_transaction.paymasterInput.length == 0, "ul");
	require(_transaction.reservedDynamic.length == 0, "um");
}
```

but the issue is the code does not validate if sufficient amount of gas is provided to completed the upgrade transaction

recommend adding gas validation to upgrade transaction as well

# Governance contract cannot handle ERC721 and ERC1155 NFT token transfer

In Governance contract, the contract can receive native ETH,

but cannot receive ERC721 and ERC1155 because when external contract calls safeTransfer from NFT, the smart contract needs to implement xxxx

# Governance execute function missing reentrancy protection

when a proposal is executed, the execution funciton is not guarded with nonRetrant modifier, allowing one proposal to execute multiple times, we recommend the protocol add the nonRetrant safe guard

# execute and executeInstant missing payable

when operation is executed via function execute and executeInstant, because these two function are missing payable modifier, the admin cannot attach ETH when calling this function

# owner of the governance contract can grief security council instant execution

the exact trust model is that the security council can bypass the owner priority and execute instant operation in the case of emergency

however, even when calling executeInstant proposal, the proposal must still be scheduled

so the owner, if does not want the security council to execute the proposal, can frontrun the executeInstant function by caneling a scheduled proposal

whether such grief attack would depends on whether the security council is capable of scheduling batch operation

such issue can potentially be avoid if the security council can batch schedule and executeInstant into one transaction

# Governance contract cannot batch transaction

according to the comment in the code, the governance contract 

> It is used for managing and coordinating upgrades
/// and changes in all zkSync Era governed contracts.

However, the operation cannot be batched, meaning the contract lacks the function to execute multiple steps of operation in one transaction

and the owner or security council can only execute transaction one by one if not calling executeInstant, each operation is subject to min delay

the recommendation is adding the functionality to let owner schedule batch transaction

# ISystemContract should only contains interface

the [ISystemContract](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/interfaces/ISystemContract.sol) implies that the code should only contains interface definition, while the code file contains a lot of feature and function and modifier

the recommendation is rename the file to SystemContractUtils.sol instead of calling it ISystemContract

# The Diamond Getter contract should expose a few crucial state

In the [diamond getter contract](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol) 

missing expose the state admin and pending admin, 

missing exposing the state totalDepositedAmountPerUser

missing exposing the state zkPorterIsAvailable

recommend exposing these function for external protocol query and integration

# Rebase token such stETH is not supported for L1ERC20Bridge.sol

when using L1ERC20TokenBridge.sol

the code enforce that the underlying token does not charge transfer fee, otherwise if the received amount does not match the transfer amount, transaction revert and block user [deposit](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L216)

```solidity
/// @dev Transfers tokens from the depositor address to the smart contract address
/// @return The difference between the contract balance before and after the transferring of funds
function _depositFunds(address _from, IERC20 _token, uint256 _amount) internal returns (uint256) {

	uint256 balanceBefore = _token.balanceOf(address(this));
	_token.safeTransferFrom(_from, address(this), _amount);
	uint256 balanceAfter = _token.balanceOf(address(this));

	return balanceAfter - balanceBefore;
}
```

However, the rebase token cannot be used in L1ERC20Bridge.sol

first of all, when a L1 ERC20 token is bridged to L2, and corresponding L2 token is created

but when L1 token rebases and change the balance, the L2 token that correspondings to the L1 token does not reflect the rebase

rebase can be positive or negative

in the case of positive rebase, meaning the L1 token balance inside L1ERC20Bridge.sol increases, the extra balance cannot be transfered out

in the case of negative rebase, this can block L2 token cross chain token transfer and lock fund

for example, 

1. User bridge 100 rebase token from L1 to L2 and the 100 rebase token is locked in L1ERC20Bridge.sol contract and minting 100 L2 Token

2. User burn 100 L2 token and want to get back the locked 100 L1 rebase token, but negative rebase happens, the 100 token goes to 98 token in L1ERC20Bridge.sol

3. the L1ERC20Bridge.sol does not hold the original 100 token and the balance is reduced, so the cross-chain token transfer from L2 to L1 is blocked

4. the failure for L2 - L1 token transfer if L1 token has rebasing behavior can happen when claimFailedDeposit as well

an example of such very popular rebase token is stETH, 

https://docs.lido.fi/guides/lido-tokens-integration-guide#accounting-oracle

# L1ERC20Bridge.sol is not capable of handling airdrop

the L1ERC20Bridge.sol is likely to hold a large amount of ERC20 token and if there are project that launch airdrop based on the snapshot of the token balance

the airdrop token can go to the L1ERC20Bridge.sol directly and because the contract lack sweeping function to get the fund out, the airdropped token is lost

# Bridge contract can be used before the bridge contract initalization

Can use L1ERC20Bridge.sol as an example

initially the [l2Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L46) is address(0)

only after the initializer is called and bridge transaction confirmed, [l2 bridge address is set](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L123) 

```solidity
// Deploy L2 bridge proxy contract
l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
	zkSync,
	_deployBridgeProxyFee,
	l2BridgeProxyBytecodeHash,
	l2BridgeProxyConstructorData,
	// No factory deps are needed for L2 bridge proxy, because it is already passed in previous step
	new bytes[](0)
);
```

but l2 bridge address is set on l1 bridge does not mean the corresponding l2 bridge address is deployed on l2

if the deployer provide insufficient gas, the deployment can fail

this means even when l2 bridge is set on l1, the l2 bridge is never deployed on l2

in either case (l2 bridge is address(0) or l2 bridge failed to deploy on l2)

user can still use bridge to deposit token by calling [deposit](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L198)

```solidity
l2TxHash = zkSync.requestL2Transaction{value: msg.value}(
	l2Bridge,
	0, // L2 msg.value
	l2TxCalldata,
	_l2TxGasLimit,
	_l2TxGasPerPubdataByte,
	new bytes[](0),
	refundRecipient
);
```

but even user lock token in l1 ERC20 token bridge, the deposit request is never relayed on l2 because the l2 bridge is not never deployed

then the user's l1 token is lost, until the failed deposit is proved and user can reclaim the asset via [claimFailedDeposit](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L255)

same issue happens on L1WETHBridge.sol

but user asset is lost because there is no [claimFailedDepsoit](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L214), the method always revert

# staticcall does not enforce cap the gas limit in _getERC20Getters

not all token support decimal and name and symbol and if external token does not support decimal or name or decimals and [revert in staticcall](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L241)

the external staticcall forward all gas remaining and the external revert waste 63 / 64 

```solidity
    /// @dev Receives and parses (name, symbol, decimals) from the token contract
    function _getERC20Getters(address _token) internal view returns (bytes memory data) {
        (, bytes memory data1) = _token.staticcall(abi.encodeCall(IERC20Metadata.name, ()));
        (, bytes memory data2) = _token.staticcall(abi.encodeCall(IERC20Metadata.symbol, ()));
        (, bytes memory data3) = _token.staticcall(abi.encodeCall(IERC20Metadata.decimals, ()));
        data = abi.encode(data1, data2, data3);
    }
```

a relevant example I want to quote is in balancer codebase, the reentrancy check staticall always revert and waste too much gas, can check the [comment here](https://github.com/balancer/balancer-v2-monorepo/blob/227683919a7031615c0bc7f144666cdf3883d212/pkg/pool-utils/contracts/lib/VaultReentrancyLib.sol#L43-L55)

then balancer dev enforce the staticall gas limit is [enforced here](https://github.com/balancer/balancer-v2-monorepo/blob/227683919a7031615c0bc7f144666cdf3883d212/pkg/pool-utils/contracts/lib/VaultReentrancyLib.sol#L78)

```solidity
(, bytes memory revertData) = address(vault).staticcall{ gas: 10_000 }(
	abi.encodeWithSelector(vault.manageUserBalance.selector, 0)
);
```

going back to our usage

_getERC20Getters, this function is called every time when deposit and bridge ERC20 token, if every time most of the gas (63 / 64) gas wasted in staticcall of quering name, decimal or symbol, the deposit transaction is likely revert in out of gas.

# Priority queue does not pop operation by priority of expiration time and layer2 tips

User can request [l2 transaction](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236) on l1 via mailbox

the transaction operation is written into the [priority queue](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L369) 

```solidity
s.priorityQueue.pushBack(
	PriorityOperation({
		canonicalTxHash: canonicalTxHash,
		expirationTimestamp: _priorityOpParams.expirationTimestamp,
		layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
	})
);
```

as we can see, the layer2Tip is hardcoded to 0, so the when poping from the queue, the code cannot really pick the highest layer2tips

the expiration timestamp is not considered when executing batch and popping from the queue as well

and the [PRIORITY_EXPIRATION is currently hardcode to 0](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L295), signal lack of usage of expirationTimestamp,

but according to the comments from the prioityOperation struct

>     /// @param expirationTimestamp The timestamp by which the priority operation must be processed by the operator.

but when validator executing the batch, the [pop front operation](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L264) just follow first-in-first-out out instead of picking the operation that has recent expiration time or higher layer2 tip

```solidity
// @dev Pops the priority operations from the priority queue and returns a rolling hash of operations
    function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {
        concatHash = EMPTY_STRING_KECCAK;

        for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
            PriorityOperation memory priorityOp = s.priorityQueue.popFront();
            concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
        }
    }
```

the impact is some time-sensitve operation may never to processed on time

or in the worst,

assume user request a l2 transaction whats to mint ETH

but before her, there are 100K transaction in the Queue, so the processing time for her transaction can be very long, basically the prioirity cannot schedule time-senstive operation

# set deposit limit in allowlist can be frontrun

when [setting deposit limit](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L129) in allowlist

a user can frontrun the set deposit limit

for example, if admin want to set deposit limit from 1.5 ETH to 1 ETH 

use can frontrun the transaction to consume the 1.5 ETH limit

then backrun the transcaction to consume the 1 ETH limit

the recommendation is consider implement increase / derease limit

# no free L2 Transaction via mailbox

when requestL2Transaction via mailbox, the [isFree flag](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L270) is always set to false

recommend create a seperate function that can only be called by whitelist address to send free l2 transaction, otherwise remove the flag isFree when requesting l2 transaction via mailbox


# ComplexUpgrader does not use msg.value

In ComplexUpgrader, when performing upgrade operation, the [payable keywords is used](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/system-contracts/contracts/ComplexUpgrader.sol#L21), meaning the upgrade should handle the ETH attached

but when delegatecall, the msg.value or ETH attached is not used

```solidity
require(_delegateTo.code.length > 0, "Delegatee is an EOA");
(bool success, bytes memory returnData) = _delegateTo.delegatecall(_calldata);
```


# ZkCircuit QA

# Should resolve the warning in rust compiler

the following warning message shows up when running the code under zk_circuit path

```solidity
carge build
```

```solidity
warning: the feature `generic_const_exprs` is incomplete and may not be safe to use and/or cause compiler crashes
 --> src/lib.rs:5:12
  |
5 | #![feature(generic_const_exprs)]
  |            ^^^^^^^^^^^^^^^^^^^
  |
  = note: see issue #76560 <https://github.com/rust-lang/rust/issues/76560> for more information
  = note: `#[warn(incomplete_features)]` on by default

warning: `zkevm_circuits` (lib) generated 1 warning
    Finished dev [unoptimized + debuginfo] target(s) in 26.20s
```

recommend resolve the warning to avoid potential issue

