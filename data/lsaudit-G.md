As stated in the scope - the Gas Report had been prepared for L1 contracts only.

# [G-01] Pre-calculate keccak256 for constant variables in `L2ContractHelper.sol`



[File: ethereum/contracts/common/libraries/L2ContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L12C5-L12C13)

```
bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```


# [G-02] Length of `_bytecode` is calculated twice in `L2ContractHelper.sol`

Calculating length of bytes every time costs gas. The better solution would be to calculate the length once and save it in a local variable:

[File: ethereum/contracts/common/libraries/L2ContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L23-L25)
```
        require(_bytecode.length % 32 == 0, "pq");

        uint256 bytecodeLenInWords = _bytecode.length / 32;
```

can be changed to:

```
		uint256 _bytecodeLength = _bytecode.length;
        require(_bytecodeLength % 32 == 0, "pq");

        uint256 bytecodeLenInWords = _bytecodeLength / 32;
```

# [G-03] `require` in `hashL2Bytecode` in `L2ContractHelper.sol` can be moved on top

[File: ethereum/contracts/common/libraries/L2ContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L23-L26)
```
        require(_bytecode.length % 32 == 0, "pq");

        uint256 bytecodeLenInWords = _bytecode.length / 32;
        require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words
```

Since `_bytecode.length` is divisible by 32 (the first `require`), we know, that we can divide `_bytecode.length` by 32, without a remainder.

Let's take a look at the 2nd `require`:

`bytecodeLenInWords < 2**16  =>  _bytecode.length / 32 < 2**16  => _bytecode.length < 2**16 * 32  => _bytecode.length < 2**21`

This implies that we can rewrite above code snippet as:

```
        require(_bytecode.length % 32 == 0, "pq");
        require(_bytecode.length < 2**21, "pp"); // bytecode length must be less than 2^16 words
        uint256 bytecodeLenInWords = _bytecode.length / 32;
        
```

That way, when `_bytecode.length` will be too big, we will revert immediately, instead of wasting gas on executing line: `uint256 bytecodeLenInWords = _bytecode.length / 32`.
That line will be executed after the 2nd `require`.

Moreover, please keep in mind, to cache `_bytecode.length` into local variable - as it was suggested in `G-02`:

```
        uint256 _bytecodeLength = _bytecode.length;
        require(_bytecodeLength % 32 == 0, "pq");
        require(_bytecodeLength < 2**21, "pp"); // bytecode length must be less than 2^16 words
        uint256 bytecodeLenInWords = _bytecodeLength / 32;
```

# [G-04] Unnecessary variables in `L2ContractHelper.sol`

* Function `validateBytecodeHash`

Variable `version` is used only once, which means, it doesn't need to be declared at all:

Below code snippet can be changed from:

[File: ethereum/contracts/common/libraries/L2ContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L40-L41)
```
        uint8 version = uint8(_bytecodeHash[0]);
        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```

to:

```
require(uint8(_bytecodeHash[0]) == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```

* Function `computeCreate2Address`

Variables `senderBytes`, `data` are used only once, which means, they don't need to be declared at all.

Below code snippet can be changed from:

[File: ethereum/contracts/common/libraries/L2ContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L66-L71)
```
        bytes32 senderBytes = bytes32(uint256(uint160(_sender)));
        bytes32 data = keccak256(
            bytes.concat(CREATE2_PREFIX, senderBytes, _salt, _bytecodeHash, _constructorInputHash)
        );

        return address(uint160(uint256(data)));
```

to:

```
        return address(uint160(uint256(keccak256(bytes.concat(CREATE2_PREFIX, bytes32(uint256(uint160(_sender))), _salt, _bytecodeHash, _constructorInputHash)))));
```

# [G-05] Unnecessary variable in `AllowList.sol`

* Function `_setPermissionToCall`

Variable `currentPermission` is used only once, which means it doesn't need to be declared at all.


Below code snippet can be changed from:

[File: ethereum/contracts/common/AllowList.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L117-L119)
```
 bool currentPermission = hasSpecialAccessToCall[_caller][_target][_functionSig];

        if (currentPermission != _enable) {
```

to:
```
        if (hasSpecialAccessToCall[_caller][_target][_functionSig] != _enable) {
```


# [G-06] Length of `_factoryDeps` is calculated multiple of times in `_verifyFactoryDeps` in `BaseZkSyncUpgrade.sol`.

Calculating length of array cost gas. Instead of doing it every time, calculate it once and assign that value to local variable.

`_factoryDeps.length` is being calculated:
* ` require(_factoryDeps.length == _expectedHashes.length, "Wrong number of factory deps");`  in the 1st `require`
* `require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32");` in the 2nd `require`
* `for (uint256 i = 0; i < _factoryDeps.length; ++i) {` in loop (this one was reported in the bot-race)

[File: ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L201-L204)
```
        require(_factoryDeps.length == _expectedHashes.length, "Wrong number of factory deps");
        require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32");

        for (uint256 i = 0; i < _factoryDeps.length; ++i) {
```


# [G-07] Unnecessary variable in `BridgeInitializationHelper.sol`

* Function `requestDeployTransaction`
Variable `deployCalldata` is used only once, which means it doesn't need to be declared at all.

Below code snippet can be changed from:

