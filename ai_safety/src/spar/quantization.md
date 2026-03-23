# Quantization in zkML

## Floats for classic LLMs

ML inputs and parameters are usually stored and computed using floats. 
Using floats is necessary to have smooth curves when computing gradient descent.

ML is trending towards less precision but better speed, so tends to
use smaller floats than before.

### Usage

- fp32, classic training standard  
- bf16, training on TPUs / GPUs 
- fp16 training + inference
- fp8 
- int8 for inference quantization
- int4 for aggressive  inference quantization

### Mixed precision
- weights stored in fp32
- forward/backward pass computed in fp16 or bf16
- this what PyTorch autocast does

### bf16/fp16
- bf16 has same exponent range as fp32 but less mantissa precision
- bf16 has no overflow issues
- bf16 is more training-stable than fp16
- Google/TPUs push it and is dominant format used

### bf16/fp16/fp32

### Binary Float Notation 
$$ value = sign * mantissa * 2^exponent $$
$$ 0.15625 = 1/8 + 1/32 = 0.00101 in binary
           = 1.01 * 2^(-3)  $$
- exponent is -3 
- mantissa is 1.01 (the leading 1 is implicit so stored part is just 01)
- sign is 0, positive

mantissa is float precision
exponent if float range

### fp32 (1 + 8 + 23 bits) 

sign | exponent (biased by 2^7 - 1 ) | mantissa
  0  |  01111100 (-3+127=124)   | 01000000000000000000000
Full bit pattern: 0 01111100 01000000000000000000000

### fp16 (1 + 5 + 10 bits)
sign | exponent (biased by 2^4 - 1) | mantissa
  0  |  0110 (-3+15=12)   | 0100000000
Full bit pattern: 0 01100 0100000000

### bf16 (1 + 8 + 7 bits)
sign | exponent (biased by 2^7 - 1) | mantissa
  0  |  01111100 (-3+127=124)   | 0100000
Full bit pattern: 0 01111100 0100000

bf16 is fp32 but with truncated mantissa

### Conclusion 

fp32: biggest range and precision, use 32 bits 
fp16: same precision as fp32 but smaller range 
bf16: same range as fp32 but lower precision

## Quantization Range, finite field

Finite fields are chosen for cryptographic properties:
- proof generation speed, proof size, verification time, security-level

Quantization scheme is chosen mostly for machine learning fidelity with $Q$-bit range:
- __model accuracy__
  - having big quantization range allow better precision
  - rounding errors have less impacts 
  - better similarity with base mode
  - error is acceptable depending on the use-case (medical or daily life)
- __computation speed__
  - bigger integers also mean slower computing speed
  - bigger lookup tables and more expensive range proofs
  - classic value of $Q$
    - $Q=8$ fast speed, lower accuracy. Used in some CNN systems
    - $Q=16$ sweet spot for most, zkGPT, zkLLM
    - $Q=32$, very high accuracy but proving is more expensive
    - below $Q=8$ you enter ternary networks where weights are restricted to {-1, 0, 1}
      - field multiplication is removed 
      - only adding and subbing 
      - proof time is super speed 
      - require dedicated training rather than transforming a base model
- __quantization granularity__
  - granularity of scale factors 
  - one scale factor per tensor, per channel/row, group of weights?
  - per tensor is simpler and cheapest to prove
  - rescaling is expensive and each rescale constant produces a range
  relation that must be proven via lookup
  - in classic quantization you usually have per-channel quantization for more accuracy 
  - in zkml proving cost is too high and uses per tensor quantization
  
- __proof system compatibility__
  - different operations have different proving costs 
    - addition is cheap 
    - multiplication is moderate  (one multiplication gate)
    - rounding/rescaling is expensive (range proof via lookup table)
    - non-linear function are the most expensive (lookup tables or polynomial approximations)
  - the quantization scheme should avoid expensive operations as much as possible

The only constraint is that quantization range should fit comfortably inside the field
which is usually the case. Smallest field used is M61, (61-bits)

Assuming we want to quantize as $Q$-bit integers.
- Choice of $Q$ gives the quantization range, the range which will contain
quantized values
- Bigger $Q$ gives better precision 
- Usually $Q$ is much smaller than the finite field chosen for ZKP
- Hence modulo operation do not happen in practice for AI quantization

