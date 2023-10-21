## Gas Optimizations

| Number        | Issue                                                                                    | Instances |
| ------------- | :--------------------------------------------------------------------------------------- | :-------: |
| [G-01](#g-01) | Using private rather than public for constants, saves gas                                |     5     |
| [G-02](#g-02) | Use assembly for math (add, sub, mul, div)                                               |    23     |
| [G-03](#g-03) | Using calldata instead of memory for read-only arguments in external functions saves gas |     3     |
| [G-04](#g-04) | Use != 0 instead of > 0 for unsigned integer comparison                                  |     4     |
| [G-05](#g-05) | Avoid contract existence checks by using low level calls                                 |     1     |
| [G-06](#g-06) | Use Assembly To Check For address(0)                                                     |    12     |
| [G-07](#g-07) | x += y and x -= y are more expensive than x = x + y and x = x-y for state variables      |     1     |
| [G-08](#g-08) | Cache state variables with stack variables                                               |     4     |
| [G-09](#g-09) | Using this to access functions results in an external call, wasting gas                  |     1     |
| [G-10](#g-10) | Use do while loops instead of for loops                                                  |     1     |
| [G-11](#g-11) | Use constants instead of type(uintx).max                                                 |     3     |
| [G-12](#g-12) | Structs can be packed into fewer storage slots by truncating timestamp bytes             |     1     |

<a name='g-01'></a>

## [G-01] Using private rather than public for constants, saves gas

When you declare a constant variable as public, Solidity generates a getter function that allows anyone to read the value of the constant. This getter function can consume gas, especially if the constant is read frequently or the contract is called by multiple users.

By using private instead of public for constants, you can prevent the generation of the getter function and reduce the overall gas cost of your contract.

Total Instances: `5`

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22

```solidity
File:code/contracts/ethereum/contracts/zksync/facets/Executor.sol
22:string public constant override getName = "ExecutorFacet";

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41

```solidity
File:code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
41:     string public constant override getName = "MailboxFacet";

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19C5-L19C62

```solidity
File:code/contracts/ethereum/contracts/zksync/facets/Getters.sol
19:    string public constant override getName = "GettersFacet";

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L24

```solidity
File:code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol
24: string public constant override getName = "ValidatorTimelock";

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15

```solidity
File:code/contracts/ethereum/contracts/zksync/facets/Admin.sol
15: string public constant override getName = "AdminFacet";

```

<a name='g-02'></a>

## [G-02] Use assembly for math (add, sub, mul, div)

Use assembly for math instead of Solidity. You can check for overflow/underflow in assembly to ensure safety. If using Solidity versions < 0.8.0 and you are using Safemath, you can gain significant gas savings by using assembly to calculate values and checking for overflow/underflow.

Addition:

```solidity
//addition in Solidity
function addTest(uint256 a, uint256 b) public pure {
    uint256 c = a + b;
}
```

Gas: 303

```solidity
//addition in assembly
function addAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := add(a, b)
        if lt(c, a) {
            mstore(0x00, "overflow")
            revert(0x00, 0x20)
        }
    }
}

```

Gas: 263

Subtraction

```solidity

//subtraction in Solidity
function subTest(uint256 a, uint256 b) public pure {
  uint256 c = a - b;
}
```

Gas: 300

```solidity
//subtraction in assembly
function subAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := sub(a, b)
        if gt(c, a) {
            mstore(0x00, "underflow")
            revert(0x00, 0x20)
        }
    }
}
```

Gas: 263

Multiplication

```solidity
//multiplication in Solidity
function mulTest(uint256 a, uint256 b) public pure {
    uint256 c = a * b;
}
```

Gas: 325

```solidity
//multiplication in assembly
function mulAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := mul(a, b)
        if lt(c, a) {
            mstore(0x00, "overflow")
            revert(0x00, 0x20)
        }
    }
}
```

Gas: 265

Division

```solidity
//division in Solidity
function divTest(uint256 a, uint256 b) public pure {
    uint256 c = a * b;
}
```

Gas: 325

```solidity
//division in assembly
function divAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := div(a, b)
        if gt(c, a) {
            mstore(0x00, "underflow")
            revert(0x00, 0x20)
        }
    }
}
```

Gas: 265

Total Instances: `23`

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L32
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L126
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L127
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L199
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L275
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L298

```solidity
File:code/contracts/ethereum/contracts/zksync/facets/Executor.sol
32:  require(_newBatch.batchNumber == _previousBatch.batchNumber + 1, "f"); // only commit next batch

126:            (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);

127:            (bytes32 logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);

199:s.totalBatchesCommitted = s.totalBatchesCommitted + _newBatchesData.length;

275: require(currentBatchNumber == s.totalBatchesExecuted + _executedBatchIdx + 1, "k"); // Execute batches in order

298: uint256 newTotalBatchesExecuted = s.totalBatchesExecuted + nBatches;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L180
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L181
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L295
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L305
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L306

```solidity
File:code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

180: uint256 pubdataPriceETH = L1_GAS_PER_PUBDATA_BYTE * _l1GasPrice;

181:        uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;

295: uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); /

305:            uint256 baseCost = params.l2GasPrice * _l2GasLimit;

306:            require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost


```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L238
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L267

```solidity
File:code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

238:  uint256 lastSelectorPosition = ds.facetToSelectors[_facet].selectors.length - 1;

267: uint256 lastFacetPosition = ds.facets.length - 1;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L86C13-L86C98
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L95C13-L95C78
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L98C13-L98C119
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L101C9-L101C52
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L120
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L138C9-L138C105
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L163C13-L163C74
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L165C13-L165C60

```solidity
File:code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol

86: costForComputation += _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_L2_GAS;

95: costForPubdata = L1_TX_INTRINSIC_PUBDATA * _l2GasPricePerPubdata;

98: costForPubdata += _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_PUBDATA * _l2GasPricePerPubdata;

101: return costForComputation + costForPubdata;

120: txBodyGasLimit = _totalGasLimit - overhead;

138: uint256 batchOverheadGas = BATCH_OVERHEAD_L2_GAS + BATCH_OVERHEAD_PUBDATA * _gasPricePerPubdata;

163: uint256 denominator = L2_TX_MAX_GAS_LIMIT + batchOverheadGas;

165: overheadForGas = (numerator - 1) / denominator;

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L60
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L80

```solidity
File:code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol

60: _queue.tail = tail + 1;

80:  _queue.head = head + 1;

```

<a name='g-03'></a>

## [G-03]Using calldata instead of memory for read-only arguments in external functions saves gas

_The following instances are missed in the automated report_

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 \* <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one

Total Instances: `3`

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L467

```solidity
File:code/contracts/ethereum/contracts/zksync/facets/Executor.sol

467: function _hashStoredBatchInfo(StoredBatchInfo memory _storedBatchInfo) internal pure returns (bytes32) {

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L52
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L67

```solidity
File:code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

52: L2Message memory _message,

67:         L2Log memory _log,


```

<a name='g-04'></a>

## [G-04] Use != 0 instead of > 0 for unsigned integer comparison

It's generally more gas-efficient to use != 0 instead of > 0 when comparing unsigned integer types.

This is because the Solidity compiler can optimize the != 0 comparison to a simple bitwise operation, while the > 0 comparison requires an additional subtraction operation. As a result, using != 0 can be more gas-efficient and can help to reduce the overall cost of your contract.

Here's an example of how you can use != 0 instead of > 0:

```solidity
contract MyContract {
    uint256 public myUnsignedInteger;

    function myFunction() public view returns (bool) {
        // Use != 0 instead of > 0
        return myUnsignedInteger != 0;
    }
}
```

Total Instances: `4`

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L185
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354

```solidity
File:code/contracts/ethereum/contracts/zksync/facets/Executor.sol
185:  require(_newBatchesData.length > 0, "No batches to commit");

354:if (_proof.serializedProof.length > 0) {

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L312

```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
312:    if (refundRecipient.code.length > 0) {

```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L106

```solidity
File: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
106:  require(selectors.length > 0, "B"); // no functions for diamond cut

```

<a name='g-05'></a>

## [G-05] Avoid contract existence checks by using low level calls

_The following instances are missed in the automated report_

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence.

Total Instances: `1`

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L276

```solidity
File:code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
276:   IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH

```

<a name='g-06'></a>

## [G-06] Use Assembly To Check For address(0)

_The following instances are missed in the automated report_

It's generally more gas-efficient to use assembly to check for a zero address (address(0)) than to use the == operator.

The reason for this is that the == operator generates additional instructions in the EVM bytecode, which can increase the gas cost of your contract. By using assembly, you can perform the zero address check more efficiently and reduce the overall gas cost of your contract.

Here's an example of how you can use assembly to check for a zero address:

```solidity
contract MyContract {
    function isZeroAddress(address addr) public pure returns (bool) {
        uint256 addrInt = uint256(addr);

        assembly {
            // Load the zero address into memory
            let zero := mload(0x00)

            // Compare the address to the zero address
            let isZero := eq(addrInt, zero)

            // Return the result
            mstore(0x00, isZero)
            return(0, 0x20)
        }
    }
}
```

In the above example, we have a function isZeroAddress that takes an address as input and returns a boolean value indicating whether the address is equal to the zero address. Inside the function, we convert the address to an integer using uint256(addr), and then use assembly to compare the integer to the zero address.

By using assembly to perform the zero address check, we can make our code more gas-efficient and reduce the overall cost of our contract. It's important to note that assembly can be more difficult to read and maintain than Solidity code, so it should be used with caution and only when necessary

Total Instances: `12`

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L310

```solidity
File:code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
310:  address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient;
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L132
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L156
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L162
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L176
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L182
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L284

```solidity
File:code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol
132: require(_facet != address(0), "G"); // facet with zero address cannot be added

156: require(_facet != address(0), "K"); // cannot replace facet with zero address

162:require(oldFacet.facetAddress != address(0), "L"); // it is impossible to replace the facet with zero address

176:require(_facet == address(0), "a1");

182: require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet

284:if (_init == address(0)) {
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L160

```solidity
File:code/contracts/ethereum/contracts/zksync/facets/Getters.sol
160:  require(ds.selectorToFacet[_selector].facetAddress != address(0), "g2");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L56
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L57
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L58

```solidity
File:code/contracts/ethereum/contracts/zksync/DiamondInit.sol
56:        require(address(_initalizeData.verifier) != address(0), "vt");
57:        require(_initalizeData.governor != address(0), "vy");
58:        require(_initalizeData.admin != address(0), "hc");
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L30

```solidity
File:code/contracts/ethereum/contracts/zksync/DiamondProxy.sol
30: require(facetAddress != address(0), "F");
```

<a name='g-07'></a>

## [G-07] x += y and x -= y are more expensive than x = x + y and x = x-y for state variables

_The following instances are missed in the automated report_

Using the addition/subtraction operator instead of plus-equals or minus-equals saves 113 gas.

Total Instances: `1`

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L280

```solidity
File:code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol
280: s.totalDepositedAmountPerUser[_depositor] += _amount;
```

<a name='g-08'></a>

## [G-08] Cache state variables with stack variables

_The following instances are missed in the automated report_

Caching of a state variable replaces each Gwarmaccess (100 gas) with a cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

Total Instances: `4`

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L219
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L242

```solidity
File: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

//@audit ds.facetToSelectors[_facet] at 213
219: bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];

//@audit ds.facetToSelectors[_facet] at 238
242:  bytes4 lastSelector = ds.facetToSelectors[_facet].selectors[lastSelectorPosition];
```

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L142
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L161

```solidity
File: code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol

//@audit ds.facetToSelectors[_facet] at 140
142: bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];

//@audit ds.facetToSelectors[selector] at 160
161: return ds.selectorToFacet[_selector].isFreezable;
```

<a name='g-09'></a>

## [G-09] Using this to access functions results in an external call, wasting gas

_The following instances are missed in the automated report_

External calls have an overhead of 100 gas, which can be avoided by not referencing the function using this. Contracts are allowed to override their parents' functions and change the visibility from external to public, so make this change if it's required in order to call the function internally

Total Instances: `1`

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L424C8-L424C89

```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol

424:  require(bytes4(functionSignature) == this.finalizeEthWithdrawal.selector, "is");
```

<a name='g-10'></a>

## [G-10] Use do while loops instead of for loops

A do while loop will cost less gas since the condition is not being checked for the first iteration.Here initialisation of variable i as zero is also redundant.

Total Instances: `1`

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L182C9-L187C10

```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Getters.sol
182: for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
183:            address facetAddr = ds.facets[i];
184:            Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];
185:
186:            result[i] = Facet(facetAddr, facetToSelectors.selectors);
187:        }
```

<a name='g-10'></a>

## [G-11] Use constants instead of type(uintx).max

It's generally more gas-efficient to use constants instead of type(uintX).max when you need to set the maximum value of an unsigned integer type.

The reason for this is that the type(uintX).max expression involves a computation at runtime, whereas a constant is evaluated at compile-time. This means that using type(uintX).max can result in additional gas costs for each transaction that involves the expression.

By using a constant instead of type(uintX).max, you can avoid these additional gas costs and make your code more efficient.

Here's an example of how you can use a constant instead of type(uintX).max:

```solidity
contract MyContract {
    uint120 constant MAX_VALUE = 2**120 - 1;

    function doSomething(uint120 value) public {
        require(value <= MAX_VALUE, "Value exceeds maximum");

        // Do something
    }
}
```

In the above example, we have a contract with a constant MAX_VALUE that represents the maximum value of a uint120. When the doSomething function is called with a value parameter, it checks whether the value is less than or equal to MAX_VALUE using the <= operator.

By using a constant instead of type(uint120).max, we can make our code more efficient and reduce the gas cost of our contract.

It's important to note that using constants can make your code more readable and maintainable, since the value is defined in one place and can be easily updated if necessary. However, constants should be used with caution and only when their value is known at compile-time.

Total Instances: `3`

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L50
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L51
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L55

```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Getters.sol
50: require(_transaction.from <= type(uint16).max, "ua");

51: require(_transaction.to <= type(uint160).max, "ub");

55: require(_transaction.reserved[1] <= type(uint160).max, "uf");
```

<a name='g-12'></a>

## [G-12] Structs can be packed into fewer storage slots by truncating timestamp bytes

_The following instances are missed in the automated report_

By using a uint32 rather than a larger type for variables that track timestamps, one can save gas by using fewer storage slots per struct, at the expense of the protocol breaking after the year 2106 (when uint32 wraps). If this is an acceptable tradeoff, each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L10C1-L14C2

```solidity
File: code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol
//@audit Variable ordering with 3 slots instead of the current 2

10:struct PriorityOperation {
11:    bytes32 canonicalTxHash;
12:    uint64 expirationTimestamp;
13:    uint192 layer2Tip;
14:}
```
