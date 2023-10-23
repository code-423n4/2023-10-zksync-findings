**[[1]]** Function `MsgValueSimulator._getAbiParams()` comment claims that the flag that determines whether or not the call should be a system one is stored in the first ABI param. But this flag is actually stored in the third ABI param.
```solidity
/// @notice Extract value, isSystemCall and to from the extraAbi params.
/// @dev The contract accepts value, the callee and whether the call should a system one via its ABI params.
/// @dev The first ABI param contains the value in the [0..127] bits. The 128th contains
/// the flag whether or not the call should be a system one.
/// The second ABI params contains the callee.
function _getAbiParams() internal view returns (uint256 value, bool isSystemCall, address to) {
    value = SystemContractHelper.getExtraAbiData(0);
    uint256 addressAsUint = SystemContractHelper.getExtraAbiData(1);
    uint256 mask = SystemContractHelper.getExtraAbiData(2);

    isSystemCall = (mask & MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT) != 0;

    to = address(uint160(addressAsUint));
}
```
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/DefaultAccount.sol#L102-L103

**[[2]]** The require statement in the `L1Messenger.publishPubdataAndClearState` doesn't check anything. But it seems that there is no way to utilize it somehow because of the `chainedMessageHash` check.
```solidity
require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L206C15-L206C15

**[[3]]** The `bootloader.yul` contains the string literal `"Failed publish timestamp data to L1"` that exceeds the 32-byte limit for string literals in Yul. The attempt to compile `bootloader.yul` with the `solc` compiler leads to the following error:
```
Error: String literal too long (35 > 32)
    --> proved_batch.yul:2433:30:
     |
2433 |                     debugLog("Failed publish timestamp data to L1", 0)
     |                              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```
Consider making this string shorter to avoid any possible runtime issues and ensure that your codebase maintains as much compatibility with the Solidity compiler whenever possible.

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L2516

**[[4]]**  The `bootloader.yul` contains the call to the `ZKSYNC_NEAR_CALL_callPostOp` with the trailing comma. The attempt to compile this code with `solc` leads to the compilation error since it is not correct according to Yul syntax.
```yul
pop(ZKSYNC_NEAR_CALL_callPostOp(
    // Maximum number of gas that the postOp could spend
    nearCallAbi,
    paymaster,
    txDataOffset,
    success,
    // Since the paymaster will be refunded with reservedGas,
    // it should know about it
    safeAdd(gasLeft, reservedGas, "jkl"),
))
```
```
Error: Literal or identifier expected.
    --> proved_batch.yul:1414:25:
     |
1414 |                         ))
     |                         ^

Error: Expected keyword "data" or "object" or "}".
    --> proved_batch.yul:1414:25:
     |
1414 |                         ))
     |                         ^
```
Consider removing the trailing comma and ensure that your codebase maintains as much compatibility with the Solidity compiler whenever possible.

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1428-L1437

**[[5]]** There is a typo in the following comment in the `Mailbox.requestL2Transaction`. Words `"the ability to provide"` appear twice.
```
// VERY IMPORTANT: nobody should rely on this constant to be fixed and every contract should give their users the ability to provide the
// ability to provide `_l2GasPerPubdataByteLimit` for each independent transaction.
// CHANGING THIS CONSTANT SHOULD BE A CLIENT-SIDE CHANGE.
```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L254-L256

**[[6]]** There are a couple of comments where `ergs` is used instead of `gas`. It can be confusing since these comments don't correspond to code.
* https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L159
* https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1179
* https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1231-L1233
* https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1263
* https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1596

**[[7]]** There are a lot of places in the docs where `ergs` is used instead of `gas`. It can be confusing since this text doesn't correspond to code.

**[[8]]** Function `validateUint32` comment in the `bootloader.yul` is misleading. The function accepts a `uint256`. Also, this function is not being used.
```solidity
/// @dev Accepts an uint32 and returns whether or not it is
/// a valid uint64
function validateUint64(x) -> ret {
    ret := lt(x, shl(64,1))
}
```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L3164C19-L3164C19

**[[9]]** Function `validateUint128` comment in the `bootloader.yul` is misleading. The function accepts a `uint256` and returns whether or not it is a valid `uint128`.
```solidity
/// @dev Accepts an uint32 and returns whether or not it is
/// a valid uint64
function validateUint128(x) -> ret {
    ret := lt(x, shl(128,1))
}
```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L3170

**[[10]]** It looks like this value can be precomputed.
```solidity
bytes32 domainSeparator = keccak256(
    abi.encode(EIP712_DOMAIN_TYPEHASH, keccak256("zkSync"), keccak256("2"), block.chainid)
);
```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#L138