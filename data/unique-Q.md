|     |     |     |
| --- | --- | --- |
| ID  | Description | Severity |
| \[L-01\] | Empty receive()/payable fallback() function does not authenticate requests | Low |
| \[L‑02\] | abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256() | Low |
| \[N-03\] | Floating pragma | Non-Critical |
| \[N-04\] | States/flags should use `Enum`s rather than separate constants | Non-Critical |
| \[N-05\] | Function must not be longer than 50 lines | Non-Critical |
| \[N‑06\] | ADD PARAMETER TO EVENT-EMIT | Non-Critical |
| \[N-07\] | Use underscores for number literals | Non-Critical |
| \[N-08\] | Functions should use mixedCase. | Non-Critical |
| \[N-09\] | Different pragma directives are used | Non-Critical |
| \[N-10\] | Assembly Codes Specific – Should Have Comments | Non-Critical |
| \[N-11\] | preventing zero amount in mint() and burn() | Non-Critical |
| \[N-12\] | Use a single file for all system-wide constants | Non-Critical |
| \[N-13\] | Use SMTChecker | Non-Critical |
| \[N-14\] | NatSpec comments should be increased in contracts | Non-Critical |
| \[N-15\] | NatSpec comments should be increased in contracts | Non-Critical |
| \[N-16\] | Function writing that does not comply with the Solidity Style Guide | Non-Critical |
| \[N-17\] | Contracts should have full test coverage | Non-Critical |
| \[S-01\] | You can explain the operation of critical functions in NatSpec with an infographic. | Suggestion |

## \[L‑01\] Empty receive()/payable fallback() function does not authenticate requests

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds. If the concern is having to spend a small amount of gas to check the sender against an immutable address, the code should at least have a function to rescue unused Ether.

```
262: receive() external payable {}
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L262

```
12:    fallback() external payable {}

14:    receive() external payable {}
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/EmptyContract.sol#L12C1-L14C34

## \[L‑02\] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).

If all arguments are strings and or bytes, bytes.concat() should be used instead.

```
131:  bytes32 hashedLog = keccak256(
            abi.encodePacked(_log.l2ShardId, _log.isService, _log.txNumberInBatch, _log.sender, _log.key, _log.value)
        );
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L131C8-L133C11

```
File:	ethereum/contracts/zksync/facets/Executor.sol

383:    keccak256(
                    abi.encodePacked(
                        _prevBatchCommitment,
                        _currentBatchCommitment,
                        _verifierParams.recursionNodeLevelVkHash,
                        _verifierParams.recursionLeafLevelVkHash
                    )
                )
```

## \[N-01\] Floating pragma

Description
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
https://swcregistry.io/docs/SWC-103

```
3: pragma solidity ^0.8.13;

3: pragma solidity ^0.8.0;

4: pragma solidity ^0.8.1;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L4

**Recommendation**
Lock the pragma version and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

## \[N-02\] States/flags should use `Enum`s rather than separate constants

```
38:  uint256 private constant _NOT_ENTERED = 1;
39:  uint256 private constant _ENTERED = 2;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L38C3-L39C43

## \[N-03\] Function must not be longer than 50 lines

```
File:	ethereum/contracts/zksync/facets/Executor.sol

101:  function _processL2Logs(CommitBatchInfo calldata _newBatch, bytes32 _expectedSystemContractUpgradeTxHash)
```

## \[N-04\] ADD PARAMETER TO EVENT-EMIT

Some event-emit description doesn’t have parameter. Add parameter for front-end website or client app, they can has that something has happened on the blockchain.

```
event Freeze();

    /// @notice Emitted when the contract is unfrozen.
    event Unfreeze();
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L57C5-L60C22

## \[N-05\] Use underscores for number literals

```
-  uint256 public difficulty = 2500000000000000;
+  uint256 public difficulty = 2500000000000000; // 2_500_000_000_000_000
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L44

```
- uint256 constant MAX_ALLOWED_PUBDATA_PER_BATCH = 520000;
+ uint256 constant MAX_ALLOWED_PUBDATA_PER_BATCH = 520_000;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Constants.sol#L87

