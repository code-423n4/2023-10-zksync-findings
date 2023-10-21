## Title
Inconsistent Gas Calculation in contracts

## Impact
The code uses gas calculations inconsistently, which could lead to unexpected behavior. In particular, there's a discrepancy in the way gas is calculated for different operations, such as when sending logs to L1 and when burning gas. Inconsistent gas calculations may result in incorrect cost estimations and could potentially lead to resource misuse or financial vulnerabilities. The calculated gas overhead therefore shows inconsistency.

## Proof of Concept
There is an inconsistency in how the gas required for sending messages to Layer 1 (L1) is calculated within the codebase. When computing the `gasToPay` for sending a message to L1, the formula involves multiplying the message's length plus some overhead by `gasPerPubdataBytes`.

The overhead, which accounts for extra data added to the message, is equal to 64 bytes. This overhead consists of 32 bytes for the encoded offset and an additional 32 bytes for the encoded length of the message.

However, in a different part of the code, when sending bytecode to L1 in `KnownCodeStorage.sol`, the same formula is used to calculate `gasToPay`. The inconsistency arises in the overhead used for this calculation, which is set at 100 instead of 64.

This inconsistency in the calculation of `gasToPay` when sending data to L1 raises a concern about uniformity and may require a review to ensure consistency and accuracy in gas estimation.

Sending Logs to L1: In the _sendBytecodeToL1 function, gas is calculated based on the number of bytes to publish on L1:

```
uint256 gasToPay = (_l1PreimageBytesLen + BYTECODE_PUBLISHING_OVERHEAD) * pricePerPubdataByteInGas;
```
Burning Gas: In the _burnGas function, gas is calculated based on a fixed value (_gasToPay):
```
function _burnGas(uint32 _gasToPay) internal view {
    bool precompileCallSuccess = SystemContractHelper.precompileCall(
        0,
        _gasToPay
    );
    require(precompileCallSuccess, "Failed to charge gas");
}

```
https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L36-L39

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/KnownCodesStorage.sol#L97

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/Constants.sol#L67

https://github.com/code-423n4/2023-03-zksync/blob/21d9a364a4a75adfa6f1e038232d8c0f39858a64/contracts/L1Messenger.sol#L29-L33

## Tools Used
Manual/ VsCode

## Recommended Mitigation Steps
To ensure consistent and reliable gas calculations, it's recommended to use a unified approach for gas calculations throughout the codebase. You should define gas costs in a single location and use these definitions consistently in all relevant functions. This helps in maintaining transparency and predictability of gas usage.

Additionally, it's crucial to ensure that gas calculations are accurate and properly reflect the cost of operations to prevent unexpected behavior or vulnerabilities.