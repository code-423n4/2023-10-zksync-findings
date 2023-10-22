# Issue 1 - Redundant Code

Location: ethereum/contracts/zksync/facets/Executor.sol
In the proveBatches function, one step is verifying the zkp from line 354 to line 368. In the comment it says “If the proof is not empty, verify it, otherwise, skip the verification”. However, the implementation seems not following the comment because no matter whether the proof is not empty, the proof is always get verified because regardless of the length of the zkp, the proof is always get verified (line 363-368)

# Issue 2

Location: ethereum/contracts/zksync/libraries/PriorityQueue.sol
The `expirationTimestamp` item of the struct `PriorityOperation` is not used and no function is written to check the `expirationTimestamp`. 

# Issue 3

Location:system-contracts/contracts/libraries/TransactionHelper.sol
Unused `signature` and `reservedDynamic` items in `Transaction` struct

# Issue 4

Location: system-contracts/contracts/ComplexUpgrader.sol
The contract uses a delegate call to execute another contract's code, but it lacks robust error handling. If the delegate call fails, the contract reverts, but it does not provide clear error messages or context, which can make it challenging to diagnose issues during upgrades. If the delegate call to _delegateTo encounters an error, the contract reverts, but the error message may not provide sufficient information to understand the cause of the failure.

It's better to improve error handling by providing informative error messages and context in case the delegate call fails. This will help in diagnosing issues during upgrades and facilitate debugging. Here's an example of how to enhance error handling:

```
function upgrade(address _delegateTo, bytes calldata _calldata) external payable onlyOwner {
    require(_delegateTo.code.length > 0, "Delegatee is an EOA");
    (bool success, bytes memory returnData) = _delegateTo.delegatecall(_calldata);
    require(success, string(returnData));
}
```

# Issue 5

Location: zksync/contracts/SystemContractsCaller.sol
The code does not validate the input parameters such as gasLimit, to, and data in the `systemCall` and hence in the `systemCallWithReturnData` function. Lack of input validation can lead to unexpected behavior or vulnerabilities. An attacker could provide malicious inputs, leading to unintended behavior or exploitation.

How to Resolve: Implement input validation checks to ensure that inputs meet expected criteria before using them. For example. 
```
require(gasLimit > 0, "Invalid gas limit");
require(to != address(0), "Invalid target address");
require(data.length > 0, "Invalid data");
// Add more specific checks as needed.
```
#Issue 6
Unchecked Return Value

Location: zksync/contracts/SystemContractsCaller.sol
Both The systemCall and systemCallWithReturndata functions do not check the return value `success` of the internal call operation. This can lead to unexpected behavior and vulnerabilities if the called contract reverts or behaves maliciously.
Even though in the current scope it won’t be a problem because the only place using systemCallWithReturndata function is the _deployBeaconProxy function where it does check the return value `success`, but it’s hard to identify the root cause and it has potential risks if systemCall and systemCallWithReturndata are used in other places in the future. 

Add a check to ensure that the success variable is true after the call operation. If it's false, handle the error accordingly.
```
require(success, "System call failed");
```




