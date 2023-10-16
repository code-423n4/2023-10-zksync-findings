### [L-01] The `UncheckedMath` library performs mathematical operations without overflow or underflow checks
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/UncheckedMath.sol#L12

## Impact

The `UncheckedMath` library contains functions that perform arithmetic operations without checking for overflows or underflows. If used improperly in other parts of the codebase, it could lead to unexpected contract behavior, resulting in potential vulnerabilities like integer overflow or underflow attacks. Such vulnerabilities could compromise the integrity of calculations and state changes in the contract.

## Proof of Concept

- For the `UncheckedMath` library, refer to the provided functions that perform unchecked operations.

```solidity
library UncheckedMath {
    function uncheckedInc(uint256 _number) internal pure returns (uint256) {
        unchecked {
            return _number + 1;
        }
    }

    function uncheckedAdd(uint256 _lhs, uint256 _rhs) internal pure returns (uint256) {
        unchecked {
            return _lhs + _rhs;
        }
    }
}

```

## Tools Used

- Integrated Python environment for code and text analysis

## Recommended Mitigation Steps

For the UncheckedMath library:
Use the library judiciously. Ensure that wherever its functions are used in the broader codebase, there are adequate checks to prevent potential overflows or underflows.
If unchecked operations are not necessary, consider using the checked arithmetic operations provided by Solidity ^0.8 or libraries like OpenZeppelin's SafeMath for Solidity versions < 0.8.

```solidity
// Example of a safe addition function
function safeAdd(uint256 _lhs, uint256 _rhs) internal pure returns (uint256) {
    uint256 result = _lhs + _rhs;
    require(result >= _lhs, "Addition overflow");
    return result;
}


```