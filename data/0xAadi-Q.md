## Low Risk Issues

### [L&#x2011;01] There is n function to withdraw ETH from `Governance` contract

The `Governance` contract can receive and hold ETH, but it doesn't have a function to withdraw these funds. This could potentially lock funds in the contract.

_There is 1 instance of this issue:_

```solidity
File: code/contracts/ethereum/contracts/governance/Governance.sol

262:    receive() external payable {}

```

_GitHub_: [262](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L262)

### [L&#x2011;02] Add `msg.sender` to the events

If both governor users and admin users can call `setPendingAdmin`, `unfreezeDiamond` and `setValidator`, it might be challenging to establish accountability for actions. Add `msg.sender` in the events to track the tra

_There is 3 instance of this issue:_

```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

49:        emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);

68:    function setValidator(address _validator, bool _active) external onlyGovernorOrAdmin {

120:    function unfreezeDiamond() external onlyGovernorOrAdmin {

```

_GitHub_: [49](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L49), [68](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L68), [120](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L120)

### [L&#x2011;03] Emitted Wrong event

Emitted `NewPendingGovernor` event from `setPendingAdmin()` function. This can cause confusions/wrong computations in the external systems which subscribed to the events.

_There is 3 instance of this issue:_

```solidity
File: code/contracts/ethereum/contracts/zksync/facets/Admin.sol

49:        emit NewPendingGovernor(oldPendingAdmin, _newPendingAdmin);

```

_GitHub_: [49](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L49)

## Non-critical Issues

### [N&#x2011;01] Use `uncheckedInc()` from `UncheckedMath` library instead of `i++` or operation to increment by 1

_There are 2 instances of this issue:_

```solidity
File: code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol

204:        for (uint256 i = 0; i < _factoryDeps.length; ++i) {

```

_GitHub_: [204](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L204)

```solidity
File: code/contracts/ethereum/contracts/governance/Governance.sol

225:        for (uint256 i = 0; i < _calls.length; ++i) {

```

_GitHub_: [225](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L225)


### [N&#x2011;03] Typos

_There are 46 instances of this issue:_

```solidity
File: code/contracts/ethereum/contracts/governance/Governance.sol

// @audit Propage
228:    // Propage an error if the call fails.

```

_GitHub_: [228](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L228)

```rust
File: code/era-zkevm_circuits/src/base_structures/log_query/mod.rs

// @audit unsused
665:        // we truncated, so let's enforce that those were unsused

```

