## Summary

|                        | Issues                                                   | Instances |
| ---------------------- | -------------------------------------------------------- | --------- |
| [[N-0](#nonCritical1)] | Incorrect ordering of natspac comments                   | 1         |
| [[N-1](#nonCritical2)] | Function not called externally should be marked internal | 2         |

## Non Critical

---

### [N-0] Incorrect ordering of natspac comments <a id="nonCritical1"></a>

_Instance 1:_

spelling of `format` is incorrect:

```Javascript
File: 2023-10-zksync/code/system-contracts/contracts/ImmutableSimulator.sol

19    /// @dev mapping (contract address) => (index of immutable variable) => value
20    /// @notice that address uses `uint256` type to leave the option to introduce 32-byte address space in future.
```

GitHub: [19-20](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ImmutableSimulator.sol#L19-L20)

### [N-1] Function not called externally should be marked <a id="nonCritical2"></a>

Function should be marked internal as there is no logical use of it externally

_Instances [Total 2]_

```Javascript
File: 2023-10-zksync/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol

142    function decodeString(bytes memory _input) external pure returns (string memory result) {
143        (result) = abi.decode(_input, (string));
144    }
145
146    /// @dev External function to decode a uint8 from bytes.
147    function decodeUint8(bytes memory _input) external pure returns (uint8 result) {
148        (result) = abi.decode(_input, (uint8));
149    }
```

Github: [142-149](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/bridge/L2StandardERC20.sol#L142-L149)
