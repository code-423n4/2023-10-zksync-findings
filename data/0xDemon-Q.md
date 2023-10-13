# Unnamed return variable can remain unassigned (`upgrade` function on `BaseZkSyncUpgrade.sol` contract)

The `uprage` function contained in the `BaseZkSyncUpgrade.sol` contract is a function that has a return value of `bytes32` data, but the variable name is not given. This can cause the return value to remain unassigned. 

*There is one instance for this issue :* 

```solidity
File : cache/solpp-generated-contracts/upgrades/BaseZkSyncUpgrade.sol

67   : function upgrade(ProposedUpgrade calldata _proposedUpgrade) public virtual returns (bytes32) {  
```

*Github : [67](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L67)* 

## Tools Used

VSCode, Manual review

## Recommended Mitigation Steps

Add an explicit return with value to all non-reverting code paths or name the variable
