### Report 1:
Wrong event function was used in setPendingAdmin(...) function in Admin.sol, it should be emit NewPendingAdmin(...) not emit NewPendingGovernor(...)
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L49
```solidity
  function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {
        // Save previous value into the stack to put it into the event later
        address oldPendingAdmin = s.pendingAdmin;
        // Change pending admin
        s.pendingAdmin = _newPendingAdmin;
---     emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);
+++     emit NewPendingAdmin(oldPendingAdmin, _newPendingAdmin);
    }
```
https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/interfaces/IAdmin.sol#L45
```solidity
    /// @notice pendingAdmin is changed
    /// @dev Also emitted when new admin is accepted and in this case, `newPendingAdmin` would be zero address
    event NewPendingAdmin(address indexed oldPendingAdmin, address indexed newPendingAdmin);
```
As noted in the code above this is the correct event meant for the emit purpose in setPendingAdmin(...) function as extracted from the IAdmin interface.

