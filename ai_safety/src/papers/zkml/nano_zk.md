# NanoZK

- [https://arxiv.org/pdf/2603.18046](https://arxiv.org/pdf/2603.18046)

## Context

Being able to prove that a model you're using match the specifications it claim to have using zk proofs.
Currently approach encode an entire transformer forward pass as one arithmetic circuit
which contains billions of constraints making it impractical.
Prior work zkGPT proves a GPT-2 forward pass in ~1 hour.

## Goals

- Faster proof system
- Constant proof size
- Compatible with selective/partial verification when full proving is too expensive
- No compromise on model output quality

## Contributions

1. Layerwise proof framework with compositional soundness. 
  - decompose the monolithic proof problem into independent per-layer proofs
  - per-layer proofs are glued via commitments
  - formal soundness theorem govern the proof composition
  
2. Zero-degradation lookup table approximations 
  - replace non arithmetic operations with $2^{16}$ Plookup tables
  - zero perplexity impact

3. Fisher information-guided layer selection 
  - Use the trace of the Fisher information matrix as a scalar importance score 
  - allow prioritizing which layers to prove when full verification is too expensive

### Layerwise proof framework

global soundness theorem
$$  ε_total ≤ Σ ε_ℓ + (L+2)·negl(λ) $$
- $ε_total$: total soundness 
- $ε_l$ represent soundness error for layer $l$
  - probability that a cheating prover convinces the verifier of a false statement at layer $l$
  - property of the chosen proof system Halo2 IPA
  - for 128-bit security each $ε_l <= 2^{-128}$
- $Σ ε_l$ represent union bound over the $L +2$ layers 
  - $L$ transformer layers + embedding + output head
  - cheating can happen at each layer so you add up the probability
- $negl(λ)$ is the probability of breaking SHA-256 
  - finding two inputs that have same hash 
  - negligible for $λ=128 (<= 2^{-128})$
- $(L+2)·negl(λ)$ covers the case where adversary does not cheat on layers proof but 
finds a SHA-256 collision to make two incompatible proofs look consistent
- With $λ=128$ and $L=32$ layers of GPT-2 
  - $ε_total = 68 * 2^{-128}$
  
### Lookup table approximations

- Non-linear layers cannot be represented by arithmetic circuits 
  - Softmax, GELU, LayerNorm...
  - this can be approximated by polynomials but error is accumulated through layers
  - NanoZK uses $2^{16}$ lookup tables
    - function values are precomputed over a fixed input range 
    - inputs are quatized to 16-bit fixed-point at runtime
    - lookup the output and verify correctness via Plookup-style range constraints in the circuit
    - Error is bounded only at input/output quantization boundaries
- empirical result gives $∆PPL = 0,00\%$ on WikiText-2 across all tested models 
  - PPL is perplexity
    - standard metric for language model quality 
    - if a model assigns probability 1.0 to every correct next token then PPL = 1 
    - a model guessing uniformly over 50,000 token vocabulary would have PPL = 50,000
    - for models tested here, WikiText-2 PPL ∈ [17-53] 
      - meaning it has roughly 17-53 equally likely choices at each token
  - 16-bit errors fall below the noise tolerance 
    - noise tolerance refers to the robustness of neural networks to small perturbations in their computations
    - this is an empirical observation
    - network trained with stochastic gradient descent, ddropout and floating-point rounding has already seen a lot "noise"
    - 16-bit quantization errors of ~$3x10^{-5}$ per operation is smaller than the gradient noise the model was trained with
  - softmax normalization caused per-entry absolute errors to cancel each other across the denominator sum

### Fisher information-guided layer selection

- When it's too expensive to prove all, which layers do we prioritize 
- NanoZK proposes using the Fisher information matrix as a layer importance score 
  - $I_ℓ = tr(F_ℓ) / |θ_ℓ|$   where   
    - $F_ℓ = E[∇{θ_ℓ} log p(y|x;θ) · ∇{θ_ℓ} log p(y|x;θ)ᵀ]$
  - Fisher matrix shows how much input/output changes with a small perturbation on model parameters
    - diagonal represent each parameter sensitivity 
    - off-diagonal represent correlation between pair of parameters
  - Scalar importance is used here to choose layers importance
    - this is the trace of (sum of diagonal entries) normalized by the number of parameters in layer $l$
    - we reduce to the trace because Fisher matrix is too big and too hard to use otherwise
- Layers with high Fisher infromation are more sensitive to perturbation
  - if an adversary manipulates them the output distribution is more impacted
  - layers with low fisher have less influence on outputs
- At 50% verif budget Fisher selection captures 62-86% of total Fisher mass 
  - for random selection of layers it's 51-79% 
  - gain is more pronounced on models when early layers are not too influential 
  - Fisher mass is the sum of importance scores across all layers
    - so n% of Fisher mass for 50% verif budget means that we selected 50% of the layers
    but they represent $n%$ of the total fisher mass 
  
## Results 

Proof generation performance 
- proof size and proof time are constant, independent from hidden dimension
- hidden dimension is the size of the output vector after each layer
  - larger $d$ means more parameters per layer

## Drawbacks 

- Proving time and native inference is roughly 60x
  - good for async auditing 
  - not live latency-sensitive inference
- Verification is slower 
  - 23ms vs 1ms for Groth16
  - the trade off is acceptable
- CPU-only implementation 
  - in theory GPU acceleration could substantially reduce proving time 
  - porting Halo2 IPA to GPU is non trivial 
  - inner product argument  involves sequential multi-scalar multiplications that
  don't parallelize cleanly across GPU cores 
- Selective verification enables adversarial targeting
  - Fisher selection is probabilistic not cryptographic 
  - an adversary that knows the partial verification strategy could target the low fisher layers
  - paper mitigation proposal: fisher top-k plus random auditing of the remainder help but does not give certainty
- GPT-2 scale only in practice
  - GPT-2 requires 3.2 minutes for 12 layers 
  - GPT-3 with 96 layers would require 25 minutes
