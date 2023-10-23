# Disputed

Talking with Vlad, these are the ones we did not reach a consensus about their possible impact:

## [D-01] Using `msg.timestamp` as an expiration timestamp invites MEV manipulations

### Impact

Setting the expiration timestamp to a value that depends directly of `block.timestamp` is useless, as `block.timestamp` is the timestamp when the transaction is **EXECUTED**, not when it is submitted to the network, opening the door to MEV manipulations. Think of the typical Uniswap-based bug in which expiration timestamp is set to `block.timestamp + K`, but applied to inter-chain swaps or highly-sensitive time-dependant operations (e. g. arbitrage or inter-chain DEXes like [Interport](https://interport.medium.com/interport-finance-whats-special-a5ea7caef237)).

### Proof of concept

Thanks to Proof of Stake, validators and, in fact, everyone in the Ethereum network knows who is gonna submit the next block with a margin of $6$ minutes and $24$ seconds to $12$ minutes and $48$ seconds (see the [References](#references) section). That's enough time for bots and validators to do MEV calculations for many of the transactions waiting in the mempool **AND** submit their own with the right amount of gas to make sure their transactions are executed within the next block. Because of this, highly-sensitive operations like swaps between tokens need 1) slippage control and 2) expirarion timestamps so that users will **NOT** incur in extreme losses due to MEV bots sandwiching/front-running them or validators *"storing"* transactions until they can make a profitable *"chain"*.

This idea can be extended to inter-chain operations pretty easily, so that the requested transactions from one layer to another do not wait idly in the mempool of the first one. However, doing request from Ethereum to zkSync Era through `Mailbox`, the expiration timestamp is set to

[**Mailbox, line 295**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L295)

```solidity
        uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast
```

with

[**Config, line 46**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Config.sol#L46)

```solidity
uint256 constant PRIORITY_EXPIRATION = 0 days;
```

so it equals `block.timestamp`, which is bad as its value will **NOT** be the time the user submitted the transaction to the network but the time the transaction is picked by a validator and included in a block. That means, virtually, there is no expiration timestamp for such a transaction.

Some ways to exploit this, from a hacker's POV, is via rogue validators (see [here](https://news.bitcoin.com/rogue-validator-exploits-mev-bots-on-ethereum-resulting-in-25-3m-in-crypto-losses)) or *"chained transaction-blocks"*. The idea is that, thanks to POS, the odds for a validator to execute sequentially $n > 2$ blocks increases with the amount of ETH staked so it is possible to delay a highly profitable transaction and place it between two validator-controlled blocks and sandwich it on zkSync Era (as they are executed sequentially via [a priority queue](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L369)). I'm not gonna deepen more in that, you can go to the [References](#references) section if you wanna know the details. This issue is real and feasible, although pretty expensive, but the damages to users may be huge if they are doing, say, an inter-chain [Furocombo](https://furucombo.app/combo) combo worth 3 million dollars or any other type of highly-sensitive inter-chain operation.

### Recommended Mitigation steps

Pass the expiration timestamp as a function argument retrieved from the front-end and revert if it is less than the `block.timestamp` in which the transaction is executed:

[**Mailbox, function requestL2Transaction**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236C1-L273C6)

```diff
    function requestL2Transaction(
        address _contractL2,
        uint256 _l2Value,
        bytes calldata _calldata,
        uint256 _l2GasLimit,
        uint256 _l2GasPerPubdataByteLimit,
        bytes[] calldata _factoryDeps,
-       address _refundRecipient
+       address _refundRecipient,
+       uint64 _expirationTimestamp
    ) external payable nonReentrant senderCanCallFunction(s.allowList) returns (bytes32 canonicalTxHash) {
+       require(_expirationTimestamp > block.timestamp, "MEV trying to do their things");
        // Change the sender address if it is a smart contract to prevent address collision between L1 and L2.
        // Please note, currently zkSync address derivation is different from Ethereum one, but it may be changed in the future.
        address sender = msg.sender;
        if (sender != tx.origin) {
            sender = AddressAliasHelper.applyL1ToL2Alias(msg.sender);
        }

        // Enforcing that `_l2GasPerPubdataByteLimit` equals to a certain constant number. This is needed
        // to ensure that users do not get used to using "exotic" numbers for _l2GasPerPubdataByteLimit, e.g. 1-2, etc.
        // VERY IMPORTANT: nobody should rely on this constant to be fixed and every contract should give their users the ability to provide the
        // ability to provide `_l2GasPerPubdataByteLimit` for each independent transaction.
        // CHANGING THIS CONSTANT SHOULD BE A CLIENT-SIDE CHANGE.
        require(_l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp");

        // The L1 -> L2 transaction may be failed and funds will be sent to the `_refundRecipient`,
        // so we use `msg.value` instead of `_l2Value` as the bridged amount.
        _verifyDepositLimit(msg.sender, msg.value);
        canonicalTxHash = _requestL2Transaction(
            sender,
            _contractL2,
            _l2Value,
            _calldata,
            _l2GasLimit,
            _l2GasPerPubdataByteLimit,
            _factoryDeps,
            false,
-           _refundRecipient
+           _refundRecipient,
+           _expirationTimestamp
        );
    }
```

[**Mailbox, function _requestL2Transaction**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L283C1-L327C6)

```diff
    function _requestL2Transaction(
        address _sender,
        address _contractAddressL2,
        uint256 _l2Value,
        bytes calldata _calldata,
        uint256 _l2GasLimit,
        uint256 _l2GasPerPubdataByteLimit,
        bytes[] calldata _factoryDeps,
        bool _isFree,
-       address _refundRecipient
+       address _refundRecipient,
+       uint64 _expirationTimestamp
    ) internal returns (bytes32 canonicalTxHash) {
        require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "uj");
-       uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast
        uint256 txId = s.priorityQueue.getTotalPriorityTxs();

        // Here we manually assign fields for the struct to prevent "stack too deep" error
        WritePriorityOpParams memory params;

        // Checking that the user provided enough ether to pay for the transaction.
        // Using a new scope to prevent "stack too deep" error
        {
            params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);
            uint256 baseCost = params.l2GasPrice * _l2GasLimit;
            require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost
        }

        // If the `_refundRecipient` is not provided, we use the `_sender` as the recipient.
        address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient;
        // If the `_refundRecipient` is a smart contract, we apply the L1 to L2 alias to prevent foot guns.
        if (refundRecipient.code.length > 0) {
            refundRecipient = AddressAliasHelper.applyL1ToL2Alias(refundRecipient);
        }

        params.sender = _sender;
        params.txId = txId;
        params.l2Value = _l2Value;
        params.contractAddressL2 = _contractAddressL2;
-       params.expirationTimestamp = expirationTimestamp;
+       params.expirationTimestamp = _expirationTimestamp;
        params.l2GasLimit = _l2GasLimit;
        params.l2GasPricePerPubdata = _l2GasPerPubdataByteLimit;
        params.valueToMint = msg.value;
        params.refundRecipient = refundRecipient;

        canonicalTxHash = _writePriorityOp(params, _calldata, _factoryDeps);
    }
```

On top of that, change the `deposit` functions in both bridges to match the ABI of the new `requestL2Transaction` and remove the `PRIORITY_EXPIRATION` constant in [Config, line 46](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Config.sol#L46).

### References

- [https://blog.uniswap.org/uniswap-v3-oracles?utm_source=blog.bytes032.xyz&utm_medium=referral&utm_campaign=why-you-should-stop-using-block-timestamp-as-deadline-in-swaps#how-much-does-a-two-block-20-manipulation-require](https://blog.uniswap.org/uniswap-v3-oracles?utm_source=blog.bytes032.xyz&utm_medium=referral&utm_campaign=why-you-should-stop-using-block-timestamp-as-deadline-in-swaps#how-much-does-a-two-block-20-manipulation-require)

## [D-02] Using `debugLog` in ∆gas calculations leads to an artificially increased value

### Impact

In [bootloader, function debugLog](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L3277C1-L3281C14) does consume `gas()`, so using it inside **$\Delta gas$** calculations leads to an artificially little increase above the real gas usage. This is bad because the amount users will be refunded depends on this *"difference blocks"* and they must **NOT** pay for debugging functionalities. You can argue that it is the same as Ethereum's `event`s, but they are executed in the *"transaction context"* regardless of where you place them, whilst these `debugLog`s can be *"moved"* to be executed in the *"bootloader context"* (where they should have been since the beggining). Like it is right now, users are paying for the next additional opcodes:

- **3** `mstore`
- **8** `add`
- **5** `mul`
- **5** `callValue`
- **2** `sub`

I asked Vlad for the gas cost of each of them but he did not know, so I will do the estimations with [the Ethereum ones](https://ethereum.org/en/developers/docs/evm/opcodes/). Just keep in mind that, as the underlying logic for these opcodes are ZK circuits, the gas cost will be higher to cover the computational overhead:

$$3 * mstore + 8 * add + 5 * mul + 5 * callValue + 2 * sub$$

$$3 * 3 + 3 * 8 + 5 * 5 + 2 * 5 + 2 * 3$$

$$\therefore 80 $$

**NOTE** ⇒ this is **NOT** a gas optimization, this is a theft of gas (just to make it clear).

### Recommended Mitigation steps

[**l1TxPreparation, lines 1006 to 1019**](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L1006C1-L1019C84)

```diff
                let gasBeforePreparation := gas()
-               debugLog("gasBeforePreparation", gasBeforePreparation)

                // Even though the smart contracts on L1 should make sure that the L1->L2 provide enough gas to generate the hash
                // we should still be able to do it even if this protection layer fails.
                canonicalL1TxHash := getCanonicalL1TxHash(txDataOffset)
-               debugLog("l1 hash", canonicalL1TxHash)

                // Appending the transaction's hash to the current L2 block
                appendTransactionHash(canonicalL1TxHash, true)

                markFactoryDepsForTx(innerTxDataOffset, true)

                gasUsedOnPreparation := safeSub(gasBeforePreparation, gas(), "xpa")
+               debugLog("gasBeforePreparation", gasBeforePreparation)
+               debugLog("l1 hash", canonicalL1TxHash)
```

Reduction of $80 * 2 = 160$ gas.

[**l2TxValidation, lines 1185 to 1213**](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L1185C1-L1213C72)

```diff
                let gasBeforeValidate := gas()

-               debugLog("gasBeforeValidate", gasBeforeValidate)

                // Saving the tx hash and the suggested signed tx hash to memory
                saveTxHashes(txDataOffset)

                // Appending the transaction's hash to the current L2 block
                appendTransactionHash(mload(CURRENT_L2_TX_HASHES_BEGIN_BYTE()), false)

                checkEnoughGas(gasLimitForTx)

                // Note, that it is assumed that `ZKSYNC_NEAR_CALL_validateTx` will always return true
                // unless some error which made the whole bootloader to revert has happened or
                // it runs out of gas.
                let isValid := 0
+               let validateABI := 0

                // Only if the gasLimit for tx is non-zero, we will try to actually run the validation
                if gasLimitForTx {
-                   let validateABI := getNearCallABI(gasLimitForTx)
+                   validateABI := getNearCallABI(gasLimitForTx)
-                   debugLog("validateABI", validateABI)

                    isValid := ZKSYNC_NEAR_CALL_validateTx(validateABI, txDataOffset, gasPrice)                    
                }

-               debugLog("isValid", isValid)

                let gasUsedForValidate := sub(gasBeforeValidate, gas())
+               debugLog("gasBeforeValidate", gasBeforeValidate)
+               debugLog("validateABI", validateABI) // TODO review this, how can you differentiate between validateABI being 0 because gasLimitForTx is 0 or because there was an error in getNearCallABI ??
+               debugLog("isValid", isValid)
```

Reduction of $80 * 3 = 240$ gas.

# Low severity findings

## [L-01] Dangerous execution path

In [L1WethBridge, function deposit](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L143C1-L151C50), and [L1ERC20Bridge, function deposit](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L161C1-L169C50), some invariants must hold to apply address aliasing to the refund recipient:

1. `_refundRecipient` on L2 $=$ `_refundRecipient` if it is an EOA in L1
2. `_refundRecipient` on L2 $=$ `alias(_refundRecipient)` if it is **NOT** an EOA in L1
3. `_refundRecipient` on L2 $=$ `msg.sender` if it is an EOA in L1 **AND** `_refundRecipient` is `address(0)`
4. `_refundRecipient` on L2 $=$ `alias(msg.sender)` if it is **NOT** an EOA in L1 **AND** `_refundRecipient` is `address(0)`

For that, both bridges check $3$ and $4$ by doing

```solidity
        address refundRecipient = _refundRecipient;
if (_refundRecipient == address(0)) {
refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
}
```

and `refundRecipient` is used as an argument to [Mailbox, requestL2Transaction](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236). The issue is that [Mailbox, requestL2Transaction](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236) does apply address aliasing too (checks $1$ and $2$)

[**Mailbox, lines 309 to 314**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L309C1-L314C10)

```solidity
        // If the `_refundRecipient` is not provided, we use the `_sender` as the recipient.
        address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient;
        // If the `_refundRecipient` is a smart contract, we apply the L1 to L2 alias to prevent foot guns.
        if (refundRecipient.code.length > 0) {
            refundRecipient = AddressAliasHelper.applyL1ToL2Alias(refundRecipient);
        }
```

so there is an edge case that breaks our four invariants:

- `_refundRecipient` on L2 = `alias(alias(msg.sender))` if `msg.sender` is **NOT** an EOA in L1 **AND** `alias(msg.sender)` is **NOT** an EOA in L1 **and** `_refundRecipient` is `address(0)`.

This is bad because the excess of gas may be sent to the wrong `_refundRecipient` or, even worst, if a WETH transfer fails on L2, the whole amount transferred. Although **VERY** unlikely to occur ($2^k$ execution steps to find a collision), I would remove this possibility from happening by modifying the code in both bridges:

```diff
         // If the refund recipient is not specified, the refund will be sent to the sender of the transaction.
         // Otherwise, the refund will be sent to the specified address.
         // If the recipient is a contract on L1, the address alias will be applied.
         address refundRecipient = _refundRecipient;
         if (_refundRecipient == address(0)) {
-            refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
+            refundRecipient = msg.sender; // it will be aliased by Mailbox if it is a contract and will default to msg.sender if EOA
        }
```

## [L-02] Code is unreachable

In [Mailbox, function _verifyDepositLimit](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L275C1-L281C6), the code after the `if` statement is never executed as `address(0)` is hard-coded and ETH deposits are uncapped (see the comment):

```solidity
    function _verifyDepositLimit(address _depositor, uint256 _amount) internal {
        IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH
        if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH

        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
        s.totalDepositedAmountPerUser[_depositor] += _amount;
    }
```

Although [AllowList, function setDepositLimit](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/common/AllowList.sol#L129C1-L132C6) does **NOT** enforce it:

```solidity
    function setDepositLimit(
  address _l1Token,
  bool _depositLimitation,
  uint256 _depositCap
) external onlyOwner {
  tokenDeposit[_l1Token].depositLimitation = _depositLimitation;
  tokenDeposit[_l1Token].depositCap = _depositCap;
}
```

consider changing it to revert if trying to cap ETH deposits

```diff
    function setDepositLimit(
        address _l1Token,
        bool _depositLimitation,
        uint256 _depositCap
    ) external onlyOwner {
+       require(_l1Token != address(0), "ETH deposits cannot be limited");
        tokenDeposit[_l1Token].depositLimitation = _depositLimitation;
        tokenDeposit[_l1Token].depositCap = _depositCap;
    }
```

and consider removing the whole `_verifyDepositLimit` function as it will always return without modifying the state of the contract (tautology)

```diff
-   function _verifyDepositLimit(address _depositor, uint256 _amount) internal {
-       IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH
-       if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH 

-       require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
-       s.totalDepositedAmountPerUser[_depositor] += _amount;
-   }
```

**NOTE** ⇒ `Mailbox` is for ETH, which is uncapped, and tokens are bridged via the bridges, which do check for the deposit limits themselves, so there is no place for that function here).

## [L-03] Changing addresses sizes from 20 to 32 bytes could break some parts of the system

In [ImmutableSimulator, line 20](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/ImmutableSimulator.sol#L20), it says `@notice that address uses uint256 type to leave the option to introduce 32-bytes address space in future` which is a pretty bad idea as

- it breaks compatibility with the EVM and **MANY** other chains (if not all) and
- your own code casts `address` to `uint160` pretty often. Changing the size of the `address` type may lead to collisions or losing information with addresses whose value is higher than `type(uint160).max`.

**TLDR** ⇒ do **NOT** do that.

## [L-04] Unused variables (missing logic?)

- In [bootloader, lines 2325 to 2326](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L2325C1-L2326C45), the local variables `dataLength` and `data` are not used. I'm putting this as a low as I do not know if there is some missing logic.
- In [bootloader, line 1296](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1296), the local variables `innerTxDataOffset` is not used. I'm putting this as a low as I do not know if there is some missing logic.

## [L-05] Code and comment mismatch

In [ContractDeployer, lines 159](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/ContractDeployer.sol#L159C1-L159C71) and [182](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/ContractDeployer.sol#L182C1-L182C71) say `@dev In case of a revert, the zero address should be returned` but the functions `create2Account` and `createAccount` revert the whole transaction instead. Consider either removing those comments or putting the code in a `try`/`catch` block, so that it returns `address(0)` in the `catch` block.

## [L-06] `basefee` may be higher than `maxPriorityFeePerGas`

In [bootloader, function getGasPrice](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L1030C1-L1052C14) checks the correctness of the fee parameters, namely:

- $maxPriorityFeePerGas > maxFeePerGas \ ? \ revert : proceed$
- $baseFee > maxFeePerGas \ ? \ revert : proceed$

But it does not check that:

- $baseFee > maxPriorityFeePerGas \ ? \ revert : proceed$

so the operator may charge users requesting priority operations more than what they initially allowed.

```diff
            /// @dev Returns the gas price that should be used by the transaction 
            /// based on the EIP1559's maxFeePerGas and maxPriorityFeePerGas.
            /// The following invariants should hold:
            /// maxPriorityFeePerGas <= maxFeePerGas
-           /// baseFee <= maxFeePerGas
+           /// baseFee <= maxPriorityFeePerGas
            /// While we charge baseFee from the users, the method is mostly used as a method for validating 
            /// the correctness of the fee parameters
            function getGasPrice(
                maxFeePerGas,
                maxPriorityFeePerGas
            ) -> ret {
                let baseFee := basefee()

                if gt(maxPriorityFeePerGas, maxFeePerGas) {
                    revertWithReason(
                        MAX_PRIORITY_FEE_PER_GAS_GREATER_THAN_MAX_FEE_PER_GAS(),
                        0
                    )
                }

-               if gt(baseFee, maxFeePerGas) {
+               if gt(baseFee, maxPriorityFeePerGas) {
                    revertWithReason(
-                       BASE_FEE_GREATER_THAN_MAX_FEE_PER_GAS(),
+                       BASE_FEE_GREATER_THAN_MAX_PRIORITY_FEE_PER_GAS(), // add it and remove `BASE_FEE_GREATER_THAN_MAX_FEE_PER_GAS` at line 3384
                        0
                    )
                }

                // We always use `baseFee` to charge the transaction 
                ret := baseFee
            }
```

so that we have $baseFee \le maxPriorityFeePerGas \le maxFeePerGas$. Right now, $maxPriorityFeePerGas$ is hard-coded to $0$

```solidity
// Mailbox

    function _serializeL2Transaction(
        WritePriorityOpParams memory _priorityOpParams,
        bytes calldata _calldata,
        bytes[] calldata _factoryDeps
    ) internal pure returns (L2CanonicalTransaction memory transaction) {
        transaction = L2CanonicalTransaction({
        txType: PRIORITY_OPERATION_L2_TX_TYPE,
        from: uint256(uint160(_priorityOpParams.sender)),
        to: uint256(uint160(_priorityOpParams.contractAddressL2)),
        gasLimit: _priorityOpParams.l2GasLimit,
        gasPerPubdataByteLimit: _priorityOpParams.l2GasPricePerPubdata,
        maxFeePerGas: uint256(_priorityOpParams.l2GasPrice),
        maxPriorityFeePerGas: uint256(0),<========================================================= HERE
        paymaster: uint256(0),
        // Note, that the priority operation id is used as "nonce" for L1->L2 transactions
        nonce: uint256(_priorityOpParams.txId),
        value: _priorityOpParams.l2Value,
        reserved: [_priorityOpParams.valueToMint, uint256(uint160(_priorityOpParams.refundRecipient)), 0, 0],
        data: _calldata,
        signature: new bytes(0),
        factoryDeps: _hashFactoryDeps(_factoryDeps),
        paymasterInput: new bytes(0),
        reservedDynamic: new bytes(0)
    });
```

so the Low, instead of the Medium **BUT** when you update that make sure that the invariants above hold, making the operator unable to charge users more than what they specified.

## [L-07] `getZkSyncMeta` does not populate fully the `ZkSyncMeta` struct

In [SystemContractHelper, function getZkSyncMeta](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L297C1-L303C6) constructs the `ZkSyncMeta` struct from the bytes returned by `getZkSyncMetaBytes()`. However, the fields `heapSize` and `auxHeapSize` are not populated, so they default to **0**. As the only field that is being used in the repo is `gasPerPubdataByte`, I am putting it as a Low.

```diff
    function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) {
        uint256 metaPacked = getZkSyncMetaBytes();
        meta.gasPerPubdataByte = getGasPerPubdataByteFromMeta(metaPacked);
        meta.shardId = getShardIdFromMeta(metaPacked);
        meta.callerShardId = getCallerShardIdFromMeta(metaPacked);
        meta.codeShardId = getCodeShardIdFromMeta(metaPacked);
+       meta.heapSize = getHeapSizeFromMeta(metaPacked);
+       meta.auxHeapSize = getAuxHeapSizeFromMeta(metaPacked);
    }
```

For reference, the struct is:

```solidity
struct ZkSyncMeta {
    uint32 gasPerPubdataByte;
    uint32 heapSize;
    uint32 auxHeapSize;
    uint8 shardId;
    uint8 callerShardId;
    uint8 codeShardId;
}
```

## [L-08] The front-end may return a wrong value for the L2 transaction base cost

In [Mailbox, function l2TransactionBaseCost](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L166C1-L166C36) is not called in the whole project so it seems it will be called by the front-end to show users an estimation of their transaction cost. However, they do permit to pass an arbitrary `_l2GasPerPubdataByteLimit` whilst function `requestL2Transaction` requires that it is equal to `REQUIRED_L2_GAS_PRICE_PER_PUBDATA`, whose value is `800` (see [here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/SystemConfig.json#L18)). As I do not know a shit about Vue and TS (and your front-end is written in that), I do recommend to hard-code the value in `l2TransactionBaseCost` to **NOT** return misleading estimations to users:

```diff
    function l2TransactionBaseCost(
        uint256 _gasPrice,
-       uint256 _l2GasLimit,
-       uint256 _l2GasPerPubdataByteLimit
+       uint256 _l2GasLimit
    ) public pure returns (uint256) {
-         uint256 l2GasPrice = _deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit);
+         uint256 l2GasPrice = _deriveL2GasPrice(_gasPrice, REQUIRED_L2_GAS_PRICE_PER_PUBDATA);
          return l2GasPrice * _l2GasLimit;
    }
```

## [L-09] `rawCall` does permit calls to `MsgValueSimulator` with the `_isSystem` flag set to false (it will revert)
In [MsgValueSimulator, fallback](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/MsgValueSimulator.sol#L35C45-L35C59) can only be called with the `systemCall` flag set (not the ABI one, which is retrieved through `SystemContractHelper.getExtraAbiData`)

```solidity
    fallback(bytes calldata _data) external onlySystemCall returns (bytes memory)
```

[**ISystemContract, lines 15 to 21**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/interfaces/ISystemContract.sol#L15C1-L21C6)

```solidity
    modifier onlySystemCall() {
        require(
            SystemContractHelper.isSystemCall() || SystemContractHelper.isSystemContract(msg.sender),
                "This method require system call flag"
        );
        _;
    }
```

However, in [EfficientCall, function rawCall](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L146) does let the door open for doing calls to `MsgValueSimulator` with the `_isSystem` flag set to `false` even if the corresponding ABI parameter is hard-coded to `true`

[**EfficientCall, lines 139 to 146**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L139C1-L146C85)

```solidity
            _loadFarCallABIIntoActivePtr(_gas, _data, false, true); <========= isSystem hard-coded to true

            // If there is provided `msg.value` call the `MsgValueSimulator` to forward ether.
            address msgValueSimulator = MSG_VALUE_SYSTEM_CONTRACT;
            address callAddr = SYSTEM_CALL_BY_REF_CALL_ADDRESS;
            // We need to supply the mask to the MsgValueSimulator to denote
            // that the call should be a system one.
            uint256 forwardMask = _isSystem ? MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT : 0; <========= may be false
```

Consider just using `MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT`, as the `_isSystem` parameter is expected to be used for non-zero value calls.

```diff
    function rawCall(
        uint256 _gas,
        address _address,
        uint256 _value,
        bytes calldata _data,
        bool _isSystem
    ) internal returns (bool success) {
        if (_value == 0) {
            _loadFarCallABIIntoActivePtr(_gas, _data, false, _isSystem);

            address callAddr = RAW_FAR_CALL_BY_REF_CALL_ADDRESS;
            assembly {
                success := call(_address, callAddr, 0, 0, 0xFFFF, 0, 0)
            }
        } else {
            _loadFarCallABIIntoActivePtr(_gas, _data, false, true);

            // If there is provided `msg.value` call the `MsgValueSimulator` to forward ether.
            address msgValueSimulator = MSG_VALUE_SYSTEM_CONTRACT;
            address callAddr = SYSTEM_CALL_BY_REF_CALL_ADDRESS;
-           // We need to supply the mask to the MsgValueSimulator to denote
-           // that the call should be a system one.
-           uint256 forwardMask = _isSystem ? MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT : 0;
+           uint256 forwardMask = MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT; 

            assembly {
                success := call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)
            }
        }
    }
```

as seen in [SystemContractsCaller, lines 103 to 112](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L103C1-L112C10) (the comments are the same, so it seems it was copy-pasted).

## [L-10] L1 ⇒ L2 transactions are not free, but bootloader behaves like they are

In [Mailbox, function requestL2Transaction](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L270C1-L270C19), the `isFree` parameter of the call to `_requestL2Transaction` is hard-coded to `false`:

```solidity
        canonicalTxHash = _requestL2Transaction(
            sender,                        // from
            _contractL2,                   // to
            _l2Value,                      // bridged ETH
            _calldata,                     // the usual calldata
            _l2GasLimit,                   // the amount of gas user is willing to spend
            _l2GasPerPubdataByteLimit,     // the amount of gas per pubdata the operator may charge
            _factoryDeps,                  // bytecode to be marked as known on L2
            false,                         // isFree
            _refundRecipient               // who is gonna receive the excess of gas or the whole ETH if the tx fails
        );
```

so the calculation of the L2 gas price

[**Mailbox, lines 303 to 307**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L303C1-L307C10)

```solidity
            params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);
            uint256 baseCost = params.l2GasPrice * _l2GasLimit;
```

defaults to

```solidity
            params.l2GasPrice = _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);
            uint256 baseCost = params.l2GasPrice * _l2GasLimit;
```

taking into account that its value can never be zero because 

```solidity
      function _deriveL2GasPrice(uint256 _l1GasPrice, uint256 _gasPricePerPubdata) internal pure returns (uint256) {
            uint256 pubdataPriceETH = L1_GAS_PER_PUBDATA_BYTE * _l1GasPrice;
            uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;
            
            return Math.max(FAIR_L2_GAS_PRICE, minL2GasPriceETH);
      }
```

returns at least `FAIR_L2_GAS_PRICE`. If we go to [bootloader, function processL1Tx](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/bootloader/bootloader.yul#L878C1-L951C18), the `refundGas` the user will receive is the sum of

$$reservedGas \propto getGasLimitForTx()$$

and

$$refundGas \propto \max(getExecuteL1TxAndGetRefund(), refundFromOperator())$$

being `reservedGas` the excess of gas between what the user provided and what the operator is willing to use, and `refundGas` (not the final one) the remaining gas that is left after preparing and executing the requested transaction. It is expected then that `_refundRecipient` will receive $refundGas * gasPrice$ as a compensation for providing more gas than the needed for the execution of the L1 ⇒ L2 transaction. However, we see that

[**Mailbox, lines 929 to 932**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L929C1-L932C45)

```solidity
                // Note, that for now, the L1->L2 transactions are free, i.e. the gasPrice
                // for such transactions is always zero, so the `refundGas` is not used anywhere
                // except for notifications for the operator for API purposes. 
                notifyAboutRefund(refundGas)
```

and it is never used as it assumes that "L1 ⇒ L2 transactions are free", which is not true. If the transaction does not revert, then `_refundRecipient` will receive:

[**bootloader, line 950**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L950)

```solidity
                    toRefundRecipient := safeSub(getReserved0(innerTxDataOffset), safeAdd(getValue(innerTxDataOffset), payToOperator, "kpa"), "ysl")
```

which equals to $msg.value - (l2Value + payToOperator)$. I tried reversing the equation and simplifying it in order to prove that $refundGas * gasPrice \not = msg.value - (l2Value + payToOperator)$ but it was a fucking rabbit hole and I started making assumptions that weren't robust enough to be in this POC. I kindly suggest changing the code to:

[**bootloader, lines 929 to 952**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L929C1-L952C1)

```diff
-               // Note, that for now, the L1->L2 transactions are free, i.e. the gasPrice
-               // for such transactions is always zero, so the `refundGas` is not used anywhere
-               // except for notifications for the operator for API purposes. 
                notifyAboutRefund(refundGas)

                // Paying the fee to the operator
                mintEther(BOOTLOADER_FORMAL_ADDR(), payToOperator, false)

                let toRefundRecipient
                switch success
                case 0 {
                    // If the transaction reverts, then minting the msg.value to the user has been reverted
                    // as well, so we can simply mint everything that the user has deposited to 
                    // the refund recipient

                    toRefundRecipient := safeSub(getReserved0(innerTxDataOffset), payToOperator, "vji")
                }
                default {
-                   // If the transaction succeeds, then it is assumed that msg.value was transferred correctly. However, the remaining 
-                   // ETH deposited will be given to the refund recipient.
+                   // If the transaction succeeds, then it is assumed that msg.value was transferred correctly, so refundGas is 
+                   // sent to the refund recipient.

-                   toRefundRecipient := safeSub(getReserved0(innerTxDataOffset), safeAdd(getValue(innerTxDataOffset), payToOperator, "kpa"), "ysl")
+                   toRefundRecipient := safeMul(refundGas, gasPrice, "whatever error you want")
                }
```

If both equations are equal, that is, $refundGas * gasPrice = msg.value - (l2Value + payToOperator)$, then this is more explicit and saves gas by not doing `safeSub`s nor `safeAdd`s (QA). If they are **NOT**, then this code is the correct one, as you are giving the refund recipient exactly the calculated `refundGas`, no more, no less (Medium).

## [L-11] It is possible to refund a single transaction more than 2³² - 1 gas, whilst the bootloader only has that amount of gas avaiable for a given batch

The bootloader is provided with $2³² - 1$ gas to execute all submitted transactions in a given batch, so it is expected that all the gas refunds will be below that amount. For L2 transactions it is checked in

[**bootloader, lines 1463 to 1465**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1463C1-L1465C18)

```solidity
                if iszero(validateUint32(refundInGas)) {
                      assertionError("refundInGas is not uint32")
                }
```

but for L1 ⇒ L2 transactions it is not enforced, leading to a possible situation in which the operator refunds, or even pay himself, more than what the bootloader really has. Consider doing in

[**bootloader, line 926**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L926)

```diff

                if gt(refundGas, gasLimit) {
                    assertionError("L1: refundGas > gasLimit")
                }

+               if iszero(validateUint32(refundInGas)) {
+                   assertionError("refundInGas is not uint32")
+               }

                let payToOperator := safeMul(gasPrice, safeSub(gasLimit, refundGas, "lpah"), "mnk")
```

## [L-12] Useless `max` pick

In [bootloader, line 1456](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1456), it is being picked the maximum between the refund provided by the operator and the one calculated by the bootloader plus the initial excess of gas. As `askOperatorForRefund` is called with the leftover gas found by the bootloader, it will return a value less than $reservedGas + gasLeft$ so the `max` function is broken. Consider doing the same as in

[**bootloader, lines 914 to 921**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L914C1-L921C57)

```solidity
                    // Asking the operator for refund
                    askOperatorForRefund(potentialRefund)
                    
                    // In case the operator provided smaller refund than the one calculated
                    // by the bootloader, we return the refund calculated by the bootloader.
                    refundGas := max(getOperatorRefundForTx(transactionIndex), potentialRefund)
                    }
                    
                refundGas := add(refundGas, reservedGas)
```

that is, rewrite

[**bootloader, lines 1450 to 1456**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1450C1-L1456C90)

```diff
               askOperatorForRefund(gasLeft)

               let operatorProvidedRefund := getOperatorRefundForTx(transactionIndex)

               // If the operator provides the value that is lower than the one suggested for 
               // the bootloader, we will use the one calculated by the bootloader.
-              let refundInGas := max(operatorProvidedRefund, add(reservedGas, gasLeft))
+              let refundInGas := max(operatorProvidedRefund, gasLeft)
+              refundInGas := add(reservedGas, refundInGas)
```

## [L-13] Misleading comment (?)

In [Executor, line 440](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L440)

```solidity
                bytes32(0) // zkPorter batch hash
```

it says *"zkPorter batch HASH"*, so it seems it should be

```diff
-                bytes32(0) // zkPorter batch hash
+                EMPTY_STRING_KECCAK // zkPorter batch hash
```

## [L-14] Lower-bound the minimum execution delay in `ValidatorTimelock`

[**ValidatorTimelock, function setExecutionDelay**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L59C1-L62C6)

```diff
+   uint32 MIN_EXECUTION_DELAY = uint32(1 hours); // whatever

    function setExecutionDelay(uint32 _executionDelay) external onlyOwner {
+       require(_executionDelay >= MIN_EXECUTION_DELAY, "Don't do that");
        executionDelay = _executionDelay;
        emit NewExecutionDelay(_executionDelay);
    }
```

## [L-15] Wrong logic (?)

Both [Utils, function bytecodeLenInWords](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/Utils.sol#L46) and [L2ContractHelper, function _bytecodeLen](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L50) do calculate the length in words of a given bytecode like the following:

```solidity
        codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
```

However, when hashing the bytecode, the bytes at position `[2]` are set to **0**:

```solidity
    function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {

        ...

        hashedBytecode = sha256(_bytecode) & 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
        // Setting the version of the hash
        hashedBytecode = (hashedBytecode | bytes32(uint256(1 << 248)));
        // Setting the length
        hashedBytecode = hashedBytecode | bytes32(bytecodeLenInWords << 224);
    }
```

It seems that `codeLengthInWords` should be equal to the bytes at position `[3]` alone, as the variable's name `bytecodeLenInWords` suggests, that is:

[**L2ContractHelper, function _bytecodeLen**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L49C1-L51C6)

```diff
    function _bytecodeLen(bytes32 _bytecodeHash) private pure returns (uint256 codeLengthInWords) {
-       codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
+       codeLengthInWords = uint256(uint8(_bytecodeHash[3]));
    }
```

[**Utils, function bytecodeLenInWords**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/Utils.sol#L44C1-L48C6)

```diff
    function bytecodeLenInWords(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) {
        unchecked {
-           codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
+           codeLengthInWords = uint256(uint8(_bytecodeHash[3]));
        }
    }
```

## [L-16] If `_enumerationIndexSize` is `0`, `compressedEnumIndex` equals `metadata`

In [**Compressor, lines 175 to 177**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Compressor.sol#L175C1-L179C81)

```solidity
            uint256 compressedEnumIndex = _sliceToUint256(_compressedStateDiffs[stateDiffPtr:stateDiffPtr + _enumerationIndexSize]);
            require(enumIndex == compressedEnumIndex, "rw: enum key mismatch");
            stateDiffPtr += _enumerationIndexSize;
            
            uint8 metadata = uint8(bytes1(_compressedStateDiffs[stateDiffPtr]));
```

if `_enumerationIndexSize` is zero, then `compressedEnumIndex` and `metadata` will have the same value, as `stateDiffPtr += 0` equals `stateDiffPtr`.

[**Compressor, lines 117 to 126**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Compressor.sol#L117C1-L126C109)

```diff
    function verifyCompressedStateDiffs(
        uint256 _numberOfStateDiffs,
        uint256 _enumerationIndexSize,
        bytes calldata _stateDiffs,
        bytes calldata _compressedStateDiffs
    ) external payable onlyCallFrom(address(L1_MESSENGER_CONTRACT)) returns (bytes32 stateDiffHash) {
        // We do not enforce the operator to use the optimal, i.e. the minimally possible _enumerationIndexSize. 
        // We do enforce however, that the _enumerationIndexSize is not larger than 8 bytes long, which is the 
        // maximal ever possible size for enumeration index.
+       require(_enumerationIndexSize != 0, "whatever");
        require(_enumerationIndexSize <= MAX_ENUMERATION_INDEX_SIZE, "enumeration index size is too large");

        ...
```

## [L-17] Tautology in `publishPubdataAndClearState`

In [**L1Messenger, function publishPubdataAndClearState**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L206) there is a tautology:

[L1Messenger, line 206](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L206)

```solidity
        require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
```

As `numberOfL2ToL1Logs <= numberOfL2ToL1Logs` always holds, it can pass more logs than `numberOfLogsToProcess`. However, the correctness of the amount of sent logs is also checked in

[**Executor, lines 166 to 170**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L166C1-L170C10)

```solidity
        if (_expectedSystemContractUpgradeTxHash == bytes32(0)) {
            require(processedLogs == 127, "b7");
        } else {
            require(processedLogs == 255, "b8");
        }
```

so the Low. 

**NOTE** ⇒ I did not test it, but it seems it lets the door open for the operator to add an additional log to the queue if there was a system upgrade, so that the first condition reverts, which may be considered as a temporal DOS/griefing. Consider changing

[**L1Messenger, line 206**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L206)

```diff
-        require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
+        require(numberOfL2ToL1Logs <= numberOfLogsToProcess, "Too many L2->L1 logs");
```

## [L-18] Chaining hashes with the wrong initial value (?)

Usually, when we want to chain hashes one after another we start with the empty string hash and then hash the rest by encoding one after another like `"" || str1 || str2 || ...` and hashing them in pairs (you know what I mean). However, in many places it is used **0** as the initial *"string"* which may lead to issues as some of your contracts in L1 do use `EMPTY_STRING_KECCAK` instead, namely

[**Executor, function _collectOperationsFromPriorityQueue**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L260C1-L267C6)

```solidity
    function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {
        concatHash = EMPTY_STRING_KECCAK;

        for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
            PriorityOperation memory priorityOp = s.priorityQueue.popFront();
            concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
        }
    }
```

and in the future it seems there will be more. On the other hand, there are some places which do not follow this:

[**L1Messenger, line 210**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L210)

```solidity
        bytes32 reconstructedChainedLogsHash; // defaults to bytes32(0)
```

[**L1Messenger, line 240**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L240)

```solidity
        bytes32 reconstructedChainedMessagesHash; // defaults to bytes32(0)
```

[**L1Messenger, line 258**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L258)

```solidity
        bytes32 reconstructedChainedL1BytecodesRevealDataHash; // defaults to bytes32(0)
```

[**L1Messenger, lines 331 to 335**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L331C1-L335C55)

```solidity
        /// Clear logs state
chainedLogsHash = bytes32(0);
numberOfLogsToProcess = 0;
chainedMessagesHash = bytes32(0);
chainedL1BytecodesRevealDataHash = bytes32(0);
```

[**SystemContext, line 293**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L293)

```solidity
        currentL2BlockTxsRollingHash = bytes32(0);
```

Consider changing them to `EMPTY_STRING_KECCAK`.

## [L-19] Wrong initialisation of the zero point (?)

I do not know a shit about affine spaces, but

[**ecrecover/secp256k1/mod.rs, lines 147 to 153**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L147C1-L153C6)

```solidity
    fn zero() -> Self {
        PointAffine {
            x: Fq::zero(),
            y: Fq::one(),
            infinity: true,
        }
    }
````

is wrong as `is_zero` is defined like

```solidity

    fn is_zero(&self) -> bool {
        self.infinity
    }
```

and `self.infinity` is toggled up when `self.x` $=$ `self.y` $=$ `0`:

[**ecrecover/secp256k1/mod.rs, lines 189 to 211**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L189C1-L211C6)

```solidity
    fn from_xy_unchecked(x: Self::Base, y: Self::Base) -> Self {
        let infinity = x.is_zero() && y.is_zero();
        Self {
            x: x,
            y: y,
            infinity,
        }
    }

    fn from_xy_checked(x: Self::Base, y: Self::Base) -> Result<Self, GroupDecodingError> {
        let infinity = x.is_zero() && y.is_zero();
        let affine = Self {
            x: x,
            y: y,
            infinity,
        };

        if !affine.is_on_curve() {
            Err(GroupDecodingError::NotOnCurve)
        } else {
            Ok(affine)
        }
    }
```

I'm putting it as a Low as it may be some weird math thing I do not know, **BUT** the zero element in all *"math-things"* is defined as the one that makes

$$a \star O = O \star a = a, \forall a \in F$$

and having two *"zero-elements"* is definetly wrong. If I am right, then it may be a High as some [functions like eq](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L47C1-L86C2) or [is_on_curve](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L124C1-L139C6) would return true for both. Moreover, I'm gonna say something that mathematicians will laugh at me, but

- say there are two *"zero-elements"*, namely $O_1$ and $O_2$, in $F$ with $O_1 \neq O_2$
- Then, for example, we have $O_1 \star O_2 = O_2 \star O_1 = O_1$ but, as both are the *"zero-element"*, the next $O_1 \star O_2 = O_2 \star O_1 = O_2$ holds too
- That implies $O_1 = O_2$ but our initial thesis was that $O_1 \neq O_2$, so we have a contradiction

$$\therefore O \ is \ unique \ under \ F$$

I can't give you a solution as I do not know which one is the right point at $\infty$, as in some places it says it is the $(0, 0)$ and in others say it is $(0, 1)$.


# Non-critical

## [NC-01] WTF

- [contracts/zksync/contracts/Dependencies.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/Dependencies.sol) ⇒ not used anywhere.
- [system-contracts/bootloader/bootloader.yul, line 862](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L862) ⇒ out of place comment?

## [NC-02] Missing comments

- [ContractDeployer, line 307](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/ContractDeployer.sol#L307C17-L307C45) ⇒ what ensures?
- [bootloader, line 1562](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L1562C17-L1562C54) ⇒ the end of the comment is missing.
- [SystemContractHelper, lines 170 and 173](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L173) ⇒ add the [ones being developed by lambdaclass](https://github.com/lambdaclass/zksync_era_precompiles#current-status) to that list.

## [NC-03] Typos

- [SystemContractHelper, line 347](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L347) ⇒ `2-bit IS set`, `not 2-bit it set`.

```diff
-        // When the system call is passed, the 2-bit it set to 1
+        // When the system call is passed, the 2-bit is set to 1
```

- [KnownCodesStorage, lines 16 to 17](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/KnownCodesStorage.sol#L16C117-L17C10) ⇒ repeated `words`.

```diff
- * the second byte denotes whether the contract is constructed, and the next two bytes denote the length in 32-byte words.
- * words. And then the next 28 bytes is the truncated hash.
+ * the second byte denotes whether the contract is constructed, the next two bytes denote the length in 32-byte words
+ * and then the next 28 bytes is the truncated hash.
```

- [SystemContext, line 22](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/SystemContext.sol#L22C32-L22C48) ⇒ `blocks' hashes`, not `blocks's hashes`.

```diff
-    /// - Store the latest 257 blocks's hashes.
+    /// - Store the latest 257 blocks' hashes.
```

- [bootloader, line 38](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L38C20-L38C60) ⇒ `That means the operator controls ...`, not `That it means that the operator controls ...`.

```diff
-                // That it means that the operator controls
+                // That means the operator controls
```

- [bootloader, line 652](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L652C57-L652C84) ⇒ `... make sure the operator ...`, not `... make sure that the operator`.

```diff
-             /// @dev The purpose of this function is to make sure that the operator
+             /// @dev The purpose of this function is to make sure the operator
```

- [bootloader, line 672](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L672C32-L672C35) ⇒ remove that `for`.

```diff
-                     // Calling for the `payForTransaction` method of the account.
+                     // Calling the `payForTransaction` method of the account.
```

- [bootloader, line 3164](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L3164C22-L3164C39) ⇒ `Accepts an uint64`, not `Accepts an uint32`.

```diff
-            /// @dev Accepts an uint32 and returns whether or not it is
+            /// @dev Accepts an uint64 and returns whether or not it is
```

- [bootloader, lines 3170 to 3171](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L3170C22-L3171C31) ⇒ `Accepts an uint128 and returns wether or not it is a valid uint128`, not `Accepts an uint32 and returns wether or not it is a valid uint64`.

```diff
-            /// @dev Accepts an uint32 and returns whether or not it is
-            /// a valid uint64
+            /// @dev Accepts an uint128 and returns whether or not it is
+            /// a valid uint128
```

- [keccak256_round_function/mod.rs, lines 87](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/era-zkevm_circuits/src/keccak256_round_function/mod.rs#L87), [88](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/era-zkevm_circuits/src/keccak256_round_function/mod.rs#L88) and [93](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/era-zkevm_circuits/src/keccak256_round_function/mod.rs#L93) ⇒ `MEMORY_QUERIES_PER_CYCLE`, not `MEMORY_EQURIES_PER_CYCLE`.

```diff
- pub const MEMORY_EQURIES_PER_CYCLE: usize = 5; // we need to read as much as possible to use a round function every cycle
+ pub const MEMORY_QUERIES_PER_CYCLE: usize = 5; // we need to read as much as possible to use a round function every cycle
- pub const NUM_U64_WORDS_PER_CYCLE: usize = 4 * MEMORY_EQURIES_PER_CYCLE;
+ pub const NUM_U64_WORDS_PER_CYCLE: usize = 4 * MEMORY_QUERIES_PER_CYCLE;
pub const NEW_BYTES_PER_CYCLE: usize = 8 * NUM_U64_WORDS_PER_CYCLE;
// we absorb 136 elements per cycle, and add 160 elements per cycle, so we need to skip memory reads
// sometimes and do absorbs instead
pub const BUFFER_SIZE_IN_U64_WORDS: usize =
-    MEMORY_EQURIES_PER_CYCLE * 4 + KECCAK256_RATE_IN_U64_WORDS - 1;
+    MEMORY_QUERIES_PER_CYCLE * 4 + KECCAK256_RATE_IN_U64_WORDS - 1;
```

- [Governance, line 83](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/governance/Governance.sol#L83) ⇒ `both` is for two elements, not three.

```diff
-    /// includes both Waiting, Ready, and Done operations.
+    /// includes Waiting, Ready, and Done operations.
```

- [NonceHolder, line 18](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/NonceHolder.sol#L18C29-L18C31) ⇒ it is `mark`, not `either marked` as it is a sentence without a replica.

```diff
- * The users can either marked a range of nonces by increasing the `minNonce`. This way all the nonces
+ * The users can mark a range of nonces by increasing the `minNonce`. This way all the nonces
```

- [NonceHolder, line 29](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/NonceHolder.sol#L29C42-L29C44) ⇒ remove the first `at`.

```diff
-    /// The minNonce can be increased by at 2^32 at a time to prevent it from
+    /// The minNonce can be increased by 2^32 at a time to prevent it from
```

- [uma.rs, line 222](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L222C12-L222C20) ⇒ `Ethereum`, not `Etherium`.

```diff
-    // NB: Etherium virtual machine is big endian;
+    // NB: Ethereum virtual machine is big endian;
```

- [cycle.rs, line 504](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/era-zkevm_circuits/src/main_vm/cycle.rs#L504C8-L504C11) ⇒ `Aux`, not `aux`.

```diff
-    // Axu heap limit
+    // Aux heap limit
```

## [NC-04] Empty error string

In [NonceHolder, line 136](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/NonceHolder.sol#L136C66-L136C68) there should be a non-empty error, as it seems you have some in-house bot that listens to the unique errors emitted by your contracts (all errors are different).

## [NC-05] Wrong comments in L1 bridges

- In [L1ERC20Bridge, line 162](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L162C1-L162C115), the comment says `If the L2 deposit finalization transaction fails, the _refundRecipient will receive the _l2Value`. However,

  - there is no such a parameter in the function and
  - as it is an ERC20 transfer, the call to [Mailbox, requestL2Transaction made in line 198](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L198) has its argument `_l2Value` hard-coded to $0$, so the comment just make no sense. Remove it.

- In [L2ERC20Bridge, line 59](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L59C1-L59C79), the comment says `@param _l2Receiver The account address that would receive minted ether`. However, the bridge is for ERC20 tokens, not for ETH nor WETH. Remove it.

## [NC-06] Wrong comment

[secp256k1/mod.rs, line 404](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L404)

```diff
-                // The two points are equal, so we double.
```

## [NC-07] Wrong comment in `ecrecover/secp256k1/mod.rs`

In [lines 56 to 58](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L56C1-L58C40), it is explained how two projective points' coordinates are checked against each other to see if both points are equal. However, the comment is wrong, as the formula has switched the places of $Z$ and $Z'$:

```diff
        // The points (X, Y, Z) and (X', Y', Z')
-       // are equal when (X * Z^2) = (X' * Z'^2)
-       // and (Y * Z^3) = (Y' * Z'^3).
+       // are equal when (X * Z'^2) = (X' * Z^2)
+       // and (Y * Z'^3) = (Y' * Z^3).
```

## [NC-08] `SystemContext::baseFee` is not a constant, although the comment says it is

The state variable `baseFee` is not a constant, as the keyword `constant` is missing and its value can be changed in `setNewBatch`. Remove that comment.

[SystemContext, line 47](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/SystemContext.sol#L47)

```diff
    /// @notice The `block.basefee`.
-   /// @dev It is currently a constant.
    uint256 public baseFee;
```

## [NC-09] Wrong comment in `Diamond`

The comment `/// NOTE: expect but NOT enforce that _selectors is NON-EMPTY array` is wrong, as it is checked inside [Diamond, function diamondCut](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L106C1-L106C80):

```solidity
            require(selectors.length > 0, "B"); // no functions for diamond cut
```

Remove them (just do `Ctrl+f` as there are three occurrences).

## [NC-10] Add getters to some variables of `AppStorage`

The variables `zkPorterIsAvaiable`, `totalDepositedAmountPerUser`, `admin` and `pendingAdmin` do not have a propper getter inside the `Getters` facet. Consider adding them.

## [NC-11] `validateUpgradeTransaction` does return as valid non-upgrade transactions

Priority transactions has a type-value of `255` and upgrades `254`, as seen in [bootloader, lines 572 to 591](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L572C1-L591C31). However, `validateUpgradeTransaction`, if all the fields of the `_transaction` are correct, accepts transactions with `txType` field different from `254`, as it is not checked:

```solidity
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

Just for completeness, check that `_transaction.txType == 255`:

```diff
    function validateUpgradeTransaction(IMailbox.L2CanonicalTransaction memory _transaction) internal pure {
+       require(_transaction.txType == 255, "WHATEVER");
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

## [NC-12] You are not using your own cached `virtualBlockInfo` in `SystemContext`

```solidity
        BlockInfo memory virtualBlockInfo = currentVirtualL2BlockInfo;

// TODO why are you using  currentVirtualL2BlockInfo.number ??
// TODO it is cached in virtualBlockInfo.number
if (currentVirtualL2BlockInfo.number == 0 && virtualBlockInfo.timestamp == 0) {
```

Do this:

```diff
        BlockInfo memory virtualBlockInfo = currentVirtualL2BlockInfo;

-       if (currentVirtualL2BlockInfo.number == 0 && virtualBlockInfo.timestamp == 0) {
+       if (virtualBlockInfo.number == 0 && virtualBlockInfo.timestamp == 0) {
```

## [NC-13] Wrong `debugLog` call

In [bootloader, line 1110](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1110), you "log" that the refund is **0** when it may be not

```diff
-               debugLog("refund", 0)
+               debugLog("refund", refund)
```

## [NC-14] Do not let users put as `refundRecipient` addresses in kernel space

[**Mailbox, lines 309 to 315**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L309C1-L315C1)

```diff
        // If the `_refundRecipient` is not provided, we use the `_sender` as the recipient.
        address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient;
        // If the `_refundRecipient` is a smart contract, we apply the L1 to L2 alias to prevent foot guns.
        if (refundRecipient.code.length > 0) {
            refundRecipient = AddressAliasHelper.applyL1ToL2Alias(refundRecipient);
        }
+       require(refundRecipient > address(0xFFFF), "You can't send your pennies to the kernel");
```

## [NC-15] Comment and code mismatch

In [bootloader, line 1116](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1116C77-L1116C120), it says it calculates the L2 gas limit without taking into account the intrinsic costs and the overhead. However, they are, as seen in

[**Bootloader, lines 1151 to 1172**](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1151C1-L1172C18)

```solidity
                let operatorOverheadForTransaction := getVerifiedOperatorOverheadForTx(
                    transactionIndex,
                    totalGasLimit,
                    gasPerPubdata,
                    txEncodingLen
                )
                gasLimitForTx := safeSub(totalGasLimit, operatorOverheadForTransaction, "qr")

                let intrinsicOverhead := safeAdd(
                    intrinsicGas, 
                    // the error messages are trimmed to fit into 32 bytes
                    safeMul(intrinsicPubdata, gasPerPubdata, "qw"),
                    "fj" 
                )

                switch lt(gasLimitForTx, intrinsicOverhead)
                case 1 {
                    gasLimitForTx := 0
                }
                default {
                    gasLimitForTx := sub(gasLimitForTx, intrinsicOverhead)
                }
```

Remove that statement:

```diff
-            /// @dev Calculates the L2 gas limit for the transaction's body, i.e. without intrinsic costs and overhead.
+            /// @dev Calculates the L2 gas limit for the transaction's body
```

## [NC-16] Wrong comment (again)

[DefaultAccount, line 15](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L15)

```solidity
 * @dev The bytecode of the contract is set by default for all addresses for which no other bytecodes are deployed.
```

[EmptyContract, line 9](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/EmptyContract.sol#L9)

```solidity
 * @dev The bytecode of the contract is set by default for all addresses for which no other bytecodes are deployed.
```

Copy-pasted, remove the `EmptyContract` one.

## [NC-17] `PointProjective::mul_assign` optimization

[secp256k1/mod.rs, line 543](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L543)

```diff
    fn mul_assign<S: Into<<Self::Scalar as PrimeField>::Repr>>(&mut self, other: S) {
+       if self.is_zero() {
+           return; // 0 *= a equals 0
+       }

+       if other.is_zero() {
+           (*self) = Self::zero(); // a *= 0 equals 0
+           return;
+       }

        let mut res = Self::zero();

        ...
```

## [NC-18] `PointProjective::add_assign_mixed` missing code

[secp256k1/mod.rs, line 486](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L486)

```diff
            ...

        } else {
            // If we're adding -a and a together, self.z becomes zero as H becomes zero.

+           if u1 == u2 {
+               (*self) = Self::zero();
+               return;
+           }

            // H = U2-X1
            let mut h = u2;

            ...
```

# Incompatibility

## [I-01] Missing support for certain precompiles

The Shanghai protocol release has nine precompiled contracts, namely `ecrecover`, `sha256`, `ripemd160`, `identity`, `modexp`, `ecadd`, `ecmul` and `blake2f` (see  [Ethereum Specification](https://ethereum.github.io/execution-specs/autoapi/ethereum/shanghai/index.html)). However, within the current version of zkSync Era, the only precompiles supported are `ecrecover` and `sha256` (plus `keccak256`) and the ones being developed by [lambdaclass](https://github.com/lambdaclass/zksync_era_precompiles#current-status), namely `ecAdd`, `ecMul` and `modexp` (plus `ecPairing`). Consider implementing the other ones too, although they are rarely used, for full equivalence with Ethereum at the precompile level (`ripemd160`, `blake2f` and `identity`)
