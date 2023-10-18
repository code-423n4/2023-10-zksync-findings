##

## [L-] ``block.timestamp >= _proposedUpgrade.upgradeTimestamp`` in this check malicious miners can manipulate ``block timestamp`` to trigger ``unauthorized upgrades``

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
 - Use a timelock. A timelock is a period of time that must pass before an upgrade can be triggered. This gives users time to react to any malicious upgrades that are proposed 


## [L-]  ``require(_l2ToL1message.length == 76, "kk")`` Attackers Can Bypass L2 to L1 Message Validation Checks

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

## [L-] ``_parseL2EthWithdrawalMessage(_message)`` can return ``l1WethWithdrawReceiver`` value address(0) and amount is 0

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

## [L-] Use expiry pattern in Verify signature processes

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

																																																																						
																						
																										FALSE	Missing checks for ecrecover() signature malleability	ecrecover() accepts as valid, two versions of signatures, meaning an attacker can use the same signature twice, or an attacker may be able to front-run the original signer with the altered version of the signature, causing the signer's transaction to revert due to nonce reuse. Consider adding checks for signature malleability, or using OpenZeppelin's ECDSA library to perform the extra checks necessary in order to prevent malleability.	206:             if (signer == ecrecover(digest, v, r, s)) {																							
																										
																																																																																											FALSE	Tokens may be minted to address(0x0)


FALSE	Consider disabling renounceOwnership()	If the plan for your project does not include eventually giving up all ownership control, consider overwriting OpenZeppelin's Ownable's renounceOwnership() function in order to disable it.	abstract contract Market is MarketERC20, BoringOwnable { , 	

																						
																										
FALSE	Array is push()ed but not pop()ed	Array entries are added but are never removed. Consider whether this should be the case, or whether there should be a maximum, or whether old entries should be removed. Cases where there are specific potential problems will be flagged separately under a different issue.	329:         singularityMasterContracts.push(mc); , 351:         bigbangMasterContracts.push(mc);																							

FALSE	Mixed usage of int/uint with int256/uint256	int256/uint256 are the preferred type names (they're what are used for function signatures), so they should be used consistently	75:      function _allowedLend(address from, uint share) internal { , 129:         (, , address to, uint256 amount, uint duration) = abi.decode(																							
																										
FALSE	Unsafe conversion from unsigned to signed values	Solidity follows [two's complement](https://en.wikipedia.org/wiki/Two%27s_complement) rules for its integers, meaning that the most significant bit for signed integers is used to denote the sign, and converting between the two requires inverting all of the bits and adding one. Because of this, casting an unsigned integer to a signed one may result in a change of the sign and or magnitude of the value. For example, int8(type(uint8).max) is not equal to type(int8).max, but is equal to -1. type(uint8).max in binary is 11111111, which if cast to a signed value, means the first binary 1 indicates a negative value, and the binary 1s, invert to all zeroes, and when one is added, it becomes one, but negative, and therefore the decimal value of binary 11111111 is -1.	456:          int256 diff = int256(minLiquidatorReward) - int256(maxLiquidatorReward); , 459:              int256(maxLiquidatorReward);																																													


FALSE	Return values of approve() not checked	
Not all IERC20 implementations revert() when there's a failure in approve(). The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything	217:          IERC20(swapData.tokenOut).approve(externalData.tOft, amountOut); , 215:          IERC20(erc20).approve(externalData.swapper, amount);	

FALSE	Cast is more restrictive than the type of the variable being assigned	If address foo is being used in an expression such as IERC20 token = FooToken(foo), then the more specific cast to FooToken is a waste because the only thing the compiler will check for is that FooToken extends IERC20 - it won't check any of the function signatures. Therefore, it makes more sense to do IERC20 token = IERC20(token) or better yet FooToken token = FooToken(foo). The former may allow the file in which it's used to remove the import for FooToken	108                   emptyStrategies[address(tapToken_)], , 566:                  clonesOfLength = clonesOfCount(mcLocation);	

FALSE	Inconsistent spacing in comments	Some lines use // x and some use //x. The instances below point out the usages that don't follow the majority, within each file	201:          // Use market helper to deposit and add asset to market , 188:          //check if OFT is still valid	

FALSE	Interfaces should be indicated with an I prefix in the contract name																									
																										
	7:    interface ApproveLike {																									
	11:   interface GatewayLike {																									
	30:   interface LiquidityPoolLike {																									
	40:   interface EscrowLike {																									
	44:   interface ERC2771Like {	


FALSE	Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, for readability	Well-organized data structures make code reviews easier, which may lead to fewer bugs. Consider combining related mappings into mappings to structs, so it's clear what data is related																								
																										
	File: src/token/ERC20.sol																									
	25        mapping(address => uint256) public balanceOf;																									
	26        mapping(address => mapping(address => uint256)) public allowance;																									
	27:       mapping(address => uint256) public nonces;

FALSE	Variables need not be initialized to false	The default value for boolean variables is false, so initializing them to false is superfluous.																								
																										
	23:      bool public paused = false;																									
																										
FALSE	Variables need not be initialized to zero																									
																										
	848:         for (uint256 i = 0; i < 128; i++) {																									
																										
FALSE	Consider using safePermit()	The [Anyswap hack](https://media.dedaub.com/phantom-functions-and-the-billion-dollar-no-op-c56f062ae49f?gi=b1123cfa95f8#ef54) occurred because the permit() function didn't really exist, but the fallback function that took its place did not complain. Consider using [safePermit()](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/5229b75785213541e93fb0a466a3d102a3bf5dbe/contracts/token/ERC20/utils/SafeERC20.sol#L89-L105) which ensures that the permit actually went through.																								
																										
	223:         ERC20PermitLike(asset).permit(owner, address(investmentManager), assets, deadline, v, r, s);		

FALSE	Consider disallowing transfers to address(this)																									
																										
	192      function transfer(uint128 token, address sender, bytes32 receiver, uint128 amount)																									
	193          public																									
	194          onlyPoolManager																									
	195          pauseable																									
	196:     {																									
	59:      function transfer(address to, uint256 value) public override restricted(_msgSender(), to, value) returns (bool) {		

FALSE	Events may be emitted out of order due to reentrancy	Ensure that events follow the best practice of check-effects-interaction, and are emitted before external calls																								
																										
	/// @audit transferFrom() prior to emission																									
	479:         emit DepositProcessed(liquidityPool, user, currencyAmount);																									
																										
	/// @audit processDeposit() prior to emission																									
	143:         emit Deposit(address(this), receiver, assets, shares);																									
	/// @audit processMint() prior to emission																									
	151:         emit Deposit(address(this), receiver, assets, shares);

FALSE	Missing checks for empty bytes when updating bytes state variables	Unless the code is attempting to 'delete' the state variable, the caller shouldn't have to write "" to the state variable																								
																										
	205:         tranche.trancheId = trancheId;	

																								
																																																																																																																																																																																										
																																																																																																																																												