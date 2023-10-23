## **L-01: Inaccurate Deposit Cap Updates on Withdrawals**

The deposit cap is not being updated accurately during withdrawals. This discrepancy is observed in two distinct scenarios:

#### 1. **ERC20 Bridge**:
   - The function `L1ERC20Bridges.sol::_verifyDepositLimit` is invoked during `deposit` and `claimFailedDeposit`, but it's omitted during `finalizeWithdrawal`. This oversight leads to an incorrect computation of the deposit cap.
    - Additionally, when Mailbox.sol::requestL2Transaction is invoked by the ERC20Bridge, the deposit cap is incremented by msg.value, which in this context represents the gas fee, not the actual deposit amount. This is an incorrect representation.

#### 2. **WETH Bridge**:
   - The deposit cap is incremented during `deposit`(Mailbox::requestL2Transaction) but isn't adjusted during withdrawals(Mailbox::finalizeEthWithdrawal), leading to another miscalculation.

### **Recommendations:**
For the ERC20Bridge:
- Implement the existing function to subtract the withdrawal value.
- Invoke `L1ERC20Bridges.sol::_verifyDepositLimit` with `_claiming` set to `true`.

For ETHBridge:
- Add the calculation on [withdraw function](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L191)

### **Notes from Warden:**
While i'm aware that this function is [temporary](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/L1%20smart%20contracts.md#deposit-limitation), it's worth to mention that the ERC20Bridge contract updates the cap during `claimFailedDeposit`. Therefore, it's logical to also update it during the withdrawal function, or the call during `claimFailedDeposit` might be redundant/useless.

---

## **L-02: Potential Double Application of Alias on `refundRecipient`**

In the current implementation, there's a THEORETICAL possibility for the alias to be applied twice to the L1 address. This could result in funds not being returned to the intended wallets.

In the `Mailbox::requestL2Transaction` method:
- If the sender is a contract, the function applies an alias to prevent potential cross-chain collisions. [See code here](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L249).

- When this function invokes [_requestL2Transaction](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L283), it checks if `refundRecipient` is set to zero.
- If it is, the function sets the sender as the `refundRecipient` and then checks again if it's a contract, potentially applying the alias for a second time.

While in practical scenarios, such a collision is highly unlikely, it's essential to recognize that the code has the potential to apply the alias twice to an address. This could lead to losing control over the original `refundRecipient`.

## **NC-01: Redundant check NonceHolder::incrementDeploymentNonce**
In the `incrementDeploymentNonce` function, there is a redundant check for msg.sender against the DEPLOYER_SYSTEM_CONTRACT address. The function uses the onlySystemCall modifier, which likely already ensures that the caller is authorized. However, immediately inside the function, there's an explicit require statement that checks msg.sender against DEPLOYER_SYSTEM_CONTRACT again.

Recommendation:
Remove onlySystemCall modifier or the DEPLOYER_SYSTEM_CONTRACT requirement.

---

## **NC-02 - Incorrect Comment Terminology**
The comments references "ergs" instead of the correct term "gas" as referenced to this [link](https://era.zksync.io/docs/reference/troubleshooting/changelog.html#system-update-v1-3-feb-8th-2023)

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1179

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1231

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1233

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1263

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1596

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L159

---

## **NC-03 - Unchecked Return Value**
In the `fallback` function, the return value of the [`SystemContractHelper::setValueForNextFarCall`](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/MsgValueSimulator.sol#L55) method is not checked.
```solidity
SystemContractHelper.setValueForNextFarCall(Utils.safeCastToU128(value));
```

---

## **NC-04 - Shadowed function**

In the [Utils:hashL2Bytecode](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L82), the variable bytecodeLenInWords shadows the function with the same [name](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L44)
```solidity
    /// @return codeLength The bytecode length in bytes
    function bytecodeLenInBytes(bytes32 _bytecodeHash) internal pure returns (uint256 codeLength) {
        codeLength = bytecodeLenInWords(_bytecodeHash) << 5; // _bytecodeHash * 32
    }
```
```
    function hashL2Bytecode(bytes calldata _bytecode) internal view returns (bytes32 hashedBytecode) {
        // Note that the length of the bytecode must be provided in 32-byte words.
        require(_bytecode.length % 32 == 0, "po");

        uint256 bytecodeLenInWords = _bytecode.length / 32;
```