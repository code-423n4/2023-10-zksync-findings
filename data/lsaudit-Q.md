# [QA-1] Unsafe addition in `unchecked` block may overflow in `NonceHolder.sol`

[File: system-contracts/contracts/NonceHolder.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L71-L73)
```
 unchecked {
            rawNonces[addressAsKey] = (oldRawNonce + _value);
        }

```

It's important to notice that parameter `_value` is passed by the user. This means, that calculating `oldRawNonce + _value` in `unchecked` block is not safe - since this value may overflow the new nonce.
The function is public, but has `onlySystemCall` modifier - thus the severity had been decreased to QA only. Nonetheless, performing calculations in `unchecked` blocks, where one of the argument is taken from the function's parameters is bad security practice and should be avoided.

The only protection from overflow is at line 66: `require(_value <= MAXIMAL_MIN_NONCE_INCREMENT, "The value for incrementing the nonce is too high");`
This requires, that `_value` cannot exceed `MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32`. However, this check does not stop from calling `increaseMinNonce(MAXIMAL_MIN_NONCE_INCREMENT)` multiple of times, until it will actually overflow. It's recommended to add a check for that overflow.


# [QA-2] Missing checks for `address(0)` when updating address state variables
This instance was not detected during bot-race
[File: ethereum/contracts/governance/Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L256-L259)
```
 function updateSecurityCouncil(address _newSecurityCouncil) external onlySelf {
        emit ChangeSecurityCouncil(securityCouncil, _newSecurityCouncil);
        securityCouncil = _newSecurityCouncil;
    }
```
There's no verification for `address(0)`, thus `securityCouncil` can be set to `address(0)`.

# [QA-3] Consider adding `address verifyingContract` to `EIP721_DOMAIN_TYPEHASH` in `TransactionHelper.sol`

According to EIP-712 documentation:

```
https://eips.ethereum.org/EIPS/eip-712#definition-of-domainseparator

address verifyingContract the address of the contract that will verify the signature. The user-agent may do contract specific phishing prevention.
```

Having `verifyingContract` in EIP721_DOMAIN_TYPEHASH - is considered as a protection from phishing attacks. 

