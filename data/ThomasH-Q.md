
## Low-Risk Findings:

### [L-01] Missing Zero Address Check

## Location

[L2Weth.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol#L104)


## Impact

Detecting and validating against the zero address is a best practice in Solidity smart contract development.

The function `withdrawTo()` does not do a zero address validation and may potentially place ETH at risk of being permanently lost and burned if someone sets the `_to` address to the zero address `0x000000000000000000000000000000000000000` or it gets initialized to the zero address.

## Proof of Concept

```solidity
require(_recipient != address(0), "Invalid: Zero Address");
```

## Tools Used

VS Code, Slither, Remix

## Recommended Mitigation Steps

Please see the recommended revised function below:

## Here's the revised `withdrawTo` function:

```solidity
//Here's the revised withdrawTo function:

function withdrawTo(address _recipient, uint256 _value) public override {
    // Validate against zero address
    require(_recipient != address(0), "Invalid: Zero Address");

    _burn(msg.sender, _value);
    (bool successful, ) = _recipient.call{value: _value}("");
    require(successful, "Withdrawal Unsuccessful");
}

```
___

### [L-02] Function `set()`: Setter-functions should emit events

## Location

[LibMap.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L36)


## Impact

In Solidity contracts, emitting an event for any function that alters storage is recommended, commonly referred to as a "setter". This practice enhances auditability and transparency by logging these changes.

## Proof of Concept

I have incorporated an event emission within the function. Below is the updated function:

```solidity
Event Declaration:
event ValueSet(uint256 indexed index, uint32 value);

Updated Function:

function set(
        Uint32Map storage _map,
        uint256 _index,
        uint32 _value
    ) internal {
        ...
        _map.map[mapIndex] = (newValueXorOldValue << bitOffset) ^ mapValue;

        // Event emission after value set
        emit ValueSet(_index, _value);
    }
```

## Tools Used

VS Code, Slither, Remix

## Recommended Mitigation Steps

Please see the recommended revised function below:

## Here's the revised event declaration:

```solidity
Event Declaration:
event ValueSet(uint256 indexed index, uint32 value);

Updated Function:

function set(
        Uint32Map storage _map,
        uint256 _index,
        uint32 _value
    ) internal {
        ...
        _map.map[mapIndex] = (newValueXorOldValue << bitOffset) ^ mapValue;

        // Event emission after value set
        emit ValueSet(_index, _value);
    }
```
___

## Quality Assurance Findings:

### [N-01] Use named constants instead of long literals in the function.

## Location

[L2ContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L28)

## Impact

We recommend utilizing named constants as opposed to magic numbers. This enhances context clarity readability, and aids in preventing potential errors during future code maintenance.

## Proof of Concept

```solidity
// At the top of your contract or inside the relevant function
bytes32 constant MASK = 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

// Later in your function
hashedBytecode = sha256(_bytecode) & MASK;

```

## Tools Used

VS Code, Slither, Remix

## Recommended Mitigation Steps

The following potential named constants have been introduced and a function has been revised accordingly:

```solidity
// At the top of your contract or inside the relevant function
bytes32 constant MASK = 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

// Later in your function
hashedBytecode = sha256(_bytecode) & MASK;

```
This modification enhances the readability and comprehensibility of the code.

___

### [N-02] Use named constants instead of “magic numbers” (8,7) in the setter function `hashL2Bytecode()`.

## Location

[LibMap.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L36)

## Impact

We recommend utilizing named constants as opposed to magic numbers. This enhances context clarity, readability, and aids in preventing potential errors during future code maintenance.

## Proof of Concept

## Tools Used

VS Code, Slither, Remix

## Recommended Mitigation Steps

The following potential named constants have been introduced and a function has been revised accordingly:

```solidity
uint256 constant STORAGE_SLOT_SIZE = 8; // Each storage slot accommodates 8 uint32 values
uint256 constant BITS_PER_UINT32 = 32; // A uint32 is 32 bits in length
uint256 constant POSITION_MASK = 7; // Utilized to ascertain position within an 8-sized storage slot
```

These constants are implemented in the revised function as follows:

```solidity
uint256 mapIndex = _index / STORAGE_SLOT_SIZE;

uint256 bitOffset = (_index & (STORAGE_SLOT_SIZE - 1)) * BITS_PER_UINT32;

uint256 bitOffset = (_index & POSITION_MASK) * BITS_PER_UINT32;
```
This modification enhances the readability and comprehensibility of the code.

___

### [N-03] Function `computeCreate2Address()` not used

## Location

[L2ContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/L2ContractHelper.sol#L105)

## Impact

The function in question seems to be unused, thus constituting dead code.

## Proof of Concept

## Tools Used

VS Code, Slither, Remix

## Recommended Mitigation Steps

Please proceed with eliminating any functions within the smart contract that are currently not in use.

___

### [N-04] Function `systemCallWithReturndata()` not used

## Location

[SystemContractsCaller.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/SystemContractsCaller.sol#L81)

## Impact

The function in question seems to be unused, thus constituting dead code.

## Proof of Concept

## Tools Used

VS Code, Slither, Remix

## Recommended Mitigation Steps

Please proceed with eliminating any functions within the smart contract that are currently not in use.
___

### [N-05] Function `applyL1ToL2Alias()` not used

## Location

[AddressAliasHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/vendor/AddressAliasHelper.sol#L28)

## Impact

The function in question seems to be unused, thus constituting dead code.

## Proof of Concept

## Tools Used

VS Code, Slither, Remix

## Recommended Mitigation Steps

Please proceed with eliminating any functions within the smart contract that are currently not in use.

___

### [N-06] Function `undoL1ToL2Alias()` not used

## Location

[AddressAliasHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/vendor/AddressAliasHelper.sol#L38)

## Impact

The function in question seems to be unused, thus constituting dead code.

## Proof of Concept

## Tools Used

VS Code, Slither, Remix

## Recommended Mitigation Steps

Please proceed with eliminating any functions within the smart contract that are currently not in use.

___

