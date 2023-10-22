QA1. The constructor of ``AllowList`` fails to pass arguments to the base contract ``Ownable``.
Similarly, the constructor of ``ValidatorTimeLock``  fails to pass arguments to the base contract ``Ownable``.
Similarly, the constructor of ``Governance``  fails to pass arguments to the base contract ``Ownable``.


[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L31-L33](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L31-L33)

Mitigation:

```diff
- constructor(address _initialOwner) {
+ constructor(address _initialOwner) Ownable(_initialOwner) {

-        _transferOwnership(_initialOwner);
    }
```

QA2. When the argument ``_refundRecipient = address(0)``, _requestL2Transaction()._requestL2Transaction() will use ``_sender`` as the refundRecipient. The problem is that _requestL2Transaction() might apply AddressAliasHelper.AddressAliasHelperapplyL1ToL2Alias(_sender) again even though _sender is already the result of AddressAliasHelper.AddressAliasHelperapplyL1ToL2Alias(msg.sender) (see L249). 

The mitigation is that we do not need to apply L1ToL2Alisas translation when _refundRecipient == _sender.

```diff

        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
        s.totalDepositedAmountPerUser[_depositor] += _amount;
    }

    function _requestL2Transaction(
        address _sender,
        address _contractAddressL2,
        uint256 _l2Value,
        bytes calldata _calldata,
        uint256 _l2GasLimit,
        uint256 _l2GasPerPubdataByteLimit,
        bytes[] calldata _factoryDeps,
        bool _isFree,
        address _refundRecipient
    ) internal returns (bytes32 canonicalTxHash) {
        require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "uj");
        uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast
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
-        if (refundRecipient.code.length > 0) {
+        if (_refundRecipient != _sender && refundRecipient.code.length > 0) {
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

        canonicalTxHash = _writePriorityOp(params, _calldata, _factoryDeps);
    }
```

QA3. Mailbox._proveL2LogInclusion() fails to ensure that ``_batchNumber < s.totalBatchesExecuted``. This is important since the largest ``_batchNumber`` that can be verified is ``s.totalBatchesExecuted - 1``. 

Mitiation: 

```diff
function _proveL2LogInclusion(
        uint256 _batchNumber,
        uint256 _index,
        L2Log memory _log,
        bytes32[] calldata _proof
    ) internal view returns (bool) {
        console2.log("\n _proveL2LogInclusion() started....");

-        require(_batchNumber <= s.totalBatchesExecuted, "xx");
+        require(_batchNumber < s.totalBatchesExecuted, "xx");

        bytes32 hashedLog = keccak256(
            abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBatch, _log.sender, _log.key, _log.value)
        );
        // Check that hashed log is not the default one,
        // otherwise it means that the value is out of range of sent L2 -> L1 logs
        require(hashedLog != L2_L1_LOGS_TREE_DEFAULT_LEAF_HASH, "tw");

        // It is ok to not check length of `_proof` array, as length
        // of leaf preimage (which is `L2_TO_L1_LOG_SERIALIZE_SIZE`) is not
        // equal to the length of other nodes preimages (which are `2 * 32`)

        bytes32 calculatedRootHash = Merkle.calculateRoot(_proof, _index, hashedLog);
        bytes32 actualRootHash = s.l2LogsRootHashes[_batchNumber];

        console2.log("\n completed with %d", actualRootHash == calculatedRootHash");
        return actualRootHash == calculatedRootHash;
    }
```

QA4. _proveL2LogInclusion() calls Merkle.calculateRoot() to prove that a specific L2 log was sent in a specific L2 batch number. However, it fails to check and make sure that the length of the proof is equal to the height of the Merkle tree of ``_batchNumber``. As a result, shorter/longer paths attack might be possible. 

[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L142](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L142)

QA5. L2WethBridge.finalizeDeposit() fails to apply AddressAliasHelper.undoL1ToL2Alias to ``_l1Token`` before comparing to ``l1WethAddress``. As a result, the require condition will always fail and the transaction might always revert. 

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L88-L107

Mitigation:

```diff
function finalizeDeposit(
        address _l1Sender,
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        bytes calldata // _data
    ) external payable override {
        require(
            AddressAliasHelper.undoL1ToL2Alias(msg.sender) == l1Bridge,
            "Only L1 WETH bridge can call this function"
        );

-        require(_l1Token == l1WethAddress, "Only WETH can be deposited");
+        require(AddressAliasHelper.undoL1ToL2Alias(_l1Token) == l1WethAddress, "Only WETH can be deposited");

        require(msg.value == _amount, "Amount mismatch");

        // Deposit WETH to L2 receiver.
        IL2Weth(l2WethAddress).depositTo{value: msg.value}(_l2Receiver);

        emit FinalizeDeposit(_l1Sender, _l2Receiver, l2WethAddress, _amount);
    }
```