_GitHub_: [665](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/base_structures/log_query/mod.rs#L665)

```rust
File: code/era-zkevm_circuits/src/base_structures/log_query/mod.rs

// @audit unsused
665:        // we truncated, so let's enforce that those were unsused

```

_GitHub_: [665](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/base_structures/log_query/mod.rs#L665)

```rust
File: code/era-zkevm_circuits/src/ecrecover/mod.rs

// @audit elleptic
250:    // (here n is the order of group of points on elleptic curve)

```
*GitHub*: [250](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/mod.rs#L250C1-L250C66)

```rust
File: code/era-zkevm_circuits/src/keccak256_round_function/mod.rs

// @audit padds
336:        // compute shifted buffer that removes first RATE elements and padds with something

```
*GitHub*: [336](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/keccak256_round_function/mod.rs#L336C18-L336C18)

```rust
File: code/era-zkevm_circuits/src/log_sorter/mod.rs

// @audit accross
345:            // We know that timestamps are unique accross logs, and are also the same between write and rollback

```
*GitHub*: [345](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/log_sorter/mod.rs#L345)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs

// @audit attemp
664:    // then compute to penalize for out of memory access attemp
```
*GitHub*: [664](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs#L664C3-L664C3)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs

// @audit convension
1217:    log.written_value = read_value; // our convension as in LOG opcode
```
*GitHub*: [1217](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs#L1217)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs

// @audit absort
1293:    // we absort with replacement
```
*GitHub*: [1293](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs#L1293)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/ret.rs

// @audit attemp
242:    // then compute to penalize for out of memory access attemp

// @audit bookkeeped
302:    // During the execution we maintain the following queue segments of what is usually called a "storage log", that is basically a sequence of bookkeeped

// @audit rollbackable
304:    // - global "forward" queue - all the changes (both rollbackable and not (read-like)) go in there, and it's "global" per block
```
GitHub*: [242](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/ret.rs#L242), [302, 304](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/ret.rs#L302C1-L304C131)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/add_sub.rs

// @audit summetric
84:    // that it's summetric over a/b
```
GitHub*: [84](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/add_sub.rs#L84C10-L84C10)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/binop.rs

// @audit alreabraic
129:    // and add alreabraic relation
```
GitHub*: [129](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/binop.rs#L129)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/context.rs

// @audit growding
188:    // now we will select in the growding width manner
```
GitHub*: [188](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/context.rs#L188)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/log.rs

// @audit convension
313:    // if we read then use the same value - convension!
```
GitHub*: [313](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/log.rs#L313)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/log.rs

// @audit absort
504:    // we absort with replacement
```
GitHub*: [504](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/log.rs#L504)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs

// @audit absort
222:    // NB: Etherium virtual machine is big endian;

// @audit accesed
224:    // every memory cell is 32 bytes long, the first cell to be accesed has idx = offset / 32

// @audit arithmetization
512:    // then it's just too heavy in our arithmetization compared to some implementation of shift

// @audit unalignment
516:    // b1000000.. LSB first if unalignment is 0
517:    // b0100000.. LSB first if unalignment is 1
```
GitHub*: [222](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L222C1-L222C51), [224](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L224C10-L224C10), [512](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L512C8-L512C8), [516](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L516C1-L517C48)

```rust
File: code/era-zkevm_circuits/src/main_vm/cycle.rs

// @audit constrainted
179:    // for DST0 it's possible to have opcode-constrainted updates only into registers

```
GitHub*: [179](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/cycle.rs#L179)

```rust
File: code/era-zkevm_circuits/src/main_vm/decoded_opcode.rs

// @audit immediatelly
52:    // decode and resolve condition immediatelly

// @audit constrainted
406:    // - conditional 3bit integer (constrainted later by lookup table)

// @audit booleanity
417:    // booleanity constraints

```
GitHub*: [52](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/decoded_opcode.rs#L52), [406](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/decoded_opcode.rs#L406), [417](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/decoded_opcode.rs#L417)

```rust
File: code/era-zkevm_circuits/src/main_vm/mod.rs

// @audit committments
112:    // here we have too large state to run self-tests, so we will compare it only against the full committments

// @audit succesfully
121:    // bootloader must exist succesfully

// @audit mergeable
166:    // but we CAN still check that it's potentially mergeable, basically to check that witness generation is good
```
GitHub*: [112](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/mod.rs#L112), [121](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/mod.rs#L121), [166](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/mod.rs#L166)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcode_bitmask.rs

// @audit computeable
14: // opcode defs only provide runtime-computeable variable, so we have to pin ISA version and assert
```
GitHub*: [14](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcode_bitmask.rs#L14)

```rust
File: code/era-zkevm_circuits/src/main_vm/pre_state.rs

// @audit happed
290:    // we did all the masking and "INVALID" opcode must never happed
```
GitHub*: [290](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/pre_state.rs#L290)

```rust
File: code/era-zkevm_circuits/src/main_vm/state_diffs.rs

// @audit bouds
44:    // memory bouds
```
GitHub*: [44](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/state_diffs.rs#L44)

```rust
File: code/era-zkevm_circuits/src/ram_permutation/mod.rs

// @audit passthrought
56:    // passthrought must be trivial

83:    // passthrought must be trivial
```
GitHub*: [56](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ram_permutation/mod.rs#L56), [83](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ram_permutation/mod.rs#L83)

```rust
File: code/era-zkevm_circuits/src/sort_decommittment_requests/mod.rs

// @audit fuunction
52:    // as usual we assume that a caller of this fuunction has already split input queue,

// @audit comsumed
53:    // so it can be comsumed in full

// @audit accross
331:        // We know that timestamps are unique accross logs, and are also the same between write and rollback

// @audit ascedning
334:        // always ascedning

// @audit convension
362:        record_to_add.is_first = Boolean::allocated_constant(cs, true); // we use convension to be easier consistent with out of circuit part

// @audit convension
389:        record_to_add.is_first = Boolean::allocated_constant(cs, true); // we use convension to be easier consistent with out of circuit part
```
GitHub*: [52-53](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/sort_decommittment_requests/mod.rs#L52C1-L53C37),[331](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/sort_decommittment_requests/mod.rs#L334), [334](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/sort_decommittment_requests/mod.rs#L334), [362](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/sort_decommittment_requests/mod.rs#L362), [389](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/sort_decommittment_requests/mod.rs#L389)

```rust
File: code/era-zkevm_circuits/src/storage_application/mod.rs

// @audit everythin
429:        // we can decompose everythin right away
```
GitHub*: [429](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/storage_application/mod.rs#L429)

```rust
File: code/era-zkevm_circuits/src/storage_validity_by_grand_product/mod.rs

// @audit passthrought
209:    // passthrought must be trivial

// @audit passthrought
279:    // passthrought must be trivial

// @audit immediatelly
589:    // increment it immediatelly

// @audit definatelly
780:    // and if it's read then it's definatelly at depth 0

// @audit definately
852:    // we definately read non-trivial, and that is on depth 0, so set to true

// @audit convensions
907:    // reset flag to match simple witness generation convensions
```
GitHub*: [209](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/storage_validity_by_grand_product/mod.rs#L209), [279](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/storage_validity_by_grand_product/mod.rs#L279), [589](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/storage_validity_by_grand_product/mod.rs#L589), [780](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/storage_validity_by_grand_product/mod.rs#L780), [852](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/storage_validity_by_grand_product/mod.rs#L852), [907](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/storage_validity_by_grand_product/mod.rs#L907)

### [N&#x2011;04] Wrong description

_There are 1 instances of this issue:_

```solidity
File: code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol

20:    /// - Bytecode words length is not odd

```

Description says that  `Bytecode words length is not odd` but the implementation checks bytecode length in words must be odd.

```solidity

27:        require(bytecodeLenInWords % 2 == 1, "ps"); // bytecode length in words must be odd

```

_GitHub_: [20](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L20)

### [N&#x2011;05] Combining the declaration and assignment of the `txHash` variable into a single line in a Solidity function `code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#upgrade()`

_There are 1 instances of this issue:_

```solidity
File: code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol

33:        bytes32 txHash;
34:        txHash = _setL2SystemContractUpgrade(
35:            _proposedUpgrade.l2ProtocolUpgradeTx,
36:            _proposedUpgrade.factoryDeps,
37:            _proposedUpgrade.newProtocolVersion
38        );
```

_GitHub_: [33](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L33C1-L38C11)

The code change is a stylistic adjustment that combines the declaration and assignment of the `txHash` variable into a single line. It is primarily improve code readability and conciseness.

Suggested Change:

```solidity
    bytes32 txHash = _setL2SystemContractUpgrade(
        _proposedUpgrade.l2ProtocolUpgradeTx,
        _proposedUpgrade.factoryDeps,
        _proposedUpgrade.newProtocolVersion
    );
```

### [N&#x2011;06] Open TODOs
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

*There are 4 instances of this issue:*

```solidity
File: code/contracts/ethereum/contracts/zksync/Config.sol

35: // TODO: change constant to the real root hash of empty Merkle tree (SMA-184)

```
*GitHub*: [35](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Config.sol#L35C1-L35C78)

```rust
File: code/era-zkevm_circuits/src/base_structures/vm_state/saved_context.rs

274:        // TODO: use more optimal allocation for bytes

```
*GitHub*: [274](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/base_structures/vm_state/saved_context.rs#L274C1-L274C55)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/log.rs

507:    // TODO: may be decide on length specialization

```
*GitHub*: [507](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/log.rs#L507)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs

1296:    // TODO: may be decide on length specialization

```
*GitHub*: [1296](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs#L1296C1-L1296C52)


### [N&#x2011;07] Remove commented out code

*There are 37 instances of this issue:*

```rust
File: code/era-zkevm_circuits/src/demux_log_queue/mod.rs

265:    // use crate::base_structures::log_query::LOG_QUERY_ABSORBTION_ROUNDS;
266:    // let mut optimizer = SpongeOptimizer::<F, R, 8, 12, 4, 6>::new(LOG_QUERY_ABSORBTION_ROUNDS * limit);


268:    for _ in 0..limit {
269:        // debug_assert!(optimizer.is_fresh());

315:        // rollup_storage_queue.push_encoding_with_optimizer_without_changing_witness(
316:        //     cs,
317:        //     popped.1,
318:        //     execute_rollup_storage,
319:        //     LogType::RollupStorage as usize,
320:        //     &mut optimizer
321:        // );
322:        // events_queue.push_encoding_with_optimizer_without_changing_witness(
323:        //     cs,
324:        //     popped.1,
325:        //     execute_event,
326:        //     LogType::Events as usize,
327:        //     &mut optimizer
328:        // );
329:        // l1_messages_queue.push_encoding_with_optimizer_without_changing_witness(
330:        //     cs,
331:        //     popped.1,
332:        //     execute_l1_message,
333:        //     LogType::L1Messages as usize,
334:        //     &mut optimizer
335:        // );
336:        // keccak_calls_queue.push_encoding_with_optimizer_without_changing_witness(
337:        //     cs,
338:        //     popped.1,
339:        //     execute_keccak_call,
340:        //     LogType::KeccakCalls as usize,
341:        //     &mut optimizer
342:        // );
343:        // sha256_calls_queue.push_encoding_with_optimizer_without_changing_witness(
344:        //     cs,
345:        //     popped.1,
346:        //     execute_sha256_call,
347:        //     LogType::Sha256Calls as usize,
348:        //     &mut optimizer
349:        // );
350:        // ecdsa_calls_queue.push_encoding_with_optimizer_without_changing_witness(
351:        //     cs,
352:        //     popped.1,
353:        //     execute_ecrecover_call,
354:        //     LogType::ECRecoverCalls as usize,
355:        //     &mut optimizer
356:        // );

391:        // // we enforce optimizer in this round, and it clears it up
392:        // optimizer.enforce(cs);

397:    // checks in "Drop" interact badly with some tools, so we check it during testing instead
398:    // debug_assert!(optimizer.is_fresh());

440:        // queue.head = <[Num<F>; T]>::conditionally_select(cs, bit, &exec_queue.head, &queue.head);
```
*GitHub*: [265, 269](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/demux_log_queue/mod.rs#L265C1-L269C48),[315](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/demux_log_queue/mod.rs#L315C9-L356C14),[391](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/demux_log_queue/mod.rs#L391C9-L392C34),[397](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/demux_log_queue/mod.rs#L397C1-L398C44), [440](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/demux_log_queue/mod.rs#L440C5-L440C101)

```rust
File: code/era-zkevm_circuits/src/ecrecover/mod.rs

53:    // pub fn empty() -> Self {
54:    //     Self {
55:    //         input_page: UInt32::<E>::zero(),
56:    //         input_offset: UInt32::<E>::zero(),
57:    //         output_page: UInt32::<E>::zero(),
58:    //         output_offset: UInt32::<E>::zero(),
59:    //     }
60:    // }

833:            // let owned_cs = ReductionGate::<F, 4>::configure_for_cs(owned_cs, GatePlacementStrategy::UseSpecializedColumns { num_repetitions: 8, share_constants: true });

859:            // let owned_cs = DotProductGate::<4>::configure_for_cs(owned_cs, GatePlacementStrategy::UseSpecializedColumns { num_repetitions: 1, share_constants: true });

```
*GitHub*: [53](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/mod.rs#L53C2-L60C9), [833](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/mod.rs#L833C17-L833C17), [859](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/mod.rs#L859C14-L859C14)

```rust
File: code/era-zkevm_circuits/src/keccak256_round_function/mod.rs

388:        // ---------------------------------
```
GitHub*: [388](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/keccak256_round_function/mod.rs#L388)

```rust

File: code/era-zkevm_circuits/src/linear_hasher/input.rs

73:    // #[serde(bound(
74:    //     serialize = "CircuitQueueRawWitness<F, LogQuery<F>, 4, LOG_QUERY_PACKED_WIDTH>: serde::Serialize"
75:    // ))]
76:    // #[serde(bound(
77:    //     deserialize = "CircuitQueueRawWitness<F, LogQuery<F>, 4, LOG_QUERY_PACKED_WIDTH>: serde::de::DeserializeOwned"
78:    // ))]
```
GitHub*: [73](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/linear_hasher/input.rs#L73C1-L78C11)

```rust

File: code/era-zkevm_circuits/src/log_sorter/mod.rs

221:    // dbg!(compact_form.create_witness());
```
GitHub*: [221](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/log_sorter/mod.rs#L221)

```rust

File: code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs

615:    // if crate::config::CIRCUIT_VERSOBE {
616:    //     if execute.witness_hook(&*cs)().unwrap() {
617:    //         dbg!(code_format_exception.witness_hook(&*cs)().unwrap());
618:    //         dbg!(call_now_in_construction_kernel.witness_hook(&*cs)().unwrap());
619:    //         dbg!(fat_ptr_expected_exception.witness_hook(&*cs)().unwrap());
620:    //     }
621:    // }

696:    // if crate::config::CIRCUIT_VERSOBE {
697:    //     if execute.witness_hook(&*cs)().unwrap() {
698:    //         dbg!(opcode_carry_parts.preliminary_ergs_left.witness_hook(&*cs)().unwrap());
699:    //         dbg!(growth_cost.witness_hook(&*cs)().unwrap());
700:    //     }
701:    // }

713:    // if crate::config::CIRCUIT_VERSOBE {
714:    //     if execute.witness_hook(&*cs)().unwrap() {
715:    //         dbg!(ergs_left_after_growth.witness_hook(&*cs)().unwrap());
716:    //     }
717:    // }

780:    // if crate::config::CIRCUIT_VERSOBE {
781:    //     if execute.witness_hook(&*cs)().unwrap() {
782:    //         dbg!(exception.witness_hook(&*cs)().unwrap());
783:    //         dbg!(ergs_left_after_extra_costs.witness_hook(&*cs)().unwrap());
784:    //     }
785:    // }

850:    // if crate::config::CIRCUIT_VERSOBE {
851:    //     if execute.witness_hook(&*cs)().unwrap() {
852:    //         dbg!(ergs_remaining_after_decommit.witness_hook(&*cs)().unwrap());
853:    //     }
854:    // }
```
GitHub*: [615](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs#L615C1-L621C9), [696](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs#L696C1-L701C9), [713](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs#L713C1-L717C9), [780](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs#L780C3-L785C9), [850](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs#L850C1-L854C9)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/ret.rs

291:    // -----------------------------------------
```
GitHub*: [388](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/ret.rs#L291)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/add_sub.rs

69:    // let add_relation = AddSubRelation {
70:    //     a: common_opcode_state.src0_view.u32x8_view,
71:    //     b: common_opcode_state.src1_view.u32x8_view,
72:    //     c: addition_result_unchecked,
73:    //     of
74:    // };


76:    // let sub_relation = AddSubRelation {
77:    //     a: common_opcode_state.src1_view.u32x8_view,
78:    //     b: subtraction_result_unchecked,
79:    //     c: common_opcode_state.src0_view.u32x8_view,
80:    //     of: uf,
81:    // };
```
GitHub*: [69](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/add_sub.rs#L69C5-L81C10)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/mul_div.rs

240:    // if crate::config::CIRCUIT_VERSOBE {
241:    //     if (should_apply_mul.witness_hook(&*cs))().unwrap_or(false) || (should_apply_div.witness_hook(&*cs))().unwrap_or(false) {
242:    //         dbg!(mul_low_unchecked.witness_hook(&*cs)().unwrap());
243:    //         dbg!(mul_high_unchecked.witness_hook(&*cs)().unwrap());
244:    //         dbg!(quotient_unchecked.witness_hook(&*cs)().unwrap());
245:    //         dbg!(remainder_unchecked.witness_hook(&*cs)().unwrap());
246:    //     }
247:    // }
```
GitHub*: [240](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/mul_div.rs#L240C1-L247C9)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/mul_div.rs

368:    // if crate::config::CIRCUIT_VERSOBE {
369:    //     if (should_apply_mul.witness_hook(&*cs))().unwrap_or(false) || (should_apply_div.witness_hook(&*cs))().unwrap_or(false) {
370:    //         dbg!(result_0.witness_hook(&*cs)().unwrap());
371:    //         dbg!(result_1.witness_hook(&*cs)().unwrap());
372:    //     }
373:    // }
```
GitHub*: [368](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/mul_div.rs#L368C1-L373C9)

```rust
File: code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs

367:        // if crate::config::CIRCUIT_VERSOBE {
368:        //     if should_apply.witness_hook(&*cs)().unwrap() {
370:        //         dbg!(should_read_a_cell.witness_hook(&*cs)().unwrap());
371:        //         dbg!(should_read_b_cell.witness_hook(&*cs)().unwrap());
372:        //     }
373:        // }

409:        // if crate::config::CIRCUIT_VERSOBE {
410:        //     if should_apply.witness_hook(&*cs)().unwrap() {
411:        //         if should_read_a_cell.witness_hook(&*cs)().unwrap() {
412:        //             dbg!(initial_state.map(|el| Num::from_variable(el)).witness_hook(&*cs)().unwrap());
413:        //             dbg!(final_state_candidate.witness_hook(&*cs)().unwrap());
414:        //         }
415:        //     }
416:        // }

474:        // if crate::config::CIRCUIT_VERSOBE {
475:        //     if should_apply.witness_hook(&*cs)().unwrap() {
476:        //         if should_read_b_cell.witness_hook(&*cs)().unwrap() {
477:        //             dbg!(initial_state.map(|el| Num::from_variable(el)).witness_hook(&*cs)().unwrap());
478:        //             dbg!(final_state_candidate.witness_hook(&*cs)().unwrap());
479:        //         }
480:        //     }
481:        // }

505:    // if crate::config::CIRCUIT_VERSOBE {
506:    //     if should_apply.witness_hook(&*cs)().unwrap() {
507:    //         dbg!(new_memory_queue_length_after_read.witness_hook(&*cs)().unwrap());
508:    //     }
509:    // }

549:        // if crate::config::CIRCUIT_VERSOBE {
550:        //     if should_apply.witness_hook(&*cs)().unwrap() {
551:        //         if should_read_a_cell.witness_hook(&*cs)().unwrap() {
552:        //             let src: [_; 32] = src.to_vec().try_into().unwrap();
553:        //             dbg!(mask_bit.witness_hook(&*cs)().unwrap());
554:        //             let src_buffer = src.witness_hook(&*cs)().unwrap();
555:        //             dbg!(hex::encode(&src_buffer));
556:        //             let dst_buffer = selected_word.witness_hook(&*cs)().unwrap();
557:        //             dbg!(hex::encode(&dst_buffer));
558:        //         }
559:        //     }
600:        // }

725:        // if crate::config::CIRCUIT_VERSOBE {
726:        //     if should_apply.witness_hook(&*cs)().unwrap() {
727:        //         if execute_write.witness_hook(&*cs)().unwrap() {
728:        //             dbg!(initial_state.map(|el| Num::from_variable(el)).witness_hook(&*cs)().unwrap());
729:        //             dbg!(final_state_candidate.witness_hook(&*cs)().unwrap());
730:        //         }
731:        //     }
732:        // }

794:        // if crate::config::CIRCUIT_VERSOBE {
795:        //     if should_apply.witness_hook(&*cs)().unwrap() {
796:        //         if execute_unaligned_write.witness_hook(&*cs)().unwrap() {
797:        //             dbg!(initial_state.map(|el| Num::from_variable(el)).witness_hook(&*cs)().unwrap());
798:        //             dbg!(final_state_candidate.witness_hook(&*cs)().unwrap());
799:        //         }
800:        //     }
801:        // }

896:    // if crate::config::CIRCUIT_VERSOBE {
897:    //     if should_apply.witness_hook(&*cs)().unwrap() {
898:    //         dbg!(new_memory_queue_length_after_writes.witness_hook(&*cs)().unwrap());
899:    //     }
900:    // }

1014:        // if crate::config::CIRCUIT_VERSOBE {
1015:        //     dbg!(offset.witness_hook(&*cs)().unwrap());
1016:        //     dbg!(start.witness_hook(&*cs)().unwrap());
1017:        //     dbg!(length.witness_hook(&*cs)().unwrap());
1018:        // }
```
GitHub*: [367](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L367C1-L372C13), [409](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L409C1-L416C13), [474](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L474C1-L481C13), [505](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L505C1-L509C9), [549](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L549C1-L560C13), [725](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L725C1-L732C13), [794](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L794C1-L801C13), [896](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L896C5-L900C9), [1014](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L1014C1-L1018C13)

```rust
File: code/era-zkevm_circuits/src/main_vm/cycle.rs

767:    // dbg!(new_state.memory_queue_state.witness_hook(&*cs)().unwrap());
768:    // dbg!(new_state.memory_queue_length.witness_hook(&*cs)().unwrap());

777:        // dbg!(_wit.memory_queue_state);
778:        // dbg!(_wit.memory_queue_length);
```
GitHub*: [767](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/cycle.rs#L767C1-L768C74), [777](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/cycle.rs#L777C1-L778C43)

```rust
File: code/era-zkevm_circuits/src/main_vm/utils.rs

150:            // dbg!(timestamp.witness_hook(&*cs)().unwrap());
151:            // dbg!(location.witness_hook(&*cs)().unwrap());
```
GitHub*: [150](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/utils.rs#L150C1-L151C61)

```rust
File: code/era-zkevm_circuits/src/storage_application/input.rs

112:    // #[serde(bound(
113:    //     serialize = "CircuitQueueRawWitness<F, LogQuery<F>, 4, LOG_QUERY_PACKED_WIDTH>: serde::Serialize"
114:    // ))]
115:    // #[serde(bound(
116:    //     deserialize = "CircuitQueueRawWitness<F, LogQuery<F>, 4, LOG_QUERY_PACKED_WIDTH>: serde::de::DeserializeOwned"
117:    // ))]
```
GitHub*: [112](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/storage_application/input.rs#L112C5-L117C11)

```rust
File: code/era-zkevm_circuits/src/storage_validity_by_grand_product/mod.rs

495:    // dbg!(compact_form.create_witness());

990:    // use boojum::cs::EmptyToolbox;
```
GitHub*: [495](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/storage_validity_by_grand_product/mod.rs#L495), [990](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/storage_validity_by_grand_product/mod.rs#L990)

```rust
File: code/era-zkevm_circuits/src/tables/integer_to_boolean_mask.rs

    // let num_bits = 2;
    // let mut all_keys = Vec::with_capacity(1 << num_bits);
    // for integer in 0..(1u64 << num_bits) {
    //     let key = smallvec::smallvec![F::from_u64_unchecked(integer as u64)];
    //     all_keys.push(key);
    // }


    // LookupTable::new_from_keys_and_generation_function(
    //     &all_keys,
    //     VM_SUBPC_TO_BITMASK_TABLE_NAME.to_string(),
    //     1,
    //     |keys| {
    //         let a = keys[0].as_u64_reduced();


    //         let result = if a == 0 {
    //             0u64
    //         } else {
    //             1u64 << (a - 1) // 1 in some position
    //         };


    //         smallvec::smallvec![F::from_u64_unchecked(result), F::ZERO]
    //     },
    // )
```
GitHub*: [68](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/tables/integer_to_boolean_mask.rs#L68C1-L90C9)

### [N&#x2011;08] Unnecessary `println`

_There are 1 instances of this issue:_

```rust
File: code/era-zkevm_circuits/src/main_vm/cycle.rs

49:        println!("------------------------------------------------------------");
```
_GitHub_: [49](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/cycle.rs#L49)

### [N&#x2011;09] Wrong `require` statement
The following statement is always true

_There are 1 instances of this issue:_

```rust
File: code/system-contracts/contracts/L1Messenger.sol

206:        require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
```
_GitHub_: [206](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L206)