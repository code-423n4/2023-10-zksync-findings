L-01 Pointless check on Capacity_Bits in several circuit files due to it being a constant 

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/decoded_opcode.rs#L317

https://github.com/matter-labs/era-boojum/blob/2771569baab9a59690d88cee6ba9b295c8a1e4c4/src/field/goldilocks/mod.rs#L414



L-02 Deviation from the docs regarding writing to DST0 into memory 
In the docs, it says the following 

``` Apply opcodes, for DST0 it's possible to have opcode-constrainted updates only into registers, apply StateDiffsAccumulator, update the memory, update the registers, apply changes to VM state, such as ergs left, etc. push data to queues for other circuits. If an event has rollback then create the same event data but with rollback flag, enforce sponges. There are only 2 outcomes:

we have dst0 write (and may be src0 read), that we taken care above
opcode itself modified memory queue, based on outcome of src0 read in parallel opcodes either
do not use sponges and only rely on src0/dst0
can not have src0/dst0 in memory, but use sponges (UMA, near_call, far call, ret) ```

However, looking at the code the dst0 write restriction is also applied to log.rs

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/log.rs#L453



L-03 No check for limit <= U32 MAX in main vm circuit 

In each circuit, there is a check to make sure that the limit is U32 MAX. However, there is no check in the main VM circuit. This can potentially introduce unexpected behavior 




L-04 Misleading statement regarding the place of the sha256 round function circuit 
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/keccak256_round_function/mod.rs#L131
According to the diagram, the sha256 round function is not first in the queue 
https://github.com/code-423n4/2023-10-zksync/blob/main/docs/Circuits%20Section/Circuits/flowchart.png



L-05 No check on empty queue can cause the circuit to not run as efficiently as it could 
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/keccak256_round_function/mod.rs#L135
https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/sha256_round_function/mod.rs#L124

Both the sha 256 and the keccak 256 circuits check whether the queue is empty. However, no other circuit makes the same check. This can cause the entire circuit system to run as efficiently as it could 


L-06 CFG should be removed from storage validity circuit 

https://github.com/matter-labs/era-zkevm_circuits/blob/4fba537ccecc238e2da9c80844dc8c185e42466f/src/storage_validity_by_grand_product/mod.rs#L4

```#[cfg(test)]``` is used to conditionally include code only when you're running tests. It's a way to tell the Rust compiler to compile and include the annotated code only during test runs and not in the final binary. However it serves no purpose in this specific instance 


L-07 No check for assert_eq!(lhs.len(), rhs.len());  in ram permutation circuits

The ramp permutation circuit uses lhs and rhs to create challenges but does not implement a check to make sure that lhs.len == rhs.len like in other circuits such as sort decommiter


L-08 A circuit should not have a higher limit than a preceding circuit 
According to the docs, each circuit has a cycle limit. But it does not make sense to have a circuit that has a higher limit than its preceding circuit e.g. Ram permutations and Sort Decomitter 

L-09 No 0 limb check in near_call.rs 

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/main_vm/opcodes/call_ret_impl/far_call.rs#L1222

Near call does not have a 0 limb check like in far call. To my understanding, this is due to an assumption regarding the data size of far call. However, this could potentially open the door for unexpected behavior or a bad actor to overload the circuit with limitless near calls

