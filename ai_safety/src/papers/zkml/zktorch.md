# ZKTorch: Compiling ML Inference to Zero-Knowledge Proofs via Parallel Proof Accumulation

## Context 

Existing zkML approaches sit at two extremes:
- general purpose ZK-SNARK compilers like EZKL that can handle diverse models but are slow 
- specialized crypto protocols that are efficient but only work for specific types 
  - zkCNN for convolutions 
  - zkLLM for attention
  
## Goal 

zkML system that is both generic and efficient 
- compile ml models into modular crypto primitives 
- folding per-layer proofs into a single compact proof

## Contributions 

__Modular Basic block architecture supporting 61 pytorch layers__
- create a lib containing 20 basic blocks that are common crypto protocols for ML operations 
- divided into 7 categories 
  - Arithmetic/Boolean: add, sub, mul, div, mulscalar, sum, eq, boolean check ... 
  - matrix multiplication: 
    - CQLin when one matrix is fixed like convolution weights
    - Matmul for general matrix multiplication based on the Aurora protocol
  - tables: CQ and CQ2 for non-linear operations like ReLU, GeLU, softmax and range checks
  - shape operations: permute, concat, copy constraint to prove that tensor reshaping 
    and element copying were done correctly 
  - sorting: OneToOne and Ordered used for ArgMax and TopK layers
- Each of 61 supported ONNX layer types are transposed into a composition of these basic blocks
- Cover all models in the MLPerfInferenceEdge benchmark suite

__Parallel Proof Accumulation (Generalized Mira)__
- each basic block produces its own proof hence full model generates thousands of proofs 
- uses the Mira accumulation scheme 
  - folds multiple proofs into a single one 
  - pairing based recursive SNARK
- Technical contribution is generalizing Mira from sequential to parallel accumulation 
  - original Mira folds one SNARK proof into an existing accumulator instance which is done sequentially
  - ZKTorch observes that a SNARK proof is just a special case of an accumulator 
  - the new Mira can fold two arbitrary accumulator instances together 
  - enables a merkle-tree style parallel proof reduction
  - proofs of the same basic block type are aggregated in a tree structure across multiple threads
  - this reduces computing time
- 13 out of the 20 basic blocks support this parallel accumulation technique
  - remaining 7 blocks are avoided as much as possible by the compiler 
  - they're present only on 18 of the 61 supported ml layers

__Optimizing Compiler and Transpiler__
- the compiler takes an ONNX model applies rule-based graph transformations 
  - optimize for proving time rather than inference time
- Specific transformation rules are implemented to optimize computations

## Limitations 

- very long proving times for complex models 
- CPU-only, no GPU acceleration is used
- BN254 field only
- Rule-based compiler, no search-based optimizationso compiler may miss optimization opportunities 
- Verification times are non-trivial
  - 64s for GPT-j
  - over 100s for LlaMA-2
- Fixed-point quantization only
