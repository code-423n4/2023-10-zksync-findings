##

## [L-1] ``pendingGovernor``, ``pendingAdmin``  can accept the governor and admin roles after indefinite time 

### Impact

This will break the intentions of the  ``acceptGovernor()``, ``acceptAdmin()`` functions 

There is a possibility that the pending governor, admin can accept the governor role indefinitely if the current governor does not call the acceptGovernor(), acceptAdmin() functions. This is because the current governor, admin  is the only one who can change the pending governor.

```solidity
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Admin.sol

/// @notice Starts the transfer of governor rights. Only the current governor can propose a new pending one.
    /// @notice New governor can accept governor rights by calling `acceptGovernor` function.
    /// @param _newPendingGovernor Address of the new governor
    function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {
        // Save previous value into the stack to put it into the event later
        address oldPendingGovernor = s.pendingGovernor;
        // Change pending governor
        s.pendingGovernor = _newPendingGovernor;
        emit NewPendingGovernor(oldPendingGovernor, _newPendingGovernor);
    }

 /// @notice Accepts transfer of governor rights. Only pending governor can accept the role.
    function acceptGovernor() external {
        address pendingGovernor = s.pendingGovernor;
        require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights

        address previousGovernor = s.governor;
        s.governor = pendingGovernor;
        delete s.pendingGovernor;

        emit NewPendingGovernor(pendingGovernor, address(0));
        emit NewGovernor(previousGovernor, pendingGovernor);
    }

    /// @notice Starts the transfer of admin rights. Only the current governor or admin can propose a new pending one.
    /// @notice New admin can accept admin rights by calling `acceptAdmin` function.
    /// @param _newPendingAdmin Address of the new admin
    function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {
        // Save previous value into the stack to put it into the event later
        address oldPendingAdmin = s.pendingAdmin;
        // Change pending admin
        s.pendingAdmin = _newPendingAdmin;
        emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);
    }

    /// @notice Accepts transfer of admin rights. Only pending admin can accept the role.
    function acceptAdmin() external {
        address pendingAdmin = s.pendingAdmin;
        require(msg.sender == pendingAdmin, "n4"); // Only proposed by current admin address can claim the admin rights

        address previousAdmin = s.admin;
        s.admin = pendingAdmin;
        delete s.pendingAdmin;

        emit NewPendingAdmin(pendingAdmin, address(0));
        emit NewAdmin(previousAdmin, pendingAdmin);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L20-L63

### Recommended Mitigation
Consider adding time limits or change acceptable patterns

##

## [L-2] _parseL2WithdrawalMessage(_message) function may reverts some cases instead of returning values 

The _parseL2WithdrawalMessage(_message) function may revert in some cases instead of returning values.

Some of the cases where the _parseL2WithdrawalMessage(_message) function may revert

  - The _message input is not long enough.
  - The function signature in the _message input does not match the expected value.
  - The l1 receiver address in the _message input is not valid.
  - The _message input is malformed or contains invalid data.

### POC

```solidity
FILE: Breadcrumbs2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

207: (address _l1WithdrawReceiver, uint256 _amount) = _parseL2WithdrawalMessage(_message);

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L207




##

## [L-3] Compromised Governor and Admin Keys Can Lead to Unrecoverable Protocol Damage

### Impact
If the governor and admin keys are compromised, then it will not be possible to change the new governor and admin. This could have a significant impact on the overall protocol, as the governor and admin have the ability to make important decisions about the protocol, such as setting parameters, upgrading contracts, and deploying new features.

Compromised governor or admin could impact the protocol:

- They could steal funds from the protocol.
- They could change the protocol's parameters in a way that harms users or investors.
- They could upgrade the protocol's contracts with malicious code.
- They could deploy new features that are malicious or harmful to users. 

