## Gas Optimizations

| Number                                                                         | Issue                                                          | Instances |
| ------------------------------------------------------------------------------ | :------------------------------------------------------------- | :-------: |
| [[G-01](#g-01-structs-can-be-packed-into-fewer-storage-slots)]                 | `Structs` can be packed into fewer storage slots               |     2     |
| [[G-02](#g-02-remove-unused-mapping-and-import-variables)]                     | Remove unused `mapping` and `import variables`                 |     2     |
| [[G-03](#g-03-using-private-for-constants-saves-gas)]                          | Using `private` for constants saves gas                        |     4     |
| [[G-04](#g-04-using-bytes32-instead-of-string-saves-gas)]                      | Using bytes32 instead of string saves gas                      |     4     |
| [[G-05](#g-05-do-not-calculate-constants-to-save-gas)]                         | Do not calculate constants to save gas                         |    144    |
| [[G-06](#g-06-using-xor--and-and--bitwise-equivalents)]                        | Using XOR (^) and AND (&) bitwise equivalents                  |     2     |
| [[G-07](#g-07-no-need-to-explicitly-initialize-variables-with-default-values)] | No need to explicitly initialize variables with default values |    18     |
| [[G-08](#g-08-can-make-the-variable-outside-the-loop-to-save-gas)]             | Can make the variable outside the loop to save gas             |    22     |
| [[G-09](#g-09-instead-of-counting-down-in-for-statements-count-up)]            | Instead of counting down in `for` statements, count up         |     1     |
| [[G-10](#g-10-refactor-require-early-in-function-to-fail-early)]               | Refactor `require()` early in function to fail early           |     1     |
| [[G-11](#g-11-custom-errors-cost-less-than-requireassert)]                     | Custom `error`s cost less than `require`/`assert`              |     2     |
| [[G-12](#g-12-check-denominator-for-0-before-dividing-from-it)]                | Check denominator for 0 before dividing from it                |     1     |
| [[G-13](#g-13-dont-cache-value-if-it-is-only-used-once)]                       | `Don’t cache` value if it is only used once                    |     2     |
| [[G-14](#g-14-use-constant-instead-of-typeuintxmax)]                           | Use constant instead of `type(uintx).max`                      |     5     |

## [G-01] Structs can be packed into fewer storage slots.

The EVM works with 32 byte words. Variables less than 32 bytes can be declared next to each other in storage and this will pack the values together into a single 32 byte storage slot (if values combined are <= 32 bytes). If the variables packed together are retrieved together in functions (more likely with structs), we will effectively save ~2000 gas with every subsequent SLOAD for that storage slot. This is due to us incurring a Gwarmaccess (100 gas) versus a Gcoldsload (2100 gas).

**Note: These instances missed by bot report**

**_2 Instances - 2 Files_**

### Reduce uint type for `newProtocolVersion` to `uint48` and pack with `address newAllowList` to save 1 SLOT(~2000 Gas)

Protocol version is based on upgrading contracts so `uint48` is more than sufficient to hold protocol version number which can save 1 SLOT by packing with address `newAllowList`.
_Note : `upgradeTimestamp` is truncated in bot and saves 1 SLOT but here we can also truncate `newProtocolVersion` also to save 2 SLOT and pack both with `address newAllowList`_

```solidity
File : ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

31: struct ProposedUpgrade {
32:        IMailbox.L2CanonicalTransaction l2ProtocolUpgradeTx;
33:        bytes[] factoryDeps;
34:        bytes32 bootloaderHash;
35:        bytes32 defaultAccountHash;
36:        address verifier;
37:        VerifierParams verifierParams;
38:        bytes l1ContractsUpgradeCalldata;
39:        bytes postUpgradeCalldata;
40:        uint256 upgradeTimestamp;
41:        uint256 newProtocolVersion;
42:        address newAllowList;
43:    }

```

[31-43](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L31C5-L43C6)

```diff
File : ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

 struct ProposedUpgrade {
        IMailbox.L2CanonicalTransaction l2ProtocolUpgradeTx;
        bytes[] factoryDeps;
        bytes32 bootloaderHash;
        bytes32 defaultAccountHash;
        address verifier;

        VerifierParams verifierParams;
        bytes l1ContractsUpgradeCalldata;
        bytes postUpgradeCalldata;
        uint256 upgradeTimestamp;
-       uint256 newProtocolVersion;
+       uint48 newProtocolVersion;
        address newAllowList;
    }

```

### Reduce uint type for `protocolVersion` to `uint48` and pack with `address admin` to save 1 SLOT(~2000 Gas)

Protocol version is based on upgrading contracts so `uint48` is more than sufficient to hold protocol version number which can save 1 SLOT by packing with address `admin`.

```solidity
File : ethereum/contracts/zksync/Storage.sol

79: struct AppStorage {
     ...

     uint256 protocolVersion;
     /// @dev Hash of the system contract upgrade transaction. If 0, then no upgrade transaction needs to be done.
     bytes32 l2SystemContractsUpgradeTxHash;
     /// @dev Batch number where the upgrade transaction has happened. If 0, then no upgrade transaction has happened
     /// yet.
     uint256 l2SystemContractsUpgradeBatchNumber;
     /// @dev Address which will exercise non-critical changes to the Diamond Proxy (changing validator set &  unfreezing)
     address admin;
     /// @notice Address that the governor or admin proposed as one that will replace admin role
     address pendingAdmin;
146: }

```

[79-146](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Storage.sol#L79C1-L146C2)

```diff
File : ethereum/contracts/zksync/Storage.sol

 struct AppStorage {
     ...

-    uint256 protocolVersion;
     /// @dev Hash of the system contract upgrade transaction. If 0, then no upgrade transaction needs to be done.
     bytes32 l2SystemContractsUpgradeTxHash;
     /// @dev Batch number where the upgrade transaction has happened. If 0, then no upgrade transaction has happened
     /// yet.
     uint256 l2SystemContractsUpgradeBatchNumber;
     /// @dev Address which will exercise non-critical changes to the Diamond Proxy (changing validator set &  unfreezing)
     address admin;
+    uint48 protocolVersion;
     /// @notice Address that the governor or admin proposed as one that will replace admin role
     address pendingAdmin;
 }

```

## [G-02] Remove unused `mapping` and `import variables`

**_2 Instances - 1 File_**

```solidity

File : contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol

  mapping(address => uint256) public __DEPRECATED_lastWithdrawalLimitReset;

    /// @dev A mapping L1 token address => the accumulated withdrawn amount during the withdrawal limit window
    mapping(address => uint256) public __DEPRECATED_withdrawnAmountInWindow;

```

[54-57](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L54C4-L57C77)

```solidity

File : contracts/ethereum/contracts/zksync/facets/Executor.sol

//@audit Remove L2ContractHelper
import {L2ContractHelper} from "../../common/libraries/L2ContractHelper.sol";
import {VerifierParams} from "../Storage.sol";

//@audit Remove L2_KNOWN_CODE_STORAGE_SYSTEM_CONTRACT_ADDR
import {L2_BOOTLOADER_ADDRESS, L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR, L2_SYSTEM_CONTEXT_SYSTEM_CONTRACT_ADDR, L2_KNOWN_CODE_STORAGE_SYSTEM_CONTRACT_ADDR} from "../../common/L2ContractAddresses.sol";

```

[11-13](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L11C1-L13C201)

## [G-03] Using `private` for constants saves gas

Saves deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table.

**_4 Instances - 4 Files_**

```solidity
File : ethereum/contracts/zksync/facets/Admin.sol

15 : string public constant override getName = "AdminFacet";

```

[15](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15)

```diff
File : ethereum/contracts/zksync/facets/Admin.sol

- 15 : string public constant override getName = "AdminFacet";
+ 15 : string private constant override getName = "AdminFacet";

```

```solidity
File : ethereum/contracts/zksync/facets/Executor.sol

22:  string public constant override getName = "ExecutorFacet";

```

[22](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22)

```diff
File : ethereum/contracts/zksync/facets/Executor.sol

- 22:  string public constant override getName = "ExecutorFacet";
+ 22:  string private constant override getName = "ExecutorFacet";

```

```solidity
File : ethereum/contracts/zksync/facets/Getters.sol

19: string public constant override getName = "GettersFacet";

```

[19](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19)

```diff
File : ethereum/contracts/zksync/facets/Getters.sol

- 19: string public constant override getName = "GettersFacet";
+ 19: string private constant override getName = "GettersFacet";

```

```solidity
File : ethereum/contracts/zksync/facets/Mailbox.sol

41:  string public constant override getName = "MailboxFacet";

```

[41](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41)

```diff
File : ethereum/contracts/zksync/facets/Mailbox.sol

- 41:  string public constant override getName = "MailboxFacet";
+ 41:  string private constant override getName = "MailboxFacet";

```

## [G-04] Using bytes32 instead of string saves gas

If you can limit the length to a certain number of bytes, always use one of bytes1 to bytes32 because they are much cheaper.

**_4 Instances - 4 Files_**

```solidity
File : ethereum/contracts/zksync/facets/Admin.sol

15 : string public constant override getName = "AdminFacet";

```

[15](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L15)

```diff
File : ethereum/contracts/zksync/facets/Admin.sol

- 15 : string public constant override getName = "AdminFacet";
+ 15 : bytes32 public constant override getName = "AdminFacet";

```

```solidity
File : ethereum/contracts/zksync/facets/Executor.sol

22:  string public constant override getName = "ExecutorFacet";

```

[22](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L22)

```diff
File : ethereum/contracts/zksync/facets/Executor.sol

- 22:  string public constant override getName = "ExecutorFacet";
+ 22:  bytes32 public constant override getName = "ExecutorFacet";

```

```solidity
File : ethereum/contracts/zksync/facets/Getters.sol

19: string public constant override getName = "GettersFacet";

```

[19](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L19)

```diff
File : ethereum/contracts/zksync/facets/Getters.sol

- 19: string public constant override getName = "GettersFacet";
+ 19: bytes32 public constant override getName = "GettersFacet";

```

```solidity
File : ethereum/contracts/zksync/facets/Mailbox.sol

41:  string public constant override getName = "MailboxFacet";

```

[41](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L41)

```diff
File : ethereum/contracts/zksync/facets/Mailbox.sol

- 41:  string public constant override getName = "MailboxFacet";
+ 41:  bytes public constant override getName = "MailboxFacet";

```

## [G-05] Do not calculate constants to save gas

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

In Solidity, a constant is a value that is known at compile-time and cannot be changed during runtime. Constants can be defined using the constant keyword or the immutable keyword. The statement "Do not calculate constants to save gas" means that if a constant value can be calculated at compile-time, it is more gas-efficient to define the constant directly with the calculated value rather than calculating it at runtime. This is because calculating a constant at runtime requires additional gas to be consumed, whereas defining it directly with the calculated value does not.

**_144 Instances - 2 Files_**

```solidity
File : ethereum/contracts/zksync/Config.sol

14: uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512;

26: uint256 constant MAX_INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE * 4765;

33: uint256 constant MAX_REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE * 7564;

92: uint256 constant BATCH_OVERHEAD_PUBDATA = BATCH_OVERHEAD_L1_GAS / L1_GAS_PER_PUBDATA_BYTE;

```

[14](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L14), [26](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L26), [33](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L33), [92](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol#L92)

```solidity
File : ethereum/contracts/zksync/Verifier.sol

27:    uint256 internal constant VK_GATE_SETUP_0_X_SLOT = 0x200 + 0x000;
    uint256 internal constant VK_GATE_SETUP_0_Y_SLOT = 0x200 + 0x020;
    uint256 internal constant VK_GATE_SETUP_1_X_SLOT = 0x200 + 0x040;
    uint256 internal constant VK_GATE_SETUP_1_Y_SLOT = 0x200 + 0x060;
    uint256 internal constant VK_GATE_SETUP_2_X_SLOT = 0x200 + 0x080;
    uint256 internal constant VK_GATE_SETUP_2_Y_SLOT = 0x200 + 0x0a0;
    uint256 internal constant VK_GATE_SETUP_3_X_SLOT = 0x200 + 0x0c0;
    uint256 internal constant VK_GATE_SETUP_3_Y_SLOT = 0x200 + 0x0e0;
    uint256 internal constant VK_GATE_SETUP_4_X_SLOT = 0x200 + 0x100;
    uint256 internal constant VK_GATE_SETUP_4_Y_SLOT = 0x200 + 0x120;
    uint256 internal constant VK_GATE_SETUP_5_X_SLOT = 0x200 + 0x140;
    uint256 internal constant VK_GATE_SETUP_5_Y_SLOT = 0x200 + 0x160;
    uint256 internal constant VK_GATE_SETUP_6_X_SLOT = 0x200 + 0x180;
    uint256 internal constant VK_GATE_SETUP_6_Y_SLOT = 0x200 + 0x1a0;
    uint256 internal constant VK_GATE_SETUP_7_X_SLOT = 0x200 + 0x1c0;
    uint256 internal constant VK_GATE_SETUP_7_Y_SLOT = 0x200 + 0x1e0;

    uint256 internal constant VK_GATE_SELECTORS_0_X_SLOT = 0x200 + 0x200;
    uint256 internal constant VK_GATE_SELECTORS_0_Y_SLOT = 0x200 + 0x220;
    uint256 internal constant VK_GATE_SELECTORS_1_X_SLOT = 0x200 + 0x240;
    uint256 internal constant VK_GATE_SELECTORS_1_Y_SLOT = 0x200 + 0x260;

    uint256 internal constant VK_PERMUTATION_0_X_SLOT = 0x200 + 0x280;
    uint256 internal constant VK_PERMUTATION_0_Y_SLOT = 0x200 + 0x2a0;
    uint256 internal constant VK_PERMUTATION_1_X_SLOT = 0x200 + 0x2c0;
    uint256 internal constant VK_PERMUTATION_1_Y_SLOT = 0x200 + 0x2e0;
    uint256 internal constant VK_PERMUTATION_2_X_SLOT = 0x200 + 0x300;
    uint256 internal constant VK_PERMUTATION_2_Y_SLOT = 0x200 + 0x320;
    uint256 internal constant VK_PERMUTATION_3_X_SLOT = 0x200 + 0x340;
    uint256 internal constant VK_PERMUTATION_3_Y_SLOT = 0x200 + 0x360;

    uint256 internal constant VK_LOOKUP_SELECTOR_X_SLOT = 0x200 + 0x380;
    uint256 internal constant VK_LOOKUP_SELECTOR_Y_SLOT = 0x200 + 0x3a0;

    uint256 internal constant VK_LOOKUP_TABLE_0_X_SLOT = 0x200 + 0x3c0;
    uint256 internal constant VK_LOOKUP_TABLE_0_Y_SLOT = 0x200 + 0x3e0;
    uint256 internal constant VK_LOOKUP_TABLE_1_X_SLOT = 0x200 + 0x400;
    uint256 internal constant VK_LOOKUP_TABLE_1_Y_SLOT = 0x200 + 0x420;
    uint256 internal constant VK_LOOKUP_TABLE_2_X_SLOT = 0x200 + 0x440;
    uint256 internal constant VK_LOOKUP_TABLE_2_Y_SLOT = 0x200 + 0x460;
    uint256 internal constant VK_LOOKUP_TABLE_3_X_SLOT = 0x200 + 0x480;
    uint256 internal constant VK_LOOKUP_TABLE_3_Y_SLOT = 0x200 + 0x4a0;

    uint256 internal constant VK_LOOKUP_TABLE_TYPE_X_SLOT = 0x200 + 0x4c0;
    uint256 internal constant VK_LOOKUP_TABLE_TYPE_Y_SLOT = 0x200 + 0x4e0;

    uint256 internal constant VK_RECURSIVE_FLAG_SLOT = 0x200 + 0x500;

    /*//////////////////////////////////////////////////////////////
                             Proof
    //////////////////////////////////////////////////////////////*/

    uint256 internal constant PROOF_PUBLIC_INPUT = 0x200 + 0x520 + 0x000;

    uint256 internal constant PROOF_STATE_POLYS_0_X_SLOT = 0x200 + 0x520 + 0x020;
    uint256 internal constant PROOF_STATE_POLYS_0_Y_SLOT = 0x200 + 0x520 + 0x040;
    uint256 internal constant PROOF_STATE_POLYS_1_X_SLOT = 0x200 + 0x520 + 0x060;
    uint256 internal constant PROOF_STATE_POLYS_1_Y_SLOT = 0x200 + 0x520 + 0x080;
    uint256 internal constant PROOF_STATE_POLYS_2_X_SLOT = 0x200 + 0x520 + 0x0a0;
    uint256 internal constant PROOF_STATE_POLYS_2_Y_SLOT = 0x200 + 0x520 + 0x0c0;
    uint256 internal constant PROOF_STATE_POLYS_3_X_SLOT = 0x200 + 0x520 + 0x0e0;
    uint256 internal constant PROOF_STATE_POLYS_3_Y_SLOT = 0x200 + 0x520 + 0x100;

    uint256 internal constant PROOF_COPY_PERMUTATION_GRAND_PRODUCT_X_SLOT = 0x200 + 0x520 + 0x120;
    uint256 internal constant PROOF_COPY_PERMUTATION_GRAND_PRODUCT_Y_SLOT = 0x200 + 0x520 + 0x140;

    uint256 internal constant PROOF_LOOKUP_S_POLY_X_SLOT = 0x200 + 0x520 + 0x160;
    uint256 internal constant PROOF_LOOKUP_S_POLY_Y_SLOT = 0x200 + 0x520 + 0x180;

    uint256 internal constant PROOF_LOOKUP_GRAND_PRODUCT_X_SLOT = 0x200 + 0x520 + 0x1a0;
    uint256 internal constant PROOF_LOOKUP_GRAND_PRODUCT_Y_SLOT = 0x200 + 0x520 + 0x1c0;

    uint256 internal constant PROOF_QUOTIENT_POLY_PARTS_0_X_SLOT = 0x200 + 0x520 + 0x1e0;
    uint256 internal constant PROOF_QUOTIENT_POLY_PARTS_0_Y_SLOT = 0x200 + 0x520 + 0x200;
    uint256 internal constant PROOF_QUOTIENT_POLY_PARTS_1_X_SLOT = 0x200 + 0x520 + 0x220;
    uint256 internal constant PROOF_QUOTIENT_POLY_PARTS_1_Y_SLOT = 0x200 + 0x520 + 0x240;
    uint256 internal constant PROOF_QUOTIENT_POLY_PARTS_2_X_SLOT = 0x200 + 0x520 + 0x260;
    uint256 internal constant PROOF_QUOTIENT_POLY_PARTS_2_Y_SLOT = 0x200 + 0x520 + 0x280;
    uint256 internal constant PROOF_QUOTIENT_POLY_PARTS_3_X_SLOT = 0x200 + 0x520 + 0x2a0;
    uint256 internal constant PROOF_QUOTIENT_POLY_PARTS_3_Y_SLOT = 0x200 + 0x520 + 0x2c0;

    uint256 internal constant PROOF_STATE_POLYS_0_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x2e0;
    uint256 internal constant PROOF_STATE_POLYS_1_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x300;
    uint256 internal constant PROOF_STATE_POLYS_2_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x320;
    uint256 internal constant PROOF_STATE_POLYS_3_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x340;

    uint256 internal constant PROOF_STATE_POLYS_3_OPENING_AT_Z_OMEGA_SLOT = 0x200 + 0x520 + 0x360;
    uint256 internal constant PROOF_GATE_SELECTORS_0_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x380;

    uint256 internal constant PROOF_COPY_PERMUTATION_POLYS_0_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x3a0;
    uint256 internal constant PROOF_COPY_PERMUTATION_POLYS_1_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x3c0;
    uint256 internal constant PROOF_COPY_PERMUTATION_POLYS_2_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x3e0;

    uint256 internal constant PROOF_COPY_PERMUTATION_GRAND_PRODUCT_OPENING_AT_Z_OMEGA_SLOT = 0x200 + 0x520 + 0x400;
    uint256 internal constant PROOF_LOOKUP_S_POLY_OPENING_AT_Z_OMEGA_SLOT = 0x200 + 0x520 + 0x420;
    uint256 internal constant PROOF_LOOKUP_GRAND_PRODUCT_OPENING_AT_Z_OMEGA_SLOT = 0x200 + 0x520 + 0x440;
    uint256 internal constant PROOF_LOOKUP_T_POLY_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x460;
    uint256 internal constant PROOF_LOOKUP_T_POLY_OPENING_AT_Z_OMEGA_SLOT = 0x200 + 0x520 + 0x480;
    uint256 internal constant PROOF_LOOKUP_SELECTOR_POLY_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x4a0;
    uint256 internal constant PROOF_LOOKUP_TABLE_TYPE_POLY_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x4c0;
    uint256 internal constant PROOF_QUOTIENT_POLY_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x4e0;
    uint256 internal constant PROOF_LINEARISATION_POLY_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x500;

    uint256 internal constant PROOF_OPENING_PROOF_AT_Z_X_SLOT = 0x200 + 0x520 + 0x520;
    uint256 internal constant PROOF_OPENING_PROOF_AT_Z_Y_SLOT = 0x200 + 0x520 + 0x540;
    uint256 internal constant PROOF_OPENING_PROOF_AT_Z_OMEGA_X_SLOT = 0x200 + 0x520 + 0x560;
    uint256 internal constant PROOF_OPENING_PROOF_AT_Z_OMEGA_Y_SLOT = 0x200 + 0x520 + 0x580;

    uint256 internal constant PROOF_RECURSIVE_PART_P1_X_SLOT = 0x200 + 0x520 + 0x5a0;
    uint256 internal constant PROOF_RECURSIVE_PART_P1_Y_SLOT = 0x200 + 0x520 + 0x5c0;

    uint256 internal constant PROOF_RECURSIVE_PART_P2_X_SLOT = 0x200 + 0x520 + 0x5e0;
    uint256 internal constant PROOF_RECURSIVE_PART_P2_Y_SLOT = 0x200 + 0x520 + 0x600;

    /*//////////////////////////////////////////////////////////////
                             Transcript slot
    //////////////////////////////////////////////////////////////*/

    uint256 internal constant TRANSCRIPT_BEGIN_SLOT = 0x200 + 0x520 + 0x620 + 0x00;
    uint256 internal constant TRANSCRIPT_DST_BYTE_SLOT = 0x200 + 0x520 + 0x620 + 0x03;
    uint256 internal constant TRANSCRIPT_STATE_0_SLOT = 0x200 + 0x520 + 0x620 + 0x04;
    uint256 internal constant TRANSCRIPT_STATE_1_SLOT = 0x200 + 0x520 + 0x620 + 0x24;
    uint256 internal constant TRANSCRIPT_CHALLENGE_SLOT = 0x200 + 0x520 + 0x620 + 0x44;

    /*//////////////////////////////////////////////////////////////
                             Partial verifier state
    //////////////////////////////////////////////////////////////*/

    uint256 internal constant STATE_ALPHA_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x000;
    uint256 internal constant STATE_BETA_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x020;
    uint256 internal constant STATE_GAMMA_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x040;
    uint256 internal constant STATE_POWER_OF_ALPHA_2_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x060;
    uint256 internal constant STATE_POWER_OF_ALPHA_3_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x080;
    uint256 internal constant STATE_POWER_OF_ALPHA_4_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x0a0;
    uint256 internal constant STATE_POWER_OF_ALPHA_5_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x0c0;
    uint256 internal constant STATE_POWER_OF_ALPHA_6_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x0e0;
    uint256 internal constant STATE_POWER_OF_ALPHA_7_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x100;
    uint256 internal constant STATE_POWER_OF_ALPHA_8_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x120;
    uint256 internal constant STATE_ETA_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x140;
    uint256 internal constant STATE_BETA_LOOKUP_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x160;
    uint256 internal constant STATE_GAMMA_LOOKUP_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x180;
    uint256 internal constant STATE_BETA_PLUS_ONE_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x1a0;
    uint256 internal constant STATE_BETA_GAMMA_PLUS_GAMMA_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x1c0;
    uint256 internal constant STATE_V_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x1e0;
    uint256 internal constant STATE_U_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x200;
    uint256 internal constant STATE_Z_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x220;
    uint256 internal constant STATE_Z_MINUS_LAST_OMEGA_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x240;
    uint256 internal constant STATE_L_0_AT_Z_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x260;
    uint256 internal constant STATE_L_N_MINUS_ONE_AT_Z_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x280;
    uint256 internal constant STATE_Z_IN_DOMAIN_SIZE = 0x200 + 0x520 + 0x620 + 0x80 + 0x2a0;

    /*//////////////////////////////////////////////////////////////
                             Queries
    //////////////////////////////////////////////////////////////*/

    uint256 internal constant QUERIES_BUFFER_POINT_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x00;

    uint256 internal constant QUERIES_AT_Z_0_X_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x40;
    uint256 internal constant QUERIES_AT_Z_0_Y_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x60;
    uint256 internal constant QUERIES_AT_Z_1_X_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x80;
    uint256 internal constant QUERIES_AT_Z_1_Y_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0xa0;

    uint256 internal constant QUERIES_T_POLY_AGGREGATED_X_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0xc0;
    uint256 internal constant QUERIES_T_POLY_AGGREGATED_Y_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0xe0;

    /*//////////////////////////////////////////////////////////////
                             Aggregated commitment
    //////////////////////////////////////////////////////////////*/

    uint256 internal constant AGGREGATED_AT_Z_X_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0x00;
    uint256 internal constant AGGREGATED_AT_Z_Y_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0x20;

    uint256 internal constant AGGREGATED_AT_Z_OMEGA_X_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0x40;
    uint256 internal constant AGGREGATED_AT_Z_OMEGA_Y_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0x60;

    uint256 internal constant AGGREGATED_OPENING_AT_Z_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0x80;
    uint256 internal constant AGGREGATED_OPENING_AT_Z_OMEGA_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0xa0;

    /*//////////////////////////////////////////////////////////////
                             Pairing data
    //////////////////////////////////////////////////////////////*/

    uint256 internal constant PAIRING_BUFFER_POINT_X_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0xc0 + 0x00;
    uint256 internal constant PAIRING_BUFFER_POINT_Y_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0xc0 + 0x20;

    uint256 internal constant PAIRING_PAIR_WITH_GENERATOR_X_SLOT =
        0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0xc0 + 0x40;
    uint256 internal constant PAIRING_PAIR_WITH_GENERATOR_Y_SLOT =
        0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0xc0 + 0x60;

    uint256 internal constant PAIRING_PAIR_WITH_X_X_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0x100 + 0x80;
    uint256 internal constant PAIRING_PAIR_WITH_X_Y_SLOT = 0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0x100 + 0xa0;

    /*//////////////////////////////////////////////////////////////
               Slots for scalar multiplication optimizations
    //////////////////////////////////////////////////////////////*/

    uint256 internal constant COPY_PERMUTATION_FIRST_AGGREGATED_COMMITMENT_COEFF =
        0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0x100 + 0xc0;
    uint256 internal constant LOOKUP_GRAND_PRODUCT_FIRST_AGGREGATED_COMMITMENT_COEFF =
        0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0x100 + 0xe0;
    uint256 internal constant LOOKUP_S_FIRST_AGGREGATED_COMMITMENT_COEFF =
229:        0x200 + 0x520 + 0x620 + 0x80 + 0x2c0 + 0x100 + 0x100 + 0x100;

```

[27-229](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Verifier.sol#L27C1-L229C70)

## [G-06] Using XOR (^) and AND (&) bitwise equivalents

Given 4 variables a, b, c and d represented as such:

0 0 0 0 0 1 1 0 <- a
0 1 1 0 0 1 1 0 <- b
0 0 0 0 0 0 0 0 <- c
1 1 1 1 1 1 1 1 <- d
To have a == b means that every 0 and 1 match on both variables. Meaning that a XOR (operator ^) would evaluate to 0 ((a ^ b) == 0), as it excludes by definition any equalities.Now, if a != b, this means that there’s at least somewhere a 1 and a 0 not matching between a and b, making (a ^ b) != 0.Both formulas are logically equivalent and using the XOR bitwise operator costs actually the same amount of gas.However, it is much cheaper to use the bitwise OR operator (|) than comparing the truthy or falsy values.These are logically equivalent too, as the OR bitwise operator (|) would result in a 1 somewhere if any value is not 0 between the XOR (^) statements, meaning if any XOR (^) statement verifies that its arguments are different.

**_2 Instances - 2 Files_**

```solidity

File : contracts/ethereum/contracts/bridge/L1WethBridge.sol

 require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");

```

[311](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L311)

```diff
File : contracts/ethereum/contracts/bridge/L1WethBridge.sol

- require(msg.sender == l1WethAddress || msg.sender == address(zkSync), "pn");
+ require(msg.sender ^ l1WethAddress & msg.sender ^ address(zkSync), "pn");

```

```solidity

File : contracts/ethereum/contracts/governance/Governance.sol

 return state == OperationState.Waiting || state == OperationState.Ready;

```

[91](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L91C8-L91C81)

```diff
File : contracts/ethereum/contracts/governance/Governance.sol

- return state == OperationState.Waiting || state == OperationState.Ready;
+ return state ^ OperationState.Waiting & state ^ OperationState.Ready;

```

## [G-07] No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address, etc.). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

**_18 Instances - 7 Files_**

```solidity

File : contracts/ethereum/contracts/common/AllowList.sol

62:   for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {
               _setAccessMode(_targets[i], _accessModes[i]);
64:          }

96:   for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
              _setPermissionToCall(_callers[i], _targets[i], _functionSigs[i], _enables[i]);
98:        }

```

[62-64](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L62C9-L64C10)
[96-98](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/common/AllowList.sol#L96C8-L98C10)

```diff
File : contracts/ethereum/contracts/common/AllowList.sol

- for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {
+ for (uint256 i; i < targetsLength; i = i.uncheckedInc()) {
            _setAccessMode(_targets[i], _accessModes[i]);
        }

-  for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
+  for (uint256 i; i < callersLength; i = i.uncheckedInc()) {
            _setPermissionToCall(_callers[i], _targets[i], _functionSigs[i], _enables[i]);
        }

```

```solidity

File : contracts/ethereum/contracts/governance/Governance.sol

 for (uint256 i = 0; i < _calls.length; ++i) {

```

[225](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L225C8-L225C54)

```diff
File : contracts/ethereum/contracts/governance/Governance.sol

- for (uint256 i = 0; i < _calls.length; ++i) {
+ for (uint256 i; i < _calls.length; ++i) {

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Executor.sol

for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

```

[209](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L209C9-L209C80)

```diff
File : contracts/ethereum/contracts/zksync/facets/Executor.sol

-  for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
+  for (uint256 i; i < _newBatchesData.length; i = i.uncheckedInc()) {

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Executor.sol

for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {

```

[241]https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L241C7-L241C80)

```diff
File : contracts/ethereum/contracts/zksync/facets/Executor.sol

-  for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
+  for (uint256 i; i < _newBatchesData.length; i = i.uncheckedInc()) {

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Executor.sol

for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

```

[123](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L123C9-L123C105)

```diff
File : contracts/ethereum/contracts/zksync/facets/Executor.sol

-for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
+for (uint256 i; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Executor.sol

  for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {

```

[263](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L263C7-L263C71)

```diff
File : contracts/ethereum/contracts/zksync/facets/Executor.sol

-  for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
+  for (uint256 i; i < _nPriorityOps; i = i.uncheckedInc()) {

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Executor.sol

for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {

```

[293](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L293C9-L293C66)

```diff
File : contracts/ethereum/contracts/zksync/facets/Executor.sol

-  for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
+  for (uint256 i; i < nBatches; i = i.uncheckedInc()) {

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Executor.sol

 for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {

```

[330](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L330C8-L330C80)

```diff
File : contracts/ethereum/contracts/zksync/facets/Executor.sol

-  for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
+  for (uint256 i; i < committedBatchesLength; i = i.uncheckedInc()) {

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Getters.sol

  for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {

```

[182](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L182C7-L182C67)

```diff
File : contracts/ethereum/contracts/zksync/facets/Getters.sol

-  for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
+  for (uint256 i; i < facetsLen; i = i.uncheckedInc()) {

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Mailbox.sol

 for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {

```

[395](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L395C8-L395C72)

```diff
File : contracts/ethereum/contracts/zksync/facets/Mailbox.sol

-  for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
+  for (uint256 i; i < factoryDepsLen; i = i.uncheckedInc()) {

```

```solidity

File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

 for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {

```

[100](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L100C8-L100C73)

```diff
File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

-  for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
+  for (uint256 i; i < facetCutsLength; i = i.uncheckedInc()) {

```

```solidity

File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

   for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

```

[138](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L138C9-L138C73)

```diff
File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

-   for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
+   for (uint256 i; i < selectorsLength; i = i.uncheckedInc()) {

```

```solidity

File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

    for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

```

[159](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L159C9-L159C73)

```diff
File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

-    for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
+    for (uint256 i; i < selectorsLength; i = i.uncheckedInc()) {

```

```solidity

File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

    for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {

```

[179](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L179C9-L179C73)

```diff
File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

-    for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
+    for (uint256 i; i < selectorsLength; i = i.uncheckedInc()) {

```

```solidity

File : contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol

  uint256 costForPubdata = 0;

```

[92](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L92C8-L92C36)

```diff
File : contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol

-   uint256 costForPubdata = 0;
+   uint256 costForPubdata;

```

```solidity

File : contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

    for (uint256 i = 0; i < _newBatchesData.length; ++i) {

```

[85](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L85C13-L85C67)

```diff
File : contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

-    for (uint256 i = 0; i < _newBatchesData.length; ++i) {
+    for (uint256 i; i < _newBatchesData.length; ++i) {

```

```solidity

File : contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

    for (uint256 i = 0; i < _newBatchesData.length; ++i) {

```

[116](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L116C11-L116C67)

```diff
File : contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

-    for (uint256 i = 0; i < _newBatchesData.length; ++i) {
+    for (uint256 i; i < _newBatchesData.length; ++i) {

```

## [G-08] Can Make The Variable Outside The `Loop` To Save Gas

When you declare a variable inside a loop, Solidity creates a new instance of the variable for each iteration of the loop. This can lead to unnecessary gas costs, especially if the loop is executed frequently or iterates over a large number of elements. By declaring the variable outside the loop, you can avoid the creation of multiple instances of the variable and reduce the gas cost of your contract

**_22 Instances - 6 Files_**

```solidity

File : contracts/ethereum/contracts/governance/Governance.sol

  function _execute(Call[] calldata _calls) internal {
        for (uint256 i = 0; i < _calls.length; ++i) {
            (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
            if (!success) {
                // Propage an error if the call fails.
                assembly {
                    revert(add(returnData, 0x20), mload(returnData))
                }
            }
        }
    }

```

[224-234](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/governance/Governance.sol#L224C3-L234C6)

```diff
File : contracts/ethereum/contracts/governance/Governance.sol

  function _execute(Call[] calldata _calls) internal {
+
+     bool success;
+     bytes memory returnData;
+
        for (uint256 i = 0; i < _calls.length; ++i) {
-            (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
+            (success, returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
            if (!success) {
                // Propage an error if the call fails.
                assembly {
                    revert(add(returnData, 0x20), mload(returnData))
                }
            }
        }
    }

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Executor.sol

        // linear traversal of the logs
        for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
            // Extract the values to be compared to/used such as the log sender, key, and value
            (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET);
            (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);
            (bytes32 logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);

```

[122-128](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L122C1-L128C1)

```diff
File : contracts/ethereum/contracts/zksync/facets/Executor.sol
+
+      address logSender;
+      uint256 logKey;
+      bytes32 logValue;
        // linear traversal of the logs
        for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
            // Extract the values to be compared to/used such as the log sender, key, and value
-            (address logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET);
+            (logSender, ) = UnsafeBytes.readAddress(emittedL2Logs, i + L2_LOG_ADDRESS_OFFSET);
-            (uint256 logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);
+            (logKey, ) = UnsafeBytes.readUint256(emittedL2Logs, i + L2_LOG_KEY_OFFSET);
-            (bytes32 logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);
+            (logValue, ) = UnsafeBytes.readBytes32(emittedL2Logs, i + L2_LOG_VALUE_OFFSET);

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Executor.sol

   for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
            // The upgrade transaction must only be included in the first batch.
            bytes32 expectedUpgradeTxHash = i == 0 ? _systemContractUpgradeTxHash : bytes32(0);

```

[241-243](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L241C6-L243C96)

```diff
File : contracts/ethereum/contracts/zksync/facets/Executor.sol

+  bytes32 expectedUpgradeTxHash;
   for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
            // The upgrade transaction must only be included in the first batch.
-            bytes32 expectedUpgradeTxHash = i == 0 ? _systemContractUpgradeTxHash : bytes32(0);
+            expectedUpgradeTxHash = i == 0 ? _systemContractUpgradeTxHash : bytes32(0);

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Executor.sol

        for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
            PriorityOperation memory priorityOp = s.priorityQueue.popFront();

```

[263-264](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L263C1-L264C78)

```diff
File : contracts/ethereum/contracts/zksync/facets/Executor.sol

+        PriorityOperation memory priorityOp;
        for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
-            PriorityOperation memory priorityOp = s.priorityQueue.popFront();
+            priorityOp = s.priorityQueue.popFront();

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Executor.sol

       bytes32 prevBatchCommitment = _prevBatch.commitment;
        for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
            currentTotalBatchesVerified = currentTotalBatchesVerified.uncheckedInc();
            require(
                _hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],
                "o1"
            );

            bytes32 currentBatchCommitment = _committedBatches[i].commitment;

```

[329-237](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L329C2-L337C78)

```diff
File : contracts/ethereum/contracts/zksync/facets/Executor.sol

       bytes32 prevBatchCommitment = _prevBatch.commitment;
+       bytes32 currentBatchCommitment;
        for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
            currentTotalBatchesVerified = currentTotalBatchesVerified.uncheckedInc();
            require(
                _hashStoredBatchInfo(_committedBatches[i]) == s.storedBatchHashes[currentTotalBatchesVerified],
                "o1"
            );

-            bytes32 currentBatchCommitment = _committedBatches[i].commitment;
+            currentBatchCommitment = _committedBatches[i].commitment;

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Getters.sol

   for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
            address facetAddr = ds.facets[i];
            Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];

            result[i] = Facet(facetAddr, facetToSelectors.selectors);
        }

```

[182-183](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L182C6-L187C10)

```diff
File : contracts/ethereum/contracts/zksync/facets/Getters.sol

+  address facetAddr;
+  Diamond.FacetToSelectors memory facetToSelectors;
   for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
-            address facetAddr = ds.facets[i];
+            facetAddr = ds.facets[i];
-            Diamond.FacetToSelectors memory facetToSelectors = ds.facetToSelectors[facetAddr];
+            facetToSelectors = ds.facetToSelectors[facetAddr];

            result[i] = Facet(facetAddr, facetToSelectors.selectors);
        }

```

```solidity

File : contracts/ethereum/contracts/zksync/facets/Mailbox.sol

  uint256 factoryDepsLen = _factoryDeps.length;
        hashedFactoryDeps = new uint256[](factoryDepsLen);
        for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
            bytes32 hashedBytecode = L2ContractHelper.hashL2Bytecode(_factoryDeps[i]);

```

[393-396](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L393C7-L396C87)

```diff
File : contracts/ethereum/contracts/zksync/facets/Mailbox.sol


        uint256 factoryDepsLen = _factoryDeps.length;
        hashedFactoryDeps = new uint256[](factoryDepsLen);
+       bytes32 hashedBytecode;
        for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
-            bytes32 hashedBytecode = L2ContractHelper.hashL2Bytecode(_factoryDeps[i]);
+            hashedBytecode = L2ContractHelper.hashL2Bytecode(_factoryDeps[i]);

```

```solidity

File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

 for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
            Action action = facetCuts[i].action;
            address facet = facetCuts[i].facet;
            bool isFacetFreezable = facetCuts[i].isFreezable;
            bytes4[] memory selectors = facetCuts[i].selectors;

```

[100-104](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L100C9-L104C64)

```diff
File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

+   Action action;
+   address facet;
+   bool isFacetFreezable;
+   bytes4[] memory selectors;
    for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
-            Action action = facetCuts[i].action;
-            address facet = facetCuts[i].facet;
-            bool isFacetFreezable = facetCuts[i].isFreezable;
-            bytes4[] memory selectors = facetCuts[i].selectors;
+            action = facetCuts[i].action;
+            facet = facetCuts[i].facet;
+            isFacetFreezable = facetCuts[i].isFreezable;
+            selectors = facetCuts[i].selectors;

```

```solidity

File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

      for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
            bytes4 selector = _selectors[i];
            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

```

[138-140](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L138C9-L140C76)

```diff
File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

+     bytes4 selector;
+     SelectorToFacet memory oldFacet;
      for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
-            bytes4 selector = _selectors[i];
-            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
+            selector = _selectors[i];
+            oldFacet = ds.selectorToFacet[selector];

```

```solidity

File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

    for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
            bytes4 selector = _selectors[i];
            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

```

[159-161](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L159C8-L161C76)

```diff
File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

+   bytes4 selector;
+   SelectorToFacet memory oldFacet;
    for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
-            bytes4 selector = _selectors[i];
-            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
+            selector = _selectors[i];
+            oldFacet = ds.selectorToFacet[selector];

```

```solidity

File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

    for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
            bytes4 selector = _selectors[i];
            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];

```

[179-181](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L179C9-L181C76)

```diff
File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

+    bytes4 selector;
+    SelectorToFacet memory oldFacet;
     for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
-            bytes4 selector = _selectors[i];
-            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
+            selector = _selectors[i];
+            oldFacet = ds.selectorToFacet[selector];

```

```solidity

File : contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

  unchecked {
            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
                uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);

```

[115-116](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L115C8-L117C108)

```diff
File : contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

+    uint256 commitBatchTimestamp;
     unchecked {
            for (uint256 i = 0; i < _newBatchesData.length; ++i) {
-                uint256 commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);
+                commitBatchTimestamp = committedBatchTimestamp.get(_newBatchesData[i].batchNumber);

```

## [G-09] Instead of counting down in `for` statements, count up

Counting down is more gas efficient than counting up because neither we are making zero variable to non-zero variable and also we will get gas refund in the last transaction when making non-zero to zero variable.

**_1 Instance - 1 File_**

```solidity

File : contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

  for (uint256 i = 0; i < _factoryDeps.length; ++i) {

```

[204](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204C7-L204C60)

```diff
File : contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

-  for (uint256 i = 0; i < _factoryDeps.length; ++i) {
+  for (uint256 i = _factoryDeps.length - 1; i >= 0; --i) {

```

## [G-10] Refactor `require()` early in function to fail early

**_1 Instance - 1 File_**

```solidity

File : contracts/ethereum/contracts/zksync/facets/Executor.sol

        // Save the variables into the stack to save gas on reading them later
        uint256 currentTotalBatchesVerified = s.totalBatchesVerified;
        uint256 committedBatchesLength = _committedBatches.length;

        // Save the variable from the storage to memory to save gas
        VerifierParams memory verifierParams = s.verifierParams;

        // Initialize the array, that will be used as public input to the ZKP
        uint256[] memory proofPublicInput = new uint256[](committedBatchesLength);

        // Check that the batch passed by the validator is indeed the first unverified batch
        require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");

```

[316-327](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L316C1-L327C109)

```diff
File : contracts/ethereum/contracts/zksync/facets/Executor.sol

        // Save the variables into the stack to save gas on reading them later
        uint256 currentTotalBatchesVerified = s.totalBatchesVerified;
+
+        require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");
+
        uint256 committedBatchesLength = _committedBatches.length;

        // Save the variable from the storage to memory to save gas
        VerifierParams memory verifierParams = s.verifierParams;

        // Initialize the array, that will be used as public input to the ZKP
        uint256[] memory proofPublicInput = new uint256[](committedBatchesLength);

        // Check that the batch passed by the validator is indeed the first unverified batch
-        require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");

```

## [G-11] Custom `error`s cost less than `require`/`assert`

Consider the use of a custom `error`, as it leads to a cheaper deploy cost and run time cost. The run time cost is only relevant when the revert condition is met.

**Note: These instances missed by bot report**

**_2 Instances - 2 Files_**

```solidity

File : contracts/ethereum/contracts/zksync/facets/Executor.sol

   assert(block.chainid != 1);

```

[351](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L351C8-L351C36)

```solidity

File : contracts/ethereum/contracts/zksync/DiamondInit.sol

  assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

```

[87](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L87C7-L87C55)

## [G-12] Check denominator for 0 before dividing from it

**_1 Instance - 1 File_**

```solidity

File : contracts/ethereum/contracts/zksync/facets/Mailbox.sol

 uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;

```

[181](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L181C8-L181C102)

```diff
File : contracts/ethereum/contracts/zksync/facets/Mailbox.sol

+   if(_gasPricePerPubdata == 0) revert();
    uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;

```

## [G-13] `Don’t cache` value if it is only used once

If a value is only intended to be used once then it should not be cached. Caching the value will result in unnecessary stack manipulation.

**_2 Instances - 2 Files_**

```solidity

File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

 function getDiamondStorage() internal pure returns (DiamondStorage storage diamondStorage) {
        bytes32 position = DIAMOND_STORAGE_POSITION;
        assembly {
            diamondStorage.slot := position
        }
    }

```

[86-91](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L86C4-L91C6)

```diff
File : contracts/ethereum/contracts/zksync/libraries/Diamond.sol

  function getDiamondStorage() internal pure returns (DiamondStorage storage diamondStorage) {
-        bytes32 position = DIAMOND_STORAGE_POSITION;
        assembly {
-            diamondStorage.slot := position
+            diamondStorage.slot := DIAMOND_STORAGE_POSITION
        }
    }

```

```solidity

File : contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

    function _propagateToZkSync() internal {
        address contractAddress = zkSyncContract;
        assembly {
            // Copy function signature and arguments from calldata at zero position into memory at pointer position
            calldatacopy(0, 0, calldatasize())
            // Call method of the zkSync contract returns 0 on error
            let result := call(gas(), contractAddress, 0, 0, calldatasize(), 0, 0)
            // Get the size of the last return data
            let size := returndatasize()
            // Copy the size length of bytes from return data at zero position to pointer position
            returndatacopy(0, 0, size)
            // Depending on the result value
            switch result
            case 0 {
                // End execution and revert state changes
                revert(0, size)
            }
            default {
                // Return data with length of size at pointers position
                return(0, size)
            }
        }
    }

```

[132-154](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L132C4-L154C6)

```diff
File : contracts/ethereum/contracts/zksync/ValidatorTimelock.sol

    function _propagateToZkSync() internal {
-        address contractAddress = zkSyncContract;
        assembly {
            // Copy function signature and arguments from calldata at zero position into memory at pointer position
            calldatacopy(0, 0, calldatasize())
            // Call method of the zkSync contract returns 0 on error
-            let result := call(gas(), contractAddress, 0, 0, calldatasize(), 0, 0)
+            let result := call(gas(), zkSyncContract, 0, 0, calldatasize(), 0, 0)
            // Get the size of the last return data
            let size := returndatasize()
            // Copy the size length of bytes from return data at zero position to pointer position
            returndatacopy(0, 0, size)
            // Depending on the result value
            switch result
            case 0 {
                // End execution and revert state changes
                revert(0, size)
            }
            default {
                // Return data with length of size at pointers position
                return(0, size)
            }
        }
    }

```

## [G-14] Use constant instead of `type(uintx).max`

**_5 Instances - 1 File_**

```solidity

File : contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol

        require(_transaction.from <= type(uint16).max, "ua");
        require(_transaction.to <= type(uint160).max, "ub");
        require(_transaction.paymaster == 0, "uc");
        require(_transaction.value == 0, "ud");
        require(_transaction.reserved[0] == 0, "ue");
        require(_transaction.reserved[1] <= type(uint160).max, "uf");

```

[50-55](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L50C1-L55C70)

```diff
File : contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol

library TransactionValidator {
    ...
+       uint16 constant MAX_from = 2**16 - 1;
+       uint160 constant MAX_to  = 2**160 - 1;
  ...

    function validateUpgradeTransaction(IMailbox.L2CanonicalTransaction memory _transaction) internal pure {
        // Restrict from to be within system contract range (0...2^16 - 1)
-        require(_transaction.from <= type(uint16).max, "ua");
-        require(_transaction.to <= type(uint160).max, "ub");
+        require(_transaction.from <= MAX_from, "ua");
+        require(_transaction.to <= MAX_to, "ub");
         require(_transaction.paymaster == 0, "uc");
         require(_transaction.value == 0, "ud");
         require(_transaction.reserved[0] == 0, "ue");
-        require(_transaction.reserved[1] <= type(uint160).max, "uf");
+        require(_transaction.reserved[1] <= MAX_to, "uf");
         require(_transaction.reserved[2] == 0, "ug");
         require(_transaction.reserved[3] == 0, "uo");
         require(_transaction.signature.length == 0, "uh");
         require(_transaction.paymasterInput.length == 0, "ul");
         require(_transaction.reservedDynamic.length == 0, "um");
    }
  ...
}


```
