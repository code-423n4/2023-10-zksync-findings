**The gas findings below have exact gas benchmark and do not cause any test fail**

Note :- Finding 1 and 3 reduce the code size of the contract which helps to save run time and deployment gas.

## 1. Ternary operation is not required :-
Here ternary operation is not required because `bool _isFree` argument is explicitly declared false where ever ` _requestL2Transaction()` internal function is called in `Mailbox.sol` contract . We can remove ternary operation and directly assign value to `params.l2GasPrice`. By this we can save deployment gas as well as runtime gas execution.If in case we consider the `_isFree` is true , the `baseCost` value will become zero and Even small amount of msg.value can be covered the transaction cost. This is not proper economic activity for Zksync protocol. Try to remove `_isFree` variable completely. We can observe the gas bench mark and if `_isFree` is remove lot of deployment gas will be saved .

**Before**
```solidity
     {
            params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);//@audit
            uint256 baseCost = params.l2GasPrice * _l2GasLimit;
            require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost
        }
```

**After**
```solidity
        {
            params.l2GasPrice = _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);//@audit changed here
            uint256 baseCost = params.l2GasPrice * _l2GasLimit;
            require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost
        }
```

Please look into `//@audit changed here` comment .

**Gas Benchmark**

Before

| Contract   | Method   | Min  | Max  | Avg  | calls  |
|---|---|---|---|---|---|
| MailboxFacet  | requestL2Transaction  | 121117  | 143524  | 127814  | 7  |


After

| Contract   | Method   | Min  | Max  | Avg  | calls  |
|---|---|---|---|---|---|
| MailboxFacet  | requestL2Transaction  | 121089  | 143496  | 127786  | 7  |


code snippet :-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L304



## 2. Avoid initiliaze varibles in Loops :-
We can initialize variable outside the loop and call inside loop this avoid memory cost .

Instance 1 
**Before**
```solidity
              for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
            bytes32 hashedBytecode = L2ContractHelper.hashL2Bytecode(_factoryDeps[i]);
            
```

**After**
```solidity
           bytes32 hashedBytecode;//@audit changed here
        for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) { //@audit changed here
             hashedBytecode = L2ContractHelper.hashL2Bytecode(_factoryDeps[i]);
```

**Gas Benchmark**

Before
| Contract   | Method   | Min  | Max  | Avg  | calls  |
|---|---|---|---|---|---|
| MailboxFacet  | requestL2Transaction  | 121089  | 143496  | 127786  | 7  |


After 
| Contract   | Method   | Min  | Max  | Avg  | calls  |
|---|---|---|---|---|---|
| MailboxFacet  | requestL2Transaction  | 121082  | 143489  | 127779  | 7  |




code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L395C1-L396C87


## 3. Refactor the _requestL2Transaction() to save gas :-

Here comment says `Request execution of L2 transaction from L1.` on top of the [`requestL2Transaction()`](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L218). There is two situation where requestL2Transaction() is called one in [L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L198) and another one in [L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L185) ,in both of this situation the process of zero check of `refundRecipient` and assigning to `refundRecipient` has been done. Therefore no need to repeat the same process in `_requestL2Transaction()` function ,because it's not possible that `_refundRecipient` can be zero address . Another [call](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol#L48C1-L48C23 ) from BridgeInitializationHelper.sol library by `_refundRecipient` as msg.sender so no need to check `_refundRecipient` for zero address in _requestL2Transaction() internal function . I had confirmed that from zkSync team that requestL2Transaction() only called by L1 bridge contracts so this finding is valid.



**Before**
```solidity
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
        params.expirationTimestamp = expirationTimestamp;
        params.l2GasLimit = _l2GasLimit;
        params.l2GasPricePerPubdata = _l2GasPerPubdataByteLimit;
        params.valueToMint = msg.value;
        params.refundRecipient = refundRecipient;
```

**After**

```solidity
  // If the `_refundRecipient` is not provided, we use the `_sender` as the recipient.
        //address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient; //@audit changed here
        // If the `_refundRecipient` is a smart contract, we apply the L1 to L2 alias to prevent foot guns.
        if (_refundRecipient.code.length > 0) {//@audit changed here
            _refundRecipient = AddressAliasHelper.applyL1ToL2Alias(_refundRecipient);//@audit changed here
        }

        params.sender = _sender;
        params.txId = txId;
        params.l2Value = _l2Value;
        params.contractAddressL2 = _contractAddressL2;
        params.expirationTimestamp = expirationTimestamp;
        params.l2GasLimit = _l2GasLimit;
        params.l2GasPricePerPubdata = _l2GasPerPubdataByteLimit;
        params.valueToMint = msg.value;
        params.refundRecipient = _refundRecipient;//@audit changed here
```