```solidity
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/zksync/facets/Admin.sol

/// @notice Starts the transfer of governor rights. Only the current governor can propose a new pending one.
    /// @notice New governor can accept governor rights by calling `acceptGovernor` function.
    /// @param _newPendingGovernor Address of the new governor
    function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {
        // Save previous value into the stack to put it into the event later
        address oldPendingGovernor = s.pendingGovernor;
        // Change pending governor
        s.pendingGovernor = _newPendingGovernor;
        emit NewPendingGovernor(oldPendingGovernor, _newPendingGovernor);
    }

 /// @notice Accepts transfer of governor rights. Only pending governor can accept the role.
    function acceptGovernor() external {
        address pendingGovernor = s.pendingGovernor;
        require(msg.sender == pendingGovernor, "n4"); // Only proposed by current governor address can claim the governor rights

        address previousGovernor = s.governor;
        s.governor = pendingGovernor;
        delete s.pendingGovernor;

        emit NewPendingGovernor(pendingGovernor, address(0));
        emit NewGovernor(previousGovernor, pendingGovernor);
    }

    /// @notice Starts the transfer of admin rights. Only the current governor or admin can propose a new pending one.
    /// @notice New admin can accept admin rights by calling `acceptAdmin` function.
    /// @param _newPendingAdmin Address of the new admin
    function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {
        // Save previous value into the stack to put it into the event later
        address oldPendingAdmin = s.pendingAdmin;
        // Change pending admin
        s.pendingAdmin = _newPendingAdmin;
        emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);
    }

    /// @notice Accepts transfer of admin rights. Only pending admin can accept the role.
    function acceptAdmin() external {
        address pendingAdmin = s.pendingAdmin;
        require(msg.sender == pendingAdmin, "n4"); // Only proposed by current admin address can claim the admin rights

        address previousAdmin = s.admin;
        s.admin = pendingAdmin;
        delete s.pendingAdmin;

        emit NewPendingAdmin(pendingAdmin, address(0));
        emit NewAdmin(previousAdmin, pendingAdmin);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L20-L63

### Recommended Mitigation
Use a multi-signature wallet to store the keys

 ##

## [L-4] ``block.timestamp >= _proposedUpgrade.upgradeTimestamp`` in this check malicious miners can manipulate ``block timestamp`` to trigger ``unauthorized upgrades``

### Impact
It is not safe to base upgrades on block timestamp because it can be manipulated by miners. A malicious miner could mine a block with a future timestamp and then upgrade the contract to their advantage.

The ``upgrade()`` function checks if the current block timestamp is greater than or equal to the proposed upgrade timestamp. If it is, then the upgrade is allowed to proceed. However, a malicious miner could mine a block with a future timestamp and then call the ``upgrade()`` function before the upgrade timestamp has actually been reached. This would allow them to upgrade the contract to their advantage.

### POC
```solidity
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

 /// @notice The main function that will be provided by the upgrade proxy
    function upgrade(ProposedUpgrade calldata _proposedUpgrade) public virtual returns (bytes32) {
        // Note that due to commitment delay, the timestamp of the L2 upgrade batch may be earlier than the timestamp
        // of the L1 block at which the upgrade occured. This means that using timestamp as a signifier of "upgraded"
        // on the L2 side would be inaccurate. The effects of this "back-dating" of L2 upgrade batches will be reduced
        // as the permitted delay window is reduced in the future.
        require(block.timestamp >= _proposedUpgrade.upgradeTimestamp, "Upgrade is not ready yet");
    }

```

### Recommended Mitigation
 - Use a decentralized oracle to verify the current time

##

## [L-5]  ``require(_l2ToL1message.length == 76, "kk")`` Attackers Can Bypass L2 to L1 Message Validation Checks

### Impact
The check ```require(_l2ToL1message.length == 76, "kk")`` is a simple way to validate the length of an L2 to L1 message. However, it is not a ``perfect way`` to validate messages, and ``attackers`` can ``bypass`` it.

Attacker bypass the check is to send a message with a ``valid length``, but with the ``addresses`` of the ``sender`` and ``receiver`` reversed. This would result in the Ether being sent to the wrong address.

A more comprehensive check would also check the contents of the message to ensure that it is a valid ``function signature``, followed by the address of the ``sender``, the address of the ``receiver``, and the amount of ``Ether`` being sent.

