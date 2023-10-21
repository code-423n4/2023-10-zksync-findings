# Summary
* L-1: Anyone can cause DoS of transaction batch processing due to bootloader revert
* L-2: `CURRENT_MAX_PRECOMPILE_ADDRESS` is wrong and causes inconsistent precompile codehashes
* L-3: `DEFAULT_L2_LOGS_TREE_ROOT_HASH` is not properly initialized
* L-4: WETH can be bridged via `L1ERC20Bridge` and could therefore get stuck
* L-5: Target address of force deployment is unrestricted
* L-6: Current refund recipient mechanism can easily lead to lost user funds
* NC-1: `KECCAK256_SYSTEM_CONTRACT` precompile is in wrong address range
* NC-2: `MAX_MSG_VALUE` is never used
* NC-3: `L2_MESSENGER` vs. `L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR` vs. `L1_MESSENGER_ADDR`

---

## L-1: Anyone can cause DoS of transaction batch processing due to bootloader revert
On normal transaction execution failure, the bootloader does not revert and the failure is recorded for finalization.  
However, anyone can craft a malicious L2 transaction that triggers an assertion in the bootloader and therefore makes the whole bootloader revert, which in turn causes DoS for the whole batch of up to 32 transactions (upgrades, L1->L2, L2->L1 and L2->L2).  

For example, one can trigger a [gas failure](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1195) or provide an incorrect signature to cause [validation failure](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L2208) leading to a bootloader revert, see also PoC:
```diff
diff --git a/code/system-contracts/test/DefaultAccount.spec.ts b/code/system-contracts/test/DefaultAccount.spec.ts
index 6231c34..d17556c 100644
--- a/code/system-contracts/test/DefaultAccount.spec.ts
+++ b/code/system-contracts/test/DefaultAccount.spec.ts
@@ -14,7 +14,7 @@ import {
     NONCE_HOLDER_SYSTEM_CONTRACT_ADDRESS,
     ETH_TOKEN_SYSTEM_CONTRACT_ADDRESS
 } from './shared/constants';
-import { Wallet } from 'zksync-web3';
+import { Wallet, utils } from 'zksync-web3';
 import { getWallets, deployContract, setCode, loadArtifact } from './shared/utils';
 import { network, ethers } from 'hardhat';
 import { hashBytecode, serialize } from 'zksync-web3/build/src/utils';
@@ -151,6 +151,36 @@ describe('DefaultAccount tests', function () {
         });
     });
 
+    describe.only('bootloader batch DoS', function () {
+        it('zero gas limit', async () => {
+            try {
+                await callable.fallback({ gasLimit: 0});
+                throw new Error("should never be reached");
+            }
+            catch (error) {
+                expect(error.body).to.contain('Transaction HALT: Account validation error: Not enough gas for transaction validation');
+            }
+        });
+
+        it('invalid signature', async () => {
+            const tx = await wallet.populateTransaction({
+                type: 113,
+                from: wallet.address,
+                to: callable.address,
+                data: "0x"
+            });
+            tx.customData = { customSignature: "0x1234" };
+            
+            try {
+                await wallet.provider.sendTransaction(utils.serialize(tx));
+                throw new Error("should never be reached");
+            }
+            catch (error) {
+                expect(error.body).to.contain('Transaction HALT: Account validation error: Signature length is incorrect');
+            }
+        });
+    });
+
     describe('executeTransaction', function () {
         it('non-deployer ignored', async () => {
             let nonce = await nonceHolder.getMinNonce(account.address);

```

Anyways, although this DoS attack comes at basically zero cost for the griefer and can therefore be repeated indefinitely, an Era node operator can detect such failing transactions and can re-run the batch without them in a matter of milliseconds. Consequently, this attack vector is not severe and can be resolved by a good node implementation.


## L-2: `CURRENT_MAX_PRECOMPILE_ADDRESS` is wrong and causes inconsistent precompile codehashes
[CURRENT_MAX_PRECOMPILE_ADDRESS](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Constants.sol#L35) is currently [SHA256_SYSTEM_CONTRACT](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Constants.sol#L26) (0x02), but should be [ECMUL_SYSTEM_CONTRACT ](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Constants.sol#L28) (0x07).  

As a consequence, [AccountCodeStorage.getCodeHash(...)](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/AccountCodeStorage.sol#L89) leads to inconsistent results:
* `getCodeHash(ECRECOVER_SYSTEM_CONTRACT) == EMPTY_STRING_KECCAK`
* `getCodeHash(SHA256_SYSTEM_CONTRACT) == EMPTY_STRING_KECCAK`
* `getCodeHash(ECADD_SYSTEM_CONTRACT) == 0x00`
* `getCodeHash(ECMUL_SYSTEM_CONTRACT) == 0x00`


## L-3: `DEFAULT_L2_LOGS_TREE_ROOT_HASH` is not properly initialized
[DEFAULT_L2_LOGS_TREE_ROOT_HASH](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Config.sol#L36) is currently `0x00` but should be set to the real root hash of an empty Merkle tree (SMA-184), see todo comment.

## L-4: WETH can be bridged via `L1ERC20Bridge` and could therefore get stuck
Although there is the dedicated [L1WethBridge](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol) for WETH, one can still use the [L1ERC20Bridge](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol) which leads to a "worthless" WETH token on L2. If the L2 target contract can only work with / transfer the "real" expected [L2Weth](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/bridge/L2Weth.sol), the bridged WETH is stuck.  

Proposed solution: Revert on [L1ERC20Bridge.deposit(...)](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L176-L183) when `_l1Token == address(WETH)`.

## L-5: Target address of force deployment is unrestricted
The [target contract address](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L225) on [ContractDeployer.forceDeployOnAddress(...)](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L214) should be restricted to the system contract range (0x0000 to 0xFFFF).

## L-6: Current refund recipient mechanism can easily lead to lost user funds
Both, the [L1WethBridge](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol) as well as the [L1ERC20Bridge](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol) set the `refundRecipient = AddressAliasHelper.applyL1ToL2Alias(msg.sender)` in case `msg.sender` is a contract and no other `refundRecipient` was explicitly specified.  
However, it is highly unlikely that the user has control over a contract at / the private key for the `AddressAliasHelper.applyL1ToL2Alias(msg.sender)` L2 address and therefore this can lead to stuck funds in many cases.  

Proposed solution: Revert in `deposit(...)` when `msg.sender` is a contract and no explicit `refundRecipient` was specified.

---

## NC-1: `KECCAK256_SYSTEM_CONTRACT` precompile is in wrong address range
[KECCAK256_SYSTEM_CONTRACT](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Constants.sol#L56) is in the system contract address range, but is actually a precompile. For (codehash) consistency it should be placed within the precompile range at address 0x03, directly  after the `SHA256_SYSTEM_CONTRACT` (0x02). 

## NC-2: `MAX_MSG_VALUE` is never used
See definition of [MAX_MSG_VALUE ](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Constants.sol#L73).

## NC-3: `L2_MESSENGER` vs. `L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR` vs. `L1_MESSENGER_ADDR`
All 3 constants point to the same contract address and therefore the naming should be unified to avoid confusion. 
See [L2_MESSENGER](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/zksync/contracts/L2ContractHelper.sol#L78), [L2_TO_L1_MESSENGER_SYSTEM_CONTRACT_ADDR](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/L2ContractAddresses.sol#L18) and [L1_MESSENGER_ADDR](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L489)