QA6. Diamond._replaceFunctions() does not check whether the old facet is the same as the new facet for a function that needs to be replaced. So it is not  EIP-2535 compliant. 

[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L149-L169](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L149-L169)

Mitigation: 
check whether the old facet is the same as the new facet for a function that needs to be replaced.

QA7. Unlike other functions, ``Efficientcall.rawMimicCall()`` does not clean input param ``_address``  as it is NOT cleaned by Solidity by default. As a result, this input might be dirty and might lead to unexpected result. 

[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L191-L210](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L191-L210)

Mitigation:

```diff
function rawMimicCall(
        uint256 _gas,
        address _address,
        bytes calldata _data,
        address _whoToMimic,
        bool _isConstructor,
        bool _isSystem
    ) internal returns (bool success) {
        _loadFarCallABIIntoActivePtr(_gas, _data, _isConstructor, _isSystem);

        address callAddr = MIMIC_CALL_BY_REF_CALL_ADDRESS;
        uint256 cleanupMask = ADDRESS_MASK;
        assembly {
            // Clearing values before usage in assembly, since Solidity
            // doesn't do it by default
            _whoToMimic := and(_whoToMimic, cleanupMask)

+           _address := and(_address, cleanupMask)
            success := call(_address, callAddr, 0, 0, _whoToMimic, 0, 0)
        }
    }
```

QA8.  Unlike other functions, ``Efficientcall.rawcall()`` does not clean input param ``_address``  as it is NOT cleaned by Solidity by default. As a result, this input might be dirty and might lead to unexpected result.

same issue for: ``Efficientcall.rawDelegateCall()``, ``Efficientcall.rawStaticCall()``, ``rawCall()``.


[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L124C14-L152](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L124C14-L152)

Mitigation:

```diff
 function rawCall(
        uint256 _gas,
        address _address,
        uint256 _value,
        bytes calldata _data,
        bool _isSystem
    ) internal returns (bool success) {
+       uint256 cleanupMask = ADDRESS_MASK;
        if (_value == 0) {
            _loadFarCallABIIntoActivePtr(_gas, _data, false, _isSystem);

            address callAddr = RAW_FAR_CALL_BY_REF_CALL_ADDRESS;
            assembly {
+               _address := and(_address, cleanupMask)
                success := call(_address, callAddr, 0, 0, 0xFFFF, 0, 0)
            }
        } else {
            _loadFarCallABIIntoActivePtr(_gas, _data, false, true);

            // If there is provided `msg.value` call the `MsgValueSimulator` to forward ether.
            address msgValueSimulator = MSG_VALUE_SYSTEM_CONTRACT;
            address callAddr = SYSTEM_CALL_BY_REF_CALL_ADDRESS;
            // We need to supply the mask to the MsgValueSimulator to denote
            // that the call should be a system one.
            uint256 forwardMask = _isSystem ? MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT : 0;

            assembly {
+               _address := and(_address, cleanupMask)

                success := call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)
            }
        }
    }
```

QA9. Make the components that are imported explicit rather than implicit:

[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/BootloaderUtilities.sol#L5-L8](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/BootloaderUtilities.sol#L5-L8)

QA10. ImmutableSimulator.setImmutables() fails to 1) whether it overwrites an existing value for a particular index; 2) check wether the input immutables have duplicate index that point to different values.
As a result, an immutable entry is not actually immutable, the entry can be revised. Two values can be entered into the same entry for the same contract with the second replacing the value of the first. 

