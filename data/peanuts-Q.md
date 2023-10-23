### [L-01] The proving of message inclusion is checked twice when calling finalizeWithdrawal() L1WethBridge

`finalizeWithdrawal()` checks if `zkSync.isEthWithdrawalFinalized` returns true or false. If it returns true, it means that the withdrawal has already been finalized. The if clause then continues to call proveL2MessageInclusion.

```
File: L1WethBridge.sol
245:         bool alreadyFinalised = zkSync.isEthWithdrawalFinalized(_l2BatchNumber, _l2MessageIndex);
246:         if (alreadyFinalised) {
247:             // Check that the specified message was actually sent while withdrawing eth from L2.
248:             L2Message memory l2ToL1Message = L2Message({
249:                 txNumberInBatch: _l2TxNumberInBatch,
250:                 sender: L2_ETH_TOKEN_SYSTEM_CONTRACT_ADDR,
251:                 data: _message
252:             });
253:             bool success = zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);
254:             require(success, "vq");
```

However, if the transaction is already finalized, that must mean that `finalizeEthWithdrawal()` is already called in Mailbox.sol as the only way for `s.isEthWithdrawalFinalized` to return true is to call `finalizeEthWithdrawal()`. This means that ETH is already transferred into the L1 contracts. This also means that the proof is checked twice, which is not needed.

```
File: Mailbox.sol
192:     function finalizeEthWithdrawal(
193:         uint256 _l2BatchNumber,
194:         uint256 _l2MessageIndex,
195:         uint16 _l2TxNumberInBatch,
196:         bytes calldata _message,
197:         bytes32[] calldata _merkleProof
198:     ) external override nonReentrant senderCanCallFunction(s.allowList) {
199:         require(!s.isEthWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "jj");
200:
201:         L2Message memory l2ToL1Message = L2Message({
202:             txNumberInBatch: _l2TxNumberInBatch,
203:             sender: L2_ETH_TOKEN_SYSTEM_CONTRACT_ADDR,
204:             data: _message
205:         });
206:
207:         (address _l1WithdrawReceiver, uint256 _amount) = _parseL2WithdrawalMessage(_message);
208:
209:         bool proofValid = proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);
210:         require(proofValid, "pi"); // Failed to verify that withdrawal was actually initialized on L2
211:         //@audit only way for withdrawal to be finalized in the protocol
212:         s.isEthWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex] = true;
213:         _withdrawFunds(_l1WithdrawReceiver, _amount);
```

If there is another way to change EthWithdrawalFinalized to true, it means that there is a flaw in the function as `_withdrawFunds()` is not called yet. This will then make it a high severity issue. Otherwise, it will just be a low issue where the proof is checked twice.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

### [L-02] The security council can bypass the minLimit, which defeats the purpose of a proposed contract

In Governance.sol, the securitycouncil (not even the owner) can call executeInstant() to execute the scheduled operation instantly. This can be dangerous if the security council is malicious, because the owner cannot overrule the security council. In Governance.sol, there is no onlyOwner modifier, so the owner cannot change the securityCouncil at any time. Furthermore, the security council can propose an operation and call `updateSecurityCouncil()` using the `executeInstant()` function and change the address of the original securityCouncil to something else. The securityCouncil can also call updateDelay to change all delays to zero, effectively destroying the purpose of the governance contract. Not sure why the security council has more power than the owner, `executeInstant()` should be on the directive of the owner instead of the council (to reduce the centralization risk)

```
File: Governance.sol
186:     function executeInstant(Operation calldata _operation) external onlySecurityCouncil {
187:         bytes32 id = hashOperation(_operation);
188:         // Check if the predecessor operation is completed.
189:         _checkPredecessorDone(_operation.predecessor);
190:         // Ensure that the operation is in a pending state before proceeding.
191:         require(isOperationPending(id), "Operation must be pending before execution");
192:         // Execute operation.
193:         _execute(_operation.calls);
194:         // Reconfirming that the operation is still pending before execution.
195:         // This is needed to avoid unexpected reentrancy attacks of re-executing the same operation.
196:         require(isOperationPending(id), "Operation must be pending after execution");
197:         // Set operation to be done
198:         timestamps[id] = EXECUTED_PROPOSAL_TIMESTAMP;
199:         emit OperationExecuted(id);
200:     }
```

