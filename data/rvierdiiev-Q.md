## QA-01. L1ERC20Bridge._verifyDepositLimit doesn't track bridged/failed amount when limitation is off for token.
## Description
When user bridges erc20 token through the `L1ERC20Bridge`, then [`_verifyDepositLimit` function is called](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L188), which should check if user doesn't restrict limitations.
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L340-L350
```solidity
    function _verifyDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal {
        IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token);
        if (!limitData.depositLimitation) return; // no deposit limitation is placed for this token


        if (_claiming) {
            totalDepositedAmountPerUser[_l1Token][_depositor] -= _amount;
        } else {
            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
        }
    }
```
As you can see, `depositLimitation` can be switched on and off for the token.
In case if it's on, then when user bridges, his `totalDepositedAmountPerUser` increases and in case if deposit has failed, then it's decreased.

As you can see, [limit can be set many times](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L129-L132). So it's possible that limit will be working, then will be switched off and then will be switched on again.

The problem is that when limitation is switched off, then `totalDepositedAmountPerUser` is not changed anymore and nothing is tracked. So when it will be switched on again, then user may already use all his limit, but `totalDepositedAmountPerUser` will not contain that information.
## Recommendation
Increase and decrease `totalDepositedAmountPerUser` even if limitation is switched off. So when it will be switched on again, `totalDepositedAmountPerUser` variable will be up to date for user.

## QA-02. Executor._verifyBatchTimestamp allows timestamp of last l2 block to be in future
## Description
`Executor._verifyBatchTimestamp` function [is used](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L52) to check that batch and last l2 block timestamps are both valid.

Currently, function allows l2 last block to be in future.
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L94
`require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2");`

This should not happen as l2 block should never have timestamp that is bigger as it creates time games, so l2 time will be bigger than l1. 
## Recommendation
I think restriction should be like that or even without `=` sign.
`require(lastL2BlockTimestamp <= block.timestamp, "h2");`

## QA-03. AccountCodeStorage.getCodeHash will return 0 for initialized account
## Description
AccountCodeStorage.getCodeHash function is used to return code hash of the address. 
According to [eip-1052](https://eips.ethereum.org/EIPS/eip-1052#specification):
> In case ount does not have code the keccak256 hash of empty data (i.e. c5d2460186f7233cthe account does not exist or is empty (as defined by EIP-161) 0 is pushed to the stack.
In case the acc927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470) is pushed to the stack.

So in case if account has non 0 balance or it's nonce is bigger than 0, then keccak256 hash of empty data should be returned. But zksync [doesn't check balance](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L89-L112) of account. It [only checks for nonce](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L102-L104). As result in case if someone will transfer funds to address, or eoa will mint value from l1 tx to zksync, then this address will be considered not initialized.

As result, `getCodeHash` works differently on ethereum and zksync. As i haven't found this is somehow used by real contracts, i put this as qa, however it's possible to craft contract that will depend on empty keccak256 in order to check if account is initialized.
## Recommendation
I believe that it should work in same way as described in eip1052.

## QA-04. L1Messenger.publishPubdataAndClearState checks that `numberOfL2ToL1Logs <= numberOfL2ToL1Logs`
## Description
`L1Messenger.publishPubdataAndClearState` function [receives `numberOfL2ToL1Logs`](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L205) and then it should check that provided amount is not big enough.

Because `l2ToL1LogsTreeArray` has [L2_TO_L1_LOGS_MERKLE_TREE_LEAVES size](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L209), that means that there can be no more than L2_TO_L1_LOGS_MERKLE_TREE_LEAVES leaves to construct tree root.

So `publishPubdataAndClearState` should change that `numberOfL2ToL1Logs <= L2_TO_L1_LOGS_MERKLE_TREE_LEAVES`, but currently it do check in another way: `numberOfL2ToL1Logs <= numberOfL2ToL1Logs`, which is incorrect.

As i can see, there is no real impact here, as even if operator will provide more logs than `L2_TO_L1_LOGS_MERKLE_TREE_LEAVES` then whole batch will just revert.
## Recommendation
Use this check: `numberOfL2ToL1Logs <= L2_TO_L1_LOGS_MERKLE_TREE_LEAVES`

## QA-05. Inconsistency in different l1->l2 messages
## Description
When user requests l1->l2 message, then he can call ContractDeployer in order to create new contract. Or he can do request without new contract creation.

When tx will be executed by bootloader on l2, then in case if it is not creating new contract, then user's nonce will not be changed anyhow as there is no any communication with user's account. But when new contract will be created, then [user's nonce will be increased](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L168).

As result in case if user didn't do any tx on l2 yet, then [his account is considered not active](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L102C33-L102C86). Then if he triggers usual tx from l1, then his account is still not active. But if he deploys contract, then his account becomes active, so hashcode and code size is shown for it.
## Recommendation
I am not sure if this is needed to be fixed, just wanted to show differences.