[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ImmutableSimulator.sol#L34-L45](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ImmutableSimulator.sol#L34-L45)

The following POC confirms my finding: it shows duplicate entries are fine (two index entries with index 23); existing entry (index 21) can be replaced. 

```javascript
import { expect } from 'chai';
import { ImmutableSimulator } from '../typechain-types';
import { DEPLOYER_SYSTEM_CONTRACT_ADDRESS } from './shared/constants';
import { Wallet } from 'zksync-web3';
import { getWallets, deployContract } from './shared/utils';
import { network, ethers } from 'hardhat';

describe('ImmutableSimulator tests', function () {
    let wallet: Wallet;
    let immutableSimulator: ImmutableSimulator;

    const RANDOM_ADDRESS = '0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef';
    const IMMUTABLES_DATA = [
        {
            index: 0,
            value: '0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef'
        },
        {
            index: 23,
            value: '0x0000000000000000000000000000000000000000000000000000000000000111'
        },

        {
            index: 21,
            value: '0x0000000000000000000000000000000000000000000000000000000000000123'
        },

        {
            index: 23,
            value: '0x0000000000000000000000000000000000000000000000000000000000000123'
        }
    ];

    const IMMUTABLES_DATA2 = [
        {
            index: 21,
            value: '0x0000000000000000000000000000000000000000000000000000000000000444'
        }
    ];

    before(async () => {
        wallet = getWallets()[0];
        immutableSimulator = (await deployContract('ImmutableSimulator')) as ImmutableSimulator;
    });

    describe('setImmutables', function () {
        it('non-deployer failed to call', async () => {
            await expect(immutableSimulator.setImmutables(RANDOM_ADDRESS, IMMUTABLES_DATA)).to.be.revertedWith(
                'Callable only by the deployer system contract'
            );
        });

       
        it('Duplicate set', async () => {
            await network.provider.request({
                method: 'hardhat_impersonateAccount',
                params: [DEPLOYER_SYSTEM_CONTRACT_ADDRESS]
            });

            const deployer_account = await ethers.getSigner(DEPLOYER_SYSTEM_CONTRACT_ADDRESS);

            await immutableSimulator.connect(deployer_account).setImmutables(RANDOM_ADDRESS, IMMUTABLES_DATA);

            await network.provider.request({
                method: 'hardhat_stopImpersonatingAccount',
                params: [DEPLOYER_SYSTEM_CONTRACT_ADDRESS]
            });

            expect(await immutableSimulator.getImmutable(RANDOM_ADDRESS, 23)).to.be.eq(
                    '0x0000000000000000000000000000000000000000000000000000000000000123'
            );
        });
        it('Existing entries', async () => {
            await network.provider.request({
                method: 'hardhat_impersonateAccount',
                params: [DEPLOYER_SYSTEM_CONTRACT_ADDRESS]
            });

            const deployer_account = await ethers.getSigner(DEPLOYER_SYSTEM_CONTRACT_ADDRESS);

            await immutableSimulator.connect(deployer_account).setImmutables(RANDOM_ADDRESS, IMMUTABLES_DATA);
            await immutableSimulator.connect(deployer_account).setImmutables(RANDOM_ADDRESS, IMMUTABLES_DATA2);  // fail to detect exiting entries

            await network.provider.request({
                method: 'hardhat_stopImpersonatingAccount',
                params: [DEPLOYER_SYSTEM_CONTRACT_ADDRESS]
            });

            expect(await immutableSimulator.getImmutable(RANDOM_ADDRESS, 21)).to.be.eq(
                    '0x0000000000000000000000000000000000000000000000000000000000000444'
            );
        });
    });
});

```

QA11.  KnownCodesStorage._validateBytecode() fails to validate that the length of the bytecode in words is less then 2**16. 

Mitigation: Add sth like this 

`` javasript
require(bytecodeLenInWords < 2 ** 16, "pp"); // bytecode length must be less than 2^16 words
``

QA12. when _callConstructor = false, _constructContract() fails to  call _storeConstructingByteCodeHashOnAddress first before calling  
ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.markAccountCodeHashAsConstructed(_newAddress). Instead, it calls 
ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.storeAccountConstructedCodeHash(_newAddress, _bytecodeHash) directly. The problem is that it might due to _bytecodeHash is not in the state of ConstructedCodeHash. 

[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L353](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L353)

Mitigation: when _callConstructor = false, 
 call _storeConstructingByteCodeHashOnAddress first before calling  
ACCOUNT_CODE_STORAGE_SYSTEM_CONTRACT.markAccountCodeHashAsConstructed(_newAddress). 

QA13. AccountCodeStorage.getCodeHash() and  AccountCodeStorage.getCodeSize() use uint256 _input as address. The problem is that many uint256 input will be mapped to the same address. 

[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/AccountCodeStorage.sol#L89-L138](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/AccountCodeStorage.sol#L89-L138)

Mitigation: changes uint256 _input to address _input.

QA13. Should be eliminate ``=``, needs to be strictly greater than here:

```javascript
            _block > currentVirtualBlockUpgradeInfo.virtualBlockFinishL2Block &&
            currentVirtualBlockUpgradeInfo.virtualBlockFinishL2Block > 0
        ) {
``

[https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L122](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L122)