[File: ethereum/contracts/common/AllowList.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol#L37-L49)
```
        bytes memory deployCalldata = abi.encodeCall(
            IL2ContractDeployer.create2,
            (bytes32(0), _bytecodeHash, _constructorData)
        );
        _zkSync.requestL2Transaction{value: _deployTransactionFee}(
            L2_DEPLOYER_SYSTEM_CONTRACT_ADDR,
            0,
            deployCalldata,
            DEPLOY_L2_BRIDGE_COUNTERPART_GAS_LIMIT,
            REQUIRED_L2_GAS_PRICE_PER_PUBDATA,
            _factoryDeps,
            msg.sender
        );
```

to:

```
        _zkSync.requestL2Transaction{value: _deployTransactionFee}(
            L2_DEPLOYER_SYSTEM_CONTRACT_ADDR,
            0,
            abi.encodeCall(
            IL2ContractDeployer.create2,
            (bytes32(0), _bytecodeHash, _constructorData)
        ),
            DEPLOY_L2_BRIDGE_COUNTERPART_GAS_LIMIT,
            REQUIRED_L2_GAS_PRICE_PER_PUBDATA,
            _factoryDeps,
            msg.sender
        );
```



# [G-08] Unnecessary variables in `L1WethBridge.sol`

* Function `initialize`
Variables `l2WethBridgeImplementationBytecodeHash` and `l2WethBridgeProxyBytecodeHash` are used only once, which means they don't need to be declared at all.

Below code snippet can be changed from:

[File: ethereum/contracts/bridge/L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L98-L133)
```
        bytes32 l2WethBridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
        bytes32 l2WethBridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);

        // Deploy L2 bridge implementation contract
        address wethBridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeImplementationFee,
            l2WethBridgeImplementationBytecodeHash,
            "", // Empty constructor data
            _factoryDeps // All factory deps are needed for L2 bridge
        );

(...)

        // Deploy L2 bridge proxy contract
        l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeProxyFee,
            l2WethBridgeProxyBytecodeHash,
            l2WethBridgeProxyConstructorData,
            // No factory deps are needed for L2 bridge proxy, because it is already passed in the previous step
            new bytes[](0)
        );
```

to:

```
 
        // Deploy L2 bridge implementation contract
        address wethBridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeImplementationFee,
            L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);,
            "", // Empty constructor data
            _factoryDeps // All factory deps are needed for L2 bridge
        );

(...)
        // Deploy L2 bridge proxy contract
        l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeProxyFee,
            L2ContractHelper.hashL2Bytecode(_factoryDeps[1]),
            l2WethBridgeProxyConstructorData,
            // No factory deps are needed for L2 bridge proxy, because it is already passed in the previous step
            new bytes[](0)
        );
```

* Function `finalizeWithdrawal`
Variables `alreadyFinalised`, `l2ToL1Message` and `success` are used only once, which means they don't need to be declared at all.

Below code snippet can be changed from:

[File: ethereum/contracts/bridge/L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L245-L254)
```
bool alreadyFinalised = zkSync.isEthWithdrawalFinalized(_l2BatchNumber, _l2MessageIndex);
        if (alreadyFinalised) {
            // Check that the specified message was actually sent while withdrawing eth from L2.
            L2Message memory l2ToL1Message = L2Message({
                txNumberInBatch: _l2TxNumberInBatch,
                sender: L2_ETH_TOKEN_SYSTEM_CONTRACT_ADDR,
                data: _message
            });
            bool success = zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);
            require(success, "vq");
```

to:

```
        if (zkSync.isEthWithdrawalFinalized(_l2BatchNumber, _l2MessageIndex)) {
            require(zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, L2Message({
                txNumberInBatch: _l2TxNumberInBatch,
                sender: L2_ETH_TOKEN_SYSTEM_CONTRACT_ADDR,
                data: _message
            }), _merkleProof), "vq");
```

# [G-09] Check if `amount` is greater than zero before performing `safeTransfer` in `L1WethBridge.sol`

[File: ethereum/contracts/bridge/L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L263)
```
        IERC20(l1WethAddress).safeTransfer(l1WethWithdrawReceiver, amount);
```

`safeTransfer`'ing  when `amount = 0` is a waste of gas, since there will be no funds transferred.
To save gas, check if `amount` is greater than 0, before calling `safeTransfer`.


# [G-10] Do not assign `offset` in the last call of `UnsafeBytes.readAddress` in `L1WethBridge.sol`

A simple test in Remix IDE has been created, to compare gas usage of assigning more than one value from function call:

```
  function getUints() public pure returns (uint, uint) {return (1, 2);}
   function getAll() public view {
    uint a; uint b; 
    (a, b) = getUints();
    uint g = gasleft();
    (a, b) = getUints();
    console.log(g - gasleft());
   }
    function getOne() public view {
    uint a; uint b; 
    (a, b) = getUints();
    uint g = gasleft();
    (a, ) = getUints();
    console.log(g - gasleft());
   }
```

`getOne()` costs 74 gas, while `getAll()` costs 82 gas. This implies, that we shouldn't assign every parameter from function call if it's not needed.

In the last call of `UnsafeBytes.readAddress(_message, offset)` (line 298):

[File: ethereum/contracts/bridge/L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L298)
```
(l1WethReceiver, offset) = UnsafeBytes.readAddress(_message, offset);
```
we won't need `offset` anymore, thus we can change line 298 to: `(l1WethReceiver, ) = UnsafeBytes.readAddress(_message, offset)`.

# [G-11] Unnecessary variables in `L1ERC20Bridge.sol`

* Function `initialize`

Variables `l2WethBridgeImplementationBytecodeHash` and `l2WethBridgeProxyBytecodeHash` are used only once, which means they don't need to be declared at all.

Below code snippet can be changed from:

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L99-L130)
```
        bytes32 l2BridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
        bytes32 l2BridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);

        // Deploy L2 bridge implementation contract
        address bridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeImplementationFee,
            l2BridgeImplementationBytecodeHash,
            "", // Empty constructor data
            _factoryDeps // All factory deps are needed for L2 bridge
        );

(...)

        // Deploy L2 bridge proxy contract
        l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeProxyFee,
            l2BridgeProxyBytecodeHash,
            l2BridgeProxyConstructorData,
            // No factory deps are needed for L2 bridge proxy, because it is already passed in previous step
            new bytes[](0)
        );
    
```

to:

