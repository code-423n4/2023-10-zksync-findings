## 1. Remove redundancy code :-
In below scenario comment says `// If the proof is not empty, verify it, otherwise, skip the verification` but in reality even proof is zero it will verify because the code which there in if() statement to verify ,the same code is in outside the if() statement it will verify even the proof is zero.Solidity does not support traditional preprocessor directives like #if, #else, or #endif. So, the comments with `// #else` and `// #endif` have no effect on the actual behavior of the code. Solidity will simply ignore these comments.

**Before**
```solidity
        // We allow skipping the zkp verification for the test(net) environment
        // If the proof is not empty, verify it, otherwise, skip the verification
        if (_proof.serializedProof.length > 0) {
            bool successVerifyProof = s.verifier.verify(
                proofPublicInput,
                _proof.serializedProof,
                _proof.recursiveAggregationInput
            );
            require(successVerifyProof, "p"); // Proof verification fail
        }
        // #else //QA
        bool successVerifyProof = s.verifier.verify( //@audit redundancy code
            proofPublicInput,//@audit redundancy code
            _proof.serializedProof,//@audit redundancy code
            _proof.recursiveAggregationInput//@audit redundancy code
        );
        require(successVerifyProof, "p"); // Proof verification fail
        // #endif
```

**After**
```solidity
// We allow skipping the zkp verification for the test(net) environment
        // If the proof is not empty, verify it, otherwise, skip the verification
        if (_proof.serializedProof.length > 0) {
            bool successVerifyProof = s.verifier.verify(
                proofPublicInput,
                _proof.serializedProof,
                _proof.recursiveAggregationInput
            );
            require(successVerifyProof, "p"); // Proof verification fail
        }
        // #else //QA
       //@audit changed here . We removed the redundancy code to skip the verification if proof is empty .
        // #endif
```

Please see `//@audit changed here ` comment in above code snippet .

Code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L362C1-L368C69

## 2 . Refactor the require statement :-

In below code snippet we can agree the first require statement that total committed batches should be greater than `newLastBatch` . In second require statement the comment says`// Already executed batches cannot be reverted` but the condition in second require statement anticipates even equal number to `s.totalBatchesExecuted` this may break the invariant it means already executed batches can also be reverted .

**Before**
```solidity
    function revertBatches(uint256 _newLastBatch) external nonReentrant onlyValidator {
        require(s.totalBatchesCommitted > _newLastBatch, "v1"); // The last committed batch is less than new last batch
        require(_newLastBatch >= s.totalBatchesExecuted, "v2"); //@audit // Already executed batches cannot be reverted
```


**After**
```solidity
function revertBatches(uint256 _newLastBatch) external nonReentrant onlyValidator {
        require(s.totalBatchesCommitted > _newLastBatch, "v1"); // The last committed batch is less than new last batch
        require(_newLastBatch > s.totalBatchesExecuted, "v2"); //@audit changed here // Already executed batches cannot be reverted

```

Please look into `//@audit changed here` comment in above comment .
code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L399C1-L401C1

## 3 . Constant name should be capitalized SNAKE_CASE

code snippet :-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24

## 4. We can directly define the `_depositSender` as msg.sender .

In below scenario we can directly define the `_depositSender` as msg.sender who have right to claim their failed deposit . In line [188](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L188) and [209](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L209) we can observe, the verifydeposit() function was called by msg.sender argument and saves the deposited amount of msg.sender instead of `_depositSender` or `depositer`. Therefore in the claimFailedDeposit() function we can directly call the `_verifyDepositLimit()` , delete the deposited amount and safeTransfer() to `msg.sender` argument only instead of `_depositSender` .This will ensure that only the msg.sender who has the failed deposit can claim it, and not a third party. This will help to build the privacy protection in users' perception . 

**Before**
```solidity
 function claimFailedDeposit(
        address _depositSender, //@audit
        address _l1Token,
        bytes32 _l2TxHash,
        uint256 _l2BatchNumber,
        uint256 _l2MessageIndex,
        uint16 _l2TxNumberInBatch,
        bytes32[] calldata _merkleProof
    ) external nonReentrant senderCanCallFunction(allowList) {
        bool proofValid = zkSync.proveL1ToL2TransactionStatus(
            _l2TxHash,
            _l2BatchNumber,
            _l2MessageIndex,
            _l2TxNumberInBatch,
            _merkleProof,
            TxStatus.Failure
        );
        require(proofValid, "yn");

        uint256 amount = depositAmount[_depositSender][_l1Token][_l2TxHash];//@audit
        require(amount > 0, "y1");

        // Change the total deposited amount by the user
        _verifyDepositLimit(_l1Token, _depositSender, amount, true);//@audit

        delete depositAmount[_depositSender][_l1Token][_l2TxHash];//@audit
        // Withdraw funds
        IERC20(_l1Token).safeTransfer(_depositSender, amount);//@audit

        emit ClaimedFailedDeposit(_depositSender, _l1Token, amount);//@audit

```