```
- uint256 constant L2_TO_L1_LOGS_MERKLE_TREE_LEAVES = 2048;
+ uint256 constant L2_TO_L1_LOGS_MERKLE_TREE_LEAVES = 2_048;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Constants.sol#L102

## \[N-6\] Functions should use mixedCase.

[reference](https://docs.soliditylang.org/en/latest/style-guide.html#function-names).

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul

## \[N-07\] Different pragma directives are used

[reference.](https://github.com/crytic/slither/wiki/Detector-Documentation#different-pragma-directives-are-used)

```
3: pragma solidity ^0.8.13;

3: pragma solidity ^0.8.0;

4: pragma solidity ^0.8.1;

2: pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L4

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/Utils.sol#L2

**Recommendation**
Versions must be consistent with each other.

## \[N-08\] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does.

This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Assembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

```
assembly {
            diamondStorage.slot := position
        }
```

```
assembly {
            mstore(0x00, _lhs)
            mstore(0x20, _rhs)
            result := keccak256(0x00, 0x40)
        }
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L41

```
function readUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256 result, uint256 offset) {
        assembly {
            offset := add(_start, 32)
            result := mload(add(_bytes, offset))
        }
    }

    function readBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32 result, uint256 offset) {
        assembly {
            offset := add(_start, 32)
            result := mload(add(_bytes, offset))
        }
    }
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UnsafeBytes.sol#L33C5-L45C6

```
function readUint16(bytes calldata _bytes, uint256 _start) internal pure returns (uint16 result) {
        assembly {
            let offset := sub(_bytes.offset, 30)
            result := calldataload(add(offset, _start))
        }
    }

    function readUint32(bytes calldata _bytes, uint256 _start) internal pure returns (uint32 result) {
        assembly {
            let offset := sub(_bytes.offset, 28)
            result := calldataload(add(offset, _start))
        }
    }

    function readUint64(bytes calldata _bytes, uint256 _start) internal pure returns (uint64 result) {
        assembly {
            let offset := sub(_bytes.offset, 24)
            result := calldataload(add(offset, _start))
        }
    }

    function readBytes32(bytes calldata _bytes, uint256 _start) internal pure returns (bytes32 result) {
        assembly {
            result := calldataload(add(_bytes.offset, _start))
        }
    }

    function readUint256(bytes calldata _bytes, uint256 _start) internal pure returns (uint256 result) {
        assembly {
            result := calldataload(add(_bytes.offset, _start))
        }
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/libraries/UnsafeBytesCalldata.sol#L19C4-L49C10

## \[N‑09\]  preventing zero amount in mint() and burn()

```
function bridgeMint(address _to, uint256 _amount) external override onlyBridge {
        _mint(_to, _amount);
        emit BridgeMint(_to, _amount);
    }

    /// @dev Burn tokens from a given account.
    /// @param _from The account from which tokens will be burned.
    /// @param _amount The amount that will be burned.
    /// @notice Should be called by bridge before withdrawing tokens to L1.
    function bridgeBurn(address _from, uint256 _amount) external override onlyBridge {
        _burn(_from, _amount);
        emit BridgeBurn(_from, _amount);
    }
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L109C4-L121C6

```
79:     _burn(_from, _amount);
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L79

## \[N-10\] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values).

This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

## \[N-11\] Use SMTChecker

The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

## \[N-12\] NatSpec comments should be increased in contracts

> all contest

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity Official documentation

in complext project such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

## \[N-13\] NatSpec comments should be increased in contracts

> all contest

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity Official documentation

in complext project such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

## \[N-14\] Function writing that does not comply with the Solidity Style Guide

Context
All Contracts

Description
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

## \[N‑15\] Contracts should have full test coverage

While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

## \[S-01\] You can explain the operation of critical functions in NatSpec with an infographic.