```
        // Deploy L2 bridge implementation contract
        address bridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeImplementationFee,
            L2ContractHelper.hashL2Bytecode(_factoryDeps[0]),
            "", // Empty constructor data
            _factoryDeps // All factory deps are needed for L2 bridge
        );

(...)

        // Deploy L2 bridge proxy contract
        l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeProxyFee,
            L2ContractHelper.hashL2Bytecode(_factoryDeps[1]),
            l2BridgeProxyConstructorData,
            // No factory deps are needed for L2 bridge proxy, because it is already passed in previous step
            new bytes[](0)
        );
```

* Function `_depositFunds`

Variable `balanceAfter` is used only once, which means it doesn't need to be declared at all.

Below code snippet can be changed from:
[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L219-L221)
```
        uint256 balanceAfter = _token.balanceOf(address(this));

        return balanceAfter - balanceBefore;
```

to:

```
return _token.balanceOf(address(this)) - balanceBefore;
```

* Function `claimFailedDeposit`
Variable `proofValid ` is used only once, which means it doesn't need to be declared at all.

Below code snippet can be changed from:
[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L264-L272)
```
 bool proofValid = zkSync.proveL1ToL2TransactionStatus(
            _l2TxHash,
            _l2BatchNumber,
            _l2MessageIndex,
            _l2TxNumberInBatch,
            _merkleProof,
            TxStatus.Failure
        );
        require(proofValid, "yn");
```

to:

```
        require(zkSync.proveL1ToL2TransactionStatus(
            _l2TxHash,
            _l2BatchNumber,
            _l2MessageIndex,
            _l2TxNumberInBatch,
            _merkleProof,
            TxStatus.Failure
        ), "yn");
```

* Function `finalizeWithdrawal`
Variable `success  ` is used only once, which means it doesn't need to be declared at all.

Below code snippet can be changed from:
[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L311-L312)
```
            bool success = zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);
            require(success, "nq");
```

to:

```
            require(zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof), "nq");
```

* Function `l2TokenAddress`

Variables `constructorInputHash` and `salt` are used only once, which means they don't need to be declared at all.
Below code snippet can be changed from:
[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L354-L357)
```
        bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""));
        bytes32 salt = bytes32(uint256(uint160(_l1Token)));

        return L2ContractHelper.computeCreate2Address(l2Bridge, salt, l2TokenProxyBytecodeHash, constructorInputHash);
```

to:

```
        return L2ContractHelper.computeCreate2Address(l2Bridge, bytes32(uint256(uint160(_l1Token))), l2TokenProxyBytecodeHash, keccak256(abi.encode(address(l2TokenBeacon), "")));
```

# [G-12] Calculation which won't underflow can be unchecked in `L1ERC20Bridge.sol`
[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L221)
```
return balanceAfter - balanceBefore;
```
Since `balanceAfter` contains token's balance after the transfer, and `balanceBefore` contains token's balance before the transfer, we know, that `balanceAfter >= balanceBefore`, thus `balanceAfter - balanceBefore` will never underflow and can be unchecked.


# [G-13] Check if `amount` is greater than zero before performing `safeTransfer` in `L1ERC20Bridge.sol`

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L317)
```
        IERC20(l1Token).safeTransfer(l1Receiver, amount);
```

`safeTransfer`'ing  when `amount = 0` is a waste of gas, since there will be no funds transferred.
To save gas, check if `amount` is greater than 0, before calling `safeTransfer`.


# [G-14] Do not assign `offset` in the last call of `UnsafeBytes.readUint256` in `L1ERC20Bridge.sol`

A simple test in Remix IDE has been created, to compare gas usage of assigning more than one value from function call:

```
  function getUints() public pure returns (uint, uint) {return (1, 2);}
   function getAll() public view {
    uint a; uint b; 
    (a, b) = getUints();
    uint g = gasleft();
    (a, b) = getUints();
    console.log(g - gasleft());
   }
    function getOne() public view {
    uint a; uint b; 
    (a, b) = getUints();
    uint g = gasleft();
    (a, ) = getUints();
    console.log(g - gasleft());
   }
```

`getOne()` costs 74 gas, while `getAll()` costs 82 gas. This implies, that we shouldn't assign every parameter from function call if it's not needed.

In the last call of `UnsafeBytes.readUint256(_l2ToL1message, offset)` (line 336):

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L336)
```
(amount, offset) = UnsafeBytes.readUint256(_l2ToL1message, offset);
```
we won't need `offset` anymore, thus we can change line 336 to: `(amount, ) = UnsafeBytes.readUint256(_l2ToL1message, offset);`.


# [G-15] One + operation is redundant in `_verifyDepositLimit` in `L1ERC20Bridge.sol`

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L346-L349)
```
 } else {
            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
        }
```

We don't need to add `amount` twice - firstly - to check `require` statement, and secondly - to increase `totalDepositedAmountPerUser[_l1Token][_depositor]`.
We can change the order of `require` to do so:

```
 } else {
            
            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
            require(totalDepositedAmountPerUser[_l1Token][_depositor]  <= limitData.depositCap, "d1");
        }
```

If `totalDepositedAmountPerUser[_l1Token][_depositor] + _amount` exceeds `limitData.depositCap`, we will still revert.

To prove, that this will indeed save some gas, a minimalized code-snippet in Remix IDE has been prepared:

```
mapping(address => mapping(address => uint256)) public totalDepositedAmountPerUser;
function current(uint _amount) public {
    uint g = gasleft();
    require(totalDepositedAmountPerUser[msg.sender][msg.sender] + _amount <= 1000, "d1");
    totalDepositedAmountPerUser[msg.sender][msg.sender] += _amount;
    console.log(g - gasleft());
}

function afterFix(uint _amount) public {
    uint g = gasleft();
    totalDepositedAmountPerUser[msg.sender][msg.sender] += _amount;
    require(totalDepositedAmountPerUser[msg.sender][msg.sender]  <= 1000, "d1");
    console.log(g - gasleft());
}
```

Function `current()` uses 5895 gas, while function `afterFix()` uses 5707, which implies, that proposed fix is better optimalized for a gas usage.


# [G-16] Unnecessary variables in `LibMap.sol`

