## 1. Remove redundancy code :-
In below scenario comment says ` // If the proof is not empty, verify it, otherwise, skip the verification` but in reality even proof is zero it will verify because the code which there in if() statement to verify the same code is in outside the if() statement it will verify even the proof is zero

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

**Our finding doesn't cause any test fail**