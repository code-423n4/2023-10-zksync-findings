# L-01: L1WethBridge.initialize can be front-runned

[initialize](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L81-L87) can be called by anyone but only once. Malicious user can call this function via front-run and provide malicious inputs. The `_governor` argument can change L2 WETH token implementation and upgrade the bridge on L2. So it can't be a bad actor.

## Recommended Mitigation Steps

Add access control for it.

# L-02: L1ERC20Bridge does not support rebasing token

When bridging ERC20 via L1ERC20Bridge, the protocol adopts a method of locking-on-L1 and mint-on-L2. L1Token is a token on L1, it is not created by L1ERC20Bridge, while the corresponding L2Token is created by L2ERC20Bridge and standard ERC20.

If L1Token is a rebasing token, then the amount of token locked in L1ERC20Bridge will not be equal to totalSupply on L2. When rebasing occurs, if all tokens on L2 are bridged to L1, this may lead to two situations:

1.  Insufficient token in L1ERC20Bridge resulted in part of the withdrawals from L2 being unable to be finalized.
2.  All withdrawals from L2 have been finalized, then L1ERC20Bridge still has excess token stuck in the contract.

## Recommended Mitigation Steps

&nbsp;

# L-03: keccakGasCost may lead to incorrect result

```solidity
File: code\system-contracts\contracts\L1Messenger.sol
58:     function keccakGasCost(uint256 _length) internal pure returns (uint256) {
59:         return KECCAK_ROUND_GAS_COST * (_length / KECCAK_ROUND_NUMBER_OF_BYTES + 1);
60:     }
```

If _`length = 0`, the returned result is `KECCAK_ROUND_GAS_COST` while the correct result should be 0.  
If `_length = KECCAK_ROUND_NUMBER_OF_BYTES`, the returned result is `KECCAK_ROUND_GAS_COST * 2` while the correct result should be `KECCAK_ROUND_GAS_COST`.

This means that when `_length` is divisible by `KECCAK_ROUND_NUMBER_OF_BYTES`, the returned result will have one more `KECCAK_ROUND_GAS_COST`.

## Recommended Mitigation Steps

```fix
function keccakGasCost(uint256 _length) internal pure returns (uint256) {
        if (_length == 0) return 0;
        return KECCAK_ROUND_GAS_COST * ((_length - 1) / KECCAK_ROUND_NUMBER_OF_BYTES + 1);
    }
```

# L-04: _verifyDepositLimit may cause DOS

```solidity
File: code\contracts\ethereum\contracts\zksync\facets\Mailbox.sol
275:     function _verifyDepositLimit(address _depositor, uint256 _amount) internal {
276:         IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH
277:         if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH
278: 
279:->       require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
280:         s.totalDepositedAmountPerUser[_depositor] += _amount;
281:     }
```

The root cause is that `s.totalDepositedAmountPerUser[_depositor]` is only increased and gets bigger over time. If `s.totalDepositedAmountPerUser[_depositor]` exceeds `limitData.depositCap`, then `_depositor` will not be able to continue calling `requestL2Transaction` due to [this check](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L261). limitData.depositCap may not be reached quickly for an EOA, but for a contract the limit may be broken easily. Image that an contract may come from large project with many users.

This problem also exists in [L1ERC20Bridge.deposit](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L188).

## Recommended Mitigation Steps

It is recommended to remove such restrictions.

# L-05: There is a problem checking numberOfL2ToL1Logs in publishPubdataAndClearState

`publishPubdataAndClearState` is [the last function called in the bootloader](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L3822). 

```solidity
File: code\system-contracts\contracts\L1Messenger.sol
199:     function publishPubdataAndClearState(
200:         bytes calldata _totalL2ToL1PubdataAndStateDiffs
201:     ) external onlyCallFromBootloader {
202:         uint256 calldataPtr = 0;
203: 
204:         /// Check logs
205:         uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
206:->       require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
207:         calldataPtr += 4;
```

L206, `numberOfL2ToL1Logs <= numberOfL2ToL1Logs` means always true, this check has no effect at all.

Because there is a limit on the maximum number of logs in a batch on L1. This will lead to an potential problem: **if the operator is malicious, it may arrange for more than the maximum number of tx to be executed by the bootloader**. Since the L206 check cannot prevent such behavior, `Executor.commitBatches` on L1 will be not executed successfully.

## Recommended Mitigation Steps

Modify `numberOfL2ToL1Logs <= numberOfL2ToL1Logs` of L206 to `numberOfL2ToL1Logs <= SomeMaxLogsCount`.

&nbsp;