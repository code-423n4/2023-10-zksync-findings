# [L] Base contract does not implement Ownable

[Instance](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Base.sol#L9)

In `Base.sol` there is an import statement of Openzeppelin Ownable contract which is not being used anywhere in the contract.

```js
File: Base.sol
9: import "@openzeppelin/contracts/access/Ownable.sol";
```

The Ownable contract from OpenZeppelin provides a standardized way to implement ownership control over a contract. It includes functions to transfer ownership and check if an address is the owner.

Since there exist a custom Admin facet that may serves the same purpose this Ownable seems just an unused import, thus we can assume this is an informational / low issue. 

But if, this Ownable contract supposed to be used in the `Base.sol`, then this issue should be upgraded to Medium because of `Base` is missing important aspect of inheritance of Ownable.

# [L] remove unnecessary `magic = bytes4(0)` else statement

[Instance](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/DefaultAccount.sol#L102-L106)

Looking at `DefaultAccount.sol`, `_validateTransaction` internal function, at the end of it there is a check of signature validity, which determine the return of `magic`.

```js
File: DefaultAccount.sol
102:         if (_isValidSignature(txHash, _transaction.signature)) {
103:             magic = ACCOUNT_VALIDATION_SUCCESS_MAGIC;
104:         } else {
105:             magic = bytes4(0);
106:         }
```

In Solidity, if a variable is not explicitly initialized, it will be assigned its default value. For `bytes4`, the default value is `0x00000000` (or simply `bytes4(0)`).

So, if you remove the line `magic = bytes4(0);`, and when the signature is not valid, `magic` will still have the default value of `bytes4(0)`. Therefore, in terms of functionality, removing that line will not change the behavior of the code.

thus, the `magic = bytes4(0);` can completely be removed

```js
File: DefaultAccount.sol
102:         if (_isValidSignature(txHash, _transaction.signature)) {
103:             magic = ACCOUNT_VALIDATION_SUCCESS_MAGIC;
104:         } 
```

# [L] Unnecessary casting

[Instance](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L212-L228)

There are two unnecessary casting of `uint128(_l2BlockNumber - 1)`

Since `_l2BlockNumber` which came from parameter is already of type `uint128`, there is no need to cast it again to `uint128`

```js
File: SystemContext.sol
212:     function _upgradeL2Blocks(uint128 _l2BlockNumber, bytes32 _expectedPrevL2BlockHash, bool _isFirstInBatch) internal {
213:         require(_isFirstInBatch, "Upgrade transaction must be first");
214:
215:         // This is how it will be commonly done in practice, but it will simplify some logic later
216:         require(_l2BlockNumber > 0, "L2 block number is never expected to be zero");
217:
218:         unchecked {
219:             bytes32 correctPrevBlockHash = _calculateLegacyL2BlockHash(uint128(_l2BlockNumber - 1));
220:             require(correctPrevBlockHash == _expectedPrevL2BlockHash, "The previous L2 block hash is incorrect");
221:
222:             // Whenever we'll be queried about the hashes of the blocks before the upgrade,
223:             // we'll use batches' hashes, so we don't need to store 256 previous hashes.
224:             // However, we do need to store the last previous hash in order to be able to correctly calculate the
225:             // hash of the new L2 block.
226:             _setL2BlockHash(uint128(_l2BlockNumber - 1), correctPrevBlockHash);
227:         }
228:     }
```

