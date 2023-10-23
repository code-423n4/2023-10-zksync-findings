QA[0]: Unimplemented and todo functions found in zksync circuits, better avoid or remove such functions

link: https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/era-zkevm_circuits/src/base_structures/register/mod.rs#L79-L86 

Explanation: Incomplete Development, but temporary solution. In some cases, unimplemented functions or 'todo' comments may serve as temporary placeholders, but in later space they may lead to unneccesary panic.

QA[1]: It would be better checking context stack depth is not greater than `VM_MAX_STACK_DEPTH`.

link: https://github.com/code-423n4/2023-10-zksync/blob/72f5f16ed4ba94c7689fe38fcb0b7d27d2a3f135/code/era-zkevm_circuits/src/base_structures/vm_state/callstack.rs#L30

```rs
pub fn is_full<CS: ConstraintSystem<F>>(&self, cs: &mut CS) -> Boolean<F> {
        let max_depth =
            UInt32::allocated_constant(cs, zkevm_opcode_defs::system_params::VM_MAX_STACK_DEPTH);
        UInt32::equals(cs, &self.context_stack_depth, &max_depth)
    }
```
Explanation: If a context stack depth is greater than max depth, this functions does return false but the actual stack is filled.

QA[2]: Redundant conditional code in opcodes 
link: https://github.com/matter-labs/era-zkevm_circuits/blob/4fba537ccecc238e2da9c80844dc8c185e42466f/src/main_vm/opcodes/mod.rs#L162-L164
Explanation: The code in the else block doesn't run in any way cause the condition is always going to be true since the variable addition is always within 15.
```rs
        } else {
                let zero_num = Num::zero(cs);
                Num::enforce_equal(cs, &zero_num, &intermidiate_overflow.into_num());
            }
```

QA[3]: Use constant declared variables instead of plain numbers while dividing array into chunks in sha256 and keccak crates.

Valid use of Constants before using array chunks: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/keccak256_round_function/mod.rs#L531-L546

Instances are: 

link: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/keccak256_round_function/mod.rs#L565
link: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/sha256_round_function/mod.rs#L216-L219

QA[4]:  Should try using variant Boolean checks for shift left instead of Boolean opcode check. Since its return is true for any variant of SHL.

valid use and source function links
valid link: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/add_sub.rs#L34-L41
source link: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcode_bitmask.rs#L59C1-L59C1
link: https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/shifts.rs#L24-L52