**After**

```solidity
 function claimFailedDeposit(
        //address _depositSender, //@audit changed here
        address _l1Token,
        bytes32 _l2TxHash,
        uint256 _l2BatchNumber,
        uint256 _l2MessageIndex,
        uint16 _l2TxNumberInBatch,
        bytes32[] calldata _merkleProof
    ) external nonReentrant senderCanCallFunction(allowList) {
        bool proofValid = zkSync.proveL1ToL2TransactionStatus(
            _l2TxHash,
            _l2BatchNumber,
            _l2MessageIndex,
            _l2TxNumberInBatch,
            _merkleProof,
            TxStatus.Failure
        );
        require(proofValid, "yn");

        uint256 amount = depositAmount[msg.sender][_l1Token][_l2TxHash];//@audit changed here
        require(amount > 0, "y1");

        // Change the total deposited amount by the user
        _verifyDepositLimit(_l1Token, msg.sender, amount, true);//@audit changed here

        delete depositAmount[msg.sender][_l1Token][_l2TxHash];//@audit changed here
        // Withdraw funds
        IERC20(_l1Token).safeTransfer(msg.sender, amount);//@audit changed here

        emit ClaimedFailedDeposit(msg.sender, _l1Token, amount);//@audit changed here

```

Please look into the `//@audit changed here` comment in above code snippet .We have already tried changing the test and Yarn cache file corresponding to the scenario described above, and all tests passed without any failures. 

Code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L255C3-L285C6

## 5. Add value to salt while deploy contract via create2() :-

Salt is used for uniqueness while creating contract . It will help to avoid storage collision while deploying contracts subsequently .It helps to predict the contract address before it is created.

Reference :-
https://docs.soliditylang.org/en/develop/control-structures.html#salted-contract-creations-create2

code snippet :-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol#L54


## 6. Add check before bridgeBurn() :-
In the following scenario, we can see that the withdraw() function first calls the bridgeBurn() function without validating the amount to be burn, after it send message to mailbox.finaliseWithdraw() even this doesn't have amount validation . 

**Before**
```solidity
    function withdraw( //Analysis 
        address _l1Receiver,
        address _l2Token,
        uint256 _amount
    ) external override {
        IL2StandardToken(_l2Token).bridgeBurn(msg.sender, _amount);
```

**After**
```solidity
    function withdraw( //Analysis 
        address _l1Receiver,
        address _l2Token,
        uint256 _amount
    ) external override {
        require(_amount != 0,"E"); //@audit changed here.
        IL2StandardToken(_l2Token).bridgeBurn(msg.sender, _amount);
```

code snippet :-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L105C1-L116C51


## 7. Refactor the if-else() statement :- 
In following scenario the else-if() statement have the condition whether _maxVirtualBlocksToCreate is equal to 0, the function just returns without further processing. So we can replace this condition in if() statement only which covers check effective pattern.

**Before**
```solidity
        if (currentVirtualL2BlockInfo.number == 0 && virtualBlockInfo.timestamp == 0) {
            uint128 currentBatchNumber = currentBatchInfo.number;

            // The virtual block is set for the first time. We can count it as 1 creation of a virtual block.
            // Note, that when setting the virtual block number we use the batch number to make a smoother upgrade from batch number to
            // the L2 block number.
            virtualBlockInfo.number = currentBatchNumber;
            // Remembering the batch number on which the upgrade to the virtual blocks has been done.
            virtualBlockUpgradeInfo.virtualBlockStartBatch = currentBatchNumber;

            require(_maxVirtualBlocksToCreate > 0, "Can't initialize the first virtual block");
            _maxVirtualBlocksToCreate -= 1;
        } else if (_maxVirtualBlocksToCreate == 0) {
            // The virtual blocks have been already initialized, but the operator didn't ask to create
            // any new virtual blocks. So we can just return.
            return;
        }
```


**After**
```solidity
 if (_maxVirtualBlocksToCreate == 0) {
            // The virtual blocks have been already initialized, but the operator didn't ask to create
            // any new virtual blocks. So we can just return.
            return;
        }else if (currentVirtualL2BlockInfo.number == 0 && virtualBlockInfo.timestamp == 0) {
            uint128 currentBatchNumber = currentBatchInfo.number;

            // The virtual block is set for the first time. We can count it as 1 creation of a virtual block.
            // Note, that when setting the virtual block number we use the batch number to make a smoother upgrade from batch number to
            // the L2 block number.
            virtualBlockInfo.number = currentBatchNumber;
            // Remembering the batch number on which the upgrade to the virtual blocks has been done.
            virtualBlockUpgradeInfo.virtualBlockStartBatch = currentBatchNumber;

            require(_maxVirtualBlocksToCreate > 0, "Can't initialize the first virtual block");
            _maxVirtualBlocksToCreate -= 1;
        }

```
In above code snippet we can see that `_maxVirtualBlocksToCreate == 0` if this condition becomes true we don't need to do other operations. Even else-if statement have the check `require(_maxVirtualBlocksToCreate > 0, "Can't initialize the first virtual block");`  which is not required now because it has been checked in if() statement only.

