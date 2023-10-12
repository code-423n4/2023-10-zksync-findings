# L-01 - Immutable should be uppercase.
lines : https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L32, https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L35 , https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L44

# G-01 -  Use constants instead of `type(uint32).max`
At the places where type(uint256).max is used it can be replaced with constant variable with the same value as this from the expression, it will save gas because constant variables are stored in the contract byte code.

line : https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/SystemContractsCaller.sol#L28


# G-02 -  `If` statements that use & can be refactored into nested `if` statements. It will save gas!
line : https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L331,
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L303

# G-03 - Use predefined address instead of `address(this)`
Instead of using `address(this)`, it is more gas-efficient to pre-calculate and use the predefined address. Foundry's `script.sol` and Solmate's `LibRlp.sol` contracts can help pre-determine the address (see computeCreateAddress -https://book.getfoundry.sh/reference/forge-std/compute-create-address). The address can be passed in via a constructor argument and assigned to an immutable variable (rather than using a hardcoded constant) so that the code can remain the same across deployments on different networks.

lines : https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L171,
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L218