* Function `get`

Variables `mapValue` and `bitOffset` are used only once, which means they don't need to be declared at all.

Below code snippet can be changed from:

[File: ethereum/contracts/zksync/libraries/LibMap.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L21-L28)
```
uint256 mapValue = _map.map[_index / 8];

// First three bits of the original `_index` denotes the position of the uint32 in that slot.
// So, '(_index & 7) * 32' is done to find the bit position of the uint32 in that storage slot.
uint256 bitOffset = (_index & 7) * 32;

// Shift the bits to the right and retrieve the uint32 value.
result = uint32(mapValue >> bitOffset);
```

to:

```
result = uint32(_map.map[_index / 8] >> ( (_index & 7) * 32));
```

* Function `set`

Variables `oldValue` and `nexValueXorOldValue` are used only once, which means they don't need to be declared at all.

Below code snippet can be changed from:

[File: ethereum/contracts/zksync/libraries/LibMap.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L56-L63)
```
            uint32 oldValue = uint32(mapValue >> bitOffset);

            // Calculate the XOR of the new value and the existing value.
            uint256 newValueXorOldValue = uint256(oldValue ^ _value);

            // Finally, we XOR the slot with the XOR of the new value and the existing value,
            // shifted to its proper position. The XOR operation will effectively replace the old value with the new value.
            _map.map[mapIndex] = (newValueXorOldValue << bitOffset) ^ mapValue;
```

to:

```
            _map.map[mapIndex] = (uint256(uint32(mapValue >> bitOffset) ^ _value) << bitOffset) ^ mapValue;
```


# [G-17] Division by powers of two should use bit shifting in `Merkle.sol`
While bot race reported every division by powers of two in a form: `A / 2`, it misses an instance, which looks like: `A /= 2`.

[File: ethereum/contracts/zksync/libraries/Merkle.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L33)
```
_index /= 2;
```

It should be changed to: `_index = _index >> 1;`


# [G-18] Precalculate constants in `Config.sol`.
While constants have known value, to save more gas, calculate their values before compile-time.

[File: ethereum/contracts/zksync/Config.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol)
```
uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512
uint256 constant MAX_INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE * 4765;
uint256 constant MAX_REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE * 7564;

```


# [G-19] Function `pushBack` in `PriorityQueue.sol` can be optimized

[File: ethereum/contracts/zksync/libraries/PriorityQueue.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L55-L61)
```
    function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal {
        // Save value into the stack to avoid double reading from the storage
        uint256 tail = _queue.tail;

        _queue.data[tail] = _operation;
        _queue.tail = tail + 1;
    }
```

Above function can be optimized by utilizing post-incrementing feature. It will allow to remove `tail` variable. Function won't be creating and reading from that variable:

```
    function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal {
        // Save value into the stack to avoid double reading from the storage
        _queue.data[_queue.tail++] = _operation;
        
    }
```


# [G-20] Remove local-testing related code in `DiamondInit.sol`

[File: ethereum/contracts/zksync/DiamondInit.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L85-L87)
```
        // While this does not provide a protection in the production, it is needed for local testing
        // Length of the L2Log encoding should not be equal to the length of other L2Logs' tree nodes preimages
        assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);
```

According to above code, `assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);` is redundant and can be removed in production, since this line of code is dedicated for local testing only.
This can be confirmed in file `ethereum/contracts/zksync/Config.sol`, where constant `L2_TO_L1_LOG_SERIALIZE_SIZE` is defined as `88`. Since this is a constant value, its value will never change, thus this `assert` is unnecessary and can be removed.


# [G-21] Length of `_encoded` is calculated twice in `validateL1ToL2Transaction` in `TransactionValidator.sol`.
Calculating length is gas-costly operation. It's better to calculate it once and store it in local variable.

[File: ethereum/contracts/zksync/libraries/TransactionValidator.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L23-L43)
```
        uint256 l2GasForTxBody = getTransactionBodyGasLimit(
            _transaction.gasLimit,
            _transaction.gasPerPubdataByteLimit,
            _encoded.length
        );

(...)
        require(
            getMinimalPriorityTransactionGasLimit(
                _encoded.length,
                _transaction.factoryDeps.length,
                _transaction.gasPerPubdataByteLimit
            ) <= _transaction.gasLimit,
            "up"
        );
```

`_encoded.length` is being calculated twice - in line 26 and line 38.


# [G-22] Use constants instead of `type(uintX).max` in `TransactionValidator.sol`

When you use type(uintX).max - it may result in higher gas costs because it involves a runtime operation to calculate the `type(uintX).max` at runtime. This calculation is performed every time the expression is evaluated.

To save gas, it is recommended to use constants to represent the maximum value. Declaration of a constant with the maximum value means that the value is known at compile-time and does not require any runtime calculations.

[File: ethereum/contracts/zksync/libraries/TransactionValidator.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L50-L55)
```
        require(_transaction.from <= type(uint16).max, "ua");
        require(_transaction.to <= type(uint160).max, "ub");
        require(_transaction.paymaster == 0, "uc");
        require(_transaction.value == 0, "ud");
        require(_transaction.reserved[0] == 0, "ue");
        require(_transaction.reserved[1] <= type(uint160).max, "uf");
```

# [G-23] Multiple adding operations can be reduced in `getMinimalPriorityTransactionGasLimit` in `TransactionValidator.sol`

[File: ethereum/contracts/zksync/libraries/TransactionValidator.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L69-L102)
```
                costForComputation = L1_TX_INTRINSIC_L2_GAS;
(...)
            costForComputation += Math.ceilDiv(_encodingLength * L1_TX_DELTA_544_ENCODING_BYTES, 544);
(...)
            costForComputation += _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_L2_GAS;
(...)
            costForComputation = Math.max(costForComputation, L1_TX_MIN_L2_GAS_BASE);
(...)
            costForPubdata = L1_TX_INTRINSIC_PUBDATA * _l2GasPricePerPubdata;
(...)
            costForPubdata += _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_PUBDATA * _l2GasPricePerPubdata;
```

