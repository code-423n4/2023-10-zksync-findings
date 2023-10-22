# QA Report

## Summary

### Low Issues

Total **16 instances** over **9 issues**:

|ID|Issue|Instances|
|:--:|:---|:--:|
| [[L&#x2011;01]](#l01-do-not-put-structs-directly-into-the-appstorage-of-a-diamond-proxy) | Do not put structs directly into the AppStorage of a Diamond proxy | 1 |
| [[L&#x2011;02]](#l02-variables-shadowing-other-definitions) | Variables shadowing other definitions | 1 |
| [[L&#x2011;03]](#l03-deprecated-state-variables-should-be-privateinternal) | Deprecated state variables should be `private`/`internal` | 2 |
| [[L&#x2011;04]](#l04-tokens-may-be-minted-to-the-zero-address) | Tokens may be minted to the zero address | 1 |
| [[L&#x2011;05]](#l05-newer-zksolc-versions-should-be-used) | Newer zksolc versions should be used | 1 |
| [[L&#x2011;06]](#l06-unsafe-solidity-low-level-call-can-cause-gas-grief-attack) | Unsafe solidity low-level call can cause gas grief attack | 4 |
| [[L&#x2011;07]](#l07-owner-can-renounce-ownership) | Owner can renounce Ownership | 3 |
| [[L&#x2011;08]](#l08-some-tokens-may-revert-when-zero-value-transfers-are-made) | Some tokens may revert when zero value transfers are made | 1 |
| [[L&#x2011;09]](#l09-unchecked-blocks-with-subtractions-may-underflow) | `unchecked` blocks with subtractions may underflow | 2 |

### Non Critical Issues

Total **166 instances** over **14 issues**:

|ID|Issue|Instances|
|:--:|:---|:--:|
| [[N&#x2011;01]](#n01-contract-name-does-not-match-its-filename) | Contract name does not match its filename | 4 |
| [[N&#x2011;02]](#n02-openzeppelincontracts-should-be-upgraded-to-a-newer-version) | @openzeppelin/contracts should be upgraded to a newer version | 23 |
| [[N&#x2011;03]](#n03-lib-openzeppelincontracts-upgradeable-should-be-upgraded-to-a-newer-version) | Lib @openzeppelin/contracts-upgradeable should be upgraded to a newer version | 3 |
| [[N&#x2011;04]](#n04-contracts-containing-only-utility-functions-should-be-made-into-libraries) | Contracts containing only utility functions should be made into libraries | 1 |
| [[N&#x2011;05]](#n05-use-inheritdoc-for-overridden-functions) | Use `@inheritdoc` for overridden functions | 19 |
| [[N&#x2011;06]](#n06-missing-zero-address-check-in-functions-with-address-parameters) | Missing zero address check in functions with address parameters | 42 |
| [[N&#x2011;07]](#n07-using-underscore-at-the-end-of-variable-name) | Using underscore at the end of variable name | 3 |
| [[N&#x2011;08]](#n08-whitespace-in-expressions) | Whitespace in Expressions | 9 |
| [[N&#x2011;09]](#n09-use-a-struct-to-encapsulate-multiple-function-parameters) | Use a struct to encapsulate multiple function parameters | 6 |
| [[N&#x2011;10]](#n10-returning-a-struct-instead-of-a-bunch-of-variables-is-better) | Returning a struct instead of a bunch of variables is better | 1 |
| [[N&#x2011;11]](#n11-contract-variables-should-have-comments) | Contract variables should have comments | 5 |
| [[N&#x2011;12]](#n12-empty-bytes-check-is-missing) | Empty bytes check is missing | 16 |
| [[N&#x2011;13]](#n13-control-structures-do-not-follow-the-solidity-style-guide) | Control structures do not follow the Solidity Style Guide | 18 |
| [[N&#x2011;14]](#n14-non-assembly-method-available) | Non-assembly method available | 16 |

## Low Issues

### [L&#x2011;01] Do not put structs directly into the AppStorage of a Diamond proxy

Do not put structs directly into the AppStorage of a Diamond proxy, as this will make the inner structs difficult to upgrade.
You won't be able to add new state variables to the inner structs during upgrades without overwriting the storage slot of variables declared after the struct.

The solution is to store the inner structs in mappings rather than directly in AppStorage, as the variable storage slot in mappings is calculated differently and is not continuous in storage.

There is 1 instance:

- *Storage.sol* ( [#L107](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Storage.sol#L107) ):

```solidity
107:     VerifierParams verifierParams;
```

### [L&#x2011;02] Variables shadowing other definitions

A variable declaration shadowing any other existing definition is error-prone. It can cause confusion for developers, potentially introduce bugs, and reduce the readability and maintainability.

There is 1 instance:

- *Utils.sol* ( [#L86](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/Utils.sol#L86) ):

```solidity
/// @audit Shadows `function bytecodeLenInWords()`
86:         uint256 bytecodeLenInWords = _bytecode.length / 32;
```

### [L&#x2011;03] Deprecated state variables should be `private`/`internal`

The deprecated state variables should no longer be used by external contracts. In many cases they are used as slot placeholders.

The public deprecated variables could confuse external contract developers or cause incorrect data to be read, leading to bugs.
If there is a need to obtain its historical data, it can be obtained by reading the storage directly off the chain.

There are 2 instances:

- *L1ERC20Bridge.sol* ( [#L54](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L54), [#L56-L57](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L56-L57) ):

```solidity
54:     mapping(address => uint256) public __DEPRECATED_lastWithdrawalLimitReset;

56:     /// @dev A mapping L1 token address => the accumulated withdrawn amount during the withdrawal limit window
57:     mapping(address => uint256) public __DEPRECATED_withdrawnAmountInWindow;
```

### [L&#x2011;04] Tokens may be minted to the zero address

The target address is not checked in the mint function.

There is 1 instance:

- *L2EthToken.sol* ( [#L64-L68](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L2EthToken.sol#L64-L68) ):

```solidity
64:     function mint(address _account, uint256 _amount) external override onlyCallFromBootloader {
65:         totalSupply += _amount;
66:         balance[_account] += _amount;
67:         emit Mint(_account, _amount);
68:     }
```

### [L&#x2011;05] Newer zksolc versions should be used

The [latest versions of zksolc](https://github.com/matter-labs/zksolc-bin/releases) have **fixed some known bugs** and added a number of new features and optimizations. See [the changelog](https://github.com/matter-labs/era-compiler-solidity/blob/main/CHANGELOG.md) for details.

There are 1 instances:

- Global finding

### [L&#x2011;06] Unsafe solidity low-level call can cause gas grief attack

Using the low-level calls of a solidity address can leave the contract open to gas grief attacks. These attacks occur when the called contract returns a large amount of data.
So when calling an external contract, it is necessary to check the length of the return data before reading/copying it (using `returndatasize()`).

There are 4 instances:

- *L1ERC20Bridge.sol* ( [#L241](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L241), [#L242](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L242), [#L243](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L243) ):

```solidity
241:         (, bytes memory data1) = _token.staticcall(abi.encodeCall(IERC20Metadata.name, ()));

242:         (, bytes memory data2) = _token.staticcall(abi.encodeCall(IERC20Metadata.symbol, ()));

243:         (, bytes memory data3) = _token.staticcall(abi.encodeCall(IERC20Metadata.decimals, ()));
```

- *Governance.sol* ( [#L226](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L226) ):

```solidity
226:             (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
```

### [L&#x2011;07] Owner can renounce Ownership

Each of the following contracts implements or inherits the `renounceOwnership()` function. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

There are 3 instances:

- *AllowList.sol* ( [#L18](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L18) ):

```solidity
18: contract AllowList is IAllowList, Ownable2Step {
```

- *Governance.sol* ( [#L20-L21](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L20-L21) ):

```solidity
20: contract Governance is IGovernance, Ownable2Step {
21:     /// @notice A constant representing the timestamp for completed operations.
```

- *ValidatorTimelock.sol* ( [#L20](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L20) ):

```solidity
20: contract ValidatorTimelock is IExecutor, Ownable2Step {
```

### [L&#x2011;08] Some tokens may revert when zero value transfers are made

Despite the fact that [EIP-20 states](https://github.com/ethereum/EIPs/blob/7500ac4fc1bbdfaf684e7ef851f798f6b667b2fe/EIPS/eip-20.md?plain=1#L116) that zero-value transfers must be accepted, some tokens, such as LEND, will revert if this is attempted, which may cause transactions that involve other tokens (such as batch operations) to fully revert. Consider skipping the transfer if the amount is zero, which will also save gas.

There is 1 instance:

- *L1ERC20Bridge.sol* ( [#L293-L320](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L293-L320) ):

```solidity
293:     function finalizeWithdrawal(
294:         uint256 _l2BatchNumber,
295:         uint256 _l2MessageIndex,
296:         uint16 _l2TxNumberInBatch,
297:         bytes calldata _message,
298:         bytes32[] calldata _merkleProof
299:     ) external nonReentrant senderCanCallFunction(allowList) {
300:         require(!isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex], "pw");
301: 
302:         L2Message memory l2ToL1Message = L2Message({
303:             txNumberInBatch: _l2TxNumberInBatch,
304:             sender: l2Bridge,
305:             data: _message
306:         });
307: 
308:         (address l1Receiver, address l1Token, uint256 amount) = _parseL2WithdrawalMessage(l2ToL1Message.data);
309:         // Preventing the stack too deep error
310:         {
311:             bool success = zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);
312:             require(success, "nq");
313:         }
314: 
315:         isWithdrawalFinalized[_l2BatchNumber][_l2MessageIndex] = true;
316:         // Withdraw funds
317:         IERC20(l1Token).safeTransfer(l1Receiver, amount);
318: 
319:         emit WithdrawalFinalized(l1Receiver, l1Token, amount);
320:     }
```

### [L&#x2011;09] `unchecked` blocks with subtractions may underflow

There aren't any checks to avoid an underflow which can happen inside an `unchecked` block, so the following subtractions may underflow silently.

There are 2 instances:

- *AddressAliasHelper.sol* ( [#L40](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/vendor/AddressAliasHelper.sol#L40) ):

```solidity
40:             l1Address = address(uint160(l2Address) - offset);
```

- *Compressor.sol* ( [#L239](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Compressor.sol#L239) ):

```solidity
239:                 require(_initialValue - convertedValue == _finalValue, "sub: initial minus converted not equal to final");
```

## Non Critical Issues

### [N&#x2011;01] Contract name does not match its filename

According to the [Solidity Style Guide](https://docs.soliditylang.org/en/latest/style-guide.html#contract-and-library-names), contract and library names should also match their filenames.

There are 4 instances:

- *Admin.sol* ( [#L14](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L14) ):

```solidity
/// @audit Not match filename `Admin.sol`
14: contract AdminFacet is Base, IAdmin {
```

- *Executor.sol* ( [#L18](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L18) ):

```solidity
/// @audit Not match filename `Executor.sol`
18: contract ExecutorFacet is Base, IExecutor {
```

- *Getters.sol* ( [#L15](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L15) ):

```solidity
/// @audit Not match filename `Getters.sol`
15: contract GettersFacet is Base, IGetters, ILegacyGetters {
```

- *Mailbox.sol* ( [#L37](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L37) ):

```solidity
/// @audit Not match filename `Mailbox.sol`
37: contract MailboxFacet is Base, IMailbox {
```

### [N&#x2011;02] @openzeppelin/contracts should be upgraded to a newer version

These contracts import contracts from `@openzeppelin/contracts`, but they are not using [the latest version](https://github.com/OpenZeppelin/openzeppelin-contracts/releases).

The imported version is `4.9.2`.
The imported version is `4.9.2`.

There are 23 instances:

- *L1ERC20Bridge.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L5), [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L5) ):

```solidity
5: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
```

- *L1WethBridge.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L5), [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L5) ):

```solidity
5: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
```

- *AllowList.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L5), [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L5) ):

```solidity
5: import "@openzeppelin/contracts/access/Ownable2Step.sol";

5: import "@openzeppelin/contracts/access/Ownable2Step.sol";
```

- *Governance.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L5), [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L5) ):

```solidity
5: import {Ownable2Step} from "@openzeppelin/contracts/access/Ownable2Step.sol";

5: import {Ownable2Step} from "@openzeppelin/contracts/access/Ownable2Step.sol";
```

- *ValidatorTimelock.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L5), [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L5) ):

```solidity
5: import "@openzeppelin/contracts/access/Ownable2Step.sol";

5: import "@openzeppelin/contracts/access/Ownable2Step.sol";
```

- *Base.sol* ( [#L9](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L9), [#L9](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L9) ):

```solidity
9: import "@openzeppelin/contracts/access/Ownable.sol";

9: import "@openzeppelin/contracts/access/Ownable.sol";
```

- *Mailbox.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L5), [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L5) ):

```solidity
5: import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";

5: import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";
```

- *Diamond.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L5), [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L5) ):

```solidity
5: import "@openzeppelin/contracts/utils/math/SafeCast.sol";

5: import "@openzeppelin/contracts/utils/math/SafeCast.sol";
```

- *TransactionValidator.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L5), [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L5) ):

```solidity
5: import "@openzeppelin/contracts/utils/math/Math.sol";

5: import "@openzeppelin/contracts/utils/math/Math.sol";
```

- *L2ERC20Bridge.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L5), [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L5) ):

```solidity
5: import "@openzeppelin/contracts/proxy/beacon/BeaconProxy.sol";

5: import "@openzeppelin/contracts/proxy/beacon/BeaconProxy.sol";
```

- *L2StandardERC20.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L5) ):

```solidity
5: import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/ERC20PermitUpgradeable.sol";
```

- *L2Weth.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L5) ):

```solidity
5: import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/ERC20PermitUpgradeable.sol";
```

- *L2WethBridge.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L5) ):

```solidity
5: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

### [N&#x2011;03] Lib @openzeppelin/contracts-upgradeable should be upgraded to a newer version

These contracts import contracts from `@openzeppelin/contracts-upgradeable`, but they are not using [the latest version](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/releases).

The imported version is `4.9.2`.

There are 3 instances:

- *L2StandardERC20.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L5) ):

```solidity
5: import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/ERC20PermitUpgradeable.sol";
```

- *L2Weth.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L5) ):

```solidity
5: import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/ERC20PermitUpgradeable.sol";
```

- *L2WethBridge.sol* ( [#L5](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L5) ):

```solidity
5: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

### [N&#x2011;04] Contracts containing only utility functions should be made into libraries

There is 1 instance:

- *BootloaderUtilities.sol* ( [#L16](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/BootloaderUtilities.sol#L16) ):

```solidity
16: contract BootloaderUtilities is IBootloaderUtilities {
```

### [N&#x2011;05] Use `@inheritdoc` for overridden functions

There are 19 instances:

- *L1WethBridge.sol* ( [#L301-L302](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L301-L302) ):

```solidity
301:     /// @return l2Token Address of an L2 token counterpart.
302:     function l2TokenAddress(address _l1Token) public view override returns (address l2Token) {
```

- *Executor.sol* ( [#L173-L182](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L173-L182) ):

```solidity
173:     /// @notice Commit batch
174:     /// @notice 1. Checks timestamp.
175:     /// @notice 2. Process L2 logs.
176:     /// @notice 3. Store batch commitments.
177:     function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)
178:         external
179:         override
180:         nonReentrant
181:         onlyValidator
182:     {
```

- *L2ERC20Bridge.sol* ( [#L130-L131](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L130-L131) ):

```solidity
130:     /// @return Address of an L2 token counterpart
131:     function l2TokenAddress(address _l1Token) public view override returns (address) {
```

- *L2StandardERC20.sol* ( [#L123](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L123), [#L129](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L129), [#L135](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L135) ):

```solidity
123:     function name() public view override returns (string memory) {

129:     function symbol() public view override returns (string memory) {

135:     function decimals() public view override returns (uint8) {
```

- *L2Weth.sol* ( [#L64-L70](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L64-L70), [#L87-L88](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L87-L88), [#L92-L93](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L92-L93), [#L97-L98](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L97-L98), [#L102-L104](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L102-L104) ):

```solidity
64:     /// @notice Function for minting tokens on L2, implemented only to be compatible with IL2StandardToken interface.
65:     /// Always reverts instead of minting anything!
66:     /// Note: Use `deposit`/`depositTo` methods instead.
67:     function bridgeMint(
68:         address, // _account
69:         uint256 // _amount
70:     ) external view override {

87:     /// @notice Deposit Ether to mint WETH.
88:     function deposit() external payable override {

92:     /// @notice Withdraw WETH to get Ether.
93:     function withdraw(uint256 _amount) external override {

97:     /// @notice Deposit Ether to mint WETH to a given account.
98:     function depositTo(address _to) public payable override {

102:     /// @notice Withdraw WETH to get Ether to a given account.
103:     /// burns sender's tokens and sends Ether to the given account
104:     function withdrawTo(address _to, uint256 _amount) public override {
```

- *L2WethBridge.sol* ( [#L109-L110](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L109-L110), [#L114-L115](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L114-L115) ):

```solidity
109:     /// @return l1Token Address of an L1 token counterpart.
110:     function l1TokenAddress(address _l2Token) public view override returns (address l1Token) {

114:     /// @return l2Token Address of an L2 token counterpart.
115:     function l2TokenAddress(address _l1Token) public view override returns (address l2Token) {
```

- *KnownCodesStorage.sol* ( [#L64-L66](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/KnownCodesStorage.sol#L64-L66) ):

```solidity
64:     /// @notice Returns the marker stored for a bytecode hash. 1 means that the bytecode hash is known
65:     /// and can be used for deploying contracts. 0 otherwise.
66:     function getMarker(bytes32 _hash) public view override returns (uint256 marker) {
```

- *L1Messenger.sol* ( [#L118-L119](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L118-L119), [#L164-L167](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L164-L167) ):

```solidity
118:     /// @notice Public functionality to send messages to L1.
119:     function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {

164:     /// @dev Can be called only by KnownCodesStorage system contract.
165:     function requestBytecodeL1Publication(
166:         bytes32 _bytecodeHash
167:     ) external override onlyCallFrom(address(KNOWN_CODE_STORAGE_CONTRACT)) {
```

- *L2EthToken.sol* ( [#L126-L128](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L2EthToken.sol#L126-L128), [#L132-L134](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L2EthToken.sol#L132-L134), [#L138-L140](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L2EthToken.sol#L138-L140) ):

```solidity
126:     /// @dev This method has not been stabilized and might be
127:     /// removed later on.
128:     function name() external pure override returns (string memory) {

132:     /// @dev This method has not been stabilized and might be
133:     /// removed later on.
134:     function symbol() external pure override returns (string memory) {

138:     /// @dev This method has not been stabilized and might be
139:     /// removed later on.
140:     function decimals() external pure override returns (uint8) {
```

### [N&#x2011;06] Missing zero address check in functions with address parameters

Adding a zero address check for each address type parameter can prevent errors.

There are 42 instances:

- *L1ERC20Bridge.sol* ( [#L144-L150](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L144-L150), [#L176-L183](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L176-L183) ):

```solidity
/// @audit `_l2Receiver not checked`
/// @audit `_l1Token not checked`
144:     function deposit(
145:         address _l2Receiver,
146:         address _l1Token,
147:         uint256 _amount,
148:         uint256 _l2TxGasLimit,
149:         uint256 _l2TxGasPerPubdataByte
150:     ) external payable returns (bytes32 l2TxHash) {

/// @audit `_l2Receiver not checked`
176:     function deposit(
177:         address _l2Receiver,
178:         address _l1Token,
179:         uint256 _amount,
180:         uint256 _l2TxGasLimit,
181:         uint256 _l2TxGasPerPubdataByte,
182:         address _refundRecipient
183:     ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) {
```

- *L1WethBridge.sol* ( [#L159-L166](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L159-L166) ):

```solidity
/// @audit `_l2Receiver not checked`
159:     function deposit(
160:         address _l2Receiver,
161:         address _l1Token,
162:         uint256 _amount,
163:         uint256 _l2TxGasLimit,
164:         uint256 _l2TxGasPerPubdataByte,
165:         address _refundRecipient
166:     ) external payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 txHash) {
```

- *AllowList.sol* ( [#L49](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L49), [#L58](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L58), [#L83-L88](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L83-L88), [#L106-L111](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L106-L111), [#L129](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L129) ):

```solidity
/// @audit `_target not checked`
49:     function setAccessMode(address _target, AccessMode _accessMode) external onlyOwner {

/// @audit `_targets not checked`
58:     function setBatchAccessMode(address[] calldata _targets, AccessMode[] calldata _accessModes) external onlyOwner {

/// @audit `_callers not checked`
83:     function setBatchPermissionToCall(
84:         address[] calldata _callers,
85:         address[] calldata _targets,
86:         bytes4[] calldata _functionSigs,
87:         bool[] calldata _enables
88:     ) external onlyOwner {

/// @audit `_caller not checked`
/// @audit `_target not checked`
106:     function setPermissionToCall(
107:         address _caller,
108:         address _target,
109:         bytes4 _functionSig,
110:         bool _enable
111:     ) external onlyOwner {

/// @audit `_l1Token not checked`
129:     function setDepositLimit(address _l1Token, bool _depositLimitation, uint256 _depositCap) external onlyOwner {
```

- *Governance.sol* ( [#L256](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L256) ):

```solidity
/// @audit `_newSecurityCouncil not checked`
256:     function updateSecurityCouncil(address _newSecurityCouncil) external onlySelf {
```

- *ValidatorTimelock.sol* ( [#L52](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L52) ):

```solidity
/// @audit `_newValidator not checked`
52:     function setValidator(address _newValidator) external onlyOwner {
```

- *Admin.sol* ( [#L20](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L20), [#L44](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L44), [#L68](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L68) ):

```solidity
/// @audit `_newPendingGovernor not checked`
20:     function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {

/// @audit `_newPendingAdmin not checked`
44:     function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {

/// @audit `_validator not checked`
68:     function setValidator(address _validator, bool _active) external onlyGovernorOrAdmin {
```

- *Mailbox.sol* ( [#L236-L244](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236-L244) ):

```solidity
/// @audit `_contractL2 not checked`
/// @audit `_refundRecipient not checked`
236:     function requestL2Transaction(
237:         address _contractL2,
238:         uint256 _l2Value,
239:         bytes calldata _calldata,
240:         uint256 _l2GasLimit,
241:         uint256 _l2GasPerPubdataByteLimit,
242:         bytes[] calldata _factoryDeps,
243:         address _refundRecipient
244:     ) external payable nonReentrant senderCanCallFunction(s.allowList) returns (bytes32 canonicalTxHash) {
```

- *L2ERC20Bridge.sol* ( [#L63-L69](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L63-L69), [#L105-L109](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L105-L109) ):

```solidity
/// @audit `_l1Sender not checked`
/// @audit `_l2Receiver not checked`
63:     function finalizeDeposit(
64:         address _l1Sender,
65:         address _l2Receiver,
66:         address _l1Token,
67:         uint256 _amount,
68:         bytes calldata _data
69:     ) external payable override {

/// @audit `_l1Receiver not checked`
/// @audit `_l2Token not checked`
105:     function withdraw(
106:         address _l1Receiver,
107:         address _l2Token,
108:         uint256 _amount
109:     ) external override {
```

- *L2StandardERC20.sol* ( [#L109](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L109), [#L118](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L118) ):

```solidity
/// @audit `_to not checked`
109:     function bridgeMint(address _to, uint256 _amount) external override onlyBridge {

/// @audit `_from not checked`
118:     function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {
```

- *L2Weth.sol* ( [#L78](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L78), [#L98](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L98), [#L104](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L104) ):

```solidity
/// @audit `_from not checked`
78:     function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {

/// @audit `_to not checked`
98:     function depositTo(address _to) public payable override {

/// @audit `_to not checked`
104:     function withdrawTo(address _to, uint256 _amount) public override {
```

- *L2WethBridge.sol* ( [#L63-L67](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L63-L67), [#L88-L94](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2WethBridge.sol#L88-L94) ):

```solidity
/// @audit `_l1Receiver not checked`
63:     function withdraw(
64:         address _l1Receiver,
65:         address _l2Token,
66:         uint256 _amount
67:     ) external override {

/// @audit `_l1Sender not checked`
/// @audit `_l2Receiver not checked`
88:     function finalizeDeposit(
89:         address _l1Sender,
90:         address _l2Receiver,
91:         address _l1Token,
92:         uint256 _amount,
93:         bytes calldata // _data
94:     ) external payable override {
```

- *AccountCodeStorage.sol* ( [#L35](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/AccountCodeStorage.sol#L35), [#L46](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/AccountCodeStorage.sol#L46), [#L54](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/AccountCodeStorage.sol#L54) ):

```solidity
/// @audit `_address not checked`
35:     function storeAccountConstructingCodeHash(address _address, bytes32 _hash) external override onlyDeployer {

/// @audit `_address not checked`
46:     function storeAccountConstructedCodeHash(address _address, bytes32 _hash) external override onlyDeployer {

/// @audit `_address not checked`
54:     function markAccountCodeHashAsConstructed(address _address) external override onlyDeployer {
```

- *ComplexUpgrader.sol* ( [#L21](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ComplexUpgrader.sol#L21) ):

```solidity
/// @audit `_delegateTo not checked`
21:     function upgrade(address _delegateTo, bytes calldata _calldata) external payable {
```

- *ContractDeployer.sol* ( [#L214](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L214) ):

```solidity
/// @audit `_sender not checked`
214:     function forceDeployOnAddress(ForceDeployment calldata _deployment, address _sender) external payable onlySelf {
```

- *ImmutableSimulator.sol* ( [#L34](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ImmutableSimulator.sol#L34) ):

```solidity
/// @audit `_dest not checked`
34:     function setImmutables(address _dest, ImmutableData[] calldata _immutables) external override {
```

- *L2EthToken.sol* ( [#L32](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L2EthToken.sol#L32), [#L64](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L2EthToken.sol#L64), [#L72](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L2EthToken.sol#L72), [#L85](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L2EthToken.sol#L85) ):

```solidity
/// @audit `_from not checked`
/// @audit `_to not checked`
32:     function transferFromTo(address _from, address _to, uint256 _amount) external override {

/// @audit `_account not checked`
64:     function mint(address _account, uint256 _amount) external override onlyCallFromBootloader {

/// @audit `_l1Receiver not checked`
72:     function withdraw(address _l1Receiver) external payable override {

/// @audit `_l1Receiver not checked`
85:     function withdrawWithMessage(address _l1Receiver, bytes memory _additionalData) external payable override {
```

- *NonceHolder.sol* ( [#L135](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L135) ):

```solidity
/// @audit `_address not checked`
135:     function incrementDeploymentNonce(address _address) external onlySystemCall returns (uint256 prevDeploymentNonce) {
```

- *SystemContext.sol* ( [#L87](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L87) ):

```solidity
/// @audit `_newOrigin not checked`
87:     function setTxOrigin(address _newOrigin) external onlyCallFromBootloader {
```

### [N&#x2011;07] Using underscore at the end of variable name

The use of the underscore at the end of the variable name is unusual, consider refactoring it.

There are 3 instances:

- *L2StandardERC20.sol* ( [#L27](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L27) ):

```solidity
/// @audit decimals_
27:     uint8 private decimals_;
```

- *L2Weth.sol* ( [#L39](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L39), [#L39](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L39) ):

```solidity
/// @audit name_
39:     function initialize(string memory name_, string memory symbol_) external initializer {

/// @audit symbol_
39:     function initialize(string memory name_, string memory symbol_) external initializer {
```

### [N&#x2011;08] Whitespace in Expressions

See the [Whitespace in Expressions](https://docs.soliditylang.org/en/latest/style-guide.html#whitespace-in-expressions) section of the Solidity Style Guide.

There are 9 instances:

- *Executor.sol* ( [#L125](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L125), [#L126](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L126), [#L127](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L127) ):

```solidity
/// @audit Whitespace inside parenthesis
125:             (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET);

/// @audit Whitespace inside parenthesis
126:             (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);

/// @audit Whitespace inside parenthesis
127:             (bytes32 logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);
```

- *L2Weth.sol* ( [#L81](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L81), [#L106](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2Weth.sol#L106) ):

```solidity
/// @audit Whitespace inside parenthesis
81:         (bool success, ) = msg.sender.call{value: _amount}("");

/// @audit Whitespace inside parenthesis
106:         (bool success, ) = _to.call{value: _amount}("");
```

- *MsgValueSimulator.sol* ( [#L42](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/MsgValueSimulator.sol#L42) ):

```solidity
/// @audit Whitespace inside parenthesis
42:             (bool success, ) = address(ETH_TOKEN_SYSTEM_CONTRACT).call(
```

- *NonceHolder.sol* ( [#L127](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L127), [#L144](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L144) ):

```solidity
/// @audit Whitespace inside parenthesis
127:         (deploymentNonce, ) = _splitRawNonce(rawNonces[addressAsKey]);

/// @audit Whitespace inside parenthesis
144:         (prevDeploymentNonce, ) = _splitRawNonce(oldRawNonce);
```

- *Address.sol* ( [#L66](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L66) ):

```solidity
/// @audit Whitespace inside parenthesis
66:         (bool success, ) = recipient.call{value: amount}("");
```

### [N&#x2011;09] Use a struct to encapsulate multiple function parameters

If a function has too many parameters, replacing them with a struct can improve code readability and maintainability, increase reusability, and reduce the likelihood of errors when passing the parameters.

There are 6 instances:

- *L1ERC20Bridge.sol* ( [#L255-L263](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L255-L263) ):

```solidity
255:     function claimFailedDeposit(
256:         address _depositSender,
257:         address _l1Token,
258:         bytes32 _l2TxHash,
259:         uint256 _l2BatchNumber,
260:         uint256 _l2MessageIndex,
261:         uint16 _l2TxNumberInBatch,
262:         bytes32[] calldata _merkleProof
263:     ) external nonReentrant senderCanCallFunction(allowList) {
```

- *L1WethBridge.sol* ( [#L214-L222](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L214-L222) ):

```solidity
214:     function claimFailedDeposit(
215:         address, // _depositSender,
216:         address, // _l1Token,
217:         bytes32, // _l2TxHash
218:         uint256, // _l2BatchNumber,
219:         uint256, // _l2MessageIndex,
220:         uint16, // _l2TxNumberInBatch,
221:         bytes32[] calldata // _merkleProof
222:     ) external pure {
```

- *IL1Bridge.sol* ( [#L30-L38](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/interfaces/IL1Bridge.sol#L30-L38) ):

```solidity
30:     function claimFailedDeposit(
31:         address _depositSender,
32:         address _l1Token,
33:         bytes32 _l2TxHash,
34:         uint256 _l2BatchNumber,
35:         uint256 _l2MessageIndex,
36:         uint16 _l2TxNumberInBatch,
37:         bytes32[] calldata _merkleProof
38:     ) external;
```

- *Mailbox.sol* ( [#L236-L244](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236-L244), [#L283-L293](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L283-L293) ):

```solidity
236:     function requestL2Transaction(
237:         address _contractL2,
238:         uint256 _l2Value,
239:         bytes calldata _calldata,
240:         uint256 _l2GasLimit,
241:         uint256 _l2GasPerPubdataByteLimit,
242:         bytes[] calldata _factoryDeps,
243:         address _refundRecipient
244:     ) external payable nonReentrant senderCanCallFunction(s.allowList) returns (bytes32 canonicalTxHash) {

283:     function _requestL2Transaction(
284:         address _sender,
285:         address _contractAddressL2,
286:         uint256 _l2Value,
287:         bytes calldata _calldata,
288:         uint256 _l2GasLimit,
289:         uint256 _l2GasPerPubdataByteLimit,
290:         bytes[] calldata _factoryDeps,
291:         bool _isFree,
292:         address _refundRecipient
293:     ) internal returns (bytes32 canonicalTxHash) {
```

- *IMailbox.sol* ( [#L120-L128](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L120-L128) ):

```solidity
120:     function requestL2Transaction(
121:         address _contractL2,
122:         uint256 _l2Value,
123:         bytes calldata _calldata,
124:         uint256 _l2GasLimit,
125:         uint256 _l2GasPerPubdataByteLimit,
126:         bytes[] calldata _factoryDeps,
127:         address _refundRecipient
128:     ) external payable returns (bytes32 canonicalTxHash);
```

### [N&#x2011;10] Returning a struct instead of a bunch of variables is better

If a function returns [too many variables](https://docs.soliditylang.org/en/v0.8.21/contracts.html#returning-multiple-values), replacing them with a struct can improve code readability, maintainability and reusability.

There is 1 instance:

- *Executor.sol* ( [#L101-L111](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L101-L111) ):

```solidity
101:     function _processL2Logs(CommitBatchInfo calldata _newBatch, bytes32 _expectedSystemContractUpgradeTxHash)
102:         internal
103:         pure
104:         returns (
105:             uint256 numberOfLayer1Txs,
106:             bytes32 chainedPriorityTxsHash,
107:             bytes32 previousBatchHash,
108:             bytes32 stateDiffHash,
109:             bytes32 l2LogsTreeRoot,
110:             uint256 packedBatchAndL2BlockTimestamp
111:         )
```

### [N&#x2011;11] Contract variables should have comments

Consider adding some comments on non-public contract variables to explain what they are supposed to do. This will help for future code reviews.

There are 5 instances:

- *Base.sol* ( [#L15](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L15) ):

```solidity
15:     AppStorage internal s;
```

- *L2StandardERC20.sol* ( [#L22](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L22) ):

```solidity
22:     ERC20Getters availableGetters;
```

- *AccountCodeStorage.sol* ( [#L23](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/AccountCodeStorage.sol#L23) ):

```solidity
23:     bytes32 constant EMPTY_STRING_KECCAK = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470;
```

- *NonceHolder.sol* ( [#L28](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/NonceHolder.sol#L28) ):

```solidity
28:     uint256 constant DEPLOY_NONCE_MULTIPLIER = 2 ** 128;
```

- *TransactionHelper.sol* ( [#L84-L87](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#L84-L87) ):

```solidity
84:     bytes32 constant EIP712_TRANSACTION_TYPE_HASH =
85:         keccak256(
86:             "Transaction(uint256 txType,uint256 from,uint256 to,uint256 gasLimit,uint256 gasPerPubdataByteLimit,uint256 maxFeePerGas,uint256 maxPriorityFeePerGas,uint256 paymaster,uint256 nonce,uint256 value,bytes data,bytes32[] factoryDeps,bytes paymasterInput)"
87:         );
```

### [N&#x2011;12] Empty bytes check is missing

Passing empty bytes to a function can cause unexpected behavior, such as certain operations failing, producing incorrect results, or wasting gas. It is recommended to check that all byte parameters are not empty.

There are 16 instances:

- *L1ERC20Bridge.sol* ( [#L293-L299](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L293-L299) ):

```solidity
/// @audit _message
293:     function finalizeWithdrawal(
294:         uint256 _l2BatchNumber,
295:         uint256 _l2MessageIndex,
296:         uint16 _l2TxNumberInBatch,
297:         bytes calldata _message,
298:         bytes32[] calldata _merkleProof
299:     ) external nonReentrant senderCanCallFunction(allowList) {
```

- *L1WethBridge.sol* ( [#L233-L239](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L233-L239) ):

```solidity
/// @audit _message
233:     function finalizeWithdrawal(
234:         uint256 _l2BatchNumber,
235:         uint256 _l2MessageIndex,
236:         uint16 _l2TxNumberInBatch,
237:         bytes calldata _message,
238:         bytes32[] calldata _merkleProof
239:     ) external nonReentrant senderCanCallFunction(allowList) {
```

- *Mailbox.sol* ( [#L192-L198](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L192-L198), [#L236-L244](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236-L244) ):

```solidity
/// @audit _message
192:     function finalizeEthWithdrawal(
193:         uint256 _l2BatchNumber,
194:         uint256 _l2MessageIndex,
195:         uint16 _l2TxNumberInBatch,
196:         bytes calldata _message,
197:         bytes32[] calldata _merkleProof
198:     ) external override nonReentrant senderCanCallFunction(s.allowList) {

/// @audit _calldata
236:     function requestL2Transaction(
237:         address _contractL2,
238:         uint256 _l2Value,
239:         bytes calldata _calldata,
240:         uint256 _l2GasLimit,
241:         uint256 _l2GasPerPubdataByteLimit,
242:         bytes[] calldata _factoryDeps,
243:         address _refundRecipient
244:     ) external payable nonReentrant senderCanCallFunction(s.allowList) returns (bytes32 canonicalTxHash) {
```

- *L2ERC20Bridge.sol* ( [#L63-L69](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L63-L69) ):

```solidity
/// @audit _data
63:     function finalizeDeposit(
64:         address _l1Sender,
65:         address _l2Receiver,
66:         address _l1Token,
67:         uint256 _amount,
68:         bytes calldata _data
69:     ) external payable override {
```

- *L2StandardERC20.sol* ( [#L46](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L46) ):

```solidity
/// @audit _data
46:     function bridgeInitialize(address _l1Address, bytes memory _data) external initializer {
```

- *ComplexUpgrader.sol* ( [#L21](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ComplexUpgrader.sol#L21) ):

```solidity
/// @audit _calldata
21:     function upgrade(address _delegateTo, bytes calldata _calldata) external payable {
```

- *Compressor.sol* ( [#L54-L57](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Compressor.sol#L54-L57), [#L117-L122](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Compressor.sol#L117-L122) ):

```solidity
/// @audit _rawCompressedData
54:     function publishCompressedBytecode(
55:         bytes calldata _bytecode,
56:         bytes calldata _rawCompressedData
57:     ) external payable onlyCallFromBootloader returns (bytes32 bytecodeHash) {

/// @audit _stateDiffs
117:     function verifyCompressedStateDiffs(
118:         uint256 _numberOfStateDiffs,
119:         uint256 _enumerationIndexSize,
120:         bytes calldata _stateDiffs,
121:         bytes calldata _compressedStateDiffs
122:     ) external payable onlyCallFrom(address(L1_MESSENGER_CONTRACT)) returns (bytes32 stateDiffHash) {
```

- *ContractDeployer.sol* ( [#L130-L134](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L130-L134), [#L146-L150](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L146-L150), [#L162-L167](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L162-L167), [#L183-L188](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L183-L188) ):

```solidity
/// @audit _input
130:     function create2(
131:         bytes32 _salt,
132:         bytes32 _bytecodeHash,
133:         bytes calldata _input
134:     ) external payable override returns (address) {

/// @audit _input
146:     function create(
147:         bytes32 _salt,
148:         bytes32 _bytecodeHash,
149:         bytes calldata _input
150:     ) external payable override returns (address) {

/// @audit _input
162:     function create2Account(
163:         bytes32 _salt,
164:         bytes32 _bytecodeHash,
165:         bytes calldata _input,
166:         AccountAbstractionVersion _aaVersion
167:     ) public payable override onlySystemCall returns (address) {

/// @audit _input
183:     function createAccount(
184:         bytes32, // salt
185:         bytes32 _bytecodeHash,
186:         bytes calldata _input,
187:         AccountAbstractionVersion _aaVersion
188:     ) public payable override onlySystemCall returns (address) {
```

- *L1Messenger.sol* ( [#L119](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L119) ):

```solidity
/// @audit _message
119:     function sendToL1(bytes calldata _message) external override returns (bytes32 hash) {
```

- *L2EthToken.sol* ( [#L85](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L2EthToken.sol#L85) ):

```solidity
/// @audit _additionalData
85:     function withdrawWithMessage(address _l1Receiver, bytes memory _additionalData) external payable override {
```

- *MsgValueSimulator.sol* ( [#L35](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/MsgValueSimulator.sol#L35) ):

```solidity
/// @audit _data
35:     fallback(bytes calldata _data) external onlySystemCall returns (bytes memory) {
```

### [N&#x2011;13] Control structures do not follow the Solidity Style Guide

Refer to the [Solidity style guide - Control Structures](https://docs.soliditylang.org/en/v0.8.20/style-guide.html#control-structures).

There are 18 instances:

- *BaseZkSyncUpgrade.sol* ( [#L128](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L128) ):

```solidity
128:         if (
```

- *DefaultUpgrade.sol* ( [#L15](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L15), [#L21](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L21) ):

```solidity
15:     function _upgradeL1Contract(bytes calldata _customCallDataForUpgrade) internal virtual {}

21:     function _postUpgrade(bytes calldata _customCallDataForUpgrade) internal virtual {}
```

- *Executor.sol* ( [#L101-L112](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L101-L112), [#L177-L182](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L177-L182), [#L422-L426](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L422-L426), [#L448-L452](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L448-L452) ):

```solidity
101:     function _processL2Logs(CommitBatchInfo calldata _newBatch, bytes32 _expectedSystemContractUpgradeTxHash)
102:         internal
103:         pure
104:         returns (
105:             uint256 numberOfLayer1Txs,
106:             bytes32 chainedPriorityTxsHash,
107:             bytes32 previousBatchHash,
108:             bytes32 stateDiffHash,
109:             bytes32 l2LogsTreeRoot,
110:             uint256 packedBatchAndL2BlockTimestamp
111:         )
112:     {

177:     function commitBatches(StoredBatchInfo memory _lastCommittedBatchData, CommitBatchInfo[] calldata _newBatchesData)
178:         external
179:         override
180:         nonReentrant
181:         onlyValidator
182:     {

422:     function _createBatchCommitment(CommitBatchInfo calldata _newBatchData, bytes32 _stateDiffHash)
423:         internal
424:         view
425:         returns (bytes32)
426:     {

448:     function _batchAuxiliaryOutput(CommitBatchInfo calldata _batch, bytes32 _stateDiffHash)
449:         internal
450:         pure
451:         returns (bytes memory)
452:     {
```

- *Mailbox.sol* ( [#L388-L392](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L388-L392), [#L406-L410](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L406-L410) ):

```solidity
388:     function _hashFactoryDeps(bytes[] calldata _factoryDeps)
389:         internal
390:         pure
391:         returns (uint256[] memory hashedFactoryDeps)
392:     {

406:     function _parseL2WithdrawalMessage(bytes memory _message)
407:         internal
408:         pure
409:         returns (address l1Receiver, uint256 amount)
410:     {
```

- *IZkSync.sol* ( [#L10](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/interfaces/IZkSync.sol#L10) ):

```solidity
10: interface IZkSync is IMailbox, IAdmin, IExecutor, IGetters {}
```

- *L2StandardERC20.sol* ( [#L125](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L125), [#L131](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L131), [#L137](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L137) ):

```solidity
125:         if (availableGetters.ignoreName) revert();

131:         if (availableGetters.ignoreSymbol) revert();

137:         if (availableGetters.ignoreDecimals) revert();
```

- *AccountCodeStorage.sol* ( [#L131](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/AccountCodeStorage.sol#L131) ):

```solidity
131:         if (
```

- *Address.sol* ( [#L91-L94](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L91-L94), [#L177-L181](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L177-L181), [#L217-L220](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L217-L220), [#L292-L295](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L292-L295) ):

```solidity
91:     function functionCall(address target, bytes memory data)
92:         internal
93:         returns (bytes memory)
94:     {

177:     function functionStaticCall(address target, bytes memory data)
178:         internal
179:         view
180:         returns (bytes memory)
181:     {

217:     function functionDelegateCall(address target, bytes memory data)
218:         internal
219:         returns (bytes memory)
220:     {

292:     function _revert(bytes memory returndata, string memory errorMessage)
293:         private
294:         pure
295:     {
```

### [N&#x2011;14] Non-assembly method available

There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it's best to avoid using it where it's not necessary. In addition, most assembly methods can be replaced by non-assembly methods, for example:
- `assembly{ g := gas() }` => `uint256 g = gasleft()`
- `assembly{ id := chainid() }` => `uint256 id = block.chainid`
- `assembly { r := mulmod(a, b, d) }` => `uint256 m = mulmod(x, y, k)`
- `assembly { size := extcodesize() }` => `uint256 size = address(a).code.length`
- etc.

There are 16 instances:

- *Governance.sol* ( [#L230](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L230) ):

```solidity
230:                     revert(add(returnData, 0x20), mload(returnData))
```

- *Mailbox.sol* ( [#L117](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L117) ):

```solidity
117:             callSuccess := call(gas(), _to, _amount, 0, 0, 0, 0)
```

- *Diamond.sol* ( [#L296](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L296) ):

```solidity
296:                     revert(add(data, 0x20), mload(data))
```

- *MsgValueSimulator.sol* ( [#L49](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/MsgValueSimulator.sol#L49) ):

```solidity
49:                     revert(0, 0)
```

- *EfficientCall.sol* ( [#L136](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L136), [#L149](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L149), [#L164](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L164), [#L178](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L178) ):

```solidity
136:                 success := call(_address, callAddr, 0, 0, 0xFFFF, 0, 0)

149:                 success := call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)

164:             success := staticcall(_address, callAddr, 0, 0xFFFF, 0, 0)

178:             success := delegatecall(_address, callAddr, 0, 0xFFFF, 0, 0)
```

- *SystemContractHelper.sol* ( [#L93](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L93), [#L226](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L226), [#L315](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L315), [#L326](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L326), [#L339](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L339) ):

```solidity
93:             addr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

226:             meta := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

315:             callFlags := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

326:             ptr := staticcall(0, callAddr, 0, 0xFFFF, 0, 0)

339:             extraAbiData := staticcall(index, callAddr, 0, 0xFFFF, 0, 0)
```

- *SystemContractsCaller.sol* ( [#L101](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L101), [#L110](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L110) ):

```solidity
101:                 success := call(to, callAddr, 0, 0, farCallAbi, 0, 0)

110:                 success := call(msgValueSimulator, callAddr, value, to, farCallAbi, forwardMask, 0)
```

- *TransactionHelper.sol* ( [#L400](https://github.com/code-423n4/2023-10-zksync/tree/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/TransactionHelper.sol#L400) ):

```solidity
400:             success := call(gas(), bootloaderAddr, amount, 0, 0, 0, 0)
```
