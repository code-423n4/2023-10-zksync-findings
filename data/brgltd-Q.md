| ID    | Title |
| -------- | ------- |
| 01 | L2StandardERC20 missing in `_factoryDeps` when calling `L1ERC20Bridge.initialize()`
| 02 | Should check that new facet is different than old facet when replacing a facet
| 03 | Verification is not skippet if `serializedProof.length` is zero
| 04 | `Governance.sol` should emit an event when salt is not zero
| 05 | Accepted proposals could be inexecutable
| 06 | `Governance.sol` could allow batch schedules 
| 07 | `Governance.sol` is not protected against returnbomb 
| 08 | `depositAmount` should be updated when withdrawing in `L1ERC20Bridge`
| 09 | TransactionValidator won't revert even if `_totalGasLimit` equals `overhead`
| 10 | Prevent receiving facets with empty selectors
| 11 | Prevent receiving duplicated selectors when replacing selectors
| 12 | Prevent accepting an operation where the predecessor has a bigger timestamp
| 13 | Potential overflow/underflow
| 14 | No need for else
| 15 | Unnecessary casting
| 16 | Math.max not needed
| 17 | require + uncheck not needed

## [01] L2StandardERC20 missing in `_factoryDeps` when calling `L1ERC20Bridge.initialize()`

### Impact

Code for `L2ERC20Bridge` will not be marked as known and bridge initialization may fail.

### Proof of Concept

We can see from the OpenZeppelin audit that one finding was to include `L2StandardERC20` and `UpgradeableBeacon` into the `_factoryDeps`.

https://blog.openzeppelin.com/zksync-fee-model-and-token-bridge-audit#missing-factory-dependencies

Presumably at that time the `_factoryDeps` include only the L2 bridge implementation and the Token proxy - note branch/commits from that code are not available.

`UpgradeableBeacon`, which is the L2 proxy, was added to the latest codebase. However, `L2StandardERC20` is still missing.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L73-L75

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L93

We can see in `L2ERC20Bridge`, that `L2StandardERC20` - the L2 token implementation, will also be deployed and its address will be used for deploying `UpgradeableBeacon`.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L52-L53

### Recommendation

Consider adding `L2ERC20Bridge` into the `_factoryDeps` for `L1ERC20Bridge.initialize()`.

## [02] Should check that new facet is different than old facet when replacing a facet

To be compliant with [EIP-2335](https://eips.ethereum.org/EIPS/eip-2535#addingreplacingremoving-functions), the code check that the new facet address is different than the old facet address when replacing facets.

As described in the EIP:

"If the action is Replace, update the function selector mapping for each functionSelectors item to the facetAddress. If any of the functionSelectors had a value equal to facetAddress or the selector was unset, revert instead."

## [03] Verification is not skippet if `serializedProof.length` is zero

The comments in `Executor` mention that verification is skipped if the proof is empty. However `s.verifier.verify()` runs for empty proofs as well.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L350-L369

## Impact

Verification is not skipped when intended and transactions could fail unexpectedly.

## [04] `Governance.sol` should emit an event when salt is not zero

I could be beneficial to emit an event when salt is not zero, to make the value retrievable. 

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L129-L133

This was an open issue on OpenZeppelin previous versions and it has been added on V5.

https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3216

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/149e1b79fecb9db32f732c8c9f05ac2d8fa97471/contracts/governance/TimelockController.sol#L266-L280

## [05] Accepted proposals could be inexecutable

If one proposal is accepted but the target calls consume too much gas, the proposal won't be able to be executed. Therefore one potential improvement could be to allow partial execution, e.g. allow executing proposals in chunks for some specific cases.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L224-L234

## [06] `Governance.sol` could allow batch schedules 

It could be beneficial to implement batch schedules like [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/149e1b79fecb9db32f732c8c9f05ac2d8fa97471/contracts/governance/TimelockController.sol#L291-L311).

## [07] `Governance.sol` is not protected against returnbomb 

Another improvement that could be made on `Governance.sol` could be to protect against [returnbomb](https://github.com/nomad-xyz/ExcessivelySafeCall).

## [08] `depositAmount` should be updated when withdrawing in `L1ERC20Bridge`

`depositAmount` gets set on deposits but currently it doesn't get updated when withdrawing.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L209

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L293-L320

## [09] TransactionValidator won't revert even if `_totalGasLimit` equals `overhead`

It could be beneficial to ensure there's some buffer between then input `_totalGasLimit` and the calculated overhead. If these values are very close, `TransactionValidator` won't revert but the transaction might fail due to small margins when/if the overhead is large and `l2GasForTxBody` is close to zero.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L23-L27

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L110-L122

## [10] Prevent receiving facets with empty selectors

If the admin tries to add a facet with empty selectors and if the facet is new, the facet will be saved with empty the empty selectors arrays. One potential improvement could be to prevent receiving empty selectors.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L135

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L190-L199

## [11] Prevent receiving duplicated selectors when replacing selectors

Currently, it's possible to receive duplicated selectors when replacing selectors and the last one will the one to persistent. One improvement that could be made is to prevent duplicated selectors.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L149-L169

## [12] Prevent accepting an operation where the predecessor has a bigger timestamp

Consider adding a revert when the predecessor has a bigger timestamp than the operation being add in `Governance.sol`.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L215-L220

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/IGovernance.sol#L32-L36

## [13] Potential overflow/underflow

Since the addition/subtraction is made inside an unchecked block, the value could silently overflow/underflow.

Doesn't seem to be a issue in practice since this code is used on many L2s. Still, most automated tools will flag as a bug, to it could be beneficial to check for overflow/underflow or use a SafeCast.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/vendor/AddressAliasHelper.sol#L30

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/vendor/AddressAliasHelper.sol#L40

## [14] No need for else

Since it's returning the value, the else/else if statements are not needed. 

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L105-L116

## [15] Unnecessary casting

The values used on the following castings are already uint256.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L341-L342

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L340

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L344

## [16] Math.max not needed

The following Math.max is not needed since batchOverheadForTransaction still has the initial value of zero.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L142

## [17] require + uncheck not needed

Perhaps require + uncheck was added as a double check, but using require + uncheck would be same as subtracting directly since if it underflows it will revert.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L117-L121
