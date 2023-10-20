# QA report for zkSync Era - Smart Contracts

## 1. In the Compressor.sol file, shouldn't publishCompressedBytecode() be payable since it doesn't involve any ETH transfer logic. Sending ETH by mistake could result in the loss of user funds.

```solidity
2023-10-zksync/code/system-contracts/contracts/Compressor.sol

function publishCompressedBytecode(
        bytes calldata _bytecode,
        bytes calldata _rawCompressedData
    ) external payable onlyCallFromBootloader returns (bytes32 bytecodeHash) {

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L54-L57

## 2. The SystemContractHelper.getZkSyncMeta function lacks information about heapSize and auxHeapSize.

The [getZkSyncMeta](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L297) function, which is intended to retrieve the [ZkSyncMeta structure](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L42), lacks the [heapSize](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L44C1-L44C1) and [auxHeapSize](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L45) fields, as evidenced here:

```solidity
    /// @notice Retrieves the ZkSyncMeta structure.
    /// @return meta The ZkSyncMeta execution context parameters.
    function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) {
        uint256 metaPacked = getZkSyncMetaBytes();
        meta.gasPerPubdataByte = getGasPerPubdataByteFromMeta(metaPacked);
        meta.shardId = getShardIdFromMeta(metaPacked);
        meta.callerShardId = getCallerShardIdFromMeta(metaPacked);
        meta.codeShardId = getCodeShardIdFromMeta(metaPacked);
    }
```

The ZkSyncMeta structure consists of the following fields:

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

### The following lines can be appended to the end of the getZkSyncMeta function:

```solidity
   meta.heapSize = getHeapSizeFromMeta(metaPacked);
   meta.auxHeapSize = getAuxHeapSizeFromMeta(metaPacked);

