
## LOW Risk

| Number | Issue | Instances |
|--------|-------|-----------|
|[L-01]|  symbol() is not a part of the ERC-20 standard | 2 |
|[L-02]| Variable being initialized with the default value   | 4 |
|[L-03]| Setters should have initial value check   | 8 |
|[L-04]| unchecked blocks with additions/multiplications may overflow   | 22 |
|[L-05]| Array lengths not checked   | 4 |
|[L-06]| Large approvals may not work with some ERC20 tokens   | 2 |
|[L-07]| Functions which set address state variables should have zero address checks   | 8 |
|[L-08]| `_addOneFunction()` does note remove old entries before adding new ones   | 2 |
|[L-09]| Consider implementing two-step procedure for updating protocol addresses   | 3 |
|[L-10]| Tokens may be minted to `address(0x0)`  | 1 |
|[L-11]| decimals() is not a part of the ERC-20 standard   | 1 |


## [L-01] symbol() is not a part of the ERC-20 standard

The symbol() function is not a part of the ERC-20 standard, and was added later as an optional extension. As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.


```solidity
file: contracts/zksync/contracts/bridge/L2StandardERC20.sol

132   return super.symbol();

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L132 

```solidity
file:  ethereum/contracts/bridge/L1ERC20Bridge.sol

242    (, bytes memory data2) = _token.staticcall(abi.encodeCall(IERC20Metadata.symbol, ()));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L242


## [L-02] Variable being initialized with the default value

Unsigned integers will already be initalized with zero on their declaration, e.g. there’s no need to manually assign zero.

```solidity
file:  ethereum/contracts/zksync/libraries/TransactionValidator.sol

92   uint256 costForPubdata = 0;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L92


```solidity
file:  system-contracts/contracts/Compressor.sol

131    uint256 numInitialWritesProcessed = 0;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L131


```solidity
file:  system-contracts/contracts/ContractDeployer.sol

247   uint256 sumOfValues = 0;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/ContractDeployer.sol#L247

```solidity
file:  system-contracts/contracts/L1Messenger.sol

202    uint256 calldataPtr = 0;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L202


## [L-03] Setters should have initial value check

Setters should have initial value check to prevent assigning wrong value to the variable. Assginment of wrong value can lead to unexpected behavior of the contract.


```solidity
file:  ethereum/contracts/common/interfaces/IAllowList.sol
   
58   function setBatchAccessMode(address[] calldata _targets, AccessMode[] calldata _accessMode) external;

60   function setAccessMode(address _target, AccessMode _accessMode) external;

62   function setBatchPermissionToCall(

69   function setPermissionToCall(

60   function setDepositLimit(

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol#L58

```solidity
file: system-contracts/contracts/libraries/SystemContractHelper.sol

189   function setValueForNextFarCall(uint128 _value) internal returns (bool success) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L189

```solidity
file: ethereum/contracts/zksync/facets/Admin.sol

20  function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {

44  function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L20


## [L-04] unchecked blocks with additions/multiplications may overflow

There aren't any checks to avoid an overflow which can happen inside an unchecked block, so the following expressions may overflow silently.


```solidity
file: ethereum/contracts/vendor/AddressAliasHelper.sol

29   unchecked {
            l2Address = address(uint160(l1Address) + offset);
        }
    
