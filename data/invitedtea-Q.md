## [L-01]  Omission of Signature Verification Cost in `getMinimalPriorityTransactionGasLimit` Function Calculations
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L69

## Impact

 The `getMinimalPriorityTransactionGasLimit` function does not take into account the cost of verifying the transaction's signature.  If signature verification is a significant part of the transaction processing and consumes a notable amount of gas, then it should be taken into account in this function.

## Proof of Concept

The getMinimalPriorityTransactionGasLimit function in the TransactionValidator.sol contract is responsible for calculating the minimal gas limit for priority transactions. This gas limit should ideally encompass all operations and computational costs associated with processing the transaction, ensuring that users pay an appropriate fee for transaction execution.

Here's a snippet of the relevant function:

```solidity
function getMinimalPriorityTransactionGasLimit(
    uint256 _encodingLength,
    uint256 _numberOfFactoryDependencies,
    uint256 _l2GasPricePerPubdata
) internal pure returns (uint256) {
    uint256 costForComputation;
    {
        // Adding the intrinsic cost for the transaction, i.e. auxiliary prices which cannot be easily accounted for
        costForComputation = L1_TX_INTRINSIC_L2_GAS;

        // Taking into account the hashing costs that depend on the length of the transaction
        costForComputation += Math.ceilDiv(_encodingLength * L1_TX_DELTA_544_ENCODING_BYTES, 544);

        // Taking into the account the additional costs of providing new factory dependenies
        costForComputation += _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_L2_GAS;

        // There is a minimal amount of computational L2 gas that the transaction should cover
        costForComputation = Math.max(costForComputation, L1_TX_MIN_L2_GAS_BASE);
    }
    ...
}


```