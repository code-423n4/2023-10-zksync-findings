##

## [L-] Return values of assembly call function not checked 

## Initializers could be front run 

## Assembly call return value not checked 

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

FALSE	Array lengths not checked	
If the length of the arrays are not required to be of the same length, user operations may not be fully executed due to a mismatch in the number of items iterated over, versus the number of items provided in the second array	310           address[] calldata users, 311           uint256[] calldata maxBorrowParts,

FALSE	Draft imports may break in new minor versions	While OpenZeppelin draft contracts are safe to use and have been audited, their 'draft' status means that the EIPs they're based on are not finalized, and thus there may be breaking changes in even [minor releases](https://docs.openzeppelin.com/contracts/3.x/api/drafts). If a bug is found in this version of OpenZeppelin, and the version that you're forced to upgrade to has breaking changes in the new version, you'll encounter unnecessary delays in porting and testing replacement contracts. Ensure that you have extensive test coverage of this area so that differences can be automatically detected, and have a plan in place for how you would fully test a new version of these contracts if they do indeed change unexpectedly. Consider creating a forked version of the file rather than importing it from the package, and manually patch your fork as changes are made.	5:    import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";																							
																										
FALSE	Functions calling contracts/addresses with transfer hooks are missing reentrancy guards	

Even if the function follows the best practice of check-effects-interaction, not using a reentrancy guard when there may be transfer hooks will open the users of this protocol up to [read-only reentrancies](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem/) with no way to protect against it, except by block-listing the whole protocol.	/// @audit `extractUnderlying()` 148:             IERC20(erc20).safeTransfer(msg.sender, _amount);																							
																										
																							
																										
FALSE	Signature use at deadlines should be allowed	

According to [EIP-2612](https://github.com/ethereum/EIPs/blob/71dc97318013bf2ac572ab63fab530ac9ef419ca/EIPS/eip-2612.md?plain=1#L58), signatures used on exactly the deadline timestamp are supposed to be allowed. While the signature may or may not be used for the exact EIP-2612 use case (transfer approvals), for consistency's sake, all deadlines should follow this semantic. If the timestamp is an expiration rather than a deadline, consider whether it makes more sense to include the expiration timestamp as a valid timestamp, as is done for deadlines.	159:         if (participant.expiry < block.timestamp) { ,  require(aoTapOption.expiry > block.timestamp, "adb: Option expired");																							
																										
																						
																										
FALSE	decimals() is not a part of the ERC-20 standard	The decimals() function is not a part of the [ERC-20 standard](https://eips.ethereum.org/EIPS/eip-20), and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.	_paymentToken.decimals() , _paymentToken.decimals()																							
																										
																						
																										
FALSE	Open TODOs	Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment	//    (TODO: Word better?) ,// TODO: Make whole function unchecked																							
																										
FALSE	Calls to _get() will revert when totalSupply() returns zero	totalSupply() being zero will result in a division by zero, causing the transaction to fail. The function should instead special-case this scenario, and avoid reverting.	uint256 lpPrice = (SG_POOL.totalLiquidity() * 51:              uint256(UNDERLYING.latestAnswer())) / SG_POOL.totalSupply();																							
																										
FALSE	latestAnswer() is deprecated	Use latestRoundData() instead so that you can tell whether the answer is stale or not. The latestAnswer() function returns zero if it is unable to fetch data, which may be the case if ChainLink stops supporting this API. The API and its deprecation message no longer even appear on the ChainLink website, so it is dangerous to continue using it.	uint256 _btcPrice = uint256(BTC_FEED.latestAnswer()) * 1e10;

FALSE	Missing checks for ecrecover() signature malleability	ecrecover() accepts as valid, two versions of signatures, meaning an attacker can use the same signature twice, or an attacker may be able to front-run the original signer with the altered version of the signature, causing the signer's transaction to revert due to nonce reuse. Consider adding checks for signature malleability, or using OpenZeppelin's ECDSA library to perform the extra checks necessary in order to prevent malleability.	206:             if (signer == ecrecover(digest, v, r, s)) {																							
																										
FALSE	Code does not follow the best practice of check-effects-interaction	Code should follow the best-practice of [check-effects-interaction](https://blockchain-academy.hs-mittweida.de/courses/solidity-coding-beginners-to-intermediate/lessons/solidity-11-coding-patterns/topic/checks-effects-interactions/), where state variables are updated before any external calls are made. Doing so prevents a large class of reentrancy bugs.

FALSE	addStrategyToken() does note remove old entries before adding new ones																									
																										
FALSE	name() is not a part of the ERC-20 standard																									
																										
FALSE	symbol() is not a part of the ERC-20 standard																									
																										
FALSE	SafeTransferLib does not ensure that the token contract exists																									
																										
FALSE	Tokens may be minted to address(0x0)


FALSE	Consider disabling renounceOwnership()	If the plan for your project does not include eventually giving up all ownership control, consider overwriting OpenZeppelin's Ownable's renounceOwnership() function in order to disable it.	abstract contract Market is MarketERC20, BoringOwnable { , 	

FALSE	Use OpenZeppelin's or Solady's Ownable, rather than re-inventing the wheel	Both implementations have been optimized and are usage-hardened, so writing your own is unnecessary	4:   import "@boringcrypto/boring-solidity/contracts/BoringOwnable.sol";																							
																										
FALSE	Array is push()ed but not pop()ed	Array entries are added but are never removed. Consider whether this should be the case, or whether there should be a maximum, or whether old entries should be removed. Cases where there are specific potential problems will be flagged separately under a different issue.	329:         singularityMasterContracts.push(mc); , 351:         bigbangMasterContracts.push(mc);																							

FALSE	Mixed usage of int/uint with int256/uint256	int256/uint256 are the preferred type names (they're what are used for function signatures), so they should be used consistently	75:      function _allowedLend(address from, uint share) internal { , 129:         (, , address to, uint256 amount, uint duration) = abi.decode(																							
																										
FALSE	Unsafe conversion from unsigned to signed values	Solidity follows [two's complement](https://en.wikipedia.org/wiki/Two%27s_complement) rules for its integers, meaning that the most significant bit for signed integers is used to denote the sign, and converting between the two requires inverting all of the bits and adding one. Because of this, casting an unsigned integer to a signed one may result in a change of the sign and or magnitude of the value. For example, int8(type(uint8).max) is not equal to type(int8).max, but is equal to -1. type(uint8).max in binary is 11111111, which if cast to a signed value, means the first binary 1 indicates a negative value, and the binary 1s, invert to all zeroes, and when one is added, it becomes one, but negative, and therefore the decimal value of binary 11111111 is -1.	456:          int256 diff = int256(minLiquidatorReward) - int256(maxLiquidatorReward); , 459:              int256(maxLiquidatorReward);																																													


FALSE	Return values of approve() not checked	Not all IERC20 implementations revert() when there's a failure in approve(). The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything	217:          IERC20(swapData.tokenOut).approve(externalData.tOft, amountOut); , 215:          IERC20(erc20).approve(externalData.swapper, amount);	

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

																								
																																																																																																																																																																																										
																																																																																																																																												