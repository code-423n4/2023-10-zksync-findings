##use >> instead of /

Contract:Mailbox.sol
Code line:181
(https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol)

using >> instead of the / reduces gas fee

## Check for revert strings errors
use custom errors instead of revert strings errors.
custom errors decrease both deploy and runtime gas costs.

 require(_admin != address(0), "Admin should be non zero address");

if(_admin !=address(0)){
revert Unauthorized();
}

code: governance.sol
line number : 42
(https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol)