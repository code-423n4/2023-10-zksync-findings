##

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

 

## [L-] Excess funds sent via msg.value not refunded	

The code below allows the caller to provide Ether, but does not refund the amount in excess of what's required, leaving funds stranded in the contract. The condition should be changed to check for equality, or the code should refund the excess.	

if (msg.value <= _amount) revert FeeAmountNotSet();	

FALSE	Unsafe use of transfer()/transferFrom() with IERC20	Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions on L1 do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their [function signatures](https://medium.com/coinmonks/missing-return-value-bug-at-least-130-tokens-affected-d67bf08521ca) do not match and therefore the calls made, revert (see this [link](https://gist.github.com/IllIllI000/2b00a32e8f0559e8f386ea4f1800abc5) for a test case). Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead	_paymentToken.transferFrom(, paymentToken.transfer(																							

FALSE	Return values of transfer()/transferFrom() not checked	Not all IERC20 implementations revert() when there's a failure in transfer()/transferFrom(). The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment	paymentToken.transfer(, _paymentToken.transferFrom(																							
																										
FALSE	_safeMint() should be used rather than _mint() wherever possible	_mint() is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/transmissions11/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function. In the cases below, _mint() does not call ERC721TokenReceiver.onERC721Received() on the recipient.	_mint(to, assetId, share);																							
																										
FALSE	Contracts are vulnerable to rebasing accounting-related issues	Rebasing tokens are tokens that have each holder's balanceof() increase over time. Aave aTokens are an example of such tokens. If rebasing tokens are used, rewards accrue to the contract holding the tokens, and cannot be withdrawn by the original depositor. To address the issue, track 'shares' deposited on a pro-rata basis, and let shares be redeemed for their proportion of the current balance at the time of the withdrawal.																																														

Revert on Transfer to the Zero Address
Some tokens (e.g. openzeppelin) revert when attempting to transfer to address(0).

This may break systems that expect to be able to burn tokens by transferring them to address(0).

example: RevertToZero.sol


FALSE	Some tokens may revert when  zero value transfers are made	In spite of the fact that EIP-20 [states](https://github.com/ethereum/EIPs/blob/46b9b698815abbfa628cd1097311deee77dd45c5/EIPS/eip-20.md?plain=1#L116) that zero-valued transfers must be accepted, some tokens, such as LEND will revert if this is attempted, which may cause transactions that involve other tokens (such as batch operations) to fully revert. Consider skipping the transfer if the amount is zero, which will also save gas.	IERC20(erc20).safeTransferFrom(_fromAddress, address(this), _amount); , IERC20(erc20).safeTransfer(msg.sender, _amount);	

FALSE	Allowed fees/rates should be capped by smart contracts	Fees/rates should be required to be below 100%, preferably at a much lower limit, to ensure users don't have to monitor the blockchain for changes prior to using the protocol	256      function setBigBangEthMarketDebtRate(uint256 _rate) external onlyOwner { , 257          bigBangEthDebtRate = _rate;

FALSE	Array lengths not checked	If the length of the arrays are not required to be of the same length, user operations may not be fully executed due to a mismatch in the number of items iterated over, versus the number of items provided in the second array	310           address[] calldata users, 311           uint256[] calldata maxBorrowParts,

FALSE	Draft imports may break in new minor versions	While OpenZeppelin draft contracts are safe to use and have been audited, their 'draft' status means that the EIPs they're based on are not finalized, and thus there may be breaking changes in even [minor releases](https://docs.openzeppelin.com/contracts/3.x/api/drafts). If a bug is found in this version of OpenZeppelin, and the version that you're forced to upgrade to has breaking changes in the new version, you'll encounter unnecessary delays in porting and testing replacement contracts. Ensure that you have extensive test coverage of this area so that differences can be automatically detected, and have a plan in place for how you would fully test a new version of these contracts if they do indeed change unexpectedly. Consider creating a forked version of the file rather than importing it from the package, and manually patch your fork as changes are made.	5:    import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";																							
																										
FALSE	Functions calling contracts/addresses with transfer hooks are missing reentrancy guards	Even if the function follows the best practice of check-effects-interaction, not using a reentrancy guard when there may be transfer hooks will open the users of this protocol up to [read-only reentrancies](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem/) with no way to protect against it, except by block-listing the whole protocol.	/// @audit `extractUnderlying()` 148:             IERC20(erc20).safeTransfer(msg.sender, _amount);																							
																										
FALSE	Use Ownable2Step rather than Ownable	[Ownable2Step](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/3d7a93876a2e5e1d7fe29b5a0e96e222afdc4cfa/contracts/access/Ownable2Step.sol#L31-L56) and [Ownable2StepUpgradeable ]p (https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/25aabd286e002a1526c345c8db259d57bdf0ad28/contracts/access/Ownable2StepUpgradeable.sol#L47-L63 ) revent the contract ownership from mistakenly being transferred to an address that cannot handle it (e.g. due to a typo in the address), by requiring that the recipient of the owner permissions actively accept via a contract call of its own.	26    contract TapiocaWrapper is Ownable { , 22    contract Multicall3 is Ownable {																							
																										
FALSE	Signature use at deadlines should be allowed	According to [EIP-2612](https://github.com/ethereum/EIPs/blob/71dc97318013bf2ac572ab63fab530ac9ef419ca/EIPS/eip-2612.md?plain=1#L58), signatures used on exactly the deadline timestamp are supposed to be allowed. While the signature may or may not be used for the exact EIP-2612 use case (transfer approvals), for consistency's sake, all deadlines should follow this semantic. If the timestamp is an expiration rather than a deadline, consider whether it makes more sense to include the expiration timestamp as a valid timestamp, as is done for deadlines.	159:         if (participant.expiry < block.timestamp) { ,  require(aoTapOption.expiry > block.timestamp, "adb: Option expired");																							
																										
FALSE	NFT doesn't handle hard forks	When there are hard forks, users often have to go through [many hoops](https://twitter.com/elerium115/status/1558471934924431363) to ensure that they control ownership on every fork. Consider adding require(1 == chain.chainId), or the chain ID of whichever chain you prefer, to the functions below, or at least include the chain ID in the URI, so that there is no confusion about which chain is the owner of the NFT.	function tokenURI( 69           uint256 _tokenId  70       ) public view override returns (string memory) {  71           return tokenURIs[_tokenId];																							
																										
FALSE	decimals() is not a part of the ERC-20 standard	The decimals() function is not a part of the [ERC-20 standard](https://eips.ethereum.org/EIPS/eip-20), and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.	_paymentToken.decimals() , _paymentToken.decimals()																							
																										
FALSE	tokenURI() does not follow EIP-721	The [EIP](https://eips.ethereum.org/EIPS/eip-721) states that tokenURI() "Throws if _tokenId is not a valid NFT", which the code below does not do. If the NFT has not yet been minted, tokenURI() should revert	68       function tokenURI(																							
																										
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

																								
																																																																																																																																																																																										
																																																																																																																																												