## 1. INPUT VALIDATION CHECK FOR `address(0)` IS NOT PERFORMED FOR THE `_l1WithdrawReceiver` ADDRESS IN THE `Mailbox.finalizeEthWithdrawal` WHICH COULD LEAD TO LOSS OF FUNDS

The `Mailbox.finalizeEthWithdrawal` function is used to finalize the `Eth` withdrawal and release the funds. Once the `merkle proof` for the L2 withdrawal message is validated the `Eth` is released to the `_l1WithdrawReceiver` address which is parsed from the L2 withdraw data (`_message`).

Then the Eth withdrawal `_amount` is released to the `_l1WithdrawReceiver` address as shown below:

        _withdrawFunds(_l1WithdrawReceiver, _amount);

The `_withdrawFunds` function will release the funds using the low-level `call` function as shown below:

        assembly {
            callSuccess := call(gas(), _to, _amount, 0, 0, 0, 0)
        } 

But the issue here is that there is no validation check on `address(0)` for the `_l1WithdrawReceiver` address. If the parsed `_l1WithdrawReceiver` address is `address(0)` then the released `Eth` will be lost.

Hence it is recommended to check whether `_l1WithdrawReceiver` address is not equal to `address(0)` inside the `Mailbox.finalizeEthWithdrawal` before calling the `_withdrawFunds` function as shown below:

    require(_l1WithdrawReceiver != address(0), "Can not be address(0)");

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L207
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L116-L118

## 2. UNSAFE DOWNCASTING FROM `uint256` TO `uint8` IN THE `Executor._processL2Logs` FUNCTION

In the `Executor._processL2Logs` function the `_newBatch.systemLogs` bytes array which includes the L2 log details is processed to retreive the required details for the new batch commit.

The `logKey` of `uint256` type, is retrieved from the `emittedL2Logs` bytes array as shown below:

            (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET); 

But when the `logKey` value is used to check the `bitmap` of `processedLogs` variable the `logKey` is unsafely downcasted to `uint8` as shwon below:

            require(!_checkBit(processedLogs, uint8(logKey)), "kp");
            processedLogs = _setBit(processedLogs, uint8(logKey)); 

Hence even if the `logKey` contains a value greater than `(uint8).max` value, the transaction will still execute where as it should have `reverted` due to `overflow`.

Hence it is recommended to use `safeCasting` when downcasting `logKey` from `uint256` to `uint8`. 

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L126
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L130-L131

## 3. IT IS SUGGESTED TO IMPLEMENT THE `supportsInterface` FUNCTION OF THE `EIP-165` IN THE `DiamondProxy.sol` CONTRACT

The `DiamondProxy.sol` contract is used to initialize the `storage` of the L1 contracts in the zkSync protocol by initializing the `AppStorage` struct in the `Base.sol` contract. `DiamondProxy.sol` contract can be used for the upgradeablitliy of its facets (Other upgradeable contracts in the protocol) in the future as well. 

The `Natspec` comments given in the `DiamonProxy.sol` contract mentions that the contract follows the `EIP-2535` standard. But the current `DiamonProxy.sol` contract has not implemented the `supportsInterface` function of the `EIP-165` standard.

Hence it is suggested to implement the `supportsInterface` function of the `EIP-165` standard in the `DiamondProxy.sol` contract, so users of the protocol will be able to find the `interfaces` implemented in the `DimondProxy.sol` contract. This will make the interoperabilitiy of the `DiamondProxy` contract with the user-interfaces and other software-tools and libraries more effiecient and convienient.

For example the `support for  diamond loupe interface` can be depicted by returning the `IDiamondLoupe.facets.selector ^ IDiamondLoupe.facetFunctionSelectors.selector ^ IDiamondLoupe.facetAddresses.selector ^ IDiamondLoupe.facetAddress.selector` value as the `interfaceId` via the `supportsInterface` function.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L10-L56

## 4. `Mailbox._proveL2LogInclusion` FUNCTION IS VULNERABLE TO VERIFYING `L2Logs` WITH SHORTER `MERKLE PROOF` LENGTH

The `Mailbox._proveL2LogInclusion` function is used verify the inclusion of a `L2Log` inside the merkle tree of an executed L2 batch. The `_proveL2LogInclusion` function uses the `Merkle.calculateRoot` as shown below:

        bytes32 calculatedRootHash = Merkle.calculateRoot(_proof, _index, hashedLog);

But the issue here is that the length of the `_proof` is not checked in this function. Hence an attacker can exploit this with a shorther `_proof` path and a known intermediate node hash. Since the `leaf` consists of `bytes88` pre-image there is no immediate threat to the protocol. But if in the future the same `merkle.sol` library is used to verify the merkle root for `bytes32` pre-image `leafs` this could lead to `critical vulnerabilty` which an attacker can `exploit`.