```

## 3. Prevent ETH Transfers to System Contracts

[L2EthToken](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol) has the ability to create **ETH** directly and send it to **system contracts**. For instance, a user can move **ETH** from **L1** to **L2** and choose a **system contract** as the destination. This triggers a function called [L2EthToken.mint](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L64), which creates **ETH** in any account, even if that address has a contract with no **payable receive** or **fallback callback**.

However, it doesn't make much sense for **system contracts** to receive **ETH** through these transfers or coin creations. That's why we could add a check in those functions to ensure that the recipient is not a system contract, except perhaps for the zero address. The zero address is typically used to represent burned tokens, and we want to allow the same behavior in zkSync.

```solidity
    function transferFromTo(address _from, address _to, uint256 _amount) external override {
        require(
            msg.sender == MSG_VALUE_SYSTEM_CONTRACT ||
                msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
                msg.sender == BOOTLOADER_FORMAL_ADDRESS,
            "Only system contracts with special access can call this method"
        );
        ...................
        ...................
```

```solidity
    function mint(address _account, uint256 _amount) external override onlyCallFromBootloader {
        totalSupply += _amount;
        balance[_account] += _amount;
        emit Mint(_account, _amount);
    }
```

## 4. Risk in AccountCodeStorage.sol Functions for Inputs Exceeding 2^161-1

The code of the functions [AccountCodeStorage.getCodeHash()](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L89) and [AccountCodeStorage.getCodeSize()](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L117) may produce incorrect values when provided with inputs that exceed **2^161-1**. This is because the code converts the input to **uint160** and then returns the information associated with that address. If the input is greater than the maximum value representable by **uint160**, the resulting conversion could yield an address that actually belongs to another contract.

For example, if we add **2^161** to the address of **Contract1**, the returned value will correspond to the information of **Contract1**, even though there is no contract at the address **2^161 + Contract1_Address**. Therefore, the code should ensure that the **input of type uint256** is less than **2^161** to avoid such errors.

```solidity
    function getCodeHash(uint256 _input) external view override returns (bytes32) {
        // We consider the account bytecode hash of the last 20 bytes of the input, because
        // according to the spec "If EXTCODEHASH of A is X, then EXTCODEHASH of A + 2**160 is X".
        address account = address(uint160(_input));
        if (uint160(account) <= CURRENT_MAX_PRECOMPILE_ADDRESS) {
            return EMPTY_STRING_KECCAK;
        }
        ....................
        ....................
```

```solidity
   function getCodeSize(uint256 _input) external view override returns (uint256 codeSize) {
        // We consider the account bytecode size of the last 20 bytes of the input, because
        // according to the spec "If EXTCODESIZE of A is X, then EXTCODESIZE of A + 2**160 is X".
        address account = address(uint160(_input));
        bytes32 codeHash = getRawCodeHash(account);
        .................
        .................
```

## 5. Smart Contract Wallets Cannot Withdraw WETH

The **WETH9** implementation [uses transfer](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/dev-contracts/WETH9.sol#L30) to unwrap **WETH** to **ETH** for a **msg.sender**. If users attempt to withdraw funds using a Smart Wallet that has any extra logic on the receive method, the transaction will run out of gas and fail. Users would then need to transfer the **WETH** to an **EOA** in order to unwrap their funds.

Consider using `address.call{value: amount}("")` or the [sendValue function of the OpenZeppelin Address](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v4.9.3/contracts/utils/Address.sol#L64) library to provide them with enough gas to handle additional logic, as this is the [recommended method to use](https://solidity-by-example.org/sending-ether/). Thereby make sure to protect against reentrancy by following the check-effects-interactions pattern or adding a reentrancy guard.

### Recommended Mitigation Steps

```diff
-        payable(msg.sender).transfer(wad);
+        (bool success, ) = msg.sender.call{value:wad}("");
+        require(success, "withdraw ETH failed");
```

## 6. Address Identification issue in TransactionHelper:isEthToken() Function.

The [TransactionHelper.isEthToken()](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L94-L96) function should convert the input to an address and compare it to the **ETH_TOKEN_SYSTEM_CONTRACT**. In the current implementation, a value of **2^161 + ETH_TOKEN_SYSTEM_CONTRACT** would not be considered as an **ethToken**. However, when converted to **uint160** and an address, it would be incorrectly identified as the **ETH_TOKEN_SYSTEM_CONTRACT**. Any logic that relies on the return value of this function could be vulnerable, as it would be possible to provide a **uint256(Input1)** that bypasses the **!isEthToken(Input1)** check, even though **uint160(input1)** represents an **ETH token**.

```solidity
    function isEthToken(uint256 _addr) internal pure returns (bool) {
        return _addr == uint256(uint160(address(ETH_TOKEN_SYSTEM_CONTRACT))) || _addr == 0;
    }
```

## 7. Clarify in Documentation: L2EthToken:transferFromTo() Does Not Trigger Callbacks

[L2EthToken.transferFromTo](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L25C4-L32C27) allows certain **system contracts** to perform a direct **ETH transfer** that solely updates balances and does not trigger **receive calls** in any contract recipient. Mentioning in the **documentation** that this is a **'direct ETH transfer and does not activate any receive calls in the contract recipient'** makes this behavior explicit, saving time for developers and auditors.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L25C4-L32C27

## 8. Ensure that the immutableDataStorage does not accept the address zero.

Please consider incorporating a verification step in **ImmutableSimulator.setImmutable()** to check whether the input **_dest** is the **address zero**.

```solidity
    /// @notice Method used by the contract deployer to store the immutables for an account
    /// @param _dest The address which to store the immutables for.
    /// @param _immutables The list of the immutables.
    function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override {
        require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "Callable only by the deployer system contract");
        unchecked {
            uint256 immutablesLength = _immutables.length;
            for (uint256 i = 0; i < immutablesLength; ++i) {
                uint256 index = _immutables[i].index;
                bytes32 value = _immutables[i].value;
                immutableDataStorage[uint256(uint160(_dest))][index] = value;
            }
        }
    }
}

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ImmutableSimulator.sol#L34-L45
