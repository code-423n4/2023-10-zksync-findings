## [L-01] _safeMint() should be used rather than _mint() wherever possible
_mint() is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both open [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.


```solidity
110    _mint(_to, _amount);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L110


```solidity
99    _mint(_to, msg.value);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L99


## [L‑02] Use Ownable2Step instead of Ownable
Ownable2Step and Ownable2StepUpgradeable prevent the contract ownership from mistakenly being transferred to an address that cannot handle it (e.g. due to a typo in the address), by requiring that the recipient of the owner permissions actively accept via a contract call of its own.


```solidity
9        import "@openzeppelin/contracts/access/Ownable.sol";
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L9


## [L-03] Contracts are not using their OZ upgradeable counterparts
Description
The non-upgradeable standard version of OpenZeppelin's library, such as `Ownable`, `Pausable`, `Address`, `Context`, `SafeERC20`, `ERC1967Upgrade` etc, are inherited / used by both the proxy and the implementation contracts.

As a result, when attempting to use the upgrades plugin mentioned, the following errors are raised:

```
Error: Contract `FiatTokenV1` is not upgrade safe

contracts/v1/FiatTokenV1.sol:58: Variable `totalSupply_` is assigned an initial value
  Move the assignment to the initializer
  https://zpl.in/upgrades/error-004

contracts/v1/Pausable.sol:49: Variable `paused` is assigned an initial value
  Move the assignment to the initializer
  https://zpl.in/upgrades/error-004

contracts/v1/Ownable.sol:28: Contract `Ownable` has a constructor
  Define an initializer instead
  https://zpl.in/upgrades/error-001

contracts/util/Address.sol:186: Use of delegatecall is not allowed
  https://zpl.in/upgrades/error-002
       
```
Having reviewed these errors, none had any adversarial impact:

`totalSupply_` and `paused` are explictly assigned the default values `0` and `false`
the implementation contracts utilises the internal `_transferOwnership()` in the initializer, thus transferring ownership to `newOwner` regardless of who the current owner is
`Address's` `delegatecall` is only used by the `ERC1967Upgrade` contract. Comparing both the `Address` and `ERC1967Upgrade` contracts against their upgradeable counterparts show similar behaviour (differences are some refactoring done to shift the delegatecall into the `ERC1967Upgrade` contract).
Nevertheless, it would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

### Recommended Mitigation Steps
Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.


```solidity
5   import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
6   import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L5-L6


```solidity
5    import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L5


```solidity
9        import "@openzeppelin/contracts/access/Ownable.sol";
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L9


## [L-04] no check if the burn amount is zero or not
if the amount is zero so the unhecked block will divided zero on 3 and we use gas for nothing ! if we set zero we may pass the _burn checks, i know it is passed by only permit but it's better to avoid this happen because it's seting by human and it means it can be set with 0 balance to burn !


```solidity
119   _burn(_from, _amount);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#119


```solidity
79   _burn(_from, _amount);

105  _burn(msg.sender, _amount);
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L79


## [L-05] AVOID USING TX.ORIGIN
tx.origin is a global variable in Solidity that returns the address of the account that sent the transaction.
Using the variable could make a contract vulnerable if an authorized account calls a malicious contract. You can impersonate a user using a third party contract.


```solidity
196   refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L196


```solidity
183    refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L183


## [L-06] Possible division by 0 is not prevented
These functions can be called with 0 value in the input and this value is not checked for being bigger than 0, that means in some scenarios this can potentially trigger a division by zero.

```solidity
181   uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L181

```solidity
32    require(l2GasForTxBody / _transaction.gasPerPubdataByteLimit <= PRIORITY_TX_MAX_PUBDATA, "uk");
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L32


## [L-07] Condition will not revert when block.timestamp is == to the compared variable
The condition does not revert when block.timestamp is equal to the compared > or < variable. For example, if there is a check for block.timestamp > identifier then there should be a check for cases where block.timestamp is == to identifier

```solidity
111   } else if (timestamp > block.timestamp) {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L111


## [L-08]Gas griefing/theft is possible on unsafe external call
return data (bool success,) has to be stored due to EVM architecture, if in a usage like below, 'out' and 'outsize' values are given (0,0) . Thus, this storage disappears and may come from external contracts a possible Gas griefing/theft problem is avoided

```solidity
81    (bool success, ) = msg.sender.call{value: _amount}("");

106   (bool success, ) = _to.call{value: _amount}("");
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L81


```solidity
61   (bool success, ) = recipient.call{value: amount}("");
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L61


## [L-09] Incorrect exponentiation
Description
Detect use of bitwise `xor ^` instead of exponential `**`


```solidity
59   uint256 newValueXorOldValue = uint256(oldValue ^ _value);

63   _map.map[mapIndex] = (newValueXorOldValue << bitOffset) ^ mapValue;
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L59

Recommendation
Use the correct operator `**` for exponentiation.

## [L-10] Incorrect access control

Contract functions executing critical logic should have appropriate access control enforced via address checks (e.g. owner, controller etc.) typically in modifiers. Missing checks allow attackers to control critical logic. (see [here](https://docs.openzeppelin.com/contracts/3.x/api/access) and [here](https://dasp.co/#item-2))

```solidity
32   function transferFromTo(address _from, address _to, uint256 _amount) external override {
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/L2EthToken.sol#L32