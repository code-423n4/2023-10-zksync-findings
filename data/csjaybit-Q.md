[L-01] Numeric of pointer r-2's flags doesn't follow conventions 

## Proof of Concept
as written in docs of system contracts https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md#flags-for-calls r2 — the pointer with flags of the call,
0-th bit is used for ```isconstructor``` flag and 1-st bit is used for ```isSystem``` flag, per conventions system related objects should be written first, i.e. 0-th place
Other flags should be written in after that construction in solidity is only used once(at time of deployment) so it makes ```isConstructor``` better suited for 1-st bit of r2- flag for better convention 
## Tools Used
manual review
## Recommended Mitigation Steps
in r-2 pointer
0-th bit should be used for ```isSystem``` flag
1-st bit should be used for ```isConstructor``` flag