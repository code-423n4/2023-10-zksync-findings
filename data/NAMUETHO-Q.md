## Issues found


### Unspecific Compiler Version Pragma

#### Impact
Avoid using floating pragmas, especially for non-library contracts. Floating pragmas are suitable for libraries, enabling compatibility with various application versions. However, for application implementations, it poses a security risk.

Selecting a known vulnerable compiler version or relying on security tools that default to an older compiler version may lead to unintentional vulnerabilities in the deployed smart contract on the blockchain.

It is advisable to specify a concrete compiler version to ensure predictability and security.

Example:

🤦 Less Secure:
pragma solidity ^0.8.0;

🚀 Secure (Pinned Version):

pragma solidity 0.8.4;

In the less secure example, the pragma allows the use of any compiler version from 0.8.0 and above, potentially introducing vulnerabilities. The more secure approach pins the contract to a specific and tested compiler version, reducing the risk of unintended vulnerabilities.


#### Files tha this issue was found and the specific line:
```
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\interfaces\IL1Bridge.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\interfaces\IL1BridgeLegacy.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\interfaces\IL2Bridge.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\interfaces\IL2ERC20Bridge.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\interfaces\IL2WethBridge.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\interfaces\IWETH9.sol::2 => pragma solidity ^0.8.0;
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\L1ERC20Bridge.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\L1WethBridge.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\interfaces\IL1Bridge.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\interfaces\IL1BridgeLegacy.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\interfaces\IL2Bridge.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\interfaces\IL2ERC20Bridge.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\interfaces\IL2WethBridge.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\bridge\interfaces\IWETH9.sol::2 => pragma solidity ^0.8.0;
code\contracts\ethereum\contracts\bridge\libraries\BridgeInitializationHelper.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\governance\Governance.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\governance\IGovernance.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\upgrades\BaseZkSyncUpgrade.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\upgrades\DefaultUpgrade.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\common\AllowList.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\common\AllowListed.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\common\Dependencies.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\common\L2ContractAddresses.sol::3 => pragma solidity ^0.8.0;
code\contracts\ethereum\contracts\common\ReentrancyGuard.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\common\interfaces\IAllowList.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\common\interfaces\IL2ContractDeployer.sol::3 => pragma solidity ^0.8.0;
code\contracts\ethereum\contracts\common\libraries\L2ContractHelper.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\common\libraries\UncheckedMath.sol::3 => pragma solidity ^0.8.13;
code\contracts\ethereum\contracts\common\libraries\UnsafeBytes.sol::3 => pragma solidity ^0.8.13;
```