Please look `//@audit changed here` comment in above function.

**Process removed**
```solidity
address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient;
```


**Hardhat Gas Benchmark**
Before


| Contract   | Method   | Min  | Max  | Avg  | calls  |
|---|---|---|---|---|---|
| MailboxFacet  | requestL2Transaction  | 121082  | 143489  | 127779  | 7  |



After 

| Contract   | Method   | Min  | Max  | Avg  | calls  |
|---|---|---|---|---|---|
| MailboxFacet  | requestL2Transaction  | 121029  | 143489  | 127779  | 7  |


code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L309C1-L324C50



## 4. Use effecient way to check whether the number is even or odd :-

Bitwise AND(&) operator is efficient than modulos . Bitwise AND(&) operator set each bit 1 if only both bits are 1 . In following secanrio 1 can be fitted in uint8 for comparision . This finding doesn't cause any test fails 



EX :-  Lets take even number and odd number . 4 and 1
```
 4 & 1
 0100 & 0001 = 0
 
 5 & 1 
 0101 & 0001 = 1
 
```

**Before**
```solidity
        for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
            currentHash = (_index % 2 == 0)
```

**After**
```solidity
        for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
            //@audit check for even number
            currentHash = (_index & uint(1) == 0)

```
**HardHat Gas BenchMark**
Merkle.calculateRoot() is called in Mailbox.finalizeEthWithdrawal()

**Before**


| Contract   | Method   | Min  | Max  | Avg  | calls  |
|---|---|---|---|---|---|
| MailboxFacet  | finalizeEthWithdrawal  | -  | -  | 112962  | 7  |



**After**



| Contract   | Method   | Min  | Max  | Avg  | calls  |
|---|---|---|---|---|---|
| MailboxFacet  | finalizeEthWithdrawal  | -  | -  | 112503  | 7  |


code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L30C1-L30C44

**Scenario - 2**

Even number check

Bitwise AND(&) operator is efficient than modulos . Bitwise AND(&) operator set each bit 1 if only both bits are 1 . In following secanrio 1 can be fitted in uint8 for comparision . This finding doesn't cause any test fails.

**Before**
```solidity
        require(bytecodeLenInWords % 2 == 1, "ps");

```

**After**
```solidity
        require(bytecodeLenInWords & uint8(1) == 1, "ps");
```

require((_bytecode.length & uint(31)) == 0, "pq");
 
**Gas benchMarck**
L2ContractHelper.sol is a library which become part of the deployment of Mailbox.sol contract's bytecode we don't need explicitly deploy libraries , that's why we represent the deployment gas of Mailbox.sol's before and after gas changes. The mentioned code snippet is called by requestL2Transaction() function of mailbox contract.

code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L27


## 5. Use Do-while loop To save deployment Gas :-

Instance - 1 
**Before**
```solidity
      for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
            address facetAddr = ds.facets[i];
            Diamond.FacetToSelectors memory facetToSelectors = 
                ds.facetToSelectors[facetAddr];

            result[i] = Facet(facetAddr, facetToSelectors.selectors);
        }

```

**After**
```solidity
         uint256 i ;
         do{
            address facetAddr = ds.facets[i];
            Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];

            result[i] = Facet(facetAddr, facetToSelectors.selectors);
            i = i.uncheckedInc();
         }while(i < facetsLen);
```

code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L182C1-L187C10

Scenario - 2 

**Before**
```solidity
for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
            _lastCommittedBatchData = _commitOneBatch(_lastCommittedBatchData, _newBatchesData[i], bytes32(0));

            s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData);
            emit BlockCommit(
                _lastCommittedBatchData.batchNumber,
                _lastCommittedBatchData.batchHash,
                _lastCommittedBatchData.commitment
            );
        }

```

**After**
```solidity
uint256 i;
        do{
             _lastCommittedBatchData = _commitOneBatch(_lastCommittedBatchData, _newBatchesData[i], bytes32(0));

            s.storedBatchHashes[_lastCommittedBatchData.batchNumber] = _hashStoredBatchInfo(_lastCommittedBatchData);
            emit BlockCommit(
                _lastCommittedBatchData.batchNumber,
                _lastCommittedBatchData.batchHash,
                _lastCommittedBatchData.commitment
            );
            i = i.uncheckedInc();
        }while(i < _newBatchesData.length);
```


code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L209C8-L218C10
