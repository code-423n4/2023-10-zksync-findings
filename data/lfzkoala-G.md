## Issue 1

Location: ethereum/contracts/zksync/libraries/Merkle.sol
Shift right or left instead of dividing or multiply by 2
Shifting one to the right will calculate a division by two.

the SHR opcode only requires 3 gas, compared to the DIV opcode’s consumption of 5. Additionally, shifting is used to get around Solidity’s division operation’s division-by-0 prohibition.

Proof of concept (without optimizations):

pragma solidity 0.8.16;
contract TesterA {
function testDiv(uint a) public returns (uint) { return a / 2; }
}
contract TesterB {
function testShift(uint a) public returns (uint) { return a >> 1; }
}
Gas saving executing: 172 per entry

TesterA.testDiv:    21965 
TesterB.testShift:  21793   
The same optimization can be used to multiply by 2, using the left shift.

pragma solidity 0.8.16;
contract TesterA {
function testMul(uint a) public returns (uint) { return a * 2; }
}
contract TesterB {
function testShift(uint a) public returns (uint) { return a << 1; }
}
Gas saving executing: 201 per entry

TesterA.testMul:    21994
TesterB.testShift:  21793    

so in the function `calculateRoot` we can optimize the gas in the loop from line 29 to line 34 by changing `_index /= 2` to `_index >> 1`. 

## Issue 2
Location: ethereum/contracts/zksync/libraries/LibMap.sol

The bitwise operations within the `set` function can potentially be optimized for gas usage. The XOR operation used to replace the old value might be simplified.

Proof of Concept of Attack with code example:
Current implementation may consume slightly more gas.

Location of the security issue with code snippet:
In the set function during the bitwise operations.

```
_map.map[mapIndex] = (newValueXorOldValue << bitOffset) ^ mapValue;
```

How to Resolve the Security Issue with code example:
Consider simplifying or optimizing the bitwise operations to ensure minimal gas usage.

```
uint256 clearMask = uint32(-1) << bitOffset;
_map.map[mapIndex] = (_map.map[mapIndex] & ~clearMask) | (uint256(_value) << bitOffset);
```

The revised solution is potentially more gas-efficient because it streamlines the computation by reducing the complexity of the bitwise operations. Here’s a comparison of the two approaches:

Original Approach:
Retrieve Old Value: Calculate and retrieve the old value.
XOR Operation: XOR the old value with the new value.
Shift Operation: Shift the newValueXorOldValue to its proper position.
Final XOR Operation: XOR the above result with the entire mapValue.
Revised Approach:
Clear Mask Creation: Directly create a mask to clear the bits where the new value will be placed.
Clear Old Value: Use the mask to clear the old value’s bits from the map.
Position New Value: Position the new value correctly by shifting bits to the left.
Place New Value: OR operation to place the new value in the map.
The revised approach simplifies the operation by avoiding the retrieval of the old value and XOR operations, and directly placing the new value where it should be in the map. This direct approach can save gas because it avoids some computation and temporary variables involved in the original method.

## Issue 3
Location: system-contracts/contracts/SystemContext.sol

The system context sets the blockGasLimit to max integer value but the maximum block gas limit of Ethereum is 15M gas and up until a maximum of 30M gas (https://ethereum.org/en/developers/docs/gas/#:~:text=Each%20block%20has%20a%20target,2x%20the%20target%20block%20size). The blockGasLimit is set too large which will not result in a very high gas fee when the ETH network is busy. 

## Issue 4
Location: system-contracts/contracts/L1Messenger.sol

The code sets the keccack round gas cost to 40
```
uint256 internal constant KECCAK_ROUND_GAS_COST = 40; 
```
but according to the most recent yellow paper of ethereum (Appendix G, https://ethereum.github.io/yellowpaper/paper.pdf) the gas cost for each word (rounded up) for input data to Keccak is 30+6 gas. In particular, Appendix G mentions the gas cost of sha3 is: 30 gas + 6 gas for each word (rounded up) for input data to a SHA3 Keccak-256 operation. It seems this is a mismatch. 

## Issue 5
Location: system-contracts/contracts/precompiles/Ecrecover.yul

The function ECRECOVER_GAS_COST sets the gas cost of processing ecrecover circuit precompile as 1112. However, according to some recent research, for example, https://eprint.iacr.org/2023/939.pdf (table 1) and appendix E in https://ethereum.github.io/yellowpaper/paper.pdf , the EVM cost of ecrecover should be 3000. Actually I noticed that currently the EVM only supports secp256k1 in a limited way (as mentioned in EIP-1829) even though some optimizations are still in progress. Furthermore, there is no comment explaining why the code sets 1112 as the gas cost for ecrecover circuit precompile, so I’d like to raise this potential issue because it may raise mis-calculation of the gas cost and impact the EVM performance. 

Similarly, in the file SHA256.yul it sets the gas cost of processing one sha256 round to 7 but according to Appendix E it seems the gas usage is dependent on the input data size and is calculated as 60 + 12 * rounded_up(I_d/32) so it shouldn’t be a fixed number. I do lack information how is the number calculated. 

## Issue 6
Location: zksync/contracts/bridge/L2ERC20Bridge.sol

It’s possible to avoid storage access a save gas using immutable keyword for the following variables:

It’s also better to remove the initial values, because they will be set during the constructor.

Affected source code:

L2ERC20Bridge.sol:22
L2ERC20Bridge.sol:26
L2ERC20Bridge.sol:29