[File: system-contracts/contracts/libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#L82)
```
bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId)");
```

can be changed to:

```
bytes32 constant EIP712_DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
```

Changing `EIP712_DOMAIN_TYPEHASH` means that additional modification of code-base has to be done.

In most cases, `verifyingContract` is just `address(this)`. This means, that below code:

[File: system-contracts/contracts/libraries/TransactionHelper.so](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#L138-140)
```
        bytes32 domainSeparator = keccak256(
            abi.encode(EIP712_DOMAIN_TYPEHASH, keccak256("zkSync"), keccak256("2"), block.chainid)
        );
```

needs to be changed to:

```
        bytes32 domainSeparator = keccak256(
            abi.encode(EIP712_DOMAIN_TYPEHASH, keccak256("zkSync"), keccak256("2"), block.chainid, address(this))
        );
```


# [QA-4] Violation of EIP-712 in `TransactionHelper.sol`
According to EIP-712, the encoded EIP-712 hash of the struct must include every member field of that struct, however, in the current implementation, `transaction.signature` is missing:

[File: system-contracts/contracts/libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#L118)
```
bytes32 structHash = keccak256(
            abi.encode(
                EIP712_TRANSACTION_TYPE_HASH,
                _transaction.txType,
                _transaction.from,
                _transaction.to,
                _transaction.gasLimit,
                _transaction.gasPerPubdataByteLimit,
                _transaction.maxFeePerGas,
                _transaction.maxPriorityFeePerGas,
                _transaction.paymaster,
                _transaction.nonce,
                _transaction.value,
                EfficientCall.keccak(_transaction.data),
                keccak256(abi.encodePacked(_transaction.factoryDeps)),
                EfficientCall.keccak(_transaction.paymasterInput)
            )
        );
```

# [QA-5] Functions in `Governance.sol` do not check if new values are the same as current state

[File: ethereum/contracts/governance/Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L249-L259)
```
    function updateDelay(uint256 _newDelay) external onlySelf {
        emit ChangeMinDelay(minDelay, _newDelay);
        minDelay = _newDelay;
    }

    /// @dev Updates the address of the security council.
    /// @param _newSecurityCouncil The address of the new security council.
    function updateSecurityCouncil(address _newSecurityCouncil) external onlySelf {
        emit ChangeSecurityCouncil(securityCouncil, _newSecurityCouncil);
        securityCouncil = _newSecurityCouncil;
    }
```

`updateDelay()` and `updateSecurityCouncil()` does not check if input parameter is the same as the current state of the variable which is being updated. If function is being called with the same value as the current state - even though there's no change - function  will success and emit an event that the change has been made.
Check `_newDelay` and `_newSecurityCouncil` is not equal to current state (`minDelay` and `securityCouncil`).


# [QA-6] Incorrect type of `blockGasLimit` in `SystemContext.sol`

EVM defines `gasLimit` as uint64:

```
https://github.com/ethereum/go-ethereum/blob/c1d5a012ea4b824e902db14e47bf147d727c2657/core/types/tx_legacy.go#L30

Gas      uint64          // gas limit
```

However, the current implementation of zkEVM implements `blockGasLimit` as uint256:

[File: system-contracts/contracts/SystemContext.sol#L37](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L37)
```
    uint256 public blockGasLimit = type(uint32).max;
```

There shoudn't be any inconsistency between variable datatypes. The variable type should be the same as the EVM, so `uint64`.


# [QA-7] `L1ERC20Bridge` won't work with rebasing tokens

There are some ERC-20 tokens which make arbitrary balance modifications outside of transfer. See this [link](https://github.com/d-xo/weird-erc20#balance-modifications-outside-of-transfers-rebasingairdrops) for further reference.

The `L1ERC20Bridge` saves the amount which user deposited in `depositAmount` mapping. It is done, in case deposit failed on L2:

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L208-L209)
```
        // Save the deposited amount to claim funds on L1 if the deposit failed on L2
        depositAmount[msg.sender][_l1Token][l2TxHash] = amount;
```

When deposit fails, user can call `claimFailedDeposit()` function, to withdraw funds from the initiated deposit, that failed when finalizing on L2.
However, when token performs a rebase operation (token lowering its amount) before user calls `claimFailedDeposit()` - the user won't be able to claim that tokens.

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#LL274-L282)
```
        uint256 amount = depositAmount[_depositSender][_l1Token][_l2TxHash];
        require(amount > 0, "y1");

        // Change the total deposited amount by the user
        _verifyDepositLimit(_l1Token, _depositSender, amount, true);

        delete depositAmount[_depositSender][_l1Token][_l2TxHash];
        // Withdraw funds
        IERC20(_l1Token).safeTransfer(_depositSender, amount);
```

To fix this issue, the most reliable way would be to add `amount` parameter to `claimFailedDeposit()` function, and let the user decide how many tokens he wants to withdraw. He should be able to withdraw amount which is the same or lower (in case of rebase) as the cached deposited amount in `depositAmount`.


# [QA-8] `getZkSyncMeta()` from `SystemContractHelper.sol` does not include some fields from `ZkSyncMeta`

The `ZkSyncMeta` struct is defined as below:

[File: system-contracts/contracts/libraries/SystemContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L42-L49)
```
struct ZkSyncMeta {
    uint32 gasPerPubdataByte;
    uint32 heapSize;
    uint32 auxHeapSize;
    uint8 shardId;
    uint8 callerShardId;
    uint8 codeShardId;
}
```

Function `getZkSyncMeta()` from `SystemContractHelper.sol` is defined as below:

[File: system-contracts/contracts/libraries/SystemContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L297-303)
```
    function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) {
        uint256 metaPacked = getZkSyncMetaBytes();
        meta.gasPerPubdataByte = getGasPerPubdataByteFromMeta(metaPacked);
        meta.shardId = getShardIdFromMeta(metaPacked);
        meta.callerShardId = getCallerShardIdFromMeta(metaPacked);
        meta.codeShardId = getCodeShardIdFromMeta(metaPacked);
    }
```

As it's demonstrated above, it does not include `auxHeapSize` and `heapSize` fields, which are defined in `ZkSyncMeta`.
To fix this issue, add additional line to this function:

```
meta.heapSize = getHeapSizeFromMeta(metaPacked);
meta.auxHeapSize = getAuxHeapSizeFromMeta(metaPacked);
```


# [QA-9] Lack of cleaning of variables before `call` in inline assembler

[File: system-contracts/contracts/libraries/EfficientCall.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L203)
```
 assembly {
            // Clearing values before usage in assembly, since Solidity
            // doesn't do it by default
            _whoToMimic := and(_whoToMimic, cleanupMask)
``` 

Above code demonstrates how local variables should be cleaned before usage in inline assembly.
However, there are multiple of lines in `EfficientCall.sol` where it's not done. All instances are related to `_address` variable.

```
EfficientCall.sol

135:            assembly {
136:                success := call(_address, callAddr, 0, 0, 0xFFFF, 0, 0)   

148:     assembly {
149:                    success := call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)

163:            assembly {
164:            success := staticcall(_address, callAddr, 0, 0xFFFF, 0, 0)

177:      assembly {
178:            success := delegatecall(_address, callAddr, 0, 0xFFFF, 0, 0)

208:                success := call(_address, callAddr, 0, 0, _whoToMimic, 0, 0)
```

# [QA-10] Lack of 0-value check in `NonceHolder.sol`

[File: system-contracts/contracts/NonceHolder.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L65)
```
    function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce) {
        require(_value <= MAXIMAL_MIN_NONCE_INCREMENT, "The value for incrementing the nonce is too high");
```

`increaseMinNonce()` does not verify if `_value > 0`. This is important check, otherwise, it would be possible to call `increaseMinNonce(0)` which actually won't increase the nonce.


# [QA-11] `processPaymasterInput()` may revert for some ERC-20 tokens

There are several popular [tokens](https://github.com/d-xo/weird-erc20#revert-on-large-approvals--transfers) (e.g. COMP, UNI). which revert when the value passed to `approve()` is greater than `type(uint96).max`.
The contract won't be able to work with them. This issue affects the library. In my opinion, library functions should be implemented in a way which takes care of every edge-case. The number of supported tokens shoudn't be limited by the implementation of library function.


[File: system-contracts/contracts/libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#L374-L384)
```
 (address token, uint256 minAllowance) = abi.decode(_transaction.paymasterInput[4:68], (address, uint256));
            address paymaster = address(uint160(_transaction.paymaster));

            uint256 currentAllowance = IERC20(token).allowance(address(this), paymaster);
            if (currentAllowance < minAllowance) {
                // Some tokens, e.g. USDT require that the allowance is firsty set to zero
                // and only then updated to the new value.

                IERC20(token).safeApprove(paymaster, 0);
                IERC20(token).safeApprove(paymaster, minAllowance);
            }
```

`(address token, uint256 minAllowance) = abi.decode(_transaction.paymasterInput[4:68], (address, uint256));` - this line suggests, that `minAllowance` is `abi.decoded` as `uint256`, thus its value can be greater than `type(uint96).max`.

If that's the case (`minAllowance` > `type(uint96).max`) and if `token` is a token which reverts when the value passed to `approve()` is greater than `type(uint96).max`, then:
`IERC20(token).safeApprove(paymaster, minAllowance);` - this line will revert

# [QA-12] Lack of address(0) check in `L2ERC20Bridge` means that `finalizeDeposit()` will mint tokens to address(0)

There's no check if `_l2Receiver` is address(0). This implies, that tokens will be minted to `address(0)`.
[File: contracts/zksync/contracts/bridge/L2ERC20Bridge.so](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L63-L88)
```
IL2StandardToken(expectedL2Token).bridgeMint(_l2Receiver, _amount);
```



# [QA-13] `storedBatchHash()` might return batch which was reverted by `Executor`'s `revertBatches()`

`Executor` provides the ability to `revertBatches()`:

[File: code/contracts/ethereum/contracts/zksync/facets/Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L398)
```
    function revertBatches(uint256 _newLastBatch) external nonReentrant onlyValidator {
        require(s.totalBatchesCommitted > _newLastBatch, "v1"); // The last committed batch is less than new last batch
        require(_newLastBatch >= s.totalBatchesExecuted, "v2"); // Already executed batches cannot be reverted

        if (_newLastBatch < s.totalBatchesVerified) {
            s.totalBatchesVerified = _newLastBatch;
        }
        s.totalBatchesCommitted = _newLastBatch;

        // Reset the batch number of the executed system contracts upgrade transaction if the batch
        // where the system contracts upgrade was committed is among the reverted batches.
        if (s.l2SystemContractsUpgradeBatchNumber > _newLastBatch) {
            delete s.l2SystemContractsUpgradeBatchNumber;
        }

```

However, function `storedBatchHash()` does not take into consideration that batch might had been reverted:

[File: contracts/ethereum/contracts/zksync/facets/Getters.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L90)
```
    function storedBatchHash(uint256 _batchNumber) external view returns (bytes32) {
        return s.storedBatchHashes[_batchNumber];
    }
```

This behavior may lead to confusion, since it gives the user (who called `storedBatchHash()` on reverted batch), the impression that that batch is still valid.
There should be an additional condition in `storedBatchHash()`, which verifies:
```
if (_batchNumber > s.totalBatchesCommitted )
```
before returning the batch number.

# [QA-14] `DiamondProxy` does not check if contract exists

[File: ethereum/contracts/zksync/DiamondProxy.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L20)
```
  require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
        // Get facet from function selector
        Diamond.SelectorToFacet memory facet = diamondStorage.selectorToFacet[msg.sig];
        address facetAddress = facet.facetAddress;

        require(facetAddress != address(0), "F"); // Proxy has no facet for this selector
        require(!diamondStorage.isFrozen || !facet.isFreezable, "q1"); // Facet is frozen
```
Whenever proxy delegates to a address which is not contract, the fallback will return success, which will lead to a silent failure.
In the current implementation, both faces and it's selectors are added in the `diamondCut`, thus having this scenario is not very realistic - thus I've degraded the severity to Low. Nonetheless, it's a good security practice to  check for contract existance before delegatecall to that address.
Our recommendation is to implement a check which verifies contract's code, before calling it.

# [QA-15] Lack of input validation in `AccountCodeStorage.sol` may lead to overflow

[File: system-contracts/contracts/AccountCodeStorage.so](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/AccountCodeStorage.sol#L117)
```
function getCodeSize(uint256 _input) external view override returns (uint256 codeSize) {
(...)
        address account = address(uint160(_input));
        bytes32 codeHash = getRawCodeHash(account);
```

[File: system-contracts/contracts/AccountCodeStorage.so](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/AccountCodeStorage.sol#L89)
```
    function getCodeHash(uint256 _input) external view override returns (bytes32) {
(...)
        address account = address(uint160(_input));
```

Both functions will return incorrect values for inputs greater than the max value of `uint160`. E.g. `type(uint160).max + X` will return `X`. It's mandatory to enforce that `_input` is not greater than `type(uint160).max`.


# [QA-16] Incorrect assumption may lead to revert in `_burnMsgValue()` in `system-contracts/contracts/L2EthToken.sol`

[File: system-contracts/contracts/L2EthToken.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L2EthToken.sol#L96-109)
```
 /// NOTE: Since this contract holds the mapping of all ether balances of the system,
    /// the sent `msg.value` is added to the `this` balance before the call.
    /// So the balance of `address(this)` is always bigger or equal to the `msg.value`!
    function _burnMsgValue() internal returns (uint256 amount) {
        amount = msg.value;

        // Silent burning of the ether
        unchecked {
            // This is safe, since this contract holds the ether balances, and if user
            // send a `msg.value` it will be added to the contract (`this`) balance.
            balance[address(this)] -= amount;
            totalSupply -= amount;
        }
    }
```

Even though: "the sent `msg.value` is added to the `this` balance before the call", the `totalSupply` is not affected by `amount`. This means that above assumption is not true, because `totalSupply` may be smaller than `amount`, which will lead to revert.



# [QA-17] The current gas price might be set to 0 in `SystemContext`

There's missing 0-value check when assigning value to a state variable. This implies that it's possible to set gas price to 0. Our recommendation is to implement additional `require` which won't let setting `_gasPrice` to 0: `require(_gasPrice !=0, "gas price should be > 0")`.

[File: system-contracts/contracts/SystemContext.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L93-L95)
```
    function setGasPrice(uint256 _gasPrice) external onlyCallFromBootloader {
        gasPrice = _gasPrice;
    }
```

# [QA-18] `MsgValueSimulator` ignores `MAX_MSG_VALUE`

The `value` is defined as `uint256`:

[File: system-contracts/contracts/MsgValueSimulator.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/MsgValueSimulator.sol#L35)
```
 fallback(bytes calldata _data) external onlySystemCall returns (bytes memory) {
        (uint256 value, bool isSystemCall, address to) = _getAbiParams();
```

while the `MAX_MSG_VALUE` - as:

[File: system-contracts/contracts/Constants.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Constants.sol#L73)
```
uint256 constant MAX_MSG_VALUE = 2 ** 128 - 1;
```

This suggests that scenario where `value > MAX_MSG_VALUE` may occur (although, it's very unlikely, thus severity has been degraded to QA. The total supply of ETH is not that big).
However, the implementation of `fallback` does not take into consideration that scenario. Our recommendation is to add additonal check in `MsgValueSimulator.sol` - it should revert when `value` is greater than `MAX_MSG_VALUE`.

The only mechanisms which enforces that `value` is less than  `2**128-1` is line 55: `SystemContractHelper.setValueForNextFarCall(Utils.safeCastToU128(value));`, however, if protocol will be redeployed with any different value for `MAX_MSG_VALUE` - this line of code won't be sufficient. It's recommended to explicitly enforce `value <= MAX_MSG_VALUE`.


# [N-1] In `ContractDeployer.sol` - ensure that new value is different than the current one

[File: system-contracts/contracts/ContractDeployer.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L62)
```
 function updateAccountVersion(AccountAbstractionVersion _version) external onlySystemCall {
        accountInfo[msg.sender].supportedAAVersion = _version;

        emit AccountVersionUpdated(msg.sender, _version);
    }
```

There's no verification that `_version` is different than `accountInfo[msg.sender].supportedAAVersion`. Whenever there's a `updateAccountVersion()` call with `_version` which is already set, the event `AccountVersionUpdated()` will be emitted. This may be misleading (especially for critical operations, such as updating account version) - because, the version won't be changed (it still will be the same). Whenever calling `updateAccountVersion()`, make sure it updates `accountInfo[msg.sender].supportedAAVersion` with new version.


# [N-2] Update name of `updateNonceOrdering` to better reflect its usage

[Fiie: system-contracts/contracts/ContractDeployer.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L68-L84)
```
    /// @notice Updates the nonce ordering of the account. Currently,
    /// it only allows changes from sequential to arbitrary ordering.
    /// @param _nonceOrdering The new nonce ordering to use.
    function updateNonceOrdering(AccountNonceOrdering _nonceOrdering) external onlySystemCall {
```

According to NatSpec and function implementation - it's possible to change the ordering from sequential to arbitrary only. This implies, that when ordering will be changed - there's no way to change it back.
This behavior implies two suggestions about code refactoring:
* There's no need to have `_nonceOrdering` parameter, thus there's only one possible value for is - `AccountNonceOrdering.Arbitrary`
* The name of the function should be changed to reflect its intent, e.g. `updateNonceOrderingToArbitrary()`

# [N-3] In `NonceHolder.sol` - function `getRawNonce()` can be used inside more functions

Function `getRawNonce()` is defined as below:
[File: system-contracts/contracts/NonceHolder.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L57-L60)
```
    function getRawNonce(address _address) public view returns (uint256) {
        uint256 addressAsKey = uint256(uint160(_address));
        return rawNonces[addressAsKey];
    }
```

However, multiple of functions do not use `getRawNonce()`, e.g.:

```
 function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce) {
        require(_value <= MAXIMAL_MIN_NONCE_INCREMENT, "The value for incrementing the nonce is too high");

        uint256 addressAsKey = uint256(uint160(msg.sender));
        uint256 oldRawNonce = rawNonces[addressAsKey];
```

can be changed to:

```
 function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce) {
        require(_value <= MAXIMAL_MIN_NONCE_INCREMENT, "The value for incrementing the nonce is too high");

        uint256 oldRawNonce = getRawNonce(msg.sender);
```

Similarly:

```
function incrementMinNonceIfEquals(uint256 _expectedNonce) external onlySystemCall {
        uint256 addressAsKey = uint256(uint160(msg.sender));
        uint256 oldRawNonce = rawNonces[addressAsKey];
```

can be changed to:

```
function incrementMinNonceIfEquals(uint256 _expectedNonce) external onlySystemCall {
        uint256 oldRawNonce = getRawNonce(msg.sender);
```


# [N-4] Unused code / code for local testing should be removed

[File: ethereum/contracts/zksync/DiamondInit.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L85-L87)
```
        // While this does not provide a protection in the production, it is needed for local testing
        // Length of the L2Log encoding should not be equal to the length of other L2Logs' tree nodes preimages
        assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);
```

This code is used only for local testing - as the comment section states. Moreover, `L2_TO_L1_LOG_SERIALIZE_SIZE` is a constant variable - known before compiling time, thus checking its value is not needed at all.
File [ethereum/contracts/zksync/Config.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol) defines that: `uint256 constant L2_TO_L1_LOG_SERIALIZE_SIZE = 88;` which implies that this `assert` is redundant.



# [N-5] `ContractDeployer.sol` events do not emit parameters from old values
Whenever you change a state of some critical parameter, it's good practice to emit the event with old and new value. In `ContractDeployer.sol`, only the new values are being emitted.

This issue occurs in `updateAccountVersion()` and `updateNonceOrdering()`:

[File: system-contracts/contracts/ContractDeployer.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L62-L83)
```
emit AccountVersionUpdated(msg.sender, _version);
(...)
emit AccountNonceOrderingUpdated(msg.sender, _nonceOrdering);
```

# [N-6] NatSpec does not properly describes `_setAllowList()` in `ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol`

[File: ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L232-L237)
```
    /// @notice Change the address of the allow list smart contract
    /// @param _newAllowList Allow list smart contract address
    function _setAllowList(IAllowList _newAllowList) internal {
        if (_newAllowList == IAllowList(address(0))) {
            return;
        }
```

NatSpec misses the case, that `_setAllowList` returns nothing, when `_newAllowList == IAllowList(address(0))`. The behavior of this edge-case should be described in the NatSpec. Whenever there's a call to `_setAllowList()` wit `address(0)`, function will not update Allow List - it will return silently.

# [N-7] Redundant check in `DefaultAccount.sol`

[File: system-contracts/contracts/DefaultAccount.so](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L188-L190)
```
        address recoveredAddress = ecrecover(_hash, v, r, s);

        return recoveredAddress == address(this) && recoveredAddress != address(0);
```

In function `_isValidSignature()`, when `recoveredAddress == address(this)` is true, then we are sure that `recoveredAddress != address(0)` is also true. This implies, that there's no need to perform the second check and code can be rewritten to: `return recoveredAddress == address(this)`.



# [N-8] `incrementDeploymentNonce()` from `NonceHolder.sol` doubles access checks:

[File: system-contracts/contracts/NonceHolder.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L135-L136)
```
    function incrementDeploymentNonce(address _address) external onlySystemCall returns (uint256 prevDeploymentNonce) {
        require(msg.sender == address(DEPLOYER_SYSTEM_CONTRACT), "");
```

There is no need to use `onlySystemCall` modifier, since function already enforces that `msg.sender == address(DEPLOYER_SYSTEM_CONTRACT)`.


# [N-9] In `NonceHolder.sol` - the order of getters and setters are mixed
It's a good coding practice to separate functions which set value, from functions which get values. In the `NonceHolder.sol`, there's lack of that separation:

```
    function getMinNonce(address _address) public view returns (uint256) {
    function getRawNonce(address _address) public view returns (uint256) {
    function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce) {
    function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall {
    function getValueUnderNonce(uint256 _key) public view returns (uint256) {
    function incrementMinNonceIfEquals(uint256 _expectedNonce) external onlySystemCall {
    function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {
    function incrementDeploymentNonce(address _address) external onlySystemCall returns (uint256 prevDeploymentNonce) {
    function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) {
    function validateNonceUsage(address _address, uint256 _key, bool _shouldBeUsed) external view {
    function _splitRawNonce(uint256 _rawMinNonce) internal pure returns (uint256 deploymentNonce, uint256 minNonce) {
```
Reorganize the code, so that setters are being first, and below them - getters.

# [N-10] Incorrect comment in `SystemContext` about `baseFee`

In `bootloader.yul`, we can check, that `baseFee` is being calculated dynamically: 

[File: system-contracts/bootloader/bootloader.yul](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L36)
```
baseFee := max(
                    fairL2GasPrice,
                    ceilDiv(pubdataBytePriceETH, MAX_L2_GAS_PER_PUBDATA())
                )
```

However, the comment in `SystemContext.sol` states, it's a constant:

[File: system-contracts/contracts/SystemContext.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L47)
```
    /// @dev It is currently a constant.
    uint256 public baseFee;
```

Fix this comment, since `baseFee` should not be treated as constant.


# [N-11] The behavior of `_markBytecodeAsPublished()` may be misleading

[File: system-contracts/contracts/KnownCodesStorage.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/KnownCodesStorage.sol#L47)
```
function _markBytecodeAsPublished(bytes32 _bytecodeHash, bool _shouldSendToL1) internal {
        if (getMarker(_bytecodeHash) == 0) {
```

The name of the function and its NatSpec suggests that it is used to mark a single bytecode hash as known. The current implementation shows, that this marking occurs only when `(getMarker(_bytecodeHash) == 0)`. Otherwise, function does nothing. Based on that behavior, it's hard to distinguish if function marked a single bytecode hash as known, or not. The only way to check that, would be observing if that function emitted `MarkedAsKnown` event. However, there should be some additional (other than observing off-chain events) to distinguish if bytecode hash was marked or not.
Our recommendation is to re-write a function in a way it will be returning bool result - `true`, when it marked a single bytecode hash as known; `false` - otherwise.



# [N-12] Incorrect comment in `TransactionHelper.sol`

[File: system-contracts/contracts/libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#L19-L20)
```
/// @dev The type id of legacy transactions.
uint8 constant EIP_2930_TX_TYPE = 0x01;
```

Comment should be changed to: `/// @dev The type id of EIP2930 transactions.`


# [N-13] Improper NatSpec for `_isValidSignature()` in `DefaultAccount.sol`

[File: system-contracts/contracts/DefaultAccount.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L160)
```
/// @return EIP1271_SUCCESS_RETURN_VALUE if the signaure is correct. It reverts otherwise

```
Function can also return `false`. This case is not described in the NatSpec.

`_isValidSignature()` looks like this: `return recoveredAddress == address(this) && recoveredAddress != address(0)`, which means, that above code in NatSpec should be changed to:
```
/// @return true for valid signature, false or reverts otherwise.
```


# [N-14] Some functions define named returns, but still return value via return statement

[File: system-contracts/contracts/NonceHolder.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L125-L129)
```
    function getDeploymentNonce(address _address) external view returns (uint256 deploymentNonce) {
(...)
        return deploymentNonce;
    }
```

[File: system-contracts/contracts/ContractDeployer.so](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L34-L35)
```
    /// @notice Returns information about a certain account.
    function getAccountInfo(address _address) external view returns (AccountInfo memory info) {
        return accountInfo[_address];
```


# [N-15] Inconsistency in representing hex 0x00
Across the whole contract, the hex value of 0 is represented as `0x00`. The only exception has been spotted in two instances:

[File: system-contracts/contracts/ContractDeployer.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L264-L269)
```
        require(_bytecodeHash != bytes32(0x0), "BytecodeHash cannot be zero");
(...)
            ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getCodeHash(uint256(uint160(_newAddress))) == 0x0,
```

[File: system-contracts/contracts/libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#L18)
```
uint8 constant LEGACY_TX_TYPE = 0x0;
```

Stick to one way of representing hex numbers. In this case, `0x0` should be `0x00`


# [N-16] Misleading variable naming in `SystemContext.sol`

[File: system-contracts/contracts/SystemContext.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L422-L423)
```
        (uint128 previousBatchNumber, uint128 previousBatchTimestamp) = getBatchNumberAndTimestamp();
        require(_newTimestamp > previousBatchTimestamp, "Timestamps should be incremental");
```

Function `getBatchNumberAndTimestamp()` returns current batch number and current batch timestamp, thus naming those variables as `previousBatchNumber` and `previousBatchTimestamp` may be misleading.
Even though, we're updating those variables with `_newTimestamp`, before the update, they are still current values, thus we should stick to proper naming convention.

# [N-17] Constants in `NonceHolder.sol` does not have visibility set

[File: code/system-contracts/contracts/NonceHolder.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L28-L31)
```
    uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128;
    /// The minNonce can be increased by at 2^32 at a time to prevent it from
    /// overflowing beyond 2**128.
    uint256 constant MAXIMAL_MIN_NONCE_INCREMENT = 2 ** 32;
```

It's bad coding practice to avoid explicitly defining visibility.

# [N-18] Define constants, instead of using magic numbers:

[File: system-contracts/contracts/libraries/Utils.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/Utils.sol#L87)
```
 require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words
```

It will be more readable to define constant, instead of using `2 ** 16` value. The name of the constant will  self-explain why `bytecodeLenInWords must be less than 2**16`

# [N-19] Use ternary operators to make `if/else` statements more readable

Simple `if/else` construction can be shorten by using ternary operators

[File: system-contracts/contracts/DefaultAccount.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L102-L106)
```
if (_isValidSignature(txHash, _transaction.signature)) {
            magic = ACCOUNT_VALIDATION_SUCCESS_MAGIC;
        } else {
            magic = bytes4(0);
        }
```

can be changed to:

```
magic = _isValidSignature(txHash, _transaction.signature) ? ACCOUNT_VALIDATION_SUCCESS_MAGIC : bytes4(0)
```

# [N-20] `ForceDeployment` struct can be moved from `ConstractDeployer.sol`

[File: system-contracts/contracts/ContractDeployer.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L198)
```
struct ForceDeployment {
```

It's  bad coding and design practice to have structs mixed with functions. Consider moving `struct FordeDeployment` to `system-contracts/contracts/interfaces/IContractDeployer.sol`

# [N-21] Repeated checks can be implemented as modifiers

[File: system-contracts/contracts/openzeppelin/utils/Address.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L61-L156)
```
    function sendValue(address payable recipient, uint256 amount) internal {
        require(
            address(this).balance >= amount,
            "Address: insufficient balance"
        );

(...)
   function functionCallWithValue(
        address target,
        bytes memory data,
        uint256 value,
        string memory errorMessage
    ) internal returns (bytes memory) {
        require(
            address(this).balance >= value,
            "Address: insufficient balance for call"
        );

```

In the above code section, there are two functions: `sendValue()`, `functionCallWithValue()`, which performs basically the same check. This check can be rewritten as a modifier.
For the reusability purpose - the repeated validation statements can be reimplemented as function modifiers. This will also improve the clarity of the code - since the same block of codes won't be used in multiple of functions.


# [N-22] `ReentrancyGuard.sol` contains links to non-existing webpages

[File: ethereum/contracts/common/ReentrancyGuard.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol)
```
20:      * https://blog.openzeppelin.com/reentrancy-after-istanbul/[Reentrancy After Istanbul].
```

Make sure that links in the comment section redirects to valid, existing websites.


# [N-23] Comments are not grammatically correct:

* [File: zksync/contracts/ForceDeployUpgrader.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/ForceDeployUpgrader.sol#L11C54-L11C54)

```
 /// @notice A function that performs force deploy
```

"force deploy" is not grammatically correct, it should be changed to "force deployment"

* [File: zksync/contracts/bridge/L2Weth.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L15)

```
/// - It does not have a silent fallback method and will revert if it's called for a method it hasn't implemented.
```

"hasn't implemented", should be changed to "hasn't been implementing".

* [File: system-contracts/contracts/interfaces/ISystemContext.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/interfaces/ISystemContext.sol#L17)

```
 /// @dev It will used for the L1 batch -> L2 block migration in Q3 2023 only.
```

"will used", should be changed to "will be used".



* [File: system-contracts/contracts/libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#L173)

```
 // If the length is not equal to one, then only using the length can it be encoded definitely.
```

"length can it be encoded definitely" should be changed to "length, it can be encoded definitely"

* [File: system-contracts/contracts/libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#L251)

```
 // If the length is not equal to one, then only using the length can it be encoded definitely.
```

"length can it be encoded definitely" should be changed to "length, it can be encoded definitely"

* [File: system-contracts/contracts/libraries/TransactionHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#323)

```
 // If the length is not equal to one, then only using the length can it be encoded definitely.
```

"length can it be encoded definitely" should be changed to "length, it can be encoded definitely"


* [File: system-contracts/contracts/SystemContext.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L22)
```
 /// - Store the latest 257 blocks's hashes.
```

"blocks's" should be changed to "block's".


[File: system-contracts/contracts/SystemContext.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L108)
```
// Due to virtual blocks upgrade, we'll have to use the following logic for retreiving the blockhash:
```

"retreiving" should be changed to "retriving".



* [File: system-contracts/contracts/DefaultAccount.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L96)
```
// The fact there is are enough balance for the account
```

"there is are" should be changed to "there is"

* [File: system-contracts/contracts/NonceHolder.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L18)
```
* The users can either marked a range of nonces by increasing the `minNonce`. This way all the nonces
```

"can either marked" should be changed to "can either mark".


# [N-24] Messages in `require` are not descriptive
Make sure to use more descriptive messages in `require` statements. It will be helpful in debuging reverts. Moreover, it will increase a user experience - if his/her transaction reverts, he/she will see more descriptive reason of that revert.

This issue occurs in multiple of instances:

```
./contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol:        require(_l2TokenBeacon != address(0), "nf");
./contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol:        require(_governor != address(0), "nh");
./contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol:        require(_factoryDeps.length == 3, "mk");
./contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol:        require(_amount != 0, "2T");
./contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol:        require(amount == _amount, "1T");
./contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol:        require(proofValid, "yn");
./contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol:        require(amount > 0, "y1");
./contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol:        require(!isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "pw");
./contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol:            require(success, "nq");
./contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol:        require(_l2ToL1message.length == 76, "kk");
./contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol:        require(bytes4(functionSignature) == this.finalizeWithdrawal.selector, "nt");
./contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol:            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
./contracts/ethereum/contracts/bridge/L1WethBridge.sol:            require(success, "vq");
./contracts/ethereum/contracts/bridge/L1WethBridge.sol:        require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");
./contracts/ethereum/contracts/common/AllowList.sol:        require(targetsLength == _accessModes.length, "yg");
./contracts/ethereum/contracts/common/AllowList.sol:        require(callersLength == _targets.length, "yw");
./contracts/ethereum/contracts/common/AllowList.sol:        require(callersLength == _functionSigs.length, "yx");
./contracts/ethereum/contracts/common/AllowList.sol:        require(callersLength == _enables.length, "yy");
./contracts/ethereum/contracts/common/AllowListed.sol:            require(_allowList.canCall(msg.sender, address(this), msg.sig), "nr");
./contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol:        require(_bytecode.length % 32 == 0, "pq");
./contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol:        require(bytecodeLenInWords < 2**16, "pp");
./contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol:        require(bytecodeLenInWords % 2 == 1, "ps");
./contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol:        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");
./contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol:        require(_bytecodeLen(_bytecodeHash) % 2 == 1, "uy");
./contracts/ethereum/contracts/common/ReentrancyGuard.sol:        require(lockSlotOldValue == 0, "1B");
./contracts/ethereum/contracts/common/ReentrancyGuard.sol:        require(_status == _NOT_ENTERED, "r1");
./contracts/ethereum/contracts/zksync/DiamondInit.sol:        require(address(_initalizeData.verifier) != address(0), "vt");
./contracts/ethereum/contracts/zksync/DiamondInit.sol:        require(_initalizeData.governor != address(0), "vy");
./contracts/ethereum/contracts/zksync/DiamondInit.sol:        require(_initalizeData.admin != address(0), "hc");
./contracts/ethereum/contracts/zksync/DiamondInit.sol:        require(_initalizeData.priorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "vu");
./contracts/ethereum/contracts/zksync/DiamondProxy.sol:        require(_chainId == block.chainid, "pr");
./contracts/ethereum/contracts/zksync/DiamondProxy.sol:        require(msg.data.length >= 4 || msg.data.length == 0, "Ut");
./contracts/ethereum/contracts/zksync/DiamondProxy.sol:        require(!diamondStorage.isFrozen || !facet.isFreezable, "q1");
./contracts/ethereum/contracts/zksync/facets/Admin.sol:        require(msg.sender == pendingGovernor, "n4");
./contracts/ethereum/contracts/zksync/facets/Admin.sol:        require(msg.sender == pendingAdmin, "n4");
./contracts/ethereum/contracts/zksync/facets/Admin.sol:        require(_newPriorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "n5");
./contracts/ethereum/contracts/zksync/facets/Admin.sol:        require(!diamondStorage.isFrozen, "a9");
./contracts/ethereum/contracts/zksync/facets/Admin.sol:        require(diamondStorage.isFrozen, "a7");
./contracts/ethereum/contracts/zksync/facets/Base.sol:        require(msg.sender == s.governor, "1g");
./contracts/ethereum/contracts/zksync/facets/Base.sol:        require(s.validators[msg.sender], "1h");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:        require(expectedNumberOfLayer1Txs == _newBatch.numberOfLayer1Txs, "ta");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:        require(batchTimestamp == _expectedBatchTimestamp, "tb");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:        require(_previousBatchTimestamp < batchTimestamp, "h3");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:        require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:        require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:            require(!_checkBit(processedLogs, uint8(logKey)), "kp");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:                require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lm");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:                require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "ln");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:                require(providedL2ToL1PubdataHash == logValue, "wp");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:                require(logSender == L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, "lb");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:                require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sc");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:                require(logSender == L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, "sv");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:                require(logSender == L2_BOOTLOADER_ADDRESS, "bl");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:                require(logSender == L2_BOOTLOADER_ADDRESS, "bk");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:                require(logSender == L2_BOOTLOADER_ADDRESS, "bu");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:                require(_expectedSystemContractUpgradeTxHash == logValue, "ut");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:            require(processedLogs == 127, "b7");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:            require(processedLogs == 255, "b8");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:        require(s.l2SystemContractsUpgradeBatchNumber == 0, "ik");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:        require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:        require(s.totalBatchesCommitted > _newLastBatch, "v1");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:        require(_newLastBatch >= s.totalBatchesExecuted, "v2");
./contracts/ethereum/contracts/zksync/facets/Executor.sol:        require(_batch.systemLogs.length <= MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES, "pu");
./contracts/ethereum/contracts/zksync/facets/Getters.sol:        require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
./contracts/ethereum/contracts/zksync/facets/Mailbox.sol:        require(callSuccess, "pz");
./contracts/ethereum/contracts/zksync/facets/Mailbox.sol:        require(_batchNumber <= s.totalBatchesExecuted, "xx");
./contracts/ethereum/contracts/zksync/facets/Mailbox.sol:        require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");
./contracts/ethereum/contracts/zksync/facets/Mailbox.sol:        require(!s.isEthWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "jj");
./contracts/ethereum/contracts/zksync/facets/Mailbox.sol:        require(proofValid, "pi");
./contracts/ethereum/contracts/zksync/facets/Mailbox.sol:        require(_l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp");
./contracts/ethereum/contracts/zksync/facets/Mailbox.sol:        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
./contracts/ethereum/contracts/zksync/facets/Mailbox.sol:        require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "uj");
./contracts/ethereum/contracts/zksync/facets/Mailbox.sol:            require(msg.value >= baseCost + _l2Value, "mv");
./contracts/ethereum/contracts/zksync/facets/Mailbox.sol:        require(_message.length >= 56, "pm");
./contracts/ethereum/contracts/zksync/facets/Mailbox.sol:        require(bytes4(functionSignature) == this.finalizeEthWithdrawal.selector, "is");
./contracts/ethereum/contracts/zksync/libraries/Diamond.sol:        require(_facet == address(0), "a1");
./contracts/ethereum/contracts/zksync/libraries/Diamond.sol:            require(oldFacet.facetAddress != address(0), "a2");
./contracts/ethereum/contracts/zksync/libraries/Diamond.sol:            require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
./contracts/ethereum/contracts/zksync/libraries/Diamond.sol:            require(data.length == 32, "lp");
./contracts/ethereum/contracts/zksync/libraries/Merkle.sol:        require(pathLength > 0, "xc");
./contracts/ethereum/contracts/zksync/libraries/Merkle.sol:        require(pathLength < 256, "bt");
./contracts/ethereum/contracts/zksync/libraries/Merkle.sol:        require(_index < (1 << pathLength), "px");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(l2GasForTxBody <= _priorityTxMaxGasLimit, "ui");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(l2GasForTxBody / _transaction.gasPerPubdataByteLimit <= PRIORITY_TX_MAX_PUBDATA, "uk");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(_transaction.from <= type(uint16).max, "ua");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(_transaction.to <= type(uint160).max, "ub");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(_transaction.paymaster == 0, "uc");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(_transaction.value == 0, "ud");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(_transaction.reserved[0] == 0, "ue");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(_transaction.reserved[1] <= type(uint160).max, "uf");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(_transaction.reserved[2] == 0, "ug");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(_transaction.reserved[3] == 0, "uo");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(_transaction.signature.length == 0, "uh");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(_transaction.paymasterInput.length == 0, "ul");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(_transaction.reservedDynamic.length == 0, "um");
./contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol:        require(_totalGasLimit >= overhead, "my");
./contracts/ethereum/contracts/zksync/ValidatorTimelock.sol:        require(msg.sender == validator, "8h");
./contracts/ethereum/contracts/zksync/ValidatorTimelock.sol:                require(block.timestamp >= commitBatchTimestamp + delay, "5c");
./contracts/zksync/contracts/bridge/L2ERC20Bridge.sol:        require(_l1Bridge != address(0), "bf");
./contracts/zksync/contracts/bridge/L2ERC20Bridge.sol:        require(_l2TokenProxyBytecodeHash != bytes32(0), "df");
./contracts/zksync/contracts/bridge/L2ERC20Bridge.sol:        require(_governor != address(0), "sf");
./contracts/zksync/contracts/bridge/L2ERC20Bridge.sol:        require(AddressAliasHelper.undoL1ToL2Alias(msg.sender) == l1Bridge, "mq");
./contracts/zksync/contracts/bridge/L2ERC20Bridge.sol:            require(deployedToken == expectedL2Token, "mt");
./contracts/zksync/contracts/bridge/L2ERC20Bridge.sol:            require(currentL1Token == _l1Token, "gg");
./contracts/zksync/contracts/bridge/L2ERC20Bridge.sol:        require(l1Token != address(0), "yh");
./contracts/zksync/contracts/bridge/L2ERC20Bridge.sol:        require(success, "mk");
./contracts/zksync/contracts/bridge/L2WethBridge.sol:        require(msg.sender == l2WethAddress, "pd");
./system-contracts/contracts/libraries/Utils.sol:        require(_bytecode.length % 32 == 0, "po");
./system-contracts/contracts/libraries/Utils.sol:        require(bytecodeLenInWords < 2 ** 16, "pp");
./system-contracts/contracts/libraries/Utils.sol:        require(bytecodeLenInWords % 2 == 1, "pr");
```