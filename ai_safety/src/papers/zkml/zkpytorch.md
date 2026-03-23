# zkPyTorch: A Hierarchical Optimized Compiler for Zero-Knowledge Machine Learning (2025)

- https://eprint.iacr.org/2025/535.pdf

## Goal and contributions

Goal: design a compiler that transform standard pytorch code into zkp circuits automatically

- ZKP preprocessor 
  - convert ONNX model to DAG representations 
  - each node is a primitive operation and edge represents data flow
  - inject additional information to the DAG to allow circuit generation
- ZKP-friendly quantization 
  - optimized quantization strategy 
  - enable ZKP execution in smaller finite fields such as M61
  - keep very high cosine similarity
- Circuit Optimizations
  - Batch multiple inference operations together reducing complexity
  - FFT-based convolutions and lookup tables to accelerate primitive operations 
  - the ZKP circuit is decomposed into multiple parallel sub-circuits enables parallelization

## Quantization strategy 

- Previous zkml systems such as zkCNN, zkLLM, ZENO used fixed point representations
  - f32 range is from $[2^{-126}, 2^{127}]$
  - 32-bit fixed point integer range is $[2^{-16}, 2^{16}]$
  - quantized model are forced to work on large finite fields like BN254 (~254 bits)
  - arithmetic in large field is slow because CPUs are optimized for
    - 32/64 bits integers, native ALU operations 
    - floating point - dedicated FPUs, SIMD units
    - 254-256 bit primes like BN254 or BLS12-381 have no hardware support