Hence it is recommended to check the exact `_proof` length in the `Merkle.calculateRoot` function to ensure `merkle proofs` with shorter length and known intermediate node hashes are not incorrectly validated.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L142
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L18-L37

## 5. `L1WethBridge.constructor` FUNCTION IS ANYWAY GOING TO BE EXECUTED ONLY ONCE AND HENCE NO NEED TO USE THE REDUNDANT `reentrancyGuardInitializer` MODIFIER IN IT.

The `L1WethBridge.constructor` function is used for initialization of the proxy implementation during `L1WethBridge` deployment as shown below:

    constructor(address payable _l1WethAddress, IZkSync _zkSync, IAllowList _allowList) reentrancyGuardInitializer { //@audit-issue - use of reentrancyGuardInitializer is redundant
        l1WethAddress = _l1WethAddress;
        zkSync = _zkSync;
        allowList = _allowList;
    }

In the `constructor` function above `reentrancyGuardInitializer` is used to ensure the `constructor` is called only once. But this `modifier` is redundant since the `constructor` is anyway only called once. 

Hence it is recommended to omit the `reentrancyGuardInitializer` modifier from the `constructor` function and the modified `constructor` function is shown below:

    constructor(address payable _l1WethAddress, IZkSync _zkSync, IAllowList _allowList){
        l1WethAddress = _l1WethAddress;
        zkSync = _zkSync;
        allowList = _allowList;
    }

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L64
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L50

## 6. RETURN BOOLEAN VALUE OF `staticcall` LOW-LEVEL FUNCTION IS NOT CHECKED IN THE `L1ERC20Bridge._getERC20Getters` FUNCTION

The `L1ERC20Bridge._getERC20Getters` function is used to receive and parse the `name, symbol and decimals` values from the L1 `ERC20` token contract as shown below:

    function _getERC20Getters(address _token) internal view returns (bytes memory data) {
        (, bytes memory data1) = _token.staticcall(abi.encodeCall(IERC20Metadata.name, ()));
        (, bytes memory data2) = _token.staticcall(abi.encodeCall(IERC20Metadata.symbol, ()));
        (, bytes memory data3) = _token.staticcall(abi.encodeCall(IERC20Metadata.decimals, ()));
        data = abi.encode(data1, data2, data3);
    } 

The issue here is that the `return boolean` value of the `staticcall` low-level function is not checked for `success`. Hence even if the `staticcall` to the `L1 ERC20` token contract reverts, the `_getERC20Getters` transaction will proceed as succesful. Hence the returning `data` bytes variable will be errorneous and will not provide the correct representation of the `L1 ERC20` token contract details.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L240-L245

## 7. RECOMMENDED TO EMIT AN EVENT IN THE `Diamond._saveFacetIfNew` FUNCTION ONCE A NEW `_facet` IS ADDED TO THE DIAMOND PROXY

The `Diamond._saveFacetIfNew` function is used to add address to the list of known facets if it is not on the list yet. Initially there is a conditional check to ensure that the `_facet` is a new address by checking its `selectorsLength` and verifying the `selectorsLength == 0`. Once it is confirmed that the `_face` is a new address then the `_facet` is added to the `ds.facets` array as shown below:

            ds.facets.push(_facet);

But the issue here is that once the `_facet` is added there is no event emitted to communicate that this particular `_facet` address is added as a new `_facet` to the diamond proxy. 

Hence it is recommneded to emit an event for this critical function to communicate to the offchain tools that a new `_facet` is added to the `diamond proxy` once the `Diamond._saveFacetIfNew` function is succesfully executed.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L195-L198

## 8. IT IS RECOMMENDED FOR THE `EthReceived` EVENT TO EMIT `msg.sender` IN THE `L1WethBridge.receive` TO IDENTIFY THE TRANSACTION 

The `L1WethBridge.receive` function is used to receive ether when l1 WETH sends ether on `withdraw` or zkSync contract withdraw funds in `finalizeEthWithdrawal` transaction execution. Once the `Eth` is received the `L1WethBridge.receive` emits `EthReceived` event as shown below:

        emit EthReceived(msg.value);

But the issue here is that the event only emits `msg.value` but does not emit the `msg.sender`. Hence it is not possible for the off-chain tools or other analysis tools to identify from from which transaction the `Eth` was received to the `L1WethBridge` contract. 

Hence it is recommended to emit the `msg.sender` address in the `EthReceived` event as well. The modified line of code is shown below:

    event EthReceived(address sender, uint256 amount);

        emit EthReceived(msg.sender, msg.value);

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L312
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L41
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L121
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L23

## 9. `Executor.revertBatches` FUNCTION DOES NOT DELETE THE `s.l2SystemContractsUpgradeTxHash` IF THE CORRESPONDING BATCH REVERTS

