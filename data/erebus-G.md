# [G-01] `_bytecodeLen` can be put in an unchecked block

As seen in [Utils, function bytecodeLenInWords](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/Utils.sol#L44C1-L48C6):

```diff
    function _bytecodeLen(bytes32 _bytecodeHash) private pure returns (uint256 codeLengthInWords) {
+       unchecked {
            codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
+       }
    }
```

(without taking into account my QA submission `L-15`)

# [G-02] Code is unreachable

In [Mailbox, function _verifyDepositLimit](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L275C1-L281C6), the code after the `require` statement is never executed as `address(0)` is hard-coded and ETH deposits are uncapped (see the comment):

```solidity
    function _verifyDepositLimit(address _depositor, uint256 _amount) internal {
        IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH
        if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH

        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
        s.totalDepositedAmountPerUser[_depositor] += _amount;
    }
```

Consider removing the whole function as it "will" (see my QA `L-02`) return without changing the state of the contract. That way you save gas on both execution and deployment, as the bytecode size is smaller.