There's no need to perform multiple `costForComputation +=`, when it can be calculated at once:

```
costForComputation = L1_TX_INTRINSIC_L2_GAS +  Math.ceilDiv(_encodingLength * L1_TX_DELTA_544_ENCODING_BYTES, 544) + _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_L2_GAS;
costForComputation = Math.max(costForComputation, L1_TX_MIN_L2_GAS_BASE);
```

The whole function can be rewritten to:

```
  function getMinimalPriorityTransactionGasLimit(
        uint256 _encodingLength,
        uint256 _numberOfFactoryDependencies,
        uint256 _l2GasPricePerPubdata
    ) internal pure returns (uint256) {
        return Math.max(L1_TX_INTRINSIC_L2_GAS + Math.ceilDiv(_encodingLength * L1_TX_DELTA_544_ENCODING_BYTES, 544) + _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_L2_GAS, L1_TX_MIN_L2_GAS_BASE) + (L1_TX_INTRINSIC_PUBDATA * _l2GasPricePerPubdata + _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_PUBDATA * _l2GasPricePerPubdat);
    }
```

# [G-24] Unnecessary variables in `TransactionValidator.sol`

* Function `getOverheadForTransaction`

Variables `txSlotOverhead`, `overheadForLength`, `numerator` and `denominator` are used only once, which means they don't need to be declared at all.

Below code snippet can be changed from:
[File: ethereum/contracts/zksync/libraries/TransactionValidator.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L141-L165)
```
uint256 txSlotOverhead = Math.ceilDiv(batchOverheadGas, MAX_TRANSACTIONS_IN_BATCH);
batchOverheadForTransaction = Math.max(batchOverheadForTransaction, txSlotOverhead);

// The overhead for occupying the bootloader memory can be derived from encoded_len
uint256 overheadForLength = Math.ceilDiv(_encodingLength * batchOverheadGas, BOOTLOADER_TX_ENCODING_SPACE);
batchOverheadForTransaction = Math.max(batchOverheadForTransaction, overheadForLength);

(...)

uint256 numerator = batchOverheadGas * _totalGasLimit + L2_TX_MAX_GAS_LIMIT;
uint256 denominator = L2_TX_MAX_GAS_LIMIT + batchOverheadGas;

overheadForGas = (numerator - 1) / denominator;
```

to:

```
batchOverheadForTransaction = Math.max(batchOverheadForTransaction, Math.ceilDiv(batchOverheadGas, MAX_TRANSACTIONS_IN_BATCH));

// The overhead for occupying the bootloader memory can be derived from encoded_len

batchOverheadForTransaction = Math.max(batchOverheadForTransaction, Math.ceilDiv(_encodingLength * batchOverheadGas, BOOTLOADER_TX_ENCODING_SPACE));

(...)

overheadForGas = (batchOverheadGas * _totalGasLimit + L2_TX_MAX_GAS_LIMIT - 1) / (L2_TX_MAX_GAS_LIMIT + batchOverheadGa);
```

# [G-25] Unnecessary variables in `Getters.sol`

* Function `isFacetFreezable`
Variables `selectorsArrayLen` and `selector0` are used only once, which means they don't need to be declared at all.

Below code snippet can be changed from:
[File: ethereum/contracts/zksync/facets/Getters.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L140-L143)
```
 uint256 selectorsArrayLen = ds.facetToSelectors[_facet].selectors.length;
        if (selectorsArrayLen != 0) {
            bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];
            isFreezable = ds.selectorToFacet[selector0].isFreezable;
```

to:

```
        if (ds.facetToSelectors[_facet].selectors.length != 0) {
            isFreezable = ds.selectorToFacet[ds.facetToSelectors[_facet].selectors[0]].isFreezable;
```



# [G-26] Unnecessary variables in `Diamond.sol`

* Function `diamondCut`

Variables `facet` and `isFacetFreezable` are used only once, which means they don't need to be declared at all.

Below code snippet can be changed from:
[File: ethereum/contracts/zksync/libraries/Diamond.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L101-L113)
```
 Action action = facetCuts[i].action;
            address facet = facetCuts[i].facet;
            bool isFacetFreezable = facetCuts[i].isFreezable;
            bytes4[] memory selectors = facetCuts[i].selectors;

            require(selectors.length > 0, "B"); // no functions for diamond cut

            if (action == Action.Add) {
                _addFunctions(facet, selectors, isFacetFreezable);
            } else if (action == Action.Replace) {
                _replaceFunctions(facet, selectors, isFacetFreezable);
            } else if (action == Action.Remove) {
                _removeFunctions(facet, selectors);
```

to:

```
 Action action = facetCuts[i].action;
            bytes4[] memory selectors = facetCuts[i].selectors;

            require(selectors.length > 0, "B"); // no functions for diamond cut

            if (action == Action.Add) {
                _addFunctions(facetCuts[i].facet, selectors, facetCuts[i].isFreezable);
            } else if (action == Action.Replace) {
                _replaceFunctions(facetCuts[i].facet, selectors, facetCuts[i].isFreezable);
            } else if (action == Action.Remove) {
                _removeFunctions(facetCuts[i].facet, selectors);
```

* Function `_saveFacetIfNew`
Variable `selectorsLength` is used only once, which means it doesn't need to be declared at all.

Below code snippet can be changed from:
[File: ethereum/contracts/zksync/libraries/Diamond.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L193-L195)
```
uint256 selectorsLength = ds.facetToSelectors[_facet].selectors.length;
        // If there are no selectors associated with facet then save facet as new one
        if (selectorsLength == 0) {
```

to:

```
if (ds.facetToSelectors[_facet].selectors.length == 0) {
```


* Function `_addOneFunction`
Variable `selector0` is used only once, which means it doesn't need to be declared at all.

Below code snippet can be changed from:
[File: ethereum/contracts/zksync/libraries/Diamond.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L219-L220)

