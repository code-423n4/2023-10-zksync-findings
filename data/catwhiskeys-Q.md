# 1) Reentrancy guard wasn't added.
Instance:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L55-L71

In the comments it was indicated that the function is vulnerable to reentrancy attack, but the reentrancy guard wasn't implemented.
```
* IMPORTANT: because control is transferred to `recipient`, care must be
* taken to not create reentrancy vulnerabilities. Consider using
* {ReentrancyGuard} or the
* https://solidity.readthedocs.io/en/v0.5.11/security-considerations.html#use-the-checks-effects-interactions-pattern[checks-effects-interactions pattern].
*/
function sendValue(address payable recipient, uint256 amount) internal {
require(
    address(this).balance >= amount,
    "Address: insufficient balance"
);
```
##### Recommended mitigation steps:
Add reentrancy guard.
```
+ import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

+ function sendValue(address payable recipient, uint256 amount) internal nonReentrant {
    require(
        address(this).balance >= amount,
        "Address: insufficient balance"
    );
```

# 2) Not all security checks were implemented in the sensitive functions.
2 instances:
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L84-L102
https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/openzeppelin/utils/Address.sol#L122-L141

In the comments to functions `functionCall` and `functionCallWithValue` it is said:
```
* Requirements:
*
* - `target` must be a contract.
* - calling `target` with `data` must not revert.
...
```
and
```
* Requirements:
*
* - the calling contract must have an ETH balance of at least `value`.
* - the called Solidity function must be `payable`.
...
```    
But the security checks weren't implemented.
     
##### Recommended mitigation steps:
Consider adding mentioned security checks.
```
function functionCallWithValue(
    address target,
    bytes memory data,
    uint256 value
) internal returns (bytes memory) {
+   require(target.balance >= value, "Insufficent ETH balance");
+   require(target == target.payable, "Function is not payable");
    return
        functionCallWithValue(
            target,
            data,
            value,
            "Address: low-level call with value failed"
        );
}
```