### Choice of finite fields 

Curve fields usually used in zkML:
- BN254 
  - used by zkGPT, zkLLM
  - 100-110-bit security
  - slow proof generation on CPUs
    - 254-bits, requires 4x 64-bit chunks to represent
    - multiplication produces 508-bit intermediate result
    - modular reductions to bring it back to 254 bits requires more addition/multiplication 
    - around 50-100 cycles is necessary for 1 field multiplication
  - Pairing-base SNARKs
    - there is a pairing relation in the finite field 
    - constant-size proof and constant-time proof verification
    - requires setup and slower proof generation
- BLS12-381
  -used by VeriLoRA, Ethereum, Zcash
  - more secure than BN254
  - was designed after finding a weakness in BN254
  - field operations are more expensive (381-bit representation)
- 190-bit security
- M61 (Mersenne-61)
  - proof generation is fast
    - optimized for CPU 
      -  61-bit field arithmetic 
      - fits into 64 bits register
      - one M61 multiplication is: 1 multiply, 1 addition and 1 bit shift for Mersenne reduction 
      - so 3-4 CPU cycles
    - Mersenne modulo reduction is almost free
      - reduction mod $2^k - 1$ is super cheap
      - for $v$ slightly larger than $2^{61} -1$ 
      - you can reduce $v$ by:
        - split $v$ into it's low and high bits 
        - $v mod(2^{61} -1) = v_low + v_high$
        - this works because $2^61 === 1 mod (2^{61}-1)$
  - Do not have a pairing
    - cannot build polynomial commitments schemes (KZG) or Groth16 proofs
    - can't have succinct proofs: proofs are bigger and slower to verify
    - use other commitment schemes that work over any fields
      - FRI for STARKs: use hash-based systems rather than elliptic curves
      - Hyrax/IPA
  - used by: STARK-based and GKR-based
    - 61-bit security is enough since it's used with  Schwartz-Zippel lemma
    - the probability to randomly find a root of a polynomial of degree d from finite field $F_p$ is $d/p$
    - 
  
Actually most of implementations that use fields with pairings chose so
because efficient blockchain libraries use them.
Current zkML research move away from these fields.

## ZKML Quantization: GKR/Sumcheck Approach 

Used by zkGPT, zkCNN and zkLLM

zkGPT uses $Q = 16$ so all quantized values are in $[0, 65536]$


__Order__
1. Assign scale factors 

__Concrete example__
$$
W = [  0.362   -0.001    0.572 ]      x = [ 1.5 ]       Q = 8
    [ -0.122    0.158    0.327 ]          [ 0.8 ]
                                          [ 2.1 ]
$$

### Scale factor 

Each tensor (weights, inputs, outputs) gets its own scale factor $S$ and zero-point $z$
$ f = S * (q - z)$ or $q = round(f / S) + z$
- $f$ the real float value, real 
- $S$ scale factor, real 
- $q$ the quantized integer, int
- $z$ the zero-point, int

We compute $S_W$ and $z_W$ for $W$
- the range of $W$ is [-0.122, 0.572]
- $Q = 8$ so range of integers are $[0, 255]$
- $S_W = (0.572 - (-0.122)) / 255 = 0.00272$
- $z_W = round(-(-0.122) / 0.00272) = 45$
  - $z$ is the quantized value of real 0.0
  - to compute it we know that real $-0.122$ maps to 0 as quantized value

### Compute quantized weights

We compute the quantized weight $q_W$ using $S_W$, $z_W$ and $W$
$$ q_W = round(W / S_W) + z_W$$
$$ q_W = [ 178   45   255 ]
         [   0  103   165 ] $$
         
### Compute quantized inputs 

Similarly we compute scaling and zero-point for input $x$
$$ S_x = (2.1 - 0.8) / 255 ≈ 0.00510 $$
$$ z_x = round(-0.8 / 0.00510) = round(-156.9) = 157 $$

and we get quantized input
$$
q_x[0] = round(1.5 / 0.00510) + 157 = 294 + 157 = 451 → clamp to 255
$$

We can see the issue here with 8-bit quantization where the input overflows from range $[0, 255]$
