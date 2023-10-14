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