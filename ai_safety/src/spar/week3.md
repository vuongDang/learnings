# Week 3

Suggested research direction:

__Derive a framework that computes a metric indicating how different 
is a quantized model compared to its original version in the context of zkml__
- inspiration should be taken from EqBAB which calculates the 
how different are a model and its compressed version 
  - the quantization techniques are different 
    - all the intermediate computations are forced on finite fields in zkml 
    - standard quantization only force quantization on weights and potentially activation layer
  - quantization is only a part of the compression algorithm

__Steps__
- learn about the different existing quantization techniques
- understand better EqBab and how it could be adapted to our usecase

## Different quantization techniques

- GKR/Sumcheck-based 
- Halo2/Plonkish-based (fixed-point quantization)
- Circom/R1CS-based

### GKR/Sumcheck-Based: Affine Scale-Zero-Point Quantization

- Most used quantization techniques to prove llm-scale models
- Every floats in the model is mapped to a Q-bit integer 
through the affine relation  $x=S(q-z)$
  - $S$ is a floating point scale factor 
  - $z$ is an integer representing zero
- for example in zkGPT, $Q=16$ meaning that all values live in $[0, 2^16]$

__Implementations__
- zkGPT
- zkCNN
- zkLLM
- VeriLoRA


###  Halo2/Plonkish-Based: Fixed-Point Scaling Quantization

- Most accessible approach 
- a float $x$ is converted to a field element by multiplying 
by a power of 2
  - $q = round(x . 2^k)$
  - $k$ is the scale factor or fixed-point precision


__Implementations__
- EZKL 
- ZKML
- ZKFixedPointChip

__Pros and Cons__
- Simpler approach but less precise 
- EZKL convers ONNX model to zk-SNARK circuits directly, super practical

### Circom/R1CS-Based: Circuit-Level Integer encoding

- Oldest and used in early ZKML projects
- quantized model compile to 41CS proof systems

__Implementations__
- circomlib-ml 
- ZEN

__Pros an dcons__ 
- proof size is small 
- slower and does not scale well
- the ecosystem is mature


## EqBab

EqBaB: Efficient equivalence verification for compressed DNNs with bound propagation

- https://www.sciencedirect.com/science/article/pii/S0925231226001888

__Goal__
- Compute how similar is a model and its compressed version 
- Equivalence definition used: epsilon-equivalence 
  - $sup 𝐱∈X ‖Φ(𝐱) −̂ Φ(𝐱)‖< 𝜖$
- Context is for safety-critical deployment scenarios (aircraft, autonomous vehicles...)

__Contributions__
- The compared model are merged into a single network 
  - the output of this "network" outputs a discrepancy value
  - the equivalence problem is translated to a single netword bound computation problem
  - this allows the application of branch and bound verification technique __TODO__
- Computation of concrete value for maximum discrepancy  __TODO__
  - combines interval bound propagation with branch-and-bound strategy 
  - this compute upper and lower bounds on the worst-case output diff
- Experimental evaluation
  - compare 8 compression methods on MNIST and CIFAR-10 datasets

__Limitations__
- technique only work on piecewise linear activation functions (ReLU) 
  - for others such Sigmoid, GELU or SwigLU the approximations are too conservative
- Does not scale to large models
- no real world testing
  

## Next steps 

- Try to understand limitations and potential of the approach
- Familiarize myself with quantization techniques 
  - Quantized a minimal transformer model with the different techniques
  - Read/take notes, eventually a small presentation 
- Check EqBab in detail 
  - look for other papers doing something similar 
  - contact authors to see if they would accept to share their code
