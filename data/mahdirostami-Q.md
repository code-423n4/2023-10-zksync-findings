# 1
AllowList and ValidatorTimelock contracts could renounceOwnership

Links to affected code
- https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L18
- https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L20
## Summary
Both contracts inherit from Ownable2Step contract, but do not remove the ability for the owner to renounceOwnership. So the owner could renounceOwnership.
The contract should be implemented in the best way to decrease this type of action.
## Recommendations
It is recommended that the owner should not be able to renounce ownership. The functionality can be disabled with the following code.
```solidtiy
function renounceOwnership() public override onlyOwner {
	revert("Cannot renounce ownership");
}
```
* * *
* * *
# 2
msg.sender != tx.origin for separating contract from EOA may not work

2 instances
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L196
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L183
## Summary
This approach may stop working if EIP-3074 and its AUTH and AUTHCALL opcodes get deployed.
* * *
* * *
# 3
wrong comments

– use '=' instead of "=="
https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L73-L75  
    https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L73-L74 
 
`==` means `?`, so these comments are wrong.

Instead of `_factoryDeps[0] == a ...` should be `_factoryDeps[0] = a ...`.

```diff
-            _factoryDeps[0] == a
+            _factoryDeps[0] = a

```
	
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L3256

```diff
-            /// @dev Returns the addition of two unsigned integers, reverting on overflow.
+            /// @dev Returns the subtraction of two unsigned integers, reverting on overflow.

```

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/precompiles/EcAdd.yul#L250

```diff
-            /// @return ret The result of the Montgomery addition.
+			      /// @return ret The result of the Montgomery subtraction.
```
* * *
* * *
# 3
wrong error
1 instances
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L240
# Summary
instead of `"Operation with this proposal id already exists"` must be `"Operation with this proposal id already exists or done"`
* * *
* * *
# 4
missing docs
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/interfaces/IMailbox.sol#L83
# Summary
there is no information for l2GasPrice
* * *
* * *


