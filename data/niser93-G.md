# Gas Report

# Table of Contents

[1) Gas Analyisis Description](#gas-analyisis-description)

[2) Summaries](#summaries)
- [2.1) L1 Contracts - Findings Table](#l1-contracts---findings-table)
- [2.2) L1 Contracts - Gas Savings Table](#l1-contracts---gas-savings-table)
- [2.3) L2 Contracts - Findings Table](#l2-contracts---findings-table)

[3) L1 Contracts - Findings](#l1-contracts---findings)

[4) L2 Contracts - Findings](#l2-contracts---findings)


## Gas Analyisis Description
We choose to split gas report in two parts: L1 Contracts and L2 Contracts.

We did this due to two reasons:
- It permit a better vision of ZkSync environment and gas improvements
- We were able to provide better gas analysis and reporting of L1 Contracts


In order to report gas savings of L1 contracts, we used output from `yarn test` and [HardHat Gas Report Diff](https://pypi.org/project/hardhat-gas-report-diff/)

We have also reported summary tables of findings that save gas in multiple method calls or contract deployments.

## Summaries
### L1 Contracts - Findings Table
|       |Issue  |Instances|Total Gas Saved|
|:-----:|:------|:-------:|:-------------:|
|[[GO-01](#go-01-avoid-defining-local-variable-that-are-used-once)]|Avoid defining local variable that are used once|41|209468|
|[[GO-02](#go-02-avoid-using-ternary-operator-and-let-variable-to-assume-default-value)]|Avoid using ternary operator and let variable to assume default value|1|1320|
|[[GO-03](#go-03-modify-ternary-operator-in-order-to-use--instead-of-)]|Modify ternary operator in order to use >= instead of <|1|3|
|[[GO-04](#go-04-precompute-constants-value)]|Precompute constants value|1|9283|
|[[GO-05](#go-05-using-do-while-loop-instead-of-for-loop)]|Using `do-while`-loop instead of `for`-loop|2|4101|
|[[GO-06](#go-06-remove-useless-code-from-production)]|Remove useless code from production|1|-|
|[[GO-07](#go-07-dont-overwrite-local-variable-value-if-it-will-used-anymore)]|Don't overwrite local variable value if it will used anymore|1|12|
|[[GO-08](#go-08-using-different-conditional-path)]|Using different conditional path|2|4|
|                 |                     |          |      |
| OVERALL              |                | 50 | 224107 |


### L1 Contracts - Gas Savings Table
| Method call or Contract deployment   |   Before |    After | After - Before | (After - Before) / Before |
|:-------------------------------------|---------:|---------:|---------------:|--------------------------:|
| `AdminFacetTest.executeUpgrade`      |   126156 |   126113 |            -43 |                    -0.03% |
| `AllowList.setPermissionToCall`      |    49876 |    49870 |             -6 |                    -0.01% |
| `Forwarder.forward`                  |   132309 |   132015 |           -294 |                    -0.22% |
| `L1ERC20Bridge.deposit`              |   209487 |   209179 |           -308 |                    -0.15% |
| `MailboxFacet.finalizeEthWithdrawal` |   112962 |   112804 |           -158 |                    -0.14% |
| `MailboxFacet.requestL2Transaction`  |   127814 |   127520 |           -294 |                    -0.23% |
| `ValidatorTimelock.executeBatches`   |    56949 |    56897 |            -52 |                    -0.09% |
| `AdminFacet`                         |  2502312 |  2482922 |         -19390 |                    -0.77% |
| `AdminFacetTest`                     |  2576987 |  2557592 |         -19395 |                    -0.75% |
| `AllowList`                          |  1008207 |  1007559 |           -648 |                    -0.06% |
| `ConstructorForwarder`               |   186567 |   186273 |           -294 |                    -0.16% |
| `CustomUpgradeTest`                  |  1904340 |  1891426 |         -12914 |                    -0.68% |
| `DefaultUpgrade`                     |  1893485 |  1880571 |         -12914 |                    -0.68% |
| `DiamondInit`                        |   472534 |   472522 |            -12 |                    -0.00% |
| `DiamondProxy`                       |  3006084 |  2980275 |         -25809 |                    -0.86% |
| `DiamondProxyTest`                   |   114133 |   114145 |            +12 |                    +0.01% |
| `ERC1967Proxy`                       |   571840 |   571208 |           -632 |                    -0.11% |
| `ExecutorFacet`                      |  2359401 |  2342135 |         -17266 |                    -0.73% |
| `GettersFacet`                       |   965891 |   952918 |         -12973 |                    -1.34% |
| `L1ERC20Bridge`                      |  2821104 |  2791121 |         -29983 |                    -1.06% |
| `L1WethBridge`                       |  2506089 |  2492526 |         -13563 |                    -0.54% |
| `MailboxFacet`                       |  2364542 |  2319822 |         -44720 |                    -1.89% |
| `MerkleTest`                         |   246410 |   245114 |          -1296 |                    -0.53% |
| `TransactionValidatorTest`           |   937876 |   928005 |          -9871 |                    -1.05% |
| `ValidatorTimelock`                  |   869857 |   868573 |          -1284 |                    -0.15% |
|                                      |          |          |                |                           |
| OVERALL                              | 28123212 | 27899105 |        -224107 |                    -0,80% |


### L2 Contracts - Findings Table
|       |Issue  |Instances|Total Gas Saved|
|:-----:|:------|:-------:|:-------------:|
|[[GO-09](#go-09-avoid-defining-local-variable-that-are-used-once)]|Avoid defining local variable that are used once|14|-|
|[[GO-10](#go-10-using-different-conditional-path)]|Using different conditional path|5|-|
|[[GO-11](#go-11-using--n-1-instead-of--n)]|Using `>= n-1` instead of `> n`|2|-|
|[[GO-12](#go-12-recoding-l1messengerpublishpubdataandclearstate-in-order-to-save-gas)]|Recoding L1Messenger.publishPubdataAndClearState() in order to save gas|1|-|
|[[GO-13](#go-13-avoid-computing-math-operation-at-runtime)]|Avoid computing math operation at runtime|1|-|
|[[GO-14](#go-14-checking-easy-condition-before-inside-if-else-if-else)]|Checking easy condition before inside `if-else if-else'|1|-|
|[[GO-15](#go-15-modify-ternary-operator-in-order-to-use--instead-of-)]|Modify ternary operator in order to use == instead of !=|1|-|
|[[GO-16](#go-16-use-alternative-formulation-in-order-to-avoid-not-using-and-instead-of-or-or-vice-versa)]|Use alternative formulation in order to avoid NOT using AND instead of OR or vice versa|1|-|
|                 |                     |          |      |
| OVERALL              |                | 26 | - |

## L1 Contracts - Findings

### [GO-01] Avoid defining local variable that are used once
                
Avoid defining a local variable can save gas. When code don't lose clarity, it could be better to write inline code.

*Instances:* 41

| Method call or Contract deployment   |   Before |    After | After - Before | (After - Before) / Before |
|:-------------------------------------|---------:|---------:|---------------:|--------------------------:|
| `AllowList.setPermissionToCall`      |    49876 |    49870 |             -6 |                    -0.01% |
| `Forwarder.forward`                  |   132309 |   132015 |           -294 |                    -0.22% |
| `L1ERC20Bridge.deposit`              |   209487 |   209183 |           -304 |                    -0.15% |
| `MailboxFacet.finalizeEthWithdrawal` |   112962 |   112957 |             -5 |                    -0.00% |
| `MailboxFacet.requestL2Transaction`  |   127814 |   127520 |           -294 |                    -0.23% |
| `ValidatorTimelock.executeBatches`   |    56949 |    56897 |            -52 |                    -0.09% |
| `AdminFacet`                         |  2502312 |  2482922 |         -19390 |                    -0.77% |
| `AdminFacetTest`                     |  2576987 |  2557592 |         -19395 |                    -0.75% |
| `AllowList`                          |  1008207 |  1007559 |           -648 |                    -0.06% |
| `ConstructorForwarder`               |   186567 |   186273 |           -294 |                    -0.16% |
| `CustomUpgradeTest`                  |  1904340 |  1891426 |         -12914 |                    -0.68% |
| `DefaultUpgrade`                     |  1893485 |  1880571 |         -12914 |                    -0.68% |
| `DiamondProxy`                       |  3006084 |  2980275 |         -25809 |                    -0.86% |
| `DiamondProxyTest`                   |   114133 |   114145 |            +12 |                    +0.01% |
| `ERC1967Proxy`                       |   571840 |   571208 |           -632 |                    -0.11% |
| `ExecutorFacet`                      |  2359401 |  2352746 |          -6655 |                    -0.28% |
| `GettersFacet`                       |   965891 |   952918 |         -12973 |                    -1.34% |
| `L1ERC20Bridge`                      |  2821104 |  2791121 |         -29983 |                    -1.06% |
| `L1WethBridge`                       |  2506089 |  2493714 |         -12375 |                    -0.49% |
| `MailboxFacet`                       |  2364542 |  2321154 |         -43388 |                    -1.83% |
| `TransactionValidatorTest`           |   937876 |   928005 |          -9871 |                    -1.05% |
| `ValidatorTimelock`                  |   869857 |   868573 |          -1284 |                    -0.15% |
|                                      |          |          |                |                           |
| OVERALL                              | 27278112 | 27068644 |        -209468 |                    -0,76% |


&nbsp;
***
&nbsp;


#### File: [Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol)
*Instances:* 6

```diff
File: Executor.sol

-  87          uint256 lastL2BlockTimestamp = _packedBatchAndL2BlockTimestamp & PACKED_L2_BLOCK_TIMESTAMP_MASK;
   88  
   89          // All L2 blocks have timestamps within the range of [batchTimestamp, lastL2BatchTimestamp].
   90          // So here we need to only double check that:
   91          // - The timestamp of the batch is not too small.
   92          // - The timestamp of the last L2 block is not too big.
   93          require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small
-  94          require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big
+  94          require(_packedBatchAndL2BlockTimestamp & PACKED_L2_BLOCK_TIMESTAMP_MASK <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `ExecutorFacet`         | 2359401 | 2359161 |     -240     |          -0.01%         |

*Code link:* [[87-94](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L87-L94)]

***

```diff
File: Executor.sol

   259      /// @dev Pops the priority operations from the priority queue and returns a rolling hash of operations
   260      function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {
   261          concatHash = EMPTY_STRING_KECCAK;
   262  
   263          for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
-  264              PriorityOperation memory priorityOp = s.priorityQueue.popFront();
-  265              concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
+  264              concatHash = keccak256(abi.encode(concatHash, s.priorityQueue.popFront().canonicalTxHash));
   266          }
   267      }


```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `ExecutorFacet`         | 2359401 | 2356166 |     -3235    |          -0.14%         |

*Code link:* [[259-267](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L259-L267)]


***


```diff
File: Executor.sol

   421      /// @dev Creates batch commitment from its data
   422      function _createBatchCommitment(CommitBatchInfo calldata _newBatchData, bytes32 _stateDiffHash)
   423          internal
   424          view
   425          returns (bytes32)
   426      {
-  427          bytes32 passThroughDataHash = keccak256(_batchPassThroughData(_newBatchData));
-  428          bytes32 metadataHash = keccak256(_batchMetaParameters());
-  429          bytes32 auxiliaryOutputHash = keccak256(_batchAuxiliaryOutput(_newBatchData, _stateDiffHash));
-  430  
-  431          return keccak256(abi.encode(passThroughDataHash, metadataHash, auxiliaryOutputHash));
+  427          return keccak256(
+  428              abi.encode(
+  429                  keccak256(_batchPassThroughData(_newBatchData)),
+  430                  keccak256(_batchMetaParameters()),
+  431                  keccak256(_batchAuxiliaryOutput(_newBatchData, _stateDiffHash))
+  432                  )
+  433              );
   432      }


```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `ExecutorFacet`         | 2359401 | 2357913 |     -1488    |          -0.06%         |

*Code link:* [[421-432](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L421-L432)]


***


```diff
File: Executor.sol

   448      function _batchAuxiliaryOutput(CommitBatchInfo calldata _batch, bytes32 _stateDiffHash)
   449          internal
   450          pure
   451          returns (bytes memory)
   452      {
   453          require(_batch.systemLogs.length <= MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES, "pu");
   454  
-  455          bytes32 l2ToL1LogsHash = keccak256(_batch.systemLogs);
   456  
   457          return
   458              abi.encode(
-  459                  l2ToL1LogsHash,
+  459                  keccak256(_batch.systemLogs),
   460                  _stateDiffHash,
   461                  _batch.bootloaderHeapInitialContentsHash,
   462                  _batch.eventsQueueStateHash
   463              );
   464      }

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `ExecutorFacet`         | 2359401 | 2357673 |     -1728    |          -0.07%         |

*Code link:* [[448-464](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L448-L464)]


***
&nbsp;



#### File: [Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol)
*Instances:* 8

```diff
File: Mailbox.sol

-  100          L2Log memory l2Log = L2Log({
-  101              l2ShardId: 0,
-  102              isService: true,
-  103              txNumberInBatch: _l2TxNumberInBatch,
-  104              sender: L2_BOOTLOADER_ADDRESS,
-  105              key: _l2TxHash,
-  106              value: bytes32(uint256(_status))
-  107          });
-  108          return _proveL2LogInclusion(_l2BatchNumber, _l2MessageIndex, l2Log, _merkleProof);
+  100          return _proveL2LogInclusion(
+  101          	_l2BatchNumber, 
+  102          	_l2MessageIndex, 
+  103          	L2Log({
+  104          		l2ShardId: 0,
+  105          		isService: true,
+  106          		txNumberInBatch: _l2TxNumberInBatch,
+  107          		sender: L2_BOOTLOADER_ADDRESS,
+  108          		key: _l2TxHash,
+  109          		value: bytes32(uint256(_status))
+  110          	}),
+  111          	_merkleProof);

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `MailboxFacet`          | 2364542 | 2360870 |     -3672    |          -0.16%         |

*Code link:* [[100-108](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L100-L108)]


***


```diff
File: Mailbox.sol

-  142          bytes32 calculatedRootHash = Merkle.calculateRoot(_proof, _index, hashedLog);
-  143          bytes32 actualRootHash = s.l2LogsRootHashes[_batchNumber];
-  144  
-  145          return actualRootHash == calculatedRootHash;
+  142          return s.l2LogsRootHashes[_batchNumber] == Merkle.calculateRoot(_proof, _index, hashedLog);

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `MailboxFacet`          | 2364542 | 2363894 |     -648     |          -0.03%         |

*Code link:* [[142-145](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L142-L145)]


***


```diff
File: Mailbox.sol

-  171          uint256 l2GasPrice = _deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit);
-  172          return l2GasPrice * _l2GasLimit;
+  171          return _deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit) * _l2GasLimit;

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `MailboxFacet`          | 2364542 | 2363882 |     -660     |          -0.03%         |

*Code link:* [[171-172](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L171-L172)]


***


```diff
File: Mailbox.sol

   180          uint256 pubdataPriceETH = L1_GAS_PER_PUBDATA_BYTE * _l1GasPrice;
-  181          uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;
-  182  
-  183          return Math.max(FAIR_L2_GAS_PRICE, minL2GasPriceETH);
+  181          return Math.max(
+  182          	FAIR_L2_GAS_PRICE, 
+  183          	(pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata
+  184          	);

```
|  Method call or Contract deployment |  Before |  After  |After - Before|(After - Before) / Before|
|-------------------------------------|---------|---------|--------------|-------------------------|
|         `Forwarder.forward`         |  132309 |  132288 |      -21     |          -0.02%         |
|       `L1ERC20Bridge.deposit`       |  209487 |  209466 |      -21     |          -0.01%         |
| `MailboxFacet.requestL2Transaction` |  127814 |  127793 |      -21     |          -0.02%         |
|        `ConstructorForwarder`       |  186567 |  186546 |      -21     |          -0.01%         |
|            `ERC1967Proxy`           |  571840 |  571798 |      -42     |          -0.01%         |
|            `MailboxFacet`           | 2364542 | 2363198 |     -1344    |          -0.06%         |

*Code link:* [[180-183](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L180-L183)]


***


```diff
File: Mailbox.sol

-  295          uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast
-  296          uint256 txId = s.priorityQueue.getTotalPriorityTxs();
   297  
   298          // Here we manually assign fields for the struct to prevent "stack too deep" error
   299          WritePriorityOpParams memory params;
   300  
   301          // Checking that the user provided enough ether to pay for the transaction.
   302          // Using a new scope to prevent "stack too deep" error
   303          {
   304              params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);
-  305              uint256 baseCost = params.l2GasPrice * _l2GasLimit;
-  306              require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost
+  305              require(msg.value >= params.l2GasPrice * _l2GasLimit + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost
   307          }
   308  
   309          // If the `_refundRecipient` is not provided, we use the `_sender` as the recipient.
   310          address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient;
   311          // If the `_refundRecipient` is a smart contract, we apply the L1 to L2 alias to prevent foot guns.
   312          if (refundRecipient.code.length > 0) {
   313              refundRecipient = AddressAliasHelper.applyL1ToL2Alias(refundRecipient);
   314          }
   315  
   316          params.sender = _sender;
-  317          params.txId = txId;
+  317          params.txId = s.priorityQueue.getTotalPriorityTxs();
   318          params.l2Value = _l2Value;
   319          params.contractAddressL2 = _contractAddressL2;
-  320          params.expirationTimestamp = expirationTimestamp;
+  320          params.expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION);
   321          params.l2GasLimit = _l2GasLimit;
   322          params.l2GasPricePerPubdata = _l2GasPerPubdataByteLimit;
   323          params.valueToMint = msg.value;
   324          params.refundRecipient = refundRecipient;


```
|  Method call or Contract deployment |  Before |  After  |After - Before|(After - Before) / Before|
|-------------------------------------|---------|---------|--------------|-------------------------|
|         `Forwarder.forward`         |  132309 |  132143 |     -166     |          -0.13%         |
|       `L1ERC20Bridge.deposit`       |  209487 |  209321 |     -166     |          -0.08%         |
| `MailboxFacet.requestL2Transaction` |  127814 |  127648 |     -166     |          -0.13%         |
|        `ConstructorForwarder`       |  186567 |  186401 |     -166     |          -0.09%         |
|            `ERC1967Proxy`           |  571840 |  571508 |     -332     |          -0.06%         |
|            `MailboxFacet`           | 2364542 | 2335784 |    -28758    |          -1.22%         |

*Code link:* [[295-324](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L295-L324)]


***
&nbsp;


#### File: [Diamond.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol)
*Instances:* 2

```diff
File: Diamond.sol

-  140              SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
-  141              require(oldFacet.facetAddress == address(0), "J"); // facet for this selector already exists
+  140              require(ds.selectorToFacet[selector].facetAddress == address(0), "J"); // facet for this selector already exists

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|           `AdminFacet`           | 2502312 | 2483762 |    -18550    |          -0.74%         |
|         `AdminFacetTest`         | 2576987 | 2558444 |    -18543    |          -0.72%         |
|          `DiamondProxy`          | 3006084 | 2981232 |    -24852    |          -0.83%         |
|        `DiamondProxyTest`        |  114133 |  114145 |      +12     |          +0.01%         |

*Code link:* [[140-141](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L140-L141)]


***


```diff
File: Diamond.sol

-  193          uint256 selectorsLength = ds.facetToSelectors[_facet].selectors.length;
-  194          // If there are no selectors associated with facet then save facet as new one
-  195          if (selectorsLength == 0) {
+  193          if (ds.facetToSelectors[_facet].selectors.length == 0) {

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|           `AdminFacet`           | 2502312 | 2501436 |     -876     |          -0.04%         |
|         `AdminFacetTest`         | 2576987 | 2576111 |     -876     |          -0.03%         |
|          `DiamondProxy`          | 3006084 | 3005139 |     -945     |          -0.03%         |

*Code link:* [[193-195](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L193-L195)]


***
&nbsp;


#### File: [Getters.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol)
*Instances:* 1

```diff
File: Getters.sol

-  184              Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];
-  185  
-  186              result[i] = Facet(facetAddr, facetToSelectors.selectors);
+  184              result[i] = Facet(facetAddr, ds.facetToSelectors[facetAddr].selectors);

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `DiamondProxy`          | 3006084 | 3006076 |      -8      |          -0.00%         |
|          `GettersFacet`          |  965891 |  952906 |    -12985    |          -1.34%         |

*Code link:* [[184-186](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L184-L186)]


***
&nbsp;


#### File: [TransactionValidator.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol)
*Instances:* 7

```diff
File: TransactionValidator.sol

   63      /// @dev Calculates the approximate minimum gas limit required for executing a priority transaction.
   64      /// @param _encodingLength The length of the priority transaction encoding in bytes.
   65      /// @param _numberOfFactoryDependencies The number of new factory dependencies that will be added.
   66      /// @param _l2GasPricePerPubdata The L2 gas price for publishing the priority transaction on L2.
   67      /// @return The minimum gas limit required to execute the priority transaction.
   68      /// Note: The calculation includes the main cost of the priority transaction, however, in reality, the operator can spend a little more gas on overheads.
   69      function getMinimalPriorityTransactionGasLimit(
   70          uint256 _encodingLength,
   71          uint256 _numberOfFactoryDependencies,
   72          uint256 _l2GasPricePerPubdata
   73      ) internal pure returns (uint256) {
-  74          uint256 costForComputation;
-  75          {
-  76              // Adding the intrinsic cost for the transaction, i.e. auxiliary prices which cannot be easily accounted for
-  77              costForComputation = L1_TX_INTRINSIC_L2_GAS;
-  78  
-  79              // Taking into account the hashing costs that depend on the length of the transaction
-  80              // Note that L1_TX_DELTA_544_ENCODING_BYTES is the delta in the price for every 544 bytes of
-  81              // the transaction's encoding. It is taken as LCM between 136 and 32 (the length for each keccak256 round
-  82              // and the size of each new encoding word).
-  83              costForComputation += Math.ceilDiv(_encodingLength * L1_TX_DELTA_544_ENCODING_BYTES, 544);
-  84  
-  85              // Taking into the account the additional costs of providing new factory dependenies
-  86              costForComputation += _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_L2_GAS;
-  87  
-  88              // There is a minimal amount of computational L2 gas that the transaction should cover
-  89              costForComputation = Math.max(costForComputation, L1_TX_MIN_L2_GAS_BASE);
-  90          }
-  91  
-  92          uint256 costForPubdata = 0;
-  93          {
-  94              // Adding the intrinsic cost for the transaction, i.e. auxilary prices which cannot be easily accounted for
-  95              costForPubdata = L1_TX_INTRINSIC_PUBDATA * _l2GasPricePerPubdata;
-  96  
-  97              // Taking into the account the additional costs of providing new factory dependenies
-  98              costForPubdata += _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_PUBDATA * _l2GasPricePerPubdata;
-  99          }
-  100  
-  101          return costForComputation + costForPubdata;
-  102      }
+  74  
+  75          	// costForComputation
+  76          		// There is a minimal amount of computational L2 gas that the transaction should cover
+  77          	return Math.max(
+  78          			// Adding the intrinsic cost for the transaction, i.e. auxiliary prices which cannot be easily accounted for
+  79          				L1_TX_INTRINSIC_L2_GAS +
+  80  
+  81          			// Taking into account the hashing costs that depend on the length of the transaction
+  82          			// Note that L1_TX_DELTA_544_ENCODING_BYTES is the delta in the price for every 544 bytes of
+  83          			// the transaction's encoding. It is taken as LCM between 136 and 32 (the length for each keccak256 round
+  84          			// and the size of each new encoding word).
+  85          				Math.ceilDiv(_encodingLength * L1_TX_DELTA_544_ENCODING_BYTES, 544) +
+  86  
+  87          			// Taking into the account the additional costs of providing new factory dependenies
+  88          				_numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_L2_GAS,
+  89  
+  90          		L1_TX_MIN_L2_GAS_BASE) +
+  91          	
+  92          	// costForPubdata
+  93          		// Adding the intrinsic cost for the transaction, i.e. auxilary prices which cannot be easily accounted for
+  94          			L1_TX_INTRINSIC_PUBDATA * _l2GasPricePerPubdata +
+  95  
+  96          		// Taking into the account the additional costs of providing new factory dependenies
+  97          			_numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_PUBDATA * _l2GasPricePerPubdata;
+  98          }

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|      `ConstructorForwarder`      |  186567 |  186529 |      -38     |          -0.02%         |
|        `CustomUpgradeTest`       | 1904340 | 1900896 |     -3444    |          -0.18%         |
|         `DefaultUpgrade`         | 1893485 | 1890034 |     -3451    |          -0.18%         |
|          `ERC1967Proxy`          |  571840 |  571764 |      -76     |          -0.01%         |
|          `MailboxFacet`          | 2364530 | 2363198 |     -1332    |          -0.06%         |
|    `TransactionValidatorTest`    |  937876 |  934876 |     -3000    |          -0.32%         |

*Code link:* [[63-102](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L63-L102)]


***


```diff
File: TransactionValidator.sol

   140          // The overhead from taking up the transaction's slot
-  141          uint256 txSlotOverhead = Math.ceilDiv(batchOverheadGas, MAX_TRANSACTIONS_IN_BATCH);
-  142          batchOverheadForTransaction = Math.max(batchOverheadForTransaction, txSlotOverhead);
+  141          batchOverheadForTransaction = Math.max(
+  142          	batchOverheadForTransaction, 
+  143          	Math.ceilDiv(batchOverheadGas, MAX_TRANSACTIONS_IN_BATCH)
+  144          	);
   143  
   144          // The overhead for occupying the bootloader memory can be derived from encoded_len
-  145          uint256 overheadForLength = Math.ceilDiv(_encodingLength * batchOverheadGas, BOOTLOADER_TX_ENCODING_SPACE);
-  146          batchOverheadForTransaction = Math.max(batchOverheadForTransaction, overheadForLength);
+  145          batchOverheadForTransaction = Math.max(
+  146          	batchOverheadForTransaction, 
+  147          	Math.ceilDiv(_encodingLength * batchOverheadGas, BOOTLOADER_TX_ENCODING_SPACE)
+  148          	);

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|      `ConstructorForwarder`      |  186567 |  186541 |      -26     |          -0.01%         |
|        `CustomUpgradeTest`       | 1904340 | 1898513 |     -5827    |          -0.31%         |
|         `DefaultUpgrade`         | 1893485 | 1887646 |     -5839    |          -0.31%         |
|          `ERC1967Proxy`          |  571840 |  571788 |      -52     |          -0.01%         |
|          `MailboxFacet`          | 2364530 | 2358726 |     -5804    |          -0.25%         |
|    `TransactionValidatorTest`    |  937876 |  932061 |     -5815    |          -0.62%         |

*Code link:* [[140-146](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L140-L146)]


***


```diff
File: TransactionValidator.sol

-  160          uint256 overheadForGas;
-  161          {
-  162              uint256 numerator = batchOverheadGas * _totalGasLimit + L2_TX_MAX_GAS_LIMIT;
-  163              uint256 denominator = L2_TX_MAX_GAS_LIMIT + batchOverheadGas;
-  164  
-  165              overheadForGas = (numerator - 1) / denominator;
-  166          }
-  167          batchOverheadForTransaction = Math.max(batchOverheadForTransaction, overheadForGas);
+  160          batchOverheadForTransaction = Math.max(
+  161          // overheadForGas
+  162  
+  163          	// numerator
+  164          		(batchOverheadGas * _totalGasLimit + L2_TX_MAX_GAS_LIMIT - 1) /
+  165  
+  166          	// denominator
+  167          		(L2_TX_MAX_GAS_LIMIT + batchOverheadGas)
+  168  
+  169          , batchOverheadForTransaction);

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|      `ConstructorForwarder`      |  186567 |  186524 |      -43     |          -0.02%         |
|        `CustomUpgradeTest`       | 1904340 | 1898717 |     -5623    |          -0.30%         |
|         `DefaultUpgrade`         | 1893485 | 1887874 |     -5611    |          -0.30%         |
|          `ERC1967Proxy`          |  571840 |  571754 |      -86     |          -0.02%         |
|          `MailboxFacet`          | 2364530 | 2361326 |     -3204    |          -0.14%         |
|    `TransactionValidatorTest`    |  937876 |  932277 |     -5599    |          -0.60%         |

*Code link:* [[160-167](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L160-L167)]


***
&nbsp;


#### File: [ValidatorTimelock.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol)
*Instances:* 1

```diff
File: ValidatorTimelock.sol

-  117                  uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);
   118  
   119                  // Note: if the `commitBatchTimestamp` is zero, that means either:
   120                  // * The batch was committed, but not through this contract.
   121                  // * The batch wasn't committed at all, so execution will fail in the zkSync contract.
   122                  // We allow executing such batches.
-  123                  require(block.timestamp >= commitBatchTimestamp + delay, "5c"); // The delay is not passed
+  123                  require(block.timestamp >= committedBatchTimestamp.get(_newBatchesData[i].batchNumber) + delay, "5c"); // The delay is not passed

```
|Method call or Contract deployment| Before |  After |After - Before|(After - Before) / Before|
|----------------------------------|--------|--------|--------------|-------------------------|
|        `ValidatorTimelock`       | 869857 | 868573 |     -1284    |          -0.15%         |

*Code link:* [[117-123](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L117-L123)]


***
&nbsp;



#### File: [L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol)
*Instances:* 4

```diff
File: L1WethBridge.sol

   96          l2WethAddress = _l2WethAddress;
   97  
-  98          bytes32 l2WethBridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
-  99          bytes32 l2WethBridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);
   100  
   101          // Deploy L2 bridge implementation contract
   102          address wethBridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(
   103              zkSync,
   104              _deployBridgeImplementationFee,
-  105              l2WethBridgeImplementationBytecodeHash,
+  105              L2ContractHelper.hashL2Bytecode(_factoryDeps[0]),
   106              "", // Empty constructor data
   107              _factoryDeps // All factory deps are needed for L2 bridge
   108          );
   109  
   110          // Prepare the proxy constructor data
   111          bytes memory l2WethBridgeProxyConstructorData;
   112          {
   113              // Data to be used in delegate call to initialize the proxy
-  114              bytes memory proxyInitializationParams = abi.encodeCall(
-  115                  IL2WethBridge.initialize,
-  116                  (address(this), l1WethAddress, _l2WethAddress)
-  117              );
   118              l2WethBridgeProxyConstructorData = abi.encode(
   119                  wethBridgeImplementationAddr,
   120                  _governor,
-  121                  proxyInitializationParams
+  121                  abi.encodeCall(
+  122                      IL2WethBridge.initialize,
+  123                      (address(this), l1WethAddress, _l2WethAddress)
   122              );
   123          }
   124  
   125          // Deploy L2 bridge proxy contract
   126          l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
   127              zkSync,
   128              _deployBridgeProxyFee,
-  129              l2WethBridgeProxyBytecodeHash,
+  129              L2ContractHelper.hashL2Bytecode(_factoryDeps[1]),
   130              l2WethBridgeProxyConstructorData,
   131              // No factory deps are needed for L2 bridge proxy, because it is already passed in the previous step
   132              new bytes[](0)
   133          );


```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `L1WethBridge`          | 2506089 | 2495293 |    -10796    |          -0.43%         |

*Code link:* [[96-133](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L96-L133)]


***

```diff
File: L1WethBridge.sol

   244          // Check if the withdrawal has already been finalized on L2.
-  245          bool alreadyFinalised = zkSync.isEthWithdrawalFinalized(_l2BatchNumber, _l2MessageIndex);
-  246          if (alreadyFinalised) {
+  245          if (zkSync.isEthWithdrawalFinalized(_l2BatchNumber, _l2MessageIndex)) {

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `L1WethBridge`          | 2506089 | 2504577 |     -1512    |          -0.06%         |

*Code link:* [[244-246](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L244-L246)]


***
&nbsp;



#### File: [L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)
*Instances:* 8

```diff
File: L1ERC20Bridge.sol

-  99           bytes32 l2BridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
-  100          bytes32 l2BridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);
   101  
   102          // Deploy L2 bridge implementation contract
   103          address bridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(
   104              zkSync,
   105              _deployBridgeImplementationFee,
-  106              l2BridgeImplementationBytecodeHash,
+  106              L2ContractHelper.hashL2Bytecode(_factoryDeps[0]),
   107              "", // Empty constructor data
   108              _factoryDeps // All factory deps are needed for L2 bridge
   109          );
   110  
   111          // Prepare the proxy constructor data
   112          bytes memory l2BridgeProxyConstructorData;
   113          {
   114              // Data to be used in delegate call to initialize the proxy
   115              bytes memory proxyInitializationParams = abi.encodeCall(
   116                  IL2ERC20Bridge.initialize,
   117                  (address(this), l2TokenProxyBytecodeHash, _governor)
   118              );
   119              l2BridgeProxyConstructorData = abi.encode(bridgeImplementationAddr, _governor, proxyInitializationParams);
   120          }
   121  
   122          // Deploy L2 bridge proxy contract
   123          l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
   124              zkSync,
   125              _deployBridgeProxyFee,
-  126              l2BridgeProxyBytecodeHash,
+  126              L2ContractHelper.hashL2Bytecode(_factoryDeps[1]),
   127              l2BridgeProxyConstructorData,
   128              // No factory deps are needed for L2 bridge proxy, because it is already passed in previous step
   129              new bytes[](0)
   130          );


```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `L1ERC20Bridge`         | 2821104 | 2811616 |     -9488    |          -0.34%         |

*Code link:* [[99-130](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L99-L130)]


***


```diff
File: L1ERC20Bridge.sol

-  219          uint256 balanceAfter = _token.balanceOf(address(this));
-  220  
-  221          return balanceAfter - balanceBefore;
+  219          return _token.balanceOf(address(this)) - balanceBefore;

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `L1ERC20Bridge`         | 2821104 | 2820456 |     -648     |          -0.02%         |

*Code link:* [[219-221](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L219-L221)]


***


```diff
File: L1ERC20Bridge.sol

-  231          bytes memory gettersData = _getERC20Getters(_l1Token);
-  232  
-  233          txCalldata = abi.encodeCall(
-  234              IL2Bridge.finalizeDeposit,
-  235              (_l1Sender, _l2Receiver, _l1Token, _amount, gettersData)
-  236          );
+  231          txCalldata = abi.encodeCall(
+  232              IL2Bridge.finalizeDeposit,
+  233              (_l1Sender, _l2Receiver, _l1Token, _amount, _getERC20Getters(_l1Token))
+  234          );

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `L1ERC20Bridge`         | 2821104 | 2819808 |     -1296    |          -0.05%         |

*Code link:* [[231-236](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L231-L236)]


***


```diff
File: L1ERC20Bridge.sol

-  264          bool proofValid = zkSync.proveL1ToL2TransactionStatus(
-  265              _l2TxHash,
-  266              _l2BatchNumber,
-  267              _l2MessageIndex,
-  268              _l2TxNumberInBatch,
-  269              _merkleProof,
-  270              TxStatus.Failure
-  271          );
-  272          require(proofValid, "yn");
+  264          require(
+  265          	zkSync.proveL1ToL2TransactionStatus(
+  266          		_l2TxHash,
+  267          		_l2BatchNumber,
+  268          		_l2MessageIndex,
+  269          		_l2TxNumberInBatch,
+  270          		_merkleProof,
+  271          		TxStatus.Failure
+  272          	), "yn");

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `L1ERC20Bridge`         | 2821104 | 2819784 |     -1320    |          -0.05%         |

*Code link:* [[264-272](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L264-L272)]


***


```diff
File: L1ERC20Bridge.sol

-  311              bool success = zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);
-  312              require(success, "nq");
+  311              require(zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof), "nq");

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `L1ERC20Bridge`         | 2821104 | 2819808 |     -1296    |          -0.05%         |

*Code link:* [[311-312](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L311-L312)]


***


```diff
File: L1ERC20Bridge.sol

-  354          bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""));
-  355          bytes32 salt = bytes32(uint256(uint160(_l1Token)));
-  356  
-  357          return L2ContractHelper.computeCreate2Address(l2Bridge, salt, l2TokenProxyBytecodeHash, constructorInputHash);
+  354          return L2ContractHelper.computeCreate2Address(
+  355          	l2Bridge, 
+  356          	bytes32(uint256(uint160(_l1Token))), 
+  357          	l2TokenProxyBytecodeHash, 
+  358          	keccak256(abi.encode(address(l2TokenBeacon), ""))
+  359          	);

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `L1ERC20Bridge`         | 2821104 | 2805092 |    -16012    |          -0.57%         |

*Code link:* [[354-357](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L354-L357)]


***
&nbsp;


#### File: [BaseZkSyncUpgrade.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol)
*Instances:* 2

```diff
File: BaseZkSyncUpgrade.sol

-  85          bytes32 previousDefaultAccountBytecodeHash = s.l2DefaultAccountBytecodeHash;
-  86  
-  87          // Change the default account bytecode hash
-  88          s.l2DefaultAccountBytecodeHash = _l2DefaultAccountBytecodeHash;
-  89          emit NewL2DefaultAccountBytecodeHash(previousDefaultAccountBytecodeHash, _l2DefaultAccountBytecodeHash);
+  85          emit NewL2DefaultAccountBytecodeHash(s.l2DefaultAccountBytecodeHash, _l2DefaultAccountBytecodeHash);
+  86          s.l2DefaultAccountBytecodeHash = _l2DefaultAccountBytecodeHash;

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|        `CustomUpgradeTest`       | 1904340 | 1903260 |     -1080    |          -0.06%         |
|         `DefaultUpgrade`         | 1893485 | 1892405 |     -1080    |          -0.06%         |

*Code link:* [[85-89](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L85-L89)]


***


```diff
File: BaseZkSyncUpgrade.sol

-  102          bytes32 previousBootloaderBytecodeHash = s.l2BootloaderBytecodeHash;
-  103  
-  104          // Change the bootloader bytecode hash
-  105          s.l2BootloaderBytecodeHash = _l2BootloaderBytecodeHash;
-  106          emit NewL2BootloaderBytecodeHash(previousBootloaderBytecodeHash, _l2BootloaderBytecodeHash);
+  102          emit NewL2BootloaderBytecodeHash(s.l2BootloaderBytecodeHash, _l2BootloaderBytecodeHash);
+  103          s.l2BootloaderBytecodeHash = _l2BootloaderBytecodeHash;

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|        `CustomUpgradeTest`       | 1904340 | 1903260 |     -1080    |          -0.06%         |
|         `DefaultUpgrade`         | 1893485 | 1892393 |     -1092    |          -0.06%         |

*Code link:* [[102-106](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L102-L106)]


***
&nbsp;


#### File: [AllowList.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol)
*Instances:* 1

```diff
File: AllowList.sol

-  117          bool currentPermission = hasSpecialAccessToCall[_caller][_target][_functionSig];
-  118  
-  119          if (currentPermission != _enable) {
+  117          if (hasSpecialAccessToCall[_caller][_target][_functionSig] != _enable) {

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|            `AllowList`           | 1008207 | 1007559 |     -648     |          -0.06%         |

*Code link:* [[117-119](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L117-L119)]


***
&nbsp;


#### File: [L2ContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol)
*Instances:* 1

```diff
File: L2ContractHelper.sol

-  40          uint8 version = uint8(_bytecodeHash[0]);
-  41          require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
+  40          require(uint8(_bytecodeHash[0]) == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|        `CustomUpgradeTest`       | 1904340 | 1903908 |     -432     |          -0.02%         |
|         `DefaultUpgrade`         | 1893485 | 1893065 |     -420     |          -0.02%         |

*Code link:* [[40-41](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L40-L41)]


***
&nbsp;


                
### [GO-02] Avoid using ternary operator and let variable to assume default value
                
*Instances:* 1

#### File: [L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol)
*Instances:* 1

```diff
File: L1WethBridge.sol

   301      /// @return l2Token Address of an L2 token counterpart.
   302      function l2TokenAddress(address _l1Token) public view override returns (address l2Token) {
-  303          l2Token = _l1Token == l1WethAddress ? l2WethAddress : address(0);
+  303          if(_l1Token == l1WethAddress) l2Token = l2WethAddress;
   304      }

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `L1WethBridge`          | 2506089 | 2504769 |     -1320    |          -0.05%         |

*Code link:* [[301-304](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L301-L304)]


***
&nbsp;


                
### [GO-03] Modify ternary operator in order to use >= instead of <
                
We can't report gas savings of the following instance because _maxU256() is never used

*Instances:* 1

#### File: [Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol)
*Instances:* 1

```diff
File: Executor.sol

-  418          return a < b ? b : a;
+  418          return a >= b ? a : b;

```
*Expected gas savings:* 3 gas

*Code link:* [[418](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L418)]


***
&nbsp;


                
### [GO-04] Precompute constants value
                
Compute constants value and set final result, instead of compute at runtime.

In order to assert correct value when parameters change, it could be fine to implement this verification inside tests.

*Instances:* 1

#### File: [Config.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol)
*Instances:* 1


```diff
File: Config.sol

-  14  uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;
+  14  uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 180224;

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `ExecutorFacet`         | 2359401 | 2350106 |     -9295    |          -0.39%         |
|          `MailboxFacet`          | 2364530 | 2364542 |      +12     |          +0.00%         |
|                                      |          |          |                |                           |
| OVERALL                              | 4723931 | 4714648 |        -9283|                  -0,20% |

*Code link:* [[14](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L14)]


***
&nbsp;


                
### [GO-05] Using `do-while`-loop instead of `for`-loop
                
In the `do-while`-loop, the first iteration doesn't need any check. So, it is possible to save gas using it instead of the `for`-loop when there will be at least one iteration for sure.

*Instances:* 2

| Method call or Contract deployment   |  Before |   After | After - Before | (After - Before) / Before |
|:-------------------------------------|--------:|--------:|---------------:|--------------------------:|
| `MailboxFacet.finalizeEthWithdrawal` |  112962 |  112809 |           -153 |                    -0.14% |
| `ExecutorFacet`                      | 2359401 | 2358105 |          -1296 |                    -0.05% |
| `MailboxFacet`                       | 2364542 | 2363186 |          -1356 |                    -0.06% |
| `MerkleTest`                         |  246410 |  245114 |          -1296 |                    -0.53% |
|                                      |         |         |                |                           |
| OVERALL                              | 5083315 | 5079214 |          -4101 |                    -0,08% |


&nbsp;
***
&nbsp;


#### File: [Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol)
*Instances:* 1


```diff
File: Executor.sol

-  293          for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
-  294              _executeOneBatch(_batchesData[i], i);
-  295              emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
-  296          }
+  293          uint256 i = 0;
+  294          do {
+  295              _executeOneBatch(_batchesData[i], i);
+  296              emit BlockExecution(_batchesData[i].batchNumber, _batchesData[i].batchHash, _batchesData[i].commitment);
+  297  
+  298              i = i.uncheckedInc();
+  299          } while(i<nBatches);

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `ExecutorFacet`         | 2359401 | 2358105 |     -1296    |          -0.05%         |

*Code link:* [[293-296](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L293-L296)]


***
&nbsp;


#### File: [Merkle.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol)
*Instances:* 1


```diff
File: Merkle.sol

-  29          for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
-  30              currentHash = (_index % 2 == 0)
-  31                  ? _efficientHash(currentHash, _path[i])
-  32                  : _efficientHash(_path[i], currentHash);
-  33              _index /= 2;
-  34          }
+  29          uint256 i;
+  30          do{
+  31               currentHash = (_index % 2 == 0)
+  32                   ? _efficientHash(currentHash, _path[i])
+  33                   : _efficientHash(_path[i], currentHash);
+  34               _index /= 2;
+  35  
+  36               i = i.uncheckedInc();
+  37          }
+  38          while(i < pathLength);

```
|  Method call or Contract deployment  |  Before |  After  |After - Before|(After - Before) / Before|
|--------------------------------------|---------|---------|--------------|-------------------------|
| `MailboxFacet.finalizeEthWithdrawal` |  112962 |  112809 |     -153     |          -0.14%         |
|            `MailboxFacet`            | 2364530 | 2363186 |     -1344    |          -0.06%         |
|             `MerkleTest`             |  246410 |  245114 |     -1296    |          -0.53%         |

*Code link:* [[293-296](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L293-L296)]


***
&nbsp;


                
### [GO-06] Remove useless code from production
                
It seems that there are lines of code that are useless.

*Instances:* 1

#### File: [Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol)
*Instances:* 1

```diff
File: Executor.sol

   351          assert(block.chainid != 1);
-  352          // We allow skipping the zkp verification for the test(net) environment
-  353          // If the proof is not empty, verify it, otherwise, skip the verification
-  354          if (_proof.serializedProof.length > 0) {
-  355              bool successVerifyProof = s.verifier.verify(
-  356                  proofPublicInput,
-  357                  _proof.serializedProof,
-  358                  _proof.recursiveAggregationInput
-  359              );
-  360              require(successVerifyProof, "p"); // Proof verification fail
-  361          }
-  362          // #else
   363          bool successVerifyProof = s.verifier.verify(
   364              proofPublicInput,
   365              _proof.serializedProof,
   366              _proof.recursiveAggregationInput
   367          );
   368          require(successVerifyProof, "p"); // Proof verification fail

```

*Code link:* [[351-368](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L351-L368)]


***
&nbsp;


                
### [GO-07] Don't overwrite local variable value if it will used anymore
                
*Instances:* 1

#### File: [L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)
*Instances:* 1

```diff
File: L1ERC20Bridge.sol

-  336          (amount, offset) = UnsafeBytes.readUint256(_l2ToL1message, offset);
+  336          (amount,) = UnsafeBytes.readUint256(_l2ToL1message, offset);

```
|Method call or Contract deployment|  Before |  After  |After - Before|(After - Before) / Before|
|----------------------------------|---------|---------|--------------|-------------------------|
|          `L1ERC20Bridge`         | 2821104 | 2821092 |      -12     |          -0.00%         |

*Code link:* [[336](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L336)]


***
&nbsp;


                
### [GO-08] Using different conditional path
                
Many times, a different conditional path could lead to saving gas.

For example, it could permit to use `>=` instead of `>` or avoid using `NOT` operator.

*Instances:* 2

#### File: [L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol)
*Instances:* 1

```diff
File: L1ERC20Bridge.sol

-  342          if (!limitData.depositLimitation) return; // no deposit limitation is placed for this token
-  343  
-  344          if (_claiming) {
-  345              totalDepositedAmountPerUser[_l1Token][_depositor] -= _amount;
-  346          } else {
-  347              require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
-  348              totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
-  349          }
+  342          if (limitData.depositLimitation){
+  343          	if (_claiming) {
+  344          		totalDepositedAmountPerUser[_l1Token][_depositor] -= _amount;
+  345          	} else {
+  346          		require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
+  347          		totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
+  348          	}
+  349          }

```
|Method call or Contract deployment| Before |  After |After - Before|(After - Before) / Before|
|----------------------------------|--------|--------|--------------|-------------------------|
|      `L1ERC20Bridge.deposit`     | 209487 | 209483 |      -4      |          -0.00%         |

*Code link:* [[342-349](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L342-L349)]


***
&nbsp;


#### File: [Governance.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)
*Instances:* 1

```diff
File: Governance.sol

   107          if (timestamp == 0) {
   108              return OperationState.Unset;
   109          } else if (timestamp == EXECUTED_PROPOSAL_TIMESTAMP) {
   110              return OperationState.Done;
-  111          } else if (timestamp > block.timestamp) {
-  112              return OperationState.Waiting;
-  113          } else {
-  114              return OperationState.Ready;
-  115          }
+  111          } else if (timestamp <= block.timestamp) {
+  112          	return OperationState.Ready;
+  113          } else {
+  114          	return OperationState.Waiting;
+  115          }

```
Governance.sol seems not covered by tests. Anyway, it is expected 3 gas savings

*Code link:* [[107-115](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L107-L115)]


***
&nbsp;


## L2 Contracts - Findings


                
### [GO-09] Avoid defining local variable that are used once
                
Avoid defining a local variable can save gas. When code don't lose clarity, it could be better to write inline code.

*Instances:* 14

#### File: [L1Messenger.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)
*Instances:* 2

```diff
File: L1Messenger.sol

-  139          uint256 meta = SystemContractHelper.getZkSyncMetaBytes();
-  140          uint32 gasPerPubdataBytes = SystemContractHelper.getGasPerPubdataByteFromMeta(meta);
+  139  		uint32 gasPerPubdataBytes = SystemContractHelper.getGasPerPubdataByteFromMeta(
+  140  			SystemContractHelper.getZkSyncMetaBytes()
+  141  		);

```

*Code link:* [[139-140](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L139-L140)]



***



```diff
File: L1Messenger.sol

-  173          uint256 meta = SystemContractHelper.getZkSyncMetaBytes();
-  174          uint32 gasPerPubdataBytes = SystemContractHelper.getGasPerPubdataByteFromMeta(meta);
+  173          uint32 gasPerPubdataBytes = SystemContractHelper.getGasPerPubdataByteFromMeta(
+  174              SystemContractHelper.getZkSyncMetaBytes()
+  175          );

```

*Code link:* [[173-174](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L173-L174)]



***
&nbsp;

#### File: [ContractDeployer.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)
*Instances:* 4

```diff
File: ContractDeployer.sol

-  100          bytes32 constructorInputHash = EfficientCall.keccak(_input);
-  101  
-  102          bytes32 hash = keccak256(
-  103              bytes.concat(CREATE2_PREFIX, bytes32(uint256(uint160(_sender))), _salt, _bytecodeHash, constructorInputHash)
-  104          );
+  100          bytes32 hash = keccak256(
+  101              bytes.concat(CREATE2_PREFIX, bytes32(uint256(uint160(_sender))), _salt, _bytecodeHash, EfficientCall.keccak(_input))
+  102          );

```

*Code link:* [[100-104](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L100-L104)]


***


```diff
File: ContractDeployer.sol

-  189          uint256 senderNonce = NONCE_HOLDER_SYSTEM_CONTRACT.incrementDeploymentNonce(msg.sender);
-  190          address newAddress = getNewAddressCreate(msg.sender, senderNonce);
+  189          address newAddress = getNewAddressCreate(
+  190              msg.sender, 
+  191              NONCE_HOLDER_SYSTEM_CONTRACT.incrementDeploymentNonce(msg.sender)
+  192          );

```

*Code link:* [[189-190](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L189-L190)]


***


```diff
File: ContractDeployer.sol

-  303          uint256 knownCodeMarker = KNOWN_CODE_STORAGE_CONTRACT.getMarker(_bytecodeHash);
-  304          require(knownCodeMarker > 0, "The code hash is not known");
+  303          require(KNOWN_CODE_STORAGE_CONTRACT.getMarker(_bytecodeHash) > 0, "The code hash is not known");

```

*Code link:* [[303-304](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L303-L304)]


***


```diff
File: ContractDeployer.sol

-  312          bytes32 constructingBytecodeHash = Utils.constructingBytecodeHash(_bytecodeHash);
-  313          ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructingCodeHash(_newAddress, constructingBytecodeHash);
+  312          ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructingCodeHash(
+  313              _newAddress, 
+  314              Utils.constructingBytecodeHash(_bytecodeHash)
+  315          );

```

*Code link:* [[312-313](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L312-L313)]




***
&nbsp;

#### File: [SystemContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol)
*Instances:* 2

```diff
File: SystemContractHelper.sol

-  236          // Firstly, we delete all the bits after the field
-  237          uint256 shifted = (meta << (256 - size - offset));
-  238          // Then we shift everything back
-  239          result = (shifted >> (256 - size));
+  236          result = 
+  237              // Firstly, we delete all the bits after the field
+  238              (meta << (256 - size - offset))
+  239              // Then we shift everything back
+  240              >> (256 - size)

```

*Code link:* [[236-239](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L236-L239)]


***


```diff
File: SystemContractHelper.sol

-  361          bool precompileCallSuccess = unsafePrecompileCall(
-  362              0, // The precompile parameters are formal ones. We only need the precompile call to burn gas.
-  363              _gasToPay
-  364          );
-  365          require(precompileCallSuccess, "Failed to charge gas");
+  361          require(
+  362              unsafePrecompileCall(
+  363                  0, // The precompile parameters are formal ones. We only need the precompile call to burn gas.
+  364                  _gasToPay
+  365              ), "Failed to charge gas"
+  366          );

```

*Code link:* [[361-365](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L361-L365)]


***
&nbsp;

#### File: [EfficientCall.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol)
*Instances:* 2

```diff
File: EfficientCall.sol

-  261          uint32 shrinkTo = uint32(msg.data.length - (_data.length + dataOffset));
-  262          SystemContractHelper.ptrShrinkIntoActive(shrinkTo);
+  261          SystemContractHelper.ptrShrinkIntoActive(
+  262              uint32(msg.data.length - (_data.length + dataOffset))
+  263          );

```

*Code link:* [[261-262](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L261-L262)]


***


```diff
File: EfficientCall.sol

-  264          uint32 gas = Utils.safeCastToU32(_gas);
-  265          uint256 farCallAbi = SystemContractsCaller.getFarCallABIWithEmptyFatPointer(
-  266              gas,
-  267              // Only rollup is supported for now
-  268              0,
-  269              CalldataForwardingMode.ForwardFatPointer,
-  270              _isConstructor,
-  271              _isSystem
-  272          );
+  264          uint256 farCallAbi = SystemContractsCaller.getFarCallABIWithEmptyFatPointer(
+  265              Utils.safeCastToU32(_gas),
+  266              // Only rollup is supported for now
+  267              0,
+  268              CalldataForwardingMode.ForwardFatPointer,
+  269              _isConstructor,
+  270              _isSystem
+  271          );

```

*Code link:* [[264-272](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/EfficientCall.sol#L264-L272)]



***
&nbsp;

#### File: [Compressor.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)
*Instances:* 3

```diff
File: Compressor.sol
-  72                  uint64 encodedChunk = dictionary.readUint64(indexOfEncodedChunk);
-  73                  uint64 realChunk = _bytecode.readUint64(encodedDataPointer * 4);
-  74  
-  75                  require(encodedChunk == realChunk, "Encoded chunk does not match the original bytecode");
+  72                  require(
+  73                      dictionary.readUint64(indexOfEncodedChunk) == _bytecode.readUint64(encodedDataPointer * 4), 
+  74                      "Encoded chunk does not match the original bytecode"
+  75                  );

```

*Code link:* [[72-75](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L72-L75)]


***


```diff
File: Compressor.sol

-  251          number = uint256(bytes32(_calldataSlice));
-  252          number >>= (256 - (_calldataSlice.length * 8));
+  251          number = uint256(bytes32(_calldataSlice)) >> (256 - (_calldataSlice.length * 8));

```

*Code link:* [[251-252](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L251-L252)]



***
&nbsp;

#### File: [DefaultAccount.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)
*Instances:* 1

```diff
File: DefaultAccount.sol

-  99           uint256 totalRequiredBalance = _transaction.totalRequiredBalance();
-  100          require(totalRequiredBalance <= address(this).balance, "Not enough balance for fee + value");
+  99           require(
+  100              _transaction.totalRequiredBalance() <= address(this).balance, 
+  101              "Not enough balance for fee + value"
+  102          );

```

*Code link:* [[99-100](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L99-L100)]



***
&nbsp;

                
### [GO-10] Using different conditional path
                
Many times, a different conditional path could lead to saving gas.

For example, it could permit to use `>=` instead of `>` or avoid using `NOT` operator.

*Instances:* 5

#### File: [TransactionHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol)
*Instances:* 1

```diff
File: TransactionHelper.sol

   405      function totalRequiredBalance(Transaction calldata _transaction) internal pure returns (uint256 requiredBalance) {
-  406          if (address(uint160(_transaction.paymaster)) != address(0)) {
-  407              // Paymaster pays for the fee
-  408              requiredBalance = _transaction.value;
-  409          } else {
-  410              // The user should have enough balance for both the fee and the value of the transaction
-  411              requiredBalance = _transaction.maxFeePerGas * _transaction.gasLimit + _transaction.value;
-  412          }
+  406          if (_transaction.paymaster == 0) {
+  407              // The user should have enough balance for both the fee and the value of the transaction
+  408              requiredBalance = _transaction.maxFeePerGas * _transaction.gasLimit + _transaction.value;
+  409          } else {
+  410              // Paymaster pays for the fee
+  411              requiredBalance = _transaction.value;
+  412          }
   413      }


```

*Code link:* [[405-413](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L405-L413)]


***
&nbsp;

#### File: [ContractDeployer.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol)
*Instances:* 1

```diff
File: ContractDeployer.sol

Current Code:
   40      function extendedAccountVersion(address _address) public view returns (AccountAbstractionVersion) {
   41          AccountInfo memory info = accountInfo[_address];
-  42          if (info.supportedAAVersion != AccountAbstractionVersion.None) {
-  43              return info.supportedAAVersion;
-  44          }
-  45  
-  46          // It is an EOA, it is still an account.
-  47          if (ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getRawCodeHash(_address) == 0) {
-  48              return AccountAbstractionVersion.Version1;
-  49          }
-  50  
-  51          return AccountAbstractionVersion.None;
+  42          if (info.supportedAAVersion == AccountAbstractionVersion.None) {
+  43              if (ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.getRawCodeHash(_address) == 0) {
+  44                  return AccountAbstractionVersion.Version1;
+  45              }
+  46  
+  47              return AccountAbstractionVersion.None;
+  48          }
+  49          else{
+  50              return info.supportedAAVersion;
+  51          }
   52      }

```

*Code link:* [[40-52](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L40-L52)]


***
&nbsp;

#### File: [Address.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol)
*Instances:* 1

```diff
File: Address.sol

-  297          if (returndata.length > 0) {
-  298              // The easiest way to bubble the revert reason is using memory via assembly
-  299              /// @solidity memory-safe-assembly
-  300              assembly {
-  301                  let returndata_size := mload(returndata)
-  302                  revert(add(32, returndata), returndata_size)
-  303              }
-  304          } else {
-  305              revert(errorMessage);
-  306          }
+  297          if (returndata.length <= 0) revert(errorMessage);
+  298  		
+  299          // The easiest way to bubble the revert reason is using memory via assembly
+  300          /// @solidity memory-safe-assembly
+  301          assembly {
+  302              let returndata_size := mload(returndata)
+  303              revert(add(32, returndata), returndata_size)
+  304          }

```

*Code link:* [[297-306](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L297-L306)]


***
&nbsp;

#### File: [RLPEncoder.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol)
*Instances:* 2

```diff
File: RLPEncoder.sol

Current Code:
-  26              if (_val < 128) {
-  27                  encoded = new bytes(1);
-  28                  // Handle zero as a non-value, since stripping zeroes results in an empty byte array
-  29                  encoded[0] = (_val == 0) ? bytes1(uint8(128)) : bytes1(uint8(_val));
-  30              } else {
-  31                  uint256 hbs = _highestByteSet(_val);
-  32  
-  33                  encoded = new bytes(hbs + 2);
-  34                  encoded[0] = bytes1(uint8(hbs + 0x81));
-  35  
-  36                  uint256 lbs = 31 - hbs;
-  37                  uint256 shiftedVal = _val << (lbs * 8);
-  38  
-  39                  assembly {
-  40                      mstore(add(encoded, 0x21), shiftedVal)
-  41                  }
-  42              }
+  26              if (_val >= 128) {
+  27                  uint256 hbs = _highestByteSet(_val);
+  28  
+  29                  encoded = new bytes(hbs + 2);
+  30                  encoded[0] = bytes1(uint8(hbs + 0x81));
+  31  
+  32                  uint256 lbs = 31 - hbs;
+  33                  uint256 shiftedVal = _val << (lbs * 8);
+  34  
+  35                  assembly {
+  36                      mstore(add(encoded, 0x21), shiftedVal)
+  37                  }
+  38              } else {
+  39                  encoded = new bytes(1);
+  40                  // Handle zero as a non-value, since stripping zeroes results in an empty byte array
+  41                  encoded[0] = (_val == 0) ? bytes1(uint8(128)) : bytes1(uint8(_val));
+  42              }

```

*Code link:* [[26-42](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L26-L42)]


***


```diff
File: RLPEncoder.sol

-  62              if (_len < 56) {
-  63                  encoded = new bytes(1);
-  64                  encoded[0] = bytes1(uint8(_len + _offset));
-  65              } else {
-  66                  uint256 hbs = _highestByteSet(uint256(_len));
-  67  
-  68                  encoded = new bytes(hbs + 2);
-  69                  encoded[0] = bytes1(uint8(_offset + hbs + 56));
-  70  
-  71                  uint256 lbs = 31 - hbs;
-  72                  uint256 shiftedVal = uint256(_len) << (lbs * 8);
-  73  
-  74                  assembly {
-  75                      mstore(add(encoded, 0x21), shiftedVal)
-  76                  }
-  77              }
+  62              if (_len >= 56) {
+  63                  uint256 hbs = _highestByteSet(uint256(_len));
+  64  
+  65                  encoded = new bytes(hbs + 2);
+  66                  encoded[0] = bytes1(uint8(_offset + hbs + 56));
+  67  
+  68                  uint256 lbs = 31 - hbs;
+  69                  uint256 shiftedVal = uint256(_len) << (lbs * 8);
+  70  
+  71                  assembly {
+  72                      mstore(add(encoded, 0x21), shiftedVal)
+  73                  }
+  74              } else {
+  75                  encoded = new bytes(1);
+  76                  encoded[0] = bytes1(uint8(_len + _offset));
+  77              }

```

*Code link:* [[62-77](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/RLPEncoder.sol#L62-L77)]


***
&nbsp;

                
### [GO-11] Using `>= n-1` instead of `> n`
                
It can be used when n is not zero.

*Instances:* 2

#### File: [SystemContext.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol)
*Instances:* 1

```diff
File: SystemContext.sol

-  115          if (blockNumber <= _block || blockNumber - _block > 256) {
+  115  	if (blockNumber <= _block || blockNumber - _block >= 255) {

```

*Code link:* [[115](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L115)]



***
&nbsp;

#### File: [L1Messenger.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)
*Instances:* 1

```diff
File: L1Messenger.sol

-  227          while (nodesOnCurrentLevel > 1) {
+  227          while (nodesOnCurrentLevel >= 2) {

```

*Code link:* [[227](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L227)]


***
&nbsp;

                
### [GO-12] Recoding L1Messenger.publishPubdataAndClearState() in order to save gas
                
This function can be modified in order to save gas. `calldataPtr` can be updated less times and some local variable can be deleted.

*Instances:* 1

#### File: [L1Messenger.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol)
*Instances:* 1

```diff
File: L1Messenger.sol

Current Code:
   [199-205 - No changes]
-  206          require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
   [207-234 - No changes]
-  235          bytes32 l2ToL1LogsTreeRoot = l2ToL1LogsTreeArray[0];
   [236-288 - No changes]
-  289          calldataPtr++;
   290  
-  291          uint24 compressedStateDiffSize = uint24(bytes3(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 3]));
+  291          uint24 compressedStateDiffSize = uint24(bytes3(_totalL2ToL1PubdataAndStateDiffs[calldataPtr-1:calldataPtr + 2]));
-  292          calldataPtr += 3;
   293  
-  294          uint8 enumerationIndexSize = uint8(bytes1(_totalL2ToL1PubdataAndStateDiffs[calldataPtr+4]));
-  295          calldataPtr++;
+  295          calldataPtr += 5;
   296  
   297          bytes calldata compressedStateDiffs = _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr +
   298              compressedStateDiffSize];
   299          calldataPtr += compressedStateDiffSize;
   300  
   301          bytes calldata totalL2ToL1Pubdata = _totalL2ToL1PubdataAndStateDiffs[:calldataPtr];
   302  
   303          require(calldataPtr <= MAX_ALLOWED_PUBDATA_PER_BATCH, "L1 Messenger pubdata is too long");
   304  
   305          uint32 numberOfStateDiffs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
   306          calldataPtr += 4;
   307  
-  308          bytes calldata stateDiffs = _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr +
-  309              (numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE)];
-  310          calldataPtr += numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE;
   311  
   312          bytes32 stateDiffHash = COMPRESSOR_CONTRACT.verifyCompressedStateDiffs(
   313              numberOfStateDiffs,
   314              enumerationIndexSize,
-  315              stateDiffs,
+  315              _totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + (numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE)];
   316              compressedStateDiffs
   317          );
   318  
   319          /// Check for calldata strict format
-  320          require(calldataPtr == _totalL2ToL1PubdataAndStateDiffs.length, "Extra data in the totalL2ToL1Pubdata array");
+  320          require(calldataPtr + numberOfStateDiffs * STATE_DIFF_ENTRY_SIZE == _totalL2ToL1PubdataAndStateDiffs.length, "Extra data in the totalL2ToL1Pubdata array");
   321  
   322          /// Native (VM) L2 to L1 log
-  323          SystemContractHelper.toL1(true, bytes32(uint256(SystemLogKey.L2_TO_L1_LOGS_TREE_ROOT_KEY)), l2ToL1LogsTreeRoot);
+  323          SystemContractHelper.toL1(true, bytes32(uint256(SystemLogKey.L2_TO_L1_LOGS_TREE_ROOT_KEY)), l2ToL1LogsTreeArray[0]);
   [324-336 - No changes]
```

*Code link:* [[294-324](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L294-L324)]


***
&nbsp;

                
### [GO-13] Avoid computing math operation at runtime
                
Some math operation can be computed manually, and the result value can be explicitly written in code.

Anyway, it is better if there is no magic number in code.

*Instances:* 1

#### File: [Compressor.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)
*Instances:* 1

```diff
File: Compressor.sol

-  62              require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");
+  62              require(dictionary.length <= 524288, "Dictionary is too big");

```

*Code link:* [[62](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L62)]


***
&nbsp;

                
### [GO-14] Checking easy condition before inside `if-else if-else'
                
Checking simpler condition before can save gas. This is valid when various conditions are equally likely.

*Instances:* 1

#### File: [Compressor.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol)
*Instances:* 1

```diff
File: Compressor.sol

-  234              if (_operation == 0 || _operation == 3) {
-  235                  require(convertedValue == _finalValue, "transform or no compression: compressed and final mismatch");
-  236              } else if (_operation == 1) {
-  237                  require(_initialValue + convertedValue == _finalValue, "add: initial plus converted not equal to final");
-  238              } else if (_operation == 2) {
-  239                  require(_initialValue - convertedValue == _finalValue, "sub: initial minus converted not equal to final");
+  234              if (_operation == 1) {
+  235                  require(_initialValue + convertedValue == _finalValue, "add: initial plus converted not equal to final");
+  236              } else if (_operation == 2) {
+  237                  require(_initialValue - convertedValue == _finalValue, "sub: initial minus converted not equal to final");
+  238              } else if (_operation == 0 || _operation == 3) {
+  239                  require(convertedValue == _finalValue, "transform or no compression: compressed and final mismatch");
   240              } else {
   241                  revert("unsupported operation");
   242              }

```

*Code link:* [[234-242](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L234-L242)]


***
&nbsp;

                
### [GO-15] Modify ternary operator in order to use == instead of !=
                
*Instances:* 1

#### File: [DefaultAccount.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol)
*Instances:* 1

```diff
File: DefaultAccount.sol

-  94          bytes32 txHash = _suggestedSignedHash != bytes32(0) ? _suggestedSignedHash : _transaction.encodeHash();
+  94          bytes32 txHash = _suggestedSignedHash == bytes32(0) ? _transaction.encodeHash() : _suggestedSignedHash;

```

*Code link:* [[94](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L94)]


***
&nbsp;

                
### [GO-16] Use alternative formulation in order to avoid NOT using AND instead of OR or vice versa
                
It's possible to change boolean formula in order to use AND instead of OR or vice versa.

In this way, ```>``` could be substituted with ```>=```, ```!=``` with ```==```

 In general

#### Two variables

```
(a || b) = !(!(a || b)) = !(!a && !b)
```

| a   | b   | (a or b)  | !a and !b  | !(!a and !b)|
|:---:|:---:|:---------:|:----------:|:------------:|
| 0  | 0  | 0         | 1          | 0            |
| 0  | 1  | 1         | 0          | 1            |
| 1  | 0  | 1         | 0          | 1            |
| 1  | 1  | 1         | 0          | 1            |


#### Three variables

```
(a || b || c) = !(!(a || b || c)) = !(!a && !b && !c)
```

| a   | b   | c   | (a or b or c)  | !a and !b and !c  | !(!a and !b and !c)|
|:---:|:---:|:---:|:--------------:|:-----------------:|:------------------:|
| 0  | 0  | 0 | 0              | 1                 | 0                  |
| 0  | 0  | 1 | 1              | 0                 | 1                  |
| 0  | 1  | 0 | 1              | 0                 | 1                  |
| 0  | 1  | 1 | 1              | 0                 | 1                  |
| 1  | 0  | 0 | 1              | 0                 | 1                  |
| 1  | 0  | 1 | 1              | 0                 | 1                  |
| 1  | 1  | 0 | 1              | 0                 | 1                  |
| 1  | 1  | 1 | 1              | 0                 | 1                  |


*Instances:* 1

#### File: [AccountCodeStorage.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol)
*Instances:* 1

```diff
File: AccountCodeStorage.sol

   131          if (
-  132              uint160(account) > CURRENT_MAX_PRECOMPILE_ADDRESS &&
-  133              codeHash != 0x00 &&
-  134              !Utils.isContractConstructing(codeHash)
+  132              !(
+  133                  uint160(account) <= CURRENT_MAX_PRECOMPILE_ADDRESS ||
+  134                  codeHash == 0x00 ||
+  135                  Utils.isContractConstructing(codeHash)
+  136              )
   135          ) {
   136              codeSize = Utils.bytecodeLenInBytes(codeHash);
   137          }

```

*Code link:* [[131-137](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L131-L137)]


***
&nbsp;