39   unchecked {
            l1Address = address(uint160(l2Address) - offset);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L29-L31


```solidity
file: ethereum/contracts/zksync/libraries/TransactionValidator.sol

118   unchecked {
            // We enforce the fact that `_totalGasLimit >= overhead` explicitly above.
            txBodyGasLimit = _totalGasLimit - overhead;
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L118-L21

```solidity
file:  system-contracts/contracts/BootloaderUtilities.sol

104      unchecked {
            uint256 listLength = encodedNonce.length +
                encodedGasParam.length +
                encodedTo.length +
                encodedValue.length +
                encodedDataLength.length +
                _transaction.data.length +
                rEncoded.length +
                sEncoded.length +
                vEncoded.length;

            // Safe cast, because the length of the list can't be so large.
            encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
        }

197     unchecked {
            uint256 listLength = encodedFixedLengthParams.length +
                encodedDataLength.length +
                _transaction.data.length +
                encodedAccessListLength.length +
                rEncoded.length +
                sEncoded.length +
                vEncoded.length;

            // Safe cast, because the length of the list can't be so large.
            encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
        }

292    unchecked {
            uint256 listLength = encodedFixedLengthParams.length +
                encodedDataLength.length +
                _transaction.data.length +
                encodedAccessListLength.length +
                rEncoded.length +
                sEncoded.length +
                vEncoded.length;

            // Safe cast, because the length of the list can't be so large.
            encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/BootloaderUtilities.sol#L104-L117

```solidity
file: system-contracts/contracts/L2EthToken.sol

42     unchecked {
            balance[_from] = fromBalance - _amount;
            // Overflow not possible: the sum of all balances is capped by totalSupply, and the sum is preserved by
            // decrementing then incrementing.
            balance[_to] += _amount;
        }

103    unchecked {
            // This is safe, since this contract holds the ether balances, and if user
            // send a `msg.value` it will be added to the contract (`this`) balance.
            balance[address(this)] -= amount;
            totalSupply -= amount;
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L42-L47

```solidity
file: code/system-contracts/contracts/NonceHolder.sol
 
71    unchecked {
            rawNonces[addressAsKey] = (oldRawNonce + _value);
        }
117   unchecked {
            rawNonces[addressAsKey] = oldRawNonce + 1;
        }
    
140     unchecked {
            rawNonces[addressAsKey] = (oldRawNonce + DEPLOY_NONCE_MULTIPLIER);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/NonceHolder.sol#L71-L73

```solidity
file:  system-contracts/contracts/libraries/TransactionHelper.sol
 
189    unchecked {
            uint256 listLength = encodedNonce.length +
                encodedGasParam.length +
                encodedTo.length +
                encodedValue.length +
                encodedDataLength.length +
                _transaction.data.length +
                encodedChainId.length;

            // Safe cast, because the length of the list can't be so large.
            encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
        }

264   unchecked {
            uint256 listLength = encodedFixedLengthParams.length +
                encodedDataLength.length +
                _transaction.data.length +
                encodedAccessListLength.length;

            // Safe cast, because the length of the list can't be so large.
            encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
        }

336   unchecked {
            uint256 listLength = encodedFixedLengthParams.length +
                encodedDataLength.length +
                _transaction.data.length +
                encodedAccessListLength.length;

            // Safe cast, because the length of the list can't be so large.
            encodedListLength = RLPEncoder.encodeListLen(uint64(listLength));
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L189-L100

```solidity
file: system-contracts/contracts/L1Messenger.sol

143    unchecked {
            // 4 bytes used to encode the length of the message (see `publishPubdataAndClearState`)
            // L2_TO_L1_LOG_SERIALIZE_SIZE bytes used to encode L2ToL1Log
            pubdataLen = 4 + _message.length + L2_TO_L1_LOG_SERIALIZE_SIZE;
        }
177   unchecked {
            // 4 bytes used to encode the length of the bytecode (see `publishPubdataAndClearState`)
            pubdataLen = 4 + bytecodeLen;
        }


```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L1Messenger.sol#L143-L147

```solidity
file: ethereum/contracts/vendor/AddressAliasHelper.sol

29    unchecked {
            l2Address = address(uint160(l1Address) + offset);
        }

39     unchecked {
            l1Address = address(uint160(l2Address) - offset);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L29-L31

```solidity
file:   ethereum/contracts/zksync/libraries/LibMap.sol

17     unchecked {
            // Each storage slot can store 256 bits of data.
            // As uint32 is 32 bits long, 8 uint32s can be packed into one storage slot.
            // Hence, `_index / 8` is done to find the storage slot that contains the required uint32.
            uint256 mapValue = _map.map[_index / 8];

            // First three bits of the original `_index` denotes the position of the uint32 in that slot.
            // So, '(_index & 7) * 32' is done to find the bit position of the uint32 in that storage slot.
            uint256 bitOffset = (_index & 7) * 32;

            // Shift the bits to the right and retrieve the uint32 value.
            result = uint32(mapValue >> bitOffset);
        }
41     unchecked {
            // Each storage slot can store 256 bits of data.
            // As uint32 is 32 bits long, 8 uint32s can be packed into one storage slot.
            // Hence, `_index / 8` is done to find the storage slot that contains the required uint32.
            uint256 mapIndex = _index / 8;
            uint256 mapValue = _map.map[mapIndex];

            // First three bits of the original `_index` denotes the position of the uint32 in that slot.
            // So, '(_index & 7) * 32' is done to find the bit position of the uint32 in that storage slot.
            uint256 bitOffset = (_index & 7) * 32;

            // XORing a value A with B, and then with A again, gives the original value B.
            // We will use this property to update the uint32 value in the slot.

            // Shift the bits to the right and retrieve the uint32 value.
            uint32 oldValue = uint32(mapValue >> bitOffset);

            // Calculate the XOR of the new value and the existing value.
            uint256 newValueXorOldValue = uint256(oldValue ^ _value);

            // Finally, we XOR the slot with the XOR of the new value and the existing value,
            // shifted to its proper position. The XOR operation will effectively replace the old value with the new value.
            _map.map[mapIndex] = (newValueXorOldValue << bitOffset) ^ mapValue;
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L17-L29

```solidity
file:  ethereum/contracts/common/libraries/UncheckedMath.sol

12    unchecked {
            return _number + 1;
        }

18    unchecked {
            return _lhs + _rhs;
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UncheckedMath.sol#L18-L20


## [L-05] Array lengths not checked

If the length of the arrays are not required to be of the same length, user operations may not be fully executed due to a mismatch in the number of items iterated over, versus the number of items provided in the second array

```solidity
file:  ethereum/contracts/common/AllowList.sol

83    function setBatchPermissionToCall(
        address[] calldata _callers,
        address[] calldata _targets,
        bytes4[] calldata _functionSigs,
        bool[] calldata _enables
    ) external onlyOwner {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L83-L88

```solidity
file:  ethereum/contracts/common/interfaces/IAllowList.sol 

62     function setBatchPermissionToCall(
        address[] calldata _callers,
        address[] calldata _targets,
        bytes4[] calldata _functionSigs,
        bool[] calldata _enables
    ) external;

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/interfaces/IAllowList.sol#L62-L67

```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

125    function _addFunctions(
        address _facet,
        bytes4[] memory _selectors,
        bool _isFacetFreezable
    ) private {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L125-L131


```solidity
file:  ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol

30       function requestDeployTransaction(
        IZkSync _zkSync,
        uint256 _deployTransactionFee,
        bytes32 _bytecodeHash,
        bytes memory _constructorData,
        bytes[] memory _factoryDeps
    ) internal returns (address deployedAddress) {

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol#L30-L36


## [L-06] Large approvals may not work with some ERC20 tokens

Not all IERC20 implementations are totally compliant, and some (e.g UNI, COMP) may fail if the valued passed to approve is larger than uint96. If the approval amount is type(uint256).max, which may cause issues with systems that expect the value passed to approve to be reflected in the allowances mapping.

```solidity
file: system-contracts/contracts/libraries/TransactionHelper.sol

382    IERC20(token).safeApprove(paymaster, 0);

383    IERC20(token).safeApprove(paymaster, minAllowance);

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/TransactionHelper.sol#L382

## [L-07] Functions which set address state variables should have zero address checks

```solidity
file:  system-contracts/contracts/SystemContext.sol

87   function setTxOrigin(address _newOrigin) external onlyCallFromBootloader {
        origin = _newOrigin;
    }

93   function setGasPrice(uint256 _gasPrice) external onlyCallFromBootloader {
        gasPrice = _gasPrice;
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L87-L89

```solidity
file: ethereum/contracts/zksync/ValidatorTimelock.sol

52    function setValidator(address _newValidator) external onlyOwner {
        address oldValidator = validator;
        validator = _newValidator;
        emit NewValidator(oldValidator, _newValidator);
    }

59  function setExecutionDelay(uint32 _executionDelay) external onlyOwner {
        executionDelay = _executionDelay;
        emit NewExecutionDelay(_executionDelay);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L52-L56

```solidity
file: ethereum/contracts/governance/Governance.sol

256  function updateSecurityCouncil(address _newSecurityCouncil) external onlySelf {
        emit ChangeSecurityCouncil(securityCouncil, _newSecurityCouncil);
        securityCouncil = _newSecurityCouncil;
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L256-L259

```solidity
file: code/system-contracts/contracts/L2EthToken.sol

64  function mint(address _account, uint256 _amount) external override onlyCallFromBootloader {
        totalSupply += _amount;
        balance[_account] += _amount;
        emit Mint(_account, _amount);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L64-L68

```solidity
file: ethereum/contracts/common/AllowList.sol

68   function _setAccessMode(address _target, AccessMode _accessMode) internal {
        AccessMode accessMode = getAccessMode[_target];

        if (accessMode != _accessMode) {
            getAccessMode[_target] = _accessMode;
            emit UpdateAccessMode(_target, accessMode, _accessMode);
        }
    }
116  function _setPermissionToCall(address _caller, address _target, bytes4 _functionSig, bool _enable) internal {
        bool currentPermission = hasSpecialAccessToCall[_caller][_target][_functionSig];

        if (currentPermission != _enable) {
            hasSpecialAccessToCall[_caller][_target][_functionSig] = _enable;
            emit UpdateCallPermission(_caller, _target, _functionSig, _enable);
        }
    }


```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L68-L75


## [L-08] `_addOneFunction()` does note remove old entries before adding new ones

Each time `_addOneFunction()` is called, new entries are added to the array, but doing so does not remove any old entries. By calling the function multiple times, an attacker can can increase their voting power indefinitely, without having to acquire new tokens.

```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

206   function _addOneFunction(
        address _facet,
        bytes4 _selector,
        bool _isSelectorFreezable
    ) private {
        DiamondStorage storage ds = getDiamondStorage();

        uint16 selectorPosition = (ds.facetToSelectors[_facet].selectors.length).toUint16();

        // if selectorPosition is nonzero, it means it is not a new facet
        // so the freezability of the first selector must be matched to _isSelectorFreezable
        // so all the selectors in a facet will have the same freezability
        if (selectorPosition != 0) {
            bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];
            require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
        }

        ds.selectorToFacet[_selector] = SelectorToFacet({
            facetAddress: _facet,
            selectorPosition: selectorPosition,
            isFreezable: _isSelectorFreezable
        });
        ds.facetToSelectors[_facet].selectors.push(_selector);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L206-L229

```solidity
file:  ethereum/contracts/zksync/libraries/Diamond.sol

190      function _saveFacetIfNew(address _facet) private {
        DiamondStorage storage ds = getDiamondStorage();

        uint256 selectorsLength = ds.facetToSelectors[_facet].selectors.length;
        // If there are no selectors associated with facet then save facet as new one
        if (selectorsLength == 0) {
            ds.facetToSelectors[_facet].facetPosition = ds.facets.length.toUint16();
            ds.facets.push(_facet);
        }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L190-L198


## [L-09]  Consider implementing two-step procedure for updating protocol addresses

A copy-paste error or a typo may end up bricking protocol functionality, or sending tokens to an address with no known private key. Consider implementing a two-step procedure for updating protocol addresses, where the recipient is set as pending, and must 'accept' the assignment by making an affirmative call. A straight forward way of doing this would be to have the target contracts implement [EIP-165](https://eips.ethereum.org/EIPS/eip-165), and to have the 'set' functions ensure that the recipient is of the right interface type.

```solidity
file:  ethereum/contracts/bridge/L1ERC20Bridge.sol

144      function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        uint256 _l2TxGasLimit,
        uint256 _l2TxGasPerPubdataByte
    ) external payable returns (bytes32 l2TxHash) {
        l2TxHash = deposit(_l2Receiver, _l1Token, _amount, _l2TxGasLimit, _l2TxGasPerPubdataByte, address(0));
    }

176     function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        uint256 _l2TxGasLimit,
        uint256 _l2TxGasPerPubdataByte,
        address _refundRecipient
    ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) {
        require(_amount != 0, "2T"); // empty deposit amount
        uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
        require(amount == _amount, "1T"); // The token has non-standard transfer logic
        // verify the deposit amount is allowed

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L144-L152

```solidity
file:  ethereum/contracts/common/AllowList.sol

106    function setPermissionToCall(
        address _caller,
        address _target,
        bytes4 _functionSig,
        bool _enable
    ) external onlyOwner {
        _setPermissionToCall(_caller, _target, _functionSig, _enable);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L106-L113


## [L-10] Tokens may be minted to `address(0x0)`

Neither the listed functions, nor `_mint()` prevent minting to `address(0x0)`

```solidity
file:  system-contracts/contracts/L2EthToken.sol

64    function mint(address _account, uint256 _amount) external override onlyCallFromBootloader {
        totalSupply += _amount;
        balance[_account] += _amount;
        emit Mint(_account, _amount);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L64-L68


## [L-11] decimals() is not a part of the ERC-20 standard

The decimals() function is not a part of the ERC-20 standard, and was added later as an optional extension. As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.

```solidity
file:   contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

243    (, bytes memory data3) = _token.staticcall(abi.encodeCall(IERC20Metadata.decimals, ()));

```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L243