```
 bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];
            require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
```

to:

```

            require(_isSelectorFreezable == ds.selectorToFacet[ds.facetToSelectors[_facet].selectors[0]].isFreezable, "J1");
```


# [G-27] Function `diamondCut()` from `Diamond.sol` can be redesigned to revert earlier

[File: ethereum/contracts/zksync/libraries/Diamond.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L101-L106)
```
101:    Action action = facetCuts[i].action;
102:    address facet = facetCuts[i].facet;
103:    bool isFacetFreezable = facetCuts[i].isFreezable;
104:    bytes4[] memory selectors = facetCuts[i].selectors;
105:
106:    require(selectors.length > 0, "B"); // no functions for diamond cut
```

You can check selectors' length ealier. If their length is 0, then function will revert without wasting gas on executing lines: 101-103. Change above code to:

```
bytes4[] memory selectors = facetCuts[i].selectors;
require(selectors.length > 0, "B"); // no functions for diamond cut
Action action = facetCuts[i].action;
address facet = facetCuts[i].facet;
bool isFacetFreezable = facetCuts[i].isFreezable;

```


# [G-28] In `Diamond.sol`, move some `require` on top of function

Functions: `_addFunctions`, `_replaceFunctions`, `removeFunctions`, firstly gets the pointer to the diamond storage: `DiamondStorage storage ds = getDiamondStorage();` and then, they perform a check, if user passed a proper `_facet` address. Those lines can, however, be swapped.
If user provides incorrect `_facet` w then function should immediately revert. Afterwards, it should get the pointer to the diamond storage.

In `_addFunctions()` change:
```
DiamondStorage storage ds = getDiamondStorage();
require(_facet != address(0), "G"); // facet with zero address cannot be added
```
to:
```
require(_facet != address(0), "G"); // facet with zero address cannot be added
DiamondStorage storage ds = getDiamondStorage();
```

In `_replaceFunctions` change:
```
DiamondStorage storage ds = getDiamondStorage();
require(_facet != address(0), "K"); // cannot replace facet with zero address
```
to:
```
require(_facet != address(0), "K"); // cannot replace facet with zero address
DiamondStorage storage ds = getDiamondStorage();
```

In `_removeFunctions` change:
```
DiamondStorage storage ds = getDiamondStorage();
require(_facet == address(0), "a1"); // facet address must be zero
```
to:
```
require(_facet == address(0), "a1"); // facet address must be zero
DiamondStorage storage ds = getDiamondStorage();
```


# [G-29]  In `Diamond.sol` in `_initializeDiamondCut()`, order of `require` can be changed

[File: ethereum/contracts/zksync/libraries/Diamond.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L302-L303)
```
require(data.length == 32, "lp");
require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
```

The order of `require` can be changed, firstly check if `abi.decode` is `DIAMOND_INIT_SUCCESS_RETURN_VALUE`, then check its length:
```
require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
require(data.length == 32, "lp");
```
It will save some gas in multiple of scenario:

* when `data.length < 32` - decoding will revert and there will be no need to execute `require(data.length == 32, "lp");`
* when `data.length > 32`, but first 32 bytes of `data` are not `DIAMOND_INIT_SUCCESS_RETURN_VALUE` - comparison will revert and there will be no need to execute `require(data.length == 32, "lp");`



# [G-30] Unnecessary variables in `Mailbox.sol`

* Function `_proveL2LogInclusion`

Variables `calculatedRootHash` and `actualRootHash` are used only once, which means they don't need to be declared at all.

Below code snippet can be changed from:
[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L142-L145)
```
bytes32 calculatedRootHash = Merkle.calculateRoot(_proof, _index, hashedLog);
        bytes32 actualRootHash = s.l2LogsRootHashes[_batchNumber];

        return actualRootHash == calculatedRootHash;
```

to:

```
return s.l2LogsRootHashes[_batchNumber] == Merkle.calculateRoot(_proof, _index, hashedLog);
```

* Function `l2TransactionBaseCost`
Variable `l2GasPrice ` is used only once, which means it doesn't need to be declared at all.

Below code snippet can be changed from:
[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L171-L172)
```
        uint256 l2GasPrice = _deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit);
        return l2GasPrice * _l2GasLimit;
```
to:

```
return _deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit) * _l2GasLimit;
```

* Function `_deriveL2GasPrice`

Variables `pubdataPriceETH` and `minL2GasPriceETH` are used only once, which means they don't need to be declared at all.

Below code snippet can be changed from:
[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L180-L183)
```
        uint256 pubdataPriceETH = L1_GAS_PER_PUBDATA_BYTE * _l1GasPrice;
        uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;

        return Math.max(FAIR_L2_GAS_PRICE, minL2GasPriceETH);
```

to:

```
        return Math.max(FAIR_L2_GAS_PRICE, (L1_GAS_PER_PUBDATA_BYTE * _l1GasPrice + _gasPricePerPubdata - 1) / _gasPricePerPubdata);
```


* Function `_requestL2Transaction`

Variables `expirationTimestamp`, `txId`, `baseCost` are used only once, which means they don't need to be declared at all.

Below code snippet can be changed from:
[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L283-L328)
```
uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast
uint256 txId = s.priorityQueue.getTotalPriorityTxs();

(...)

uint256 baseCost = params.l2GasPrice * _l2GasLimit;
require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost

(...)

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

to:

```
require(msg.value >= params.l2GasPrice * _l2GasLimit + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost

(...)

params.sender = _sender;
params.txId = s.priorityQueue.getTotalPriorityTxs();
params.l2Value = _l2Value;
params.contractAddressL2 = _contractAddressL2;
params.expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION);
params.l2GasLimit = _l2GasLimit;
params.l2GasPricePerPubdata = _l2GasPerPubdataByteLimit;
params.valueToMint = msg.value;
params.refundRecipient = refundRecipient;
```

# [G-31] Reduce number of additions and readings of `s.totalDepositedAmountPerUser[_depositor]` in `_verifyDepositLimit()` in `Mailbox.sol`

[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L279-L280)
```
        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
        s.totalDepositedAmountPerUser[_depositor] += _amount;
