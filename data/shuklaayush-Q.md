Instead of calculating `y` in the ecrecover circuit using `t`, it would probably be much simpler if `y` is taken as a non-deterministic input to the circuit and only the constrain y^2 = x^3 + b is checked

https://github.com/code-423n4/2023-10-zksync/blob/1fb4649b612fac7b4ee613df6f6b7d921ddd6b0d/code/era-zkevm_circuits/src/ecrecover/mod.rs#L286-L362