In the `Executor.revertBatches` function, if the batch in which the system contracts upgrade was committed, is among the reverted batches, should reset the batch number of the executed system contracts upgrade transaction. 

The above logic is implemented as shown below:

        if (s.l2SystemContractsUpgradeBatchNumber > _newLastBatch) {
            delete s.l2SystemContractsUpgradeBatchNumber;
        }

But the issue here is that the `s.l2SystemContractsUpgradeTxHash` is not `deleted` even if the batch with the system contract upgrade transaction is reverted. 

Hence it is recommended to delete the `s.l2SystemContractsUpgradeTxHash` as well, if the batch in which the system contracts upgrade was committed, is among the reverted batches.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L409-L411

## 10. RECOMMENDED TO USE `require` INSTEAD OF `assert` FOR CONDITIONAL CHECKS

The `DiamondInit.initialize` function is used to initialize the `diamond proxy` which is used to upgrade the `L1 contracts` in the zkSync. 

In the `DiamondInit.initialize` there is a conditional check to ensure that the `length` of the `L2Log encoding` is `not equal` to the `length` of `other L2Log's` tree `nodes preimages`.

It is implemented as shown below:

        assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

The issue here is that it uses `assert` for the conditional check. `assert` is used in the solidity to check for invariants that should never be `false`. 

Hence it is recommended to replace the `assert` with the `require` statement as shown below:

        require(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32, "L2Log lengths are equal");

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L87

## 11. LACK OF INPUT VALIDATION FOR THE `previousBatchHash` VARIABLE IN THE `Executor._commitOneBatch` FUNCTION COULD LEAD TO UNINTENDED BEHAVIOUR

In the `Executor._commitOneBatch` function the `batchHash` of the `_previousBatch` struct and the `batchHash` returned from the call to the `_processL2Logs` function are checked for `equality` as shown below:

        require(_previousBatch.batchHash == previousBatchHash, "l");

But there is no input validation to ensure niether of the `_previousBatch.batchHash` or `previousBatchHash` is != bytes32(0). Hence the `require` statement could succeed if both the `_previousBatch.batchHash` and `previousBatchHash` equals the `bytes(0)`. This is not the intended behaviour since the `previousBatchHash` is expected to hold a valid value in it.

Hence it is recommended to add a `previousBatchHash != bytes32(0)` check either in the `Executor._commitOneBatch` function or in the `Executor._processL2Logs` to ensure valid data is returned and compared against each other.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L45
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L146-L148

## 12. DISCREPENCY IN THE NATSPEC COMMENTS IN THE `Storage.AppStorage` STRUCT

In the `Storage.AppStorage` struct the following two `Natspec` comments are given related to `address governor` and `address admin` variables as shown below:

    /// @notice Address which will exercise critical changes to the Diamond Proxy (upgrades, freezing & unfreezing)
    address governor;


    /// @dev Address which will exercise non-critical changes to the Diamond Proxy (changing validator set & unfreezing)
    address admin;

In the `Natspec` comment for `governor` it mentions the `unfreezing` is a critical change to the Diamond Proxy where as in the `Natspec` comment for `admin` it mentions the `unfreezing` is a non-critical change to the Diamond Proxy. Hence there is a discrepency between the `two Natspec` comments. 

Hence it is recommended to correct the `Natspec` comments to remove the confusion regarding the criticality of the `unfreezing` operation.

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L82-L83
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L142-L143

## 13. TYPO'S IN THE NATSPEC COMMENTS SHOULD BE CORRECTED

        // Check that the batch passed by the validator is indeed the `first unverified` batch

Above comment should be corrected as follows:      

        // Check that the batch passed by the validator is indeed the `last verified` batch

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L326-L327

        // The last committed batch is `less than new last batch`

Above comment should be corrected as follows: 

        // The last committed batch is `less than or equal to new last batch`    

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L399   

    /// @param _status The execution status of the L1 -> L2 transaction (`true - success` & 0 - fail)

Above comment should be corrected as follows:

    /// @param _status The execution status of the L1 -> L2 transaction (`1 - success` & 0 - fail)

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L79

        // bytes4 function signature + address l1Receiver + uint256 amount + address l2Sender + bytes _additionalData =
        // = 4 + 20 + 32 + `32` + _additionalData.length >= `68` (bytes).

In the above `bytes amount` calculation the `address l2Sender` is considered as `32 bytes` where as it should be `20 bytes`. And as a result the total bytes amount should be >=76.
Hence the above comment should be corrected as follows:

        // bytes4 function signature + address l1Receiver + uint256 amount + address l2Sender + bytes _additionalData =
        // = 4 + 20 + 32 + `20` + _additionalData.length >= `76` (bytes).

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L417-L418

    /// @param _l2Receiver The account address that would receive `minted ether`

Above comment should be corrected as follows:

    /// @param _l2Receiver The account address that would receive `minted L2 tokens`

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L59