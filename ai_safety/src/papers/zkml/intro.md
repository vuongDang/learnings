# ZKML

## Bottlenecks 

- Prover overhead
  - running a ML model inside a ZK circuit requires 1000-10000x more computing
  - it can take hours in a big server to generate the proof
- Math clash between AI and cryptography
  - ML models use floats to calculate slopes using calculus
  - cryptography hates decimals and uses finite fields 
  - Quantization is used to squish high precision floats into clunky integers 
  - it drops the accuracy of the ZK proven model
- Non-linear functions
  - models uses non-linear functions to gain performance 
    - ReLU: if x < 0 then 0 else x
    - Softmax: use exponents to turn scores into probabilities
    - Sigmoid: 1 / (1+e^(-x))
  - ZK cryptography is built on polynomials and are mainly good at addition and multiplication
  - it's difficult to express "if/then" or complex exponents using + and *
- RAM Explosion
  - ZK proofs keep every operation the model perform in its as a trace 
  before generating a proof 
  - it can require terabytes of RAM for a large language model

## Why is arithmetic over large finite fields slow

__No hardware support__
- 254-256 bit primes like BN254 or BLS12-381 have no hardware support
- arithmetic in large finite fields is slow because CPUs are optimized for
  - 32/64 bits integers, native ALU operations 
  - floating point - dedicated FPUs, SIMD units
  
1. modular reduction is expensive 
  - every addition and multiplication must be reduced mod p 
  - native reduction uses division is very slow 
  - even specialized algorithm like Barrett or Montgomery add overhead
2.  multiplication requires multi-precision arithmetic
- multiplying two 256-bit numbers produces 512-bit intermediate result 
  - this intermediate result need recution 
  - requires many 64-bit work operations
3. No native SIMD parallelism
  - SIMD (AVX2, AVX-512) works well on uniform small width-data 
  - adapting it to 256-bit field is non-trivial 
  - some specialized libs do it: blst, arkworks

__Workarounds__
- Friendly primes
  - for example Goldilocks $p = 2^{64} - 2^{32} + 1
  - reduction is cheaper due to prime structure
- Lookup tables 
  - Plookup, Lasso 
  - replace expensive nonlinearities with table lookups 
- Smaller integers
  - use smaller integers inside the field to reduce constraint coutns 
- hardware accelerators 
  - FPGAs/ASICs designed specifically for NTT and field opts 
- STARKs over binary fiels 
  - fields like $GF(2^{128})$ map better to hardware XOR operations