```
File: Governance.sol
256:     function updateSecurityCouncil(address _newSecurityCouncil) external onlySelf {
257:         emit ChangeSecurityCouncil(securityCouncil, _newSecurityCouncil);
258:         securityCouncil = _newSecurityCouncil;
259:     }

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L172

### [L-03] The yield from rebasing tokens will be lost during the period of transferring rebasing tokens from L1 to L2

If a user decides to transfer a rebasing token, the token will be locked inside the Bridge contract and a L2 token will be minted in the L2 side. When withdrawing from L2, the user can only withdraw as much tokens as he has from L1, and if the token does not rebase in L2, then the yield from rebasing in token 1 will be lost. For example, if a user bridges 1000 stETH to L2, waits for a week, and bridges back, there will only be 1000 stETH tokens to get back, but in that one week, the stETH would have become ~1000.xxx stETH. The yield earned from rebasing will be lost

```
File: L1ERC20Bridge.sol
314:
315:         isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex] = true;
316:         // Withdraw funds
317:         IERC20(l1Token).safeTransfer(l1Receiver, amount);

```

### [L-04] Open Todos results in operator not getting any tips

The transaction from L1 to L2 will not give the operator any tip, because the `layer2Tip` is set to zero for now. There is an open TODO to change the tip value.

```
File: Mailbox.sol
356:     function _writePriorityOp(
357:         WritePriorityOpParams memory _priorityOpParams,
358:         bytes calldata _calldata,
359:         bytes[] calldata _factoryDeps
360:     ) internal returns (bytes32 canonicalTxHash) {
361:         L2CanonicalTransaction memory transaction = _serializeL2Transaction(_priorityOpParams, _calldata, _factoryDeps);
362:
363:         bytes memory transactionEncoding = abi.encode(transaction);
364:
365:         TransactionValidator.validateL1ToL2Transaction(transaction, transactionEncoding, s.priorityTxMaxGasLimit);
366:
367:         canonicalTxHash = keccak256(transactionEncoding);
368:
369:         s.priorityQueue.pushBack(
370:             PriorityOperation({
371:                 canonicalTxHash: canonicalTxHash,
372:                 expirationTimestamp: _priorityOpParams.expirationTimestamp,
373:                 layer2Tip: uint192(0) // TODO: Restore after fee modeling will be stable. (SMA-1230)
374:             })
375:         );

```

### [L-05] `isFree` will always be false, resulting in redundant checks

There is a parameter in `_requestL2Transaction()` which is `_isFree`. This checks if the l2GasPrice is free, otherwise the gas price will be derived

```
File: Mailbox.sol
301:         // Checking that the user provided enough ether to pay for the transaction.
302:         // Using a new scope to prevent "stack too deep" error
303:         {
304:             params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);
```

The issue is that \_isFree will always be false because `_requestL2Transaction()` is only called via `requestL2Transaction()`, and the default value is always set to false.

```
File: Mailbox.sol
262:         canonicalTxHash = _requestL2Transaction(
263:             sender,
264:             _contractL2,
265:             _l2Value,
266:             _calldata,
267:             _l2GasLimit,
268:             _l2GasPerPubdataByteLimit,
269:             _factoryDeps,
> 270:             false,
271:             _refundRecipient
272:         );
273:     }
```

`params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);` This line is redundant.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L283

### [L-06] PRIORITY_EXPIRATION is always set at 0

```
File: Config.sol
44: /// @dev The amount of time in seconds the validator has to process the priority transaction
45: /// NOTE: The constant is set to zero for the Alpha release period
46: uint256 constant PRIORITY_EXPIRATION = 0 days;
```

Recommend letting the owner or the operator set the time of priority expiration.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol

### [N-01] Consider supporting fee-on-transfer tokens

The protocol specifically disallow fee-on-transfer tokens, but there are many tokens with a fee on transfer or a burn fee. It would be good if the protocol also includes fee-on-transfer tokens in the future

```
File: L1ERC20Bridge.sol
176:     function deposit(
177:         address _l2Receiver,
178:         address _l1Token,
179:         uint256 _amount,
180:         uint256 _l2TxGasLimit,
181:         uint256 _l2TxGasPerPubdataByte,
182:         address _refundRecipient
183:     ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) {
184:         require(_amount != 0, "2T"); // empty deposit amount
185:         uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
186:         require(amount == _amount, "1T"); // The token has non-standard transfer logic
187:         // verify the deposit amount is allowed
```

### [N-02] Typo in documentation

- In the [Documentation](https://era.zksync.io/docs/dev/how-to/send-transaction-l1-l2.html#step-by-step), the 'Send an L1 to L2 transaction' has 8 steps. In step 5, it says that 

> The gas price returned at step 2 as _gasPrice.
> The gas value returned at step 3 as _l2GasLimit.

The second line, instead of step 3, it should be step 4 because step 3 is getting the alias.
