# [Q-1] Eth sent to precompile'd contracts are stuck forever
Yul doesn't have the payable modifier. It should check if the callvalue() is zero to ensure that no value is sent to a contract address which can't transfer any token out.
this includes
* ECmul
* ECadd
* eventwriter
* keccack256
* sha256

**mitigation** :- revert on receiving a fallback call-data with msg.value
 ```solidity
   if iszero(iszero(callvalue())) {
    revert(0, 0)
    }
```