```
There's no need to access `s.totalDepositedAmountPerUser[_depositor]` twice and perform addition two times. Much cheaper solution would be to cache `s.totalDepositedAmountPerUser[_depositor] + _amount`.
Code can be rewritten to:

```
uint256 totalDepositedAmountPerUserAndAmount = s.totalDepositedAmountPerUser[_depositor] + _amount;
require(totalDepositedAmountPerUserAndAmount <= limitData.depositCap, "d2");
```
we've cached `s.totalDepositedAmountPerUser[_depositor] + _amount`, and then later checked if it does not exceed limit. If it does, function will revert.

# [G-32] Operations which won't overflow in `Mailbox.sol` can be unchecked
If, for any reason recommendation for G-31 won't be implemented, another gas optimization would be insert line 280: `s.totalDepositedAmountPerUser[_depositor] += _amount;` into unchecked block.
Since `require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");` guarantees, that  `s.totalDepositedAmountPerUser[_depositor] + _amount` won't overflow, we can perform 2nd addition as unchecked: `unchecked {s.totalDepositedAmountPerUser[_depositor] += _amount;}`

# [G-33] Addition in `_requestL2Transaction` in `Mailbox.sol` can be either removed or unchecked.

[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L295)
```
uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast
```

Constant `PRIORITY_EXPIRATION` is defined in `ethereum/contracts/zksync/Config.sol` as:

```
/// NOTE: The constant is set to zero for the Alpha release period
uint256 constant PRIORITY_EXPIRATION = 0 days;
```

