# QA Report

| ID            | Title                                                                                                           | Severity |
| ------------- | --------------------------------------------------------------------------------------------------------------- | -------- |
| [L-01](#l-01) | `L1ERC20Bridge._verifyDepositLimit` doesn't handle user deposits correctly if the token `depositCap` is changed | Low      |
| [L-02](#l-02) | `L2StandardERC20.bridgeInitialize` doesn't check the result of try-catch block before token initialization      | Low      |
| [L-03](#l-03) | `Mailbox._verifyDepositLimit` adds transaction fees as the deposited amount of ethers                           | Low      |
| [L-04](#l-04) | Unremoved critical test function in `SystemContext`                                                             | Low      |

## [L-01] `L1ERC20Bridge._verifyDepositLimit` doesn't handle user deposits correctly if the token `depositCap` is changed <a id="l-01"></a>

## Details

Let's see the following scenario that illustrates the issue:

1. first the user deposits to bridge his ERC20 tokens from L1 to L2 and the value of `totalDepositedAmountPerUser[_l1Token][_depositor]` will be updated.
2. Then the owner of the `AllowList` **removed** the deposit cap of that token.
3. The user wants to claim his L2-L1 failed deposit; but this value will not be deducted from his `totalDepositedAmountPerUser`;
4. Then the admin adds a deposit cap for this token; but the `totalDepositedAmountPerUser[_l1Token][_depositor]` will not reflect the actual deposited amount of the user as the failed deposit **hasn't been deducted from his total amounts**.

So this will result in limiting the user's deposit limit as his failed deposit hasn't been deducted when he claimed it when the token didn't have a `depositCap`.

## Proof of Concept

[L1ERC20Bridge.\_verifyDepositLimit function](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L340-L350)

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

## Recommended Mitigation Steps

Modify `L1ERC20Bridge._verifyDepositLimit` function to deduct the failed deposited amount:

```diff
   function _verifyDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal {
       IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token);
       if (!limitData.depositLimitation) return; // no deposit limitation is placed for this token

       if (_claiming) {
-           totalDepositedAmountPerUser[_l1Token][_depositor] -= _amount;
+           totalDepositedAmountPerUser[_l1Token][_depositor] = totalDepositedAmountPerUser[_l1Token]       [_depositor] > 0 ? totalDepositedAmountPerUser[_l1Token][_depositor] - _amount : 0;
       } else {
           require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
           totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
       }
   }
```

## [L-02] `L2StandardERC20.bridgeInitialize` doesn't check the result of try-catch block before token initialization <a id="l-02"></a>

## Details

No check if the try-catch block has succeded to get token `decodedName` & token `decodedSymbol` before assigning it upon token initialization.

## Proof of Concept

[L2StandardERC20.bridgeInitialize function/ L71-L87](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L71-L87)

```solidity
 try this.decodeString(nameBytes) returns (string memory nameString) {
            decodedName = nameString;
        } catch {
            getters.ignoreName = true;
        }

        try this.decodeString(symbolBytes) returns (string memory symbolString) {
            decodedSymbol = symbolString;
        } catch {
            getters.ignoreSymbol = true;
        }

        // Set decoded values for name and symbol.
        __ERC20_init_unchained(decodedName, decodedSymbol);

        // Set the name for EIP-712 signature.
        __ERC20Permit_init(decodedName);
```

## Recommended Mitigation Steps

Check the `getters.ignoreName` and `getters.ignoreSymbol` before initializing the ERC20 token.

## [L-03] `Mailbox._verifyDepositLimit` adds transaction fees as the deposited amount of ethers <a id="l-03"></a>

## Details

- `Mailbox._verifyDepositLimit` function is implemented in the `requestL2Transaction` function to check that the user bridged (deposited) L1-L2 Ethers (which represents WEth value) is within the allowble limit set for a user.

- But it was noticed that the value sent to be checked by this function includes the transaction fees as well; knowing that these fees are going to be paid for the node operator and are not considered as a deposit for the user:

  ```solidity
  _verifyDepositLimit(msg.sender, msg.value);
  ```

- When verifying the deposited amount feature is enabled in the future : this will result in wrongly adds the base fees to the `totalDepositedAmountPerUser`;

## Proof of Concept

[Mailbox.requestL2Transaction function/L261](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L261)

```solidity
_verifyDepositLimit(msg.sender, msg.value);
```

[Mailbox.\_verifyDepositLimit function](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L275-L281)

```solidity
    function _verifyDepositLimit(address _depositor, uint256 _amount) internal {
        IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH
        if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH

        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
        s.totalDepositedAmountPerUser[_depositor] += _amount;
    }
```

## Recommended Mitigation Steps

Increment the `totalDepositedAmountPerUser` by the actual deposited amount excluding the transaction fees.

## [L-04] Unremoved critical test function in `SystemContext` <a id="l-04"></a>

## Details

In `SystemContext` contract; the `unsafeOverrideBatch` function is a testing method that manually sets the current blocks' number and timestamp and it is supposed to be used only for testing, and it was clearly stated by the comment that this function should never be used in production.

## Proof of Concept

[SystemContext.unsafeOverrideBatch function](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L441-L452)

```solidity
    /// @notice A testing method that manually sets the current blocks' number and timestamp.
    /// @dev Should be used only for testing / ethCalls and should never be used in production.
    function unsafeOverrideBatch(
        uint256 _newTimestamp,
        uint256 _number,
        uint256 _baseFee
    ) external onlyCallFromBootloader {
        BlockInfo memory newBlockInfo = BlockInfo({number: uint128(_number), timestamp: uint128(_newTimestamp)});
        currentBatchInfo = newBlockInfo;

        baseFee = _baseFee;
    }
```

## Recommended Mitigation Steps

Remove `SystemContext.unsafeOverrideBatch` test function to prevent accidential usage of it.
