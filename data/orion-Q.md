## Invalid check on DefaultAccount allows for many issues

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L94



## Description
The `DefaultAccount.validateTransaction` function validates the transaction by the given signature, Line 102


```
        if (_isValidSignature(txHash, _transaction.signature)) {

```

and checked with nonces on `NONCE_HOLDER_SYSTEM_CONTRACT` lines 83, 87, if the later checks fails it will revert since the nonce is not the same set on the NONCE_HOLDER_SYSTEM_CONTRACT for msg.sender (which is DefaultAccount), thus the transaction will fails except if the nonce is right, 

However the nonce that is checked against `NONCE_HOLDER_SYSTEM_CONTRACT` is forwarded directly via the `_transaction` tuple, which is not checked via `_validateSignature`, this can allow the caller to forward any arbitrary `_transaction.nonce` that is the same for `DefaultAccount` on  `NONCE_HOLDER_SYSTEM_CONTRACT`, since the nonce is not checked on the signature it will pass, the call will succeed 

**I didn't understand the exact logic of DefaultAccount.validateTransaction yet** to have a better insight of the issue impact, but anyways the attacker can manage to make a significant threat, example :

**scenario one**
* UserA (owner of DefaultAccount) calls `DefaultAccount.validateTransaction` with the right nonce and signature for specific call
* Attacker front runs it, since the transaction.nonce will be valid only once and not checked, so attacker make transaction tuple with different parameters and will be accepted
* UserA's call eill fails since transaction.nonce has been user

**scenario two**
Since DefaultAccount._validateSignature does not check for signature replay, attacker will re-call DefaultAccount.validateTransaction for the same signature many times

...etc and so more

## Impact
The signature verification on DefaultAccount have weak implementations which allows for many security issues

## Tools Used
Manual review

## Recommended Mitigation Steps
Make the nonce checked with the the user signature, and make metigitions against signature replay attacks