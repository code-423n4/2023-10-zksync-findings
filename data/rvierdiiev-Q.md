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
> In case the account does not exist or is empty (as defined by EIP-161) 0 is pushed to the stack.
In case the account does not have code the keccak256 hash of empty data (i.e. c5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470) is pushed to the stack.

So in case if account has non 0 balance or it's nonce is bigger than 0, then keccak256 hash of empty data should be returned. But zksync [doesn't check balance](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L89-L112) of account. It [only checks for nonce](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L102-L104). As result in case if someone will transfer funds to address, or eoa will mint value from l1 tx to zksync, then this address will be considered not initialized.

As result, `getCodeHash` works differently on ethereum and zksync. As i haven't found this is somehow used by real contracts, i put this as qa, however it's possible to craft contract that will depend on empty keccak256 in order to check if account is initialized.
## Recommendation
I believe that it should work in same way as described in eip1052.