This addition is then redundant (since we're adding 0). However, if for further reference you want to keep this code as it is (e.g. new contract will be deployed with different Alpha release period), make this operation `unchecked` at least. Since `PRIORITY_EXPIRATION` is a constant value, known during compiling time, and `block.timestamp` is impossible to overflow, it's safe to assume, that `block.timestamp + PRIORITY_EXPIRATION` will never overflow. The max value of `uint64` converted into timestamp is `Sun Jul 21 2554`. It's almost impossible that `block.timestamp + PRIORITY_EXPIRATION` will ever exceed that number (assuming that `PRIORITY_EXPIRATION` will be set to some reasonable value).


# [G-34] Do not assign `offset` in the last call of `UnsafeBytes.readUint256()` in `Mailbox.sol`

A simple test in Remix IDE has been created, to compare gas usage of assigning more than one value from function call:

```
  function getUints() public pure returns (uint, uint) {return (1, 2);}
   function getAll() public view {
    uint a; uint b; 
    (a, b) = getUints();
    uint g = gasleft();
    (a, b) = getUints();
    console.log(g - gasleft());
   }
    function getOne() public view {
    uint a; uint b; 
    (a, b) = getUints();
    uint g = gasleft();
    (a, ) = getUints();
    console.log(g - gasleft());
   }
```

`getOne()` costs 74 gas, while `getAll()` costs 82 gas. This implies, that we shouldn't assign every parameter from function call if it's not needed.

In the last call of `UnsafeBytes.readUint256(_message, offset)` (line 427):

[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L427)
```
(amount, offset) = UnsafeBytes.readUint256(_message, offset);
```
we won't need `offset` anymore, thus we can change line 427 to: `(amount, ) = UnsafeBytes.readUint256(_message, offset);`.


# [G-35] Loop in `_commitBatchesWithSystemContractsUpgrade()` in `Executor.sol` can be optimized

[File: ethereum/contracts/zksync/facets/Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L241-L243)
```
        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
            // The upgrade transaction must only be included in the first batch.
            bytes32 expectedUpgradeTxHash = i == 0 ? _systemContractUpgradeTxHash : bytes32(0);
```

The `for` loop iterates from 0 to `_newBatchesData.length`. In every iterations (at line 243), it checks if `i == 0`. However, this condition occurs only during the first loop iteration.
This means, that it will be better to divide the loop into two smaller loops:
* `for (uint256 i = 0; i < 1; i = i.uncheckedInc()) { bytes32 expectedUpgradeTxHash =  _systemContractUpgradeTxHash; (...)` 
* `for (uint256 i = 1; i < _newBatchesData.length; i = i.uncheckedInc()) { bytes32 expectedUpgradeTxHash =  bytes32(0); (...)` 

That way, we will be performing `i == 0` check only during the first loop iteration, thus this comparison will be done at least once.

Moreover, to better optimize these two loops, please make sure to declare `i` counter outside that loops. That way, you won't be redeclaring `i` inside the 2nd loop:

```
uint256 i = 0;
for (i; i < 1; i = i.uncheckedInc()) { 
bytes32 expectedUpgradeTxHash =  _systemContractUpgradeTxHash; 

(...)
}

for (i; i < _newBatchesData.length; i = i.uncheckedInc())
{
    bytes32 expectedUpgradeTxHash =  bytes32(0); 
    (...)
}
```

Additionally, as you can notice, `expectedUpgradeTxHash` is used only once, so it does not need to be declared at all. It can be used directly:

* in the first loop:
```
_lastCommittedBatchData = _commitOneBatch(
                _lastCommittedBatchData,
                _newBatchesData[i],
                _systemContractUpgradeTxHash
            );
```

* in the second loop:
```
_lastCommittedBatchData = _commitOneBatch(
                _lastCommittedBatchData,
                _newBatchesData[i],
                bytes32(0)
            );
```

# [G-36] Calculations which won't underflow/overflow in `Executor.sol` can be unchecked

[File: ethereum/contracts/zksync/facets/Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L93-L94)
```
require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small
require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big
```
Since `COMMIT_TIMESTAMP_NOT_OLDER` and `COMMIT_TIMESTAMP_APPROXIMATION_DELTA` are constant values, which are known before compilation time, it's reasonable to assume, that they are chosen in a way, which:
* `block.timestamp >= COMMIT_TIMESTAMP_NOT_OLDER`
* `block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA` won't overflow.
If that's the case - those two lines can be unchecked.

# [G-37] Move some `require` checks on top of function in `Executor.sol`

[File: ethereum/contracts/zksync/facets/Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L317-L327)
```
uint256 currentTotalBatchesVerified = s.totalBatchesVerified;
uint256 committedBatchesLength = _committedBatches.length;

// Save the variable from the storage to memory to save gas
VerifierParams memory verifierParams = s.verifierParams;

// Initialize the array, that will be used as public input to the ZKP
uint256[] memory proofPublicInput = new uint256[](committedBatchesLength);

// Check that the batch passed by the validator is indeed the first unverified batch
require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");
```

`require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");` can be moved up:

```
uint256 currentTotalBatchesVerified = s.totalBatchesVerified;

// Check that the batch passed by the validator is indeed the first unverified batch
require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");

uint256 committedBatchesLength = _committedBatches.length;

// Save the variable from the storage to memory to save gas
VerifierParams memory verifierParams = s.verifierParams;

// Initialize the array, that will be used as public input to the ZKP
uint256[] memory proofPublicInput = new uint256[](committedBatchesLength);


```
That way, when this check will revert, function won't waste gas on performing other instructions.

# [G-38] Simply unchecked operations can be done directly in the code, instead of being imported from the library

File `ethereum/contracts/common/libraries/UncheckedMath.sol` is a library which implements two simple unchecked functions: `uncheckedInc()` and `uncheckedAdd()`.
Those functions are used across the whole code-base, mostly inside loops:

* File `ethereum/contracts/zksync/facets/Executor.sol`
```
209:        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
241:        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
263:        for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
293:        for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
330:        for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
```

* File `ethereum/contracts/zksync/facets/Mailbox.sol`
```
395:        for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
```

* File `ethereum/contracts/zksync/libraries/Diamond.sol`

```
100:        for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
138:        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
159:        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
179:        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
```

* File `ethereum/contracts/zksync/facets/Getters.sol`

```
182:        for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
```

* File `ethereum/contracts/zksync/libraries/Merkle.sol`

```
29:        for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
```

* File: `ethereum/contracts/common/AllowList.sol`

```
62:        for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {
96:        for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
```

However, using library functions, instead of directly increasing `i` in an `unchecked` block is very gas ineffective.

To prove that, a simple test in Remix IDE was prepared:

```
    function uncheckedInt() public view {
    uint gas = gasleft();
    for (uint i = 0; i < 100; ) {
        require(i == i);
    unchecked {++i;}
    }

    console.log(gas - gasleft());
}

    function uncheckedIntLib() public view {
    uint gas = gasleft();
    for (uint i = 0; i < 100; i = i.uncheckedInc()) {require(i == i);
    }
    console.log(gas - gasleft());
    }
```

Function `uncheckedInt()` costs 7442 gas, while `uncheckedIntLib()` costs 11742 gas.
This implies, that every loop in a form:

```
for (uint256 i = 0; (...) i = i.uncheckedInc()) {
```

should be rewritten to:

```
for (uint256 i = 0; (...) i = i.uncheckedInc()) {
(...)
unchecked {++i;}
}
```

There are two additional places in `ethereum/contracts/zksync/facets/Executor.sol`, where the similar rule applies

* File `ethereum/contracts/zksync/facets/Executor.sol`
```
123:        for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
331:            currentTotalBatchesVerified = currentTotalBatchesVerified.uncheckedInc();
```

In line 123, `i = i + L2_TO_L1_LOG_SERIALIZE_SIZE` should also be unchecked.
In line 331, it should be: `unchecked {++currentTotalBatchesVerified;}`


# [G-39] Redundant `if` condition in `Executor.sol`

[File: ethereum/contracts/zksync/facets/Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L367)
```
 if (_proof.serializedProof.length > 0) {
            bool successVerifyProof = s.verifier.verify(
                proofPublicInput,
                _proof.serializedProof,
                _proof.recursiveAggregationInput
            );
            require(successVerifyProof, "p"); // Proof verification fail
        }
        // #else
        bool successVerifyProof = s.verifier.verify(
            proofPublicInput,
            _proof.serializedProof,
            _proof.recursiveAggregationInput
        );
       require(successVerifyProof, "p"); // Proof verification fail
```

In above code section, we're performing zkp verification twice.
For the first time - when `if (_proof.serializedProof.length > 0)`.
And then, for the second time - in line 363.

This is redundant. Remove the whole `if (_proof.serializedProof.length > 0)` block, because we're repeating `s.verifier.verify()` call after that block for the second time.



# [G-40] Event in `Executor.sol` can be optimized

[File: ethereum/contracts/zksync/facets/Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L405C49-L405C49-L413)
```
 s.totalBatchesCommitted = _newLastBatch;

        // Reset the batch number of the executed system contracts upgrade transaction if the batch
        // where the system contracts upgrade was committed is among the reverted batches.
        if (s.l2SystemContractsUpgradeBatchNumber > _newLastBatch) {
            delete s.l2SystemContractsUpgradeBatchNumber;
        }

        emit BlocksRevert(s.totalBatchesCommitted, s.totalBatchesVerified, s.totalBatchesExecuted);
```

Since ` s.totalBatchesCommitted = _newLastBatch`, we can change event to:

```
 emit BlocksRevert(_newLastBatch, s.totalBatchesVerified, s.totalBatchesExecuted);
```


# [G-41] Save one negation by rewritting the statement in `DiamonProxy.sol`

[File: ethereum/contracts/zksync/DiamondProxy.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L31)
```
require(!diamondStorage.isFrozen || !facet.isFreezable, "q1");
```

`!A || !B <=> !(A & B)` which means, it can be rewritten to:

```
require(!(diamondStorage.isFrozen && facet.isFreezable)), "q1");
```
