# 

## Libraries to turn AI model into cryptographic proof

__ezkl (Easy Zero Knowledge Library)__
- open-source tool for verifiable AI 
- compiler that takes an AI model in ONYX format and output a ZK-SNARK circuit 
- the engine behind ezkl is __Halo2.__

__Halo2.__
- low level cryptographic library to build whatever circuits you want
- it's a Rust crate
- you define your circuits in Rust by implementing the Circuit trait
- it's really low level to build circuits 
- it's hard to learn but the best for performance

__ZKLoRA__
- specialized protocol to verify LoRA (Low Rank Adaptation) weights 
- LoRA allows fine tuning without changing the weights of the base model
  - instead of changing original weights _W_, it adds two small matrices _A_ and _B_
  - _W_ is frozen 
  - during inference the output is the result of _W_ plus the output of _A_ and _B_
  - the "Rank" in LoRA refers to the rank of _A_ and _B_ which are tiny
  - rank 8 or 16 if often enough for _A_ and _B_
- You can have a base model _W_ that is public or licensed and a LoRA adapter that if private IP 
  - Adapters _A_ and _B_ are much smaller than _W_ hence easier to apply ZKP
- you can prove "This specific adapter was applied to this specific base model" without 
revealing the adapter's contents

## ZK Virtual Machines

__RISC Zero__
- implementation of a _ZK Virtual machine_
- instead of writing code in mathematical circuits developers write standard code (Rust, C++, Go)
  - it's compile to RISC-V and proven
- Uses STARK based proving to prove the correct execution of RISC-V machine code
- you write standard program and RISC Zero generates a proof that the program ran 
correctly and produced specific output
- it can be used to implement a universal circuit 
  - in standard circuit a model architecture is encoded in the circuit and only the weights are input
  - in a universal circuit both the architecture and the weights are circuits  
  - hence a universal circuit can prove that an inference run was correct for any model architecture with any weights
- you can use RISC Zero to implement a universal circuit which inputs would
  - a model code/architecture
  - its weights 
  - the input the inference run is working on
- downsides is that it's extremely slower

__SP1__
- another implementation of _ZK Virtual machine_
- direct rival of RISC Zero 
- rust-based zkVM that claims to be much faster

__Jolt and Lasso__ 
- a new technique that makes universal Circuits much faster using lookup tables
- this is arguably the most promising tech for universal zkML 

## Protocols 

Research constructions designed to fix the slowest part of zk-ML: __commitment verification__

## Tools that helps ZKP 

Massive models are still hard to prove. Some tools can alleviate the task.

__TeleSparse__
- Post-processing tools that uses Sparsification and Neural Teleportation
  - __Sparsification__
    - prune unnecessary neurons from the model
  - __Neural Teleportation__
    - map neurons to better mathematical ranges for ZKP
- it makes the model "lighter" for ZK prover
- reduces proof generation by 46% and memory usage by 67%
- it allows equal but sparse model to run on smaller device 
while providing proof that the logic remain the same as base model

## Libraries 

__Arkworks__
- comprehensive list of Rust crates for ZK programming
- ark-ff, ark-ec, ark-snark for finite fields, elliptic curve or snarks
- used to implement crypto research or build new cryptographic protocols