### POC
```solidity
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

329: require(_l2ToL1message.length == 76, "kk");

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L329

### Recommended Mitigation
Could use a library to check the function signature, the sender address, the receiver address, and the amount of Ether being sent
																																																																																			##

## [L-6] ``_parseL2EthWithdrawalMessage(_message)`` can return ``l1WethWithdrawReceiver`` value address(0) and amount is 0

### Impact

If the ``_parseL2EthWithdrawalMessage()`` function returns ``address(0)`` and ``0``, the ``finalizeWithdrawal()`` function will not be able to finalize the withdrawal. This is because the ``finalizeWithdrawal()`` function needs to know the recipient address and the amount of ETH to be withdrawn in order to finalize the withdrawal.

Additionally, if the ``_parseL2EthWithdrawalMessage()`` function returns ``address(0)`` and ``0``, the finalizeWithdrawal() function will not be able to wrap the ``ETH`` to ``WETH tokens`` and transfer the WETH tokens to the withdrawal receiver. This is because the ``finalizeWithdrawal()`` function needs to know the recipient address and the amount of ETH to be withdrawn in order to wrap the ETH to WETH tokens and transfer the WETH tokens to the withdrawal receiver.

As a result, if the ``_parseL2EthWithdrawalMessage()`` function returns address(0) and 0, the finalizeWithdrawal() function will fail and the withdrawal will not be finalized.

The situations readUint256() function return amount 0

 - The bytes array does not contain a valid uint256 value at the specified offset.
 - The bytes array is too short to contain a full uint256 value
 - The offset is greater than the length of the bytes array

The situations readAddress() function return address(0)

 - The bytes array does not contain a valid address at the specified offset
 - The bytes array is too short to contain a full address

### POC

```solidity
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol

242: (address l1WethWithdrawReceiver, uint256 amount) = _parseL2EthWithdrawalMessage(_message);

272: function _parseL2EthWithdrawalMessage(
        bytes memory _message
    ) internal view returns (address l1WethReceiver, uint256 ethAmount) {
        // Check that the message length is correct.
        // additionalData (WETH withdrawal data): l2 sender address + weth receiver address = 20 + 20 = 40 (bytes)
        // It should be equal to the length of the function signature + eth receiver address + uint256 amount +
        // additionalData = 4 + 20 + 32 + 40 = 96 (bytes).
        require(_message.length == 96, "Incorrect ETH message with additional data length");

        (uint32 functionSignature, uint256 offset) = UnsafeBytes.readUint32(_message, 0);
        require(
            bytes4(functionSignature) == IMailbox.finalizeEthWithdrawal.selector,
            "Incorrect ETH message function selector"
        );

        address l1EthReceiver;
        (l1EthReceiver, offset) = UnsafeBytes.readAddress(_message, offset);
        require(l1EthReceiver == address(this), "Wrong L1 ETH withdraw receiver");

        (ethAmount, offset) = UnsafeBytes.readUint256(_message, offset);

        address l2Sender;
        (l2Sender, offset) = UnsafeBytes.readAddress(_message, offset);
        require(l2Sender == l2Bridge, "The withdrawal was not initiated by L2 bridge");

        // Parse additional data
        (l1WethReceiver, offset) = UnsafeBytes.readAddress(_message, offset);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L242

### Recommended Mitigation
Add the ``address(0)`` and amount > 0 check 

##

## [L-7] Use expiry pattern in Verify signature processes

Including this check helps prevent replay attacks and ensures that the signature is only valid for a limited time period, making your function more secure.

### POC
```solidity
FILE: 2023-10-zksync/code/system-contracts/contracts/DefaultAccount.sol

102: if (_isValidSignature(txHash, _transaction.signature)) {

161: function _isValidSignature(bytes32 _hash, bytes memory _signature) internal view returns (bool) {
        require(_signature.length == 65, "Signature length is incorrect");
        uint8 v;
        bytes32 r;
        bytes32 s;
        // Signature loading code
        // we jump 32 (0x20) as the first slot of bytes contains the length
        // we jump 65 (0x41) per signature
        // for v we load 32 bytes ending with v (the first 31 come from s) then apply a mask
        assembly {
            r := mload(add(_signature, 0x20))
            s := mload(add(_signature, 0x40))
            v := and(mload(add(_signature, 0x41)), 0xff)
        }
        require(v == 27 || v == 28, "v is neither 27 nor 28");

        // EIP-2 still allows signature malleability for ecrecover(). Remove this possibility and make the signature
        // unique. Appendix F in the Ethereum Yellow paper (https://ethereum.github.io/yellowpaper/paper.pdf), defines
        // the valid range for s in (301): 0 < s < secp256k1n ÷ 2 + 1, and for v in (302): v ∈ {27, 28}. Most
        // signatures from current libraries generate a unique signature with an s-value in the lower half order.
        //
        // If your library generates malleable signatures, such as s-values in the upper range, calculate a new s-value
        // with 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141 - s1 and flip v from 27 to 28 or
        // vice versa. If your library also generates signatures with 0/1 for v instead 27/28, add 27 to v to accept
        // these malleable signatures as well.
        require(uint256(s) <= 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0, "Invalid s");

        address recoveredAddress = ecrecover(_hash, v, r, s);

        return recoveredAddress == address(this) && recoveredAddress != address(0);
    }

```

### Recommended Mitigation
Consider add expiry time for ``_isValidSignature() ``

##

## [L-8] _verifyDepositLimit() function name conflict with implementations 

The function name ``_verifyDepositLimit()`` does indeed suggest that the function only performs verification, but the implementation also updates the user deposit amount.

This is a potential source of confusion, and it is generally a good practice to avoid using function names that are misleading or inaccurate.

### POC

```solidity
FILE: 2023-10-zksync/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

/// @dev Verify the deposit limit is reached to its cap or not
    function _verifyDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal {
        IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token);
        if (!limitData.depositLimitation) return; // no deposit limitation is placed for this token

        if (_claiming) {
            totalDepositedAmountPerUser[_l1Token][_depositor] -= _amount;
        } else {
            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
        }
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L339-L350

### Recommended Mitigation

Add more descriptive and accurate function name

```diff

/// @dev Verify the deposit limit is reached to its cap or not
-     function _verifyDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal {
+     function _verifyAndUpdateUserDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal {
        IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token);
        if (!limitData.depositLimitation) return; // no deposit limitation is placed for this token

        if (_claiming) {
            totalDepositedAmountPerUser[_l1Token][_depositor] -= _amount;
        } else {
            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
        }
    }

```

##

## [L-9] ``claimFailedDeposit()`` function allow to claim others failed amount if the other sender deposit also failed and have the permission 

### Impact
The ``claimFailedDeposit()`` function allows the caller to claim other sender's failed amount if the following conditions are met:

The caller is authorized to call the function, as checked by the senderCanCallFunction() modifier.
The other sender's deposit has failed, as proven by the Merkle proof.
The caller knows the other sender's deposit address, token address, and transaction hash.

This is possible because the function does not check the sender's address when verifying the Merkle proof. This means that anyone with the correct Merkle proof can claim a failed deposit, regardless of who made the deposit.

### POC
```
 function claimFailedDeposit(
        address _depositSender,
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

        uint256 amount = depositAmount[_depositSender][_l1Token][_l2TxHash];
        require(amount > 0, "y1");

        // Change the total deposited amount by the user
        _verifyDepositLimit(_l1Token, _depositSender, amount, true);

        delete depositAmount[_depositSender][_l1Token][_l2TxHash];
        // Withdraw funds
        IERC20(_l1Token).safeTransfer(_depositSender, amount);

        emit ClaimedFailedDeposit(_depositSender, _l1Token, amount);
    }

```
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L255-L285




	

	
																																																																						
																						
																																																

																								
																																																																																																																																																																																										
																																																																																																																																												