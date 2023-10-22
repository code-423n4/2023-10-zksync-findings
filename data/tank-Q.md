according to this line of code: https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L307-L313

this function: `receive() external payable` in `L1WethBridge.sol` is implementation of `TransparentUpgradeableProxy`.

In `istanbul` hardfork, there is a bump on `SLOAD` gas cost from `200` to `800`:https://chainsecurity.com/istanbul-hardfork-eips-increasing-gas-costs-and-more/

that's lead to a bug when the `L1WethBridge.sol` is `witdraw` ETH from `WETH`, the transaction will revert with `out of gas` because `WETH` uses `.trasnfer()` to receiver which limit the `receive()` gas used to 2,300 gas.

so I tried running test to withdraw ETH from WETH to `proxy` contract with `receive()` in implementation
, the transaction will always revert.

However, currently this bug is not happening because `SLOAD` 2nd time gas cost is only `100 gas`

recommendation:
move `receive() external payable` to Proxy contract.