code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L248C1-L264C10

## 8 . Modify the require statement :-
In below scenario the condition is `numberOfL2ToL1Logs <= numberOfL2ToL1Logs` in require statement is always be true , the error message will never be revert because the condition always be true. It can be change to maintain a certain threshold to be executed and checking each time when publish pubdata on L1 . This may become serious security risk because it doesn't act as proper validation. Please look into the After section . If this check is intentional please remove it . 
**Before**
```solidity
        uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
        require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
```

**After**
```solidity
        uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
        uint256 maxAllowedLogs = 100; // Define any maximum allowed number of logs
        require(numberOfL2ToL1Logs <= maxAllowedLogs, "Too many L2->L1 logs");
```

Code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L206

## 9. Unnecessary typecast :-

In following scenario we can directly pass the value instead of type cast. In bot-report this instance mark as `Unsafe downcast`  but our intensions is differ from that. We are saying that we can directly pass the value instead of type cast.

**Before**
```solidity
 _setL2BlockHash(uint128(_l2BlockNumber - 1), correctPrevBlockHash);

```
**After**

```solidity
 _setL2BlockHash(_l2BlockNumber - 1, correctPrevBlockHash);
```

code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L226

## 10. Refactor the return statement :-

In below scenario if `recoveredAddress == address(this)` is true , then it never become zero address(0).Because the `_isValidSignature()` internal function can only be called after the deployment of DefaultAccount.sol then address(this) never gonna zero in other scenario if DefaultAccount.sol is inherited by other contract then also address(this) never become zero. However `_isValidSignature()` internal function is in DefaultAccount.sol  called by validateTransaction() external function.

**Code snippet**
```solidity

        return recoveredAddress == address(this) && recoveredAddress != address(0);
```

Recommendation :-
```solidity
//Either this
return recoveredAddress == address(this);

//Or this
return recoveredAddress != address(0);
```
code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L190C1-L190C84

## 11.Add check that nonce is not less than exist one .
In following scenario we can see user or onlySystemCall can set nonce value lower than the existing one. In line [18 & 19](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L18C1-L19C42) says `less than `minNonce` will become used in future` means through this function authority or msg.sender can set already used nonce. As we know the nature of nonce's must be unique and incrementing for each transaction but here is not seems to be like that .

Recommendation :-
```solidity
 // Ensure that the new nonce is greater than or equal to the existing one
    require(oldRawNonce + _value >= oldRawNonce, "New nonce cannot be lower than the existing one");

// Or call the isNonceUsed() inside the increaseMinNonce()
```

code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L66C1-L76C6

## 12 . Call the block.timestamp inside the loop :-

In following scenario it sets the timestamp once before the loop starts and then uses this fixed timestamp for every iteration, which means that all batches in _newBatchesData would have the same timestamp value, and they won't reflect the actual time when they were processed. As we know every seconds block.timestamp value will be changed, if the `_newBatchesData` has large amount of array every batches will be set as same block.timestamp.They won't reflect the actual time when they were processed.

**Before**
```solidity
uint32 timestamp = uint32(block.timestamp);
            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
                committedBatchTimestamp.set(_newBatchesData[i].batchNumber, timestamp);
            }
```

**After**
```solidity
uint32 timestamp ;
            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
                timestamp = uint32(block.timestamp);
                committedBatchTimestamp.set(_newBatchesData[i].batchNumber, timestamp);
            }
```

code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L84C1-L88C1

## 13. Add zero-check while increasing the minimal nonce :-
In following scenario if `_value` is zero first check will be passed and increaseMinNonce() function also passed because there is no proper input validation .

**Before**
```solidity
    function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce) {
        require(_value <= MAXIMAL_MIN_NONCE_INCREMENT, "The value for incrementing the nonce is too high");

```

**After**
```solidity
    function increaseMinNonce(uint256 _value) public onlySystemCall returns (uint256 oldMinNonce) {
        require(_value != 0 ,"Error");
        require(_value <= MAXIMAL_MIN_NONCE_INCREMENT, "The value for incrementing the nonce is too high");
```
code snippet:-
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L65C4-L66C108

Note :-
**Our finding doesn't cause any test fail**