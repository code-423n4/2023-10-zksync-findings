`zkSync Era Low Risk & Non-Critical Findings  21`
# zkSync Era QA Report

This was a time-boxed audit hosted by Code4rena as a contest and below is the Low & Non-Critical Findings List of Bugs Found by Warden Shealtielanz.
> NOTE for The Judge and Sponsors to contextualize the findings below before Judging

The issues below are not merely of Low-Risk severity as some can be ranked as Medium or High Risk based on the Impact.


**In the severity ranking Column I will rank the severity of the Issue as follows.**

| Severity Rank Title | Explanation |
|-----:|----|
| `Undefined-H` | The Issue is of High Severity in my opinion which I decided to leave as Low but can be upgraded by the Judge if he sees fit |
| `Undefined-M` | The Issue is of Medium Severity in my opinion which I decided to leave as Low but can be upgraded by the Judge if he sees fit |
| `Low` | The Issue is of Low Impact|
| `Non-Critical` | The Issue's Impact is Extremely Low but will be a great improvement to the codebase if mitigated |

The Issues found have coverage mainly on the `Layer 1` & `Layer 2` contracts written in solidity by `73`% 0n (L1 contracts) to `27`% 0n (L2 contracts)
  
# Low-Risk Findings List
-use-the-latest-openzeppelin-Library) | Use the latest `OpenZeppelin` Library. | Low |
| Number | Issue Detail | Severity Ranking |
|-----:|----|-----|
| [`L-01`](#L-01-the-event-emitted-by-scheduleshadow-will-completely-break-the-functionality-of-shadow-upgrades) | The event emitted by `scheduleShadow()` will completely break the functionality of shadow upgrades. | `Undefined-M` |
| [`L-02`](#L-02-use-the-latest-openzeppelin-Library) | Use the latest `OpenZeppelin` Library. | `Low` |
| [`L-03`](#L-03-the-diamond-can-be-initialized-multiple-times) | The `Diamond Proxy` can be `Initialized` multiple times. | `Low` |
| [`L-04`](#L-04-use-of-unlocked-pragma-with-inconsistencies-also) | Use of Unnlocked Pragma with Inconsistencies also. | `Low` |
| [`L-05`](#L-05-the-diamond-proxy-isnt-completely-compatible-with-the-eip-2535-standard-because-it-is-not-completely-transparent) | The `Diamond Proxy` Isn't Completely compatible with The `EIP-2535` Standard because it is not completely transparent. | `Low` |
| [`L-06`](#L-06-the-reentrancyguardinitializer`-called-twice-in-contracts-will-cause-calls-to-initialize-to-always-revert) | The `reentrancyGuardInitializer()` called twice in contracts will cause calls to `initialize()` to always revert. | `Undefined-M` |
| [`L-07`](#L-07-no-need-for-the-allowlistedsol-contract) | No need for the `Allowlisted.sol` contract. | `Low` |
| [`L-08`](#L-08-no-check-to-ensure-security-council-is-a-multisig-acct) | No check to ensure `Security council` is a multisig acct. | `Low` |
| [`L-09`](#L-09-diamond-can-implement-eip-165-interface) | Diamond can implement `EIP-165` Interface. | `Non-Critical` |
| [`L-10`](#L-10-potential-storage-collision-can-be-seen-in-storagesol-can-break-the-entire-system) | Potential `storage` collision can be seen in `storage.sol` can break the entire system. | `Undefined-M` |
| [`L-11`](#L-11-delay-should-have-a-maxlimit`that-should-not-be-crossed) | delay should have a `max limit` that should not be crossed. | `Low` |
| [`L-12`](#L-12-beacon-proxy-is-not-properly-validated) | Beacon proxy is not properly validated. | `Low` |
| [`L-13`](#L-13-brigdes-are-prone-to-wintermuteattacks-as-a-different-contract-could-be-deployed-under-that-address-on-layer2) | Brigdes are prone to `Wintermute attacks` as a different `contract` could be deployed under that same `address` on `Layer 2`. | `Undefined-H` |
| [`L-14`](#L-14-addresses-that-eth-will-be-sent-to-should-be-marked-as-payable) | `Addresses` that `eth` will be sent to should be marked as `payable`. | `Low` |
| [`L-15`](#L-15-updateDelay-as-the-function-name-is-misleading) | `updateDelay()` as the function name is misleading. | `Low` |
| [`L-16`](#L-16-timing-defers-on-different-and-chains-can-break-zksync) | Timing defers on different `chains` and can break `zkSync`. | `Undefined-M` |
| [`L-17`](#L-17-allowlistsol-should-be-able-to-block-some-addresses-even-when-set-to-public) | `Allowlist.sol` should be able to block some `addresses` even when set to `public`. | `Low` |
| [`L-18`](#L-18-validate-that-the-validator-is-an-eoa-to-avoid-unwanted-behaviors) | Validate that the `validator` is an `EOA` to avoid unwanted behaviors. | `Low` |
| [`L-19`](#L-19-dddresses-might-not-be-owned-by-the-same-person-on-different-chains-especially-with-the-rise-of-accountabstraction) | `Addresses` might not be owned by the same person on different `chains`, especially with the rise of `account abstraction`. | `Low` |
| [`L-20`](#L-20-documentation-for-the-audit-is-not-enough-and-Incomplete) | Documentation for the audit is not enough and Incomplete. | `Low` |
| [`L-21`](#L-21-rebasing-tokens-will-be-lost-in-the-bridge-contracts) | Rebasing `tokens` will be lost in the bridge contracts. | `Undefined-M` |


> Total ~ 21 Issues found


# Low Risk Findings 

## L-01 The event emitted by `scheduleShadow()` will completely break the functionality of shadow upgrades.
### Summary 
> Severity ranking ~  `Undefined-M`

Shadow upgrades are supposed to be done in critical situations, to fix a bug or other issues that would need to be mitigated promptly.
[`scheduleShadow()`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L135C1-L145C6)
```solidity
    /// @notice Propose "shadow" upgrade, upgrade data is not publishing on-chain.
    /// @notice The owner will be able to execute the proposal either:
    /// - With a `delay` timelock on its own.
    /// - With security council instantly.
    /// @dev Only the current owner can propose an upgrade.
    /// @param _id The operation hash (see `hashOperation` function)
    /// @param _delay The delay time (in seconds) after which the proposed upgrade may be executed by the owner.
    function scheduleShadow(bytes32 _id, uint256 _delay) external onlyOwner {
        _schedule(_id, _delay);
        emit ShadowOperationScheduled(_id, _delay);
    }
```
as said by the docs
> Shadow upgrade. The owner only shows the commitment to the upgrade. This upgrade type is mostly useful for fixing critical issues in the production environment.

The issue stems from the fact that emitting an event when scheduling the shadow upgrade breaks the idea of secrecy as the event is emitted for logs on the blockchain anyone can see it, creating room for comprise.
### POC
- The MATTERlABS teams confirm a bug reported on Immunity by a whitehat that funds can be stolen or fake blocks could be validated on L1.
- the team finds a solution to the issue and plans to schedule a shadow upgrade so that the bug will not be exploited.
- on the call to scheduleShadow(), an event of a shadow upgrade is emitted to the blockchain.
- blackhats(Lazarus Group) is now aware that a shadow upgrade is scheduled because they keep checks on logs from events zkSync, they know something is up.
- Many black hats are now aware that a shadow upgrade is scheduled meaning the zkSync team is trying to fix a critical issue on a chain.
- As more eyes are on the code, chances are that before the upgrade is executed.
- one or more Blackhat has seen the issue and will exploit it before the upgrade is executed.
  
here zkSync era has already paid the Whitehat handsomely and when trying to prevent the issue from ever happening(by scheduling a shadow) the issue  is exploited by multiple Blackhats thereby breaking the entire system.

**This same issue has happened in the past**

When the Polygon team was notified of a bug, they started looking for a way to mitigate the issue secretly because it could be exploited easily, more Whitehats came after weeks to notify them of the same bug and they paid all the Whitehats that submitted that same issue heavily, in a scenario where the other white hats choose to exploit the issue, Polygon would have been broken.

**Note for the Judge** 

according to code4rena's severity guidelines, issues with low likelihood but potential critical Impact are marked as medium severity. maintaining such judging consistency is my justification for putting this under medium severity.
### Impact 
The idea behind scheduling shadow upgrades is broken and will lead to shadow schedules being bricked, to break the zkSync era.

### Impact 
don't emit events when scheduling shadow upgrades.
```diff
    function scheduleShadow(bytes32 _id, uint256 _delay) external onlyOwner {
        _schedule(_id, _delay);
-         emit ShadowOperationScheduled(_id, _delay);
    }
```





## L-02 Use the latest `OpenZeppelin` Library.
### Summary 
> Severity ranking `Low`


In package.json we can see that the OpenZeppelin library version used is "@openzeppelin/contracts": "^4.3.1". According to OpenZeppelin's [Security Advisories](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories), you can see that this version contains multiple High and Moderate severity vulnerabilities. While the code in-scope is not using the vulnerable parts of the library code at the moment, it is highly recommended to update to the latest stable version that has no breaking changes, meaning version 4.9.3.


## L-03 The `Diamond Proxy` can be `Initialized` multiple times.
### Summary 
The Diamond can be reinitialized as many times as possible, by the entity with the access to do so as you can see
```
   function _initializeDiamondCut(address _init, bytes memory _calldata) private {
   // --- some code---
}
```
zkSync uses the `DiamondInit` contract to initialize, via _initializeDiamondCut() in Diamond.sol, the is no check or mechanism that stops it from being reinitialized over and over again, this is bad and can lead to Griefing if the Admin is ever malicious.
### Recommended mitigation step
Ensure that the Diamond can only be Initialized again during an Upgrade.

## L-04 Use of Unlocked Pragma with Inconsistencies also.
### Summary 
> Severity ranking `Low`

This issue was missed in the bot report, Use of Unlocked pragma can lead to issues with different versions of solidity and integration bugs.
### Recommended mitigation step

Use a locked pragma across all the contracts.

## L-05 The `Diamond Proxy` Isn't Completely compatible with The `EIP-2535` Standard because it is not completely transparent.
### Summary 
**One of the most considered security issues in the `EIP-2535` standard is Transparency, you can see below as written:**

- [`Link Here`](https://eips.ethereum.org/EIPS/eip-2535#inspecting-facets--functions)
Diamonds `must` support inspecting facets and functions by implementing the IDiamondLoupe interface.

- [`Link Here`](https://eips.ethereum.org/EIPS/eip-2535#addingreplacingremoving-functions)
During the deployment of a diamond any immutable functions and any external functions added to the diamond **`must`** be emitted in the DiamondCut event.
A DiamondCut event **`must`** be emitted any time external functions are added, replaced, or removed. This applies to all **`upgrades`**, all functions changes, at any time, **whether through diamondCut or not**.


> **Security Considerations**
Transparency
Diamonds emit an event every time one or more functions are added, replaced or removed. All source code can be verified. This enables people and software to monitor changes to a contract. If any bad acting function is added to a diamond then it can be seen.
Security and domain experts can review the history of change of a diamond to detect any history of foul play.

> **Loupe Functions & DiamondCut Event**
To find out what functions a regular contract has it is only necessary to look at its verified source code.
The verified source code of a diamond does not include what functions it has so a different mechanism is needed.
A diamond has four standard functions called the loupe functions that are used to show what functions a diamond has.
The loupe functions can be used for many things including:
> - To show all functions used by a diamond.
> - To query services like Etherscan or files to retrieve and show all source code used by a diamond.
> - To query services like Etherscan or files to retrieve ABI information for a diamond.
> - To test or verify that a transaction that adds/replaces/removes functions on a diamond succeeded.
> - To find out what functions a diamond has before calling functions on it.
> - To be used by tools and programming libraries to deploy and upgrade diamonds.
> - To be used by user interfaces to show information about diamonds.
> - To be used by user interfaces to enable users to call functions on diamonds.
> - **Diamonds** support another form of transparency which is a historical record of all upgrades on a diamond. This is done with the DiamondCut event which is used to record all functions that are added, replaced or removed on a diamond.

- `Links Here` [`1`](https://eips.ethereum.org/EIPS/eip-2535#transparency), [`2`](https://eips.ethereum.org/EIPS/eip-2535#loupe-functions--diamondcut-event)

**However, when the Diamond is initialized in zkSkync, there is no event emitted when it is initialized for the reasons mentioned above.**

### POC
when [`diamondCut()`]() is called it can optionally be used for initialization
```solidity
    /// @dev Add/replace/remove any number of selectors and optionally execute a function with delegatecall
    /// @param _diamondCut Diamond's facet changes and the parameters to optional initialization delegatecall
    function diamondCut(DiamondCutData memory _diamondCut) internal {
        FacetCut[] memory facetCuts = _diamondCut.facetCuts;
        address initAddress = _diamondCut.initAddress;
        bytes memory initCalldata = _diamondCut.initCalldata;
   // --- some code ---

        _initializeDiamondCut(initAddress, initCalldata);
        emit DiamondCut(facetCuts, initAddress, initCalldata);
    }
```
The [`DiamondCut()`]() event only emits events for when functions-selectors are removed, replaced, or added. And you can see that when `_initializeDiamondCut()` it calls [`initialize()`]() under the hood, in the `DiamondInit.sol` contract.
```solidity
    function initialize(InitializeData calldata _initalizeData) external reentrancyGuardInitializer returns (bytes32) {
       require(address(_initalizeData.verifier) != address(0), "vt");
       require(_initalizeData.governor != address(0), "vy");
       require(_initalizeData.admin != address(0), "hc");
        require(_initalizeData.priorityTxMaxGasLimit <= L2_TX_MAX_GAS_LIMIT, "vu");
      // --- some code ---
        assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);

        return Diamond.DIAMOND_INIT_SUCCESS_RETURN_VALUE;
    }
```
you can clearly see that no event is emitted for the initialization of the Diamond proxy.
### Impact 
Complete Transparency required by the `EIP-2535` standard is broken here, In a situation where the proxy is initialized or upgraded, there will be no logs that can be easily accessed concerning its initialization, as specified above issues could arise when initialization goes wrong and it would be hard for the team to understand what went wrong easily and all.
- **NOTE**
> I understand that this can be seen as Over-Inflated severity as this can be seen as a QA but the reason I put this under medium severity is because The `EIP-2535` ponders on the importance of emitting events and this is acknowledged by the devs who implemented the `IDiamondLoupe interface`
```
Separate facet, whose only function is providing view and pure methods. It also implements diamond loupe which makes managing facets easier. This contract must never be frozen.
```
[`Link to Docs`](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/L1%20smart%20contracts.md#gettersfacet)

### Recommended mitigation step
Ensure an event is emitted when the diamond is initialized



## L-06  The `reentrancyGuardInitializer()` called twice in contracts will cause calls to `initialize()` to always revert.
### Summary 
> Severity ranking ~  `Undefined-M`


The `nonReentrant()`, is gotten from an abstract contract ReentrancyGuard to ensure that functions can not be reenter during a call, even by themselves,
however most of the contracts that use the [reentrancyGuardInitializer()](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/ReentrancyGuard.sol#L41) modifier to initialize the `LOCK_FLAG_ADDRESS`
```solidity
    modifier reentrancyGuardInitializer() {
        _initializeReentrancyGuard();
        _;
    }
```
```solidity
  function _initializeReentrancyGuard() private {
        uint256 lockSlotOldValue;

        // Storing an initial non-zero value makes deployment a bit more
        // expensive but in exchange every call to nonReentrant
        // will be cheaper.
        assembly {
            lockSlotOldValue := sload(LOCK_FLAG_ADDRESS)
            sstore(LOCK_FLAG_ADDRESS, _NOT_ENTERED)
        }

        // Check that storage slot for reentrancy guard is empty to rule out possibility of slot conflict
        require(lockSlotOldValue == 0, "1B");
    }
```
The issue is that the [L1 bridges](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L89) add the reentrancyGuardInitializer() modifier to their constructor. and also adds it to their initialize() functions respectively
```solidity
    constructor(IZkSync _zkSync, IAllowList _allowList) reentrancyGuardInitializer {
        zkSync = _zkSync;
        allowList = _allowList;
    }
```
```
    function initialize(
        bytes[] calldata _factoryDeps,
        address _l2TokenBeacon,
        address _governor,
        uint256 _deployBridgeImplementationFee,
        uint256 _deployBridgeProxyFee
    ) external payable reentrancyGuardInitializer {
```
Here you can see that if the contract is deployed it automatically initializes the `LOCK_FLAG_ADDRESS`, as it is added to the constructor, so when calling the initialize() the modifier added to it will cause the function to revert on the check 
```soildity
   require(lockSlotOldValue == 0, "1B");
```
in _initializeReentrancyGuard(), thereby bricking the Initialization of the contracts that do this.
### Recommended mitigation step
Apply this for the L1 brigde contracts.
```diff
    function initialize(
        bytes[] calldata _factoryDeps,
        address _l2TokenBeacon,
        address _governor,
        uint256 _deployBridgeImplementationFee,
        uint256 _deployBridgeProxyFee
-    ) external payable reentrancyGuardInitializer {
+    ) external payable nonReentrant() {
```


## L-07  No need for the `Allowlisted.sol` contract
### Summary 
The Allowlisted.sol contract has only one modifier in it, which can be put in Allowlist.sol, as this contract wastes gas on deployment and increases the complexity of zkSync having so many contracts to maintain.
## L-08 No check to ensure `Security council` is a multi-sig acct.
### Summary 
```
    constructor(address _admin, address _securityCouncil, uint256 _minDelay) {
        require(_admin != address(0), "Admin should be non zero address");

        _transferOwnership(_admin);

        securityCouncil = _securityCouncil;
        emit ChangeSecurityCouncil(address(0), _securityCouncil);

        minDelay = _minDelay;
        emit ChangeMinDelay(0, _minDelay);
    }
```
The security council is meant to be a multi-sig wallet, however, this is not checked in a scenario where an EOA is set as the security council, and centralization can break the protocol.
### Recommended Mitigation step
check the the address to be set as the security council is a contract.

## L-09 Diamond can implement `EIP-165` Interface.
### Summary 
According to EIP-2535. The Diamond can implement the ERC-165 interface, as it is best practice however The Diamond doesn't Implement this interface.

## L-10 Potential `storage` collision can be seen in `storage.sol` can break the entire system..
### Summary 
In the Storage.sol contract, the AppStorage struct keeps track of all stored values for the layer 1 system as part of the Diamond proxy pattern. The first entry of this struct is the DiamondCutStorage struct for the deployed code version and the UpgradeStorage struct for the audited and to-be-upgraded version. Both are used to track upgrade data for the DiamondCut facet.

As seen by comparing their entries, the structs have changed in size. Originally, DiamondCutStorage occupied 7 storage slots, while UpgradeStorage only occupies 2. Respectively, the slots of all following variables in the AppStorage struct shift, thereby resulting in a storage collision. Due to this desynchronization of storage, the entire system functionality would break and come to a halt
## L-11 delay should have a `max limit` that should not be crossed.
### Summary
the delay for when operations can be executed only has a check for the minimum delay that is required the issue is that scenarios can occur where the operation might have a delay period thats too long
```solidity
 /// @notice The minimum delay in seconds for operations to be ready for execution.
    uint256 public minDelay;

    /// @dev Changes the minimum timelock duration for future operations.
    /// @param _newDelay The new minimum delay time (in seconds) for future operations.
    function updateDelay(uint256 _newDelay) external onlySelf {
        emit ChangeMinDelay(minDelay, _newDelay);
        minDelay = _newDelay;
    }
```
### Recommended Mitigation Steps
add a max delay that cannot be passed when setting.


## L-12 Bridge address is not properly validated.
### Summary
During the deployment of bridge contracts via L2ERCBridge.sol & L1WethBridge in the initialize().
```solidity 
  function initialize(
        bytes[] calldata _factoryDeps,
        address _l2WethAddress,
        address _governor,
        uint256 _deployBridgeImplementationFee,
        uint256 _deployBridgeProxyFee
    ) external payable reentrancyGuardInitializer {
        require(_l2WethAddress != address(0), "L2 WETH address cannot be zero");
        require(_governor != address(0), "Governor address cannot be zero");
        require(_factoryDeps.length == 2, "Invalid factory deps length provided");
        require(
            msg.value == _deployBridgeImplementationFee + _deployBridgeProxyFee,
            "Miscalculated deploy transactions fees"
        );

        l2WethAddress = _l2WethAddress;

        bytes32 l2WethBridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
        bytes32 l2WethBridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);

        // Deploy L2 bridge implementation contract
        address wethBridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeImplementationFee,
            l2WethBridgeImplementationBytecodeHash,
            "", // Empty constructor data
            _factoryDeps // All factory deps are needed for L2 bridge
        );

        // Prepare the proxy constructor data
        bytes memory l2WethBridgeProxyConstructorData;
        {
            // Data to be used in delegate call to initialize the proxy
            bytes memory proxyInitializationParams = abi.encodeCall(
                IL2WethBridge.initialize,
                (address(this), l1WethAddress, _l2WethAddress)
            );
            l2WethBridgeProxyConstructorData = abi.encode(
                wethBridgeImplementationAddr,
                _governor,
                proxyInitializationParams
            );
        }
```
There is no zero address check to ensure, that the returned address of the brigde to be deployed on L2 is not a zero address.
### Summary 
add a check to ensure the returned address is not the zero address.

## L-13 Brigdes are prone to `Wintermute attacks` as a different `contract` could be deployed under that same `address` on `Layer 2`.
### Summary 
On initialization of L1 bridges, it sends a request for the corresponding bridges to be deployed using create2 on L2 and computes their contract address after which it saves it to storage, the issue here is just like the wintermute attack an attacker can create a contract under that same address and grief user who want to bridge tokens from Layer 1
## L-14 `Addresses` that `eth` will be sent to should be marked as `payable`.
### Summary 
if a contract doesn't have the recieve() or fallback function, a call to send eth will always revert.
withdrawTo() in MailBox.sol
```solidity
    function withdrawTo(address _to, uint256 _amount) public override {
        _burn(msg.sender, _amount);
        (bool success, ) = _to.call{value: _amount}("");
        require(success, "Failed withdrawal");
    }
```
_withdrawFunds() in L2WETH.sol
```solidity 

    function _withdrawFunds(address _to, uint256 _amount) internal {
        bool callSuccess;
        // Low-level assembly call, to avoid any memory copying (save gas)
        assembly {
            callSuccess := call(gas(), _to, _amount, 0, 0, 0, 0)
        }
        require(callSuccess, "pz");
    }
```
### Recommended Mitigation Steps
Mark addresses that eth will be sent to as payable.

## L-15 `updateDelay()` as the function name is misleading.
### Summary
The updateDelay function's name is very misleading in the sense that it sounds as if it updates the delay of operations or some other delay.
```solidity 
    /// @dev Changes the minimum timelock duration for future operations.
    /// @param _newDelay The new minimum delay time (in seconds) for future operations.
    function updateDelay(uint256 _newDelay) external onlySelf {
        emit ChangeMinDelay(minDelay, _newDelay);
        minDelay = _newDelay;
    }
```
### Recommended Mitigation Steps
rename as `updateMinDelay()`


## L-16 Timing defers on different `chains` and can break `zkSync`.
### Summary
When committing batches executor.sol verifies the L2 timestamps and previous timestamps
```solidity
    function _verifyBatchTimestamp(
        uint256 _packedBatchAndL2BlockTimestamp,
        uint256 _expectedBatchTimestamp,
        uint256 _previousBatchTimestamp
    ) internal view {
        // Check that the timestamp that came from the system context is expected
        uint256 batchTimestamp = _packedBatchAndL2BlockTimestamp >> 128;
        require(batchTimestamp == _expectedBatchTimestamp, "tb");

        // While the fact that _previousBatchTimestamp < batchTimestamp is already checked on L2,
        // we double check it here for clarity
        require(_previousBatchTimestamp < batchTimestamp, "h3");

        uint256 lastL2BlockTimestamp = _packedBatchAndL2BlockTimestamp & PACKED_L2_BLOCK_TIMESTAMP_MASK;

        // All L2 blocks have timestamps within the range of [batchTimestamp, lastL2BatchTimestamp].
        // So here we need to only double check that:
        // - The timestamp of the batch is not too small.
        // - The timestamp of the last L2 block is not too big.
        require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small
        require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big
    }
```
Precautions should be taken when using L1 block.timestamp for verification where L1 timestamp != L2 timestamp, it would break the system.

### Recommended Mitigation steps
ensure the mechanism  used for timestamp verification takes this into consideration.
## L-17 `Allowlist.sol` should be able to block some `addresses` even when set to `public`.
### Summary
The canVall() ensures that the caller has access but it will allow any address if the access mode is set to public
```solidity
    function canCall(address _caller, address _target, bytes4 _functionSig) external view returns (bool) {
        AccessMode accessMode = getAccessMode[_target];
        return
            accessMode == AccessMode.Public ||
            (accessMode == AccessMode.SpecialAccessOnly && hasSpecialAccessToCall[_caller][_target][_functionSig]);
    }
```
The Allowlist should be able to not allow blocked or blacklisted users.
### Recommended Mitigation steps
add a blocked access type, the ensure blocked addresses can't be able to call certain functions even when public. aslo add a function to block certain addresses
## L-18 Validate that the `validator` is an `EOA` to avoid unwanted behaviors.
### Summary 
when setting the validators address, there is no check to ensure the validator is an EOA
```solidity
    function setValidator(address _newValidator) external onlyOwner {
        address oldValidator = validator;
        validator = _newValidator;
        emit NewValidator(oldValidator, _newValidator);
    }
  ```
thereby breaking the assumption that only EOAs can be validators.
### Recommended Mitigation Steps
Check to ensure the address is not a contract



## L-19 `Addresses` might not be owned by the same person on different `chains`, especially with the rise of `account abstraction`.
### Summary 
users who bridge tokens or WETH from one layer to another might not be aware that they might not be the owner of the address as the can put their address as the L2 recipient and their funds will be lost forever.
### Recommended Mitigation Steps
Let the users know through the documentation so they are informed and any mistakes made from then onwards will solely be their fault as they are aware

## L-20 Documentation for the audit is not enough and Incomplete
### Summary 
The documenation does not do justice to the functionalities and assumed flow of zkSync, the functions are not well covered and the mechanisms aren't explained thoroughly, this migh affect the protocol when going through an audit as there will be lots of misconceptions surrounding the assumed flows and patterns
### Recommended Mitigation Steps
More advanced documentation should be written covering the mechanisms and contracts of both L1 & L2 contracts.

## L-21 Rebasing `tokens` will be lost in the bridge contracts.
### Summary 
according to the docs, weird tokens with weird logic are no allowed on the protocol, and this is checked in the bridge contract as

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L186

```solidity
    function deposit(
        address _l2Receiver,
        address _l1Token,
        uint256 _amount,
        uint256 _l2TxGasLimit,
        uint256 _l2TxGasPerPubdataByte,
        address _refundRecipient
    ) public payable nonReentrant senderCanCallFunction(allowList) returns (bytes32 l2TxHash) {
        require(_amount != 0, "2T"); // empty deposit amount
        uint256 amount = _depositFunds(msg.sender, IERC20(_l1Token), _amount);
        require(amount == _amount, "1T");
```
however this logic only applies to fee-on-transfer or deflationary tokens, but  with rebasing tokens like USDC/MATIC after certain periods of time, it disburses tokens to user and with no logic to get the token that might be added to the user out over time the tokens will be lost forever.

### Recommended Mitigation Steps
ensure against this kind of token or create a medium to get this kind of token out of the contract.










