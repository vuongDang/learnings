# SDP-crown (Semi Definite Programing)

## Problem

- Bound propagation verifiers are the SOTA of large scale verification
- Perform poorly under l_2 perturbations
  - α-CROWN has 2,5% verified accuracy
  - naive Lipschitz baseline 47,5% verified accuracy (a model specifically designed to have low Lipschitz constant)
  - a simple method like Lipschitz beats bound propagation verifiers
- Diagnosis of the problem
  - bound propagation technique are too conservative on $l2$ robustness 
  - they often do a conservative approximation on the bounds of input and modelize the box $l∞$

## Goals

- Use the precision of SDP-based verifiers which capture inter-neuron coupling 
  - precise but compute heavy
- keep the scalability of bound propagation
  - pure SDP fails starting at thousands of neurons
- compatibility with existing bound propagation pipelines like α-CROWN 

## Contributions

1. Diagnosis of the looseness
- prove that $l2$ sphere bound propagation approximated to the $l∞$ box is too loose
  - $l2$: variant inputs $x'$ are at euclidean distance inferior to ρ from the reference input $x$
  - $l∞$: variant inputs $x'$ have their features at distance inferior to ρ from the features of reference input $x$
- the $l∞$ box corners are at distance $√n * ρ$
- bound propagation is certifying against an adversary $√n$ stronger than necessary
2. New linear bound for ReLU derived from SDP

## Context 

α-CROWN 
- Standard bound propagation 
- Adds parameter α, the lower slope of ReLU relaxation 
- α is optimized via gradient ascent 
- α-CROWN runs 300 iterations to find α that maximized the lower bound

β-CROWN
- improvement over α-CROWN
- introduces split constraints via branch and bound
- ReLU is split into two scenarios:
  - neuron is active $z >= 0$
  - neuron is inactive $z <= 0$
- number of computation is double for every ReLU

GCP-CROWN
- improvement over β-CROWN
- add cutting planes
  - additional linear constraint that improves precision 
  - these constraints are derived by solving mixed integer programming subprobles
- the MIP formulations consider the $l_2$ ball rather than using the box approximation
- even with this improvement the fundamental bound propagation operated on the box
  - improve precision but does not overcome the $√n$ problem
- MIP solver adds significant overhead

LipNaive - Lipschitz Constant Approach
- No bound propagation at all
- uses the the network Lipschitz constant to bound :
  - $c^T * f(x) ≥ c^T * f(x^) − L · ρ$
  - $L$ is computed as the product of spectral norms of weight matrices:
    - $L = ∥W(N)∥_2 · ∥W(N−1)∥_2 ... ∥W(1)∥_2$
- Models were retrained to have small Lipschitz constants
  - $L$ small -> $c^T * f(x^) − L · ρ$ stays positive for reasonable ρ
- LipNaive is extremely fast
- Limitations 
  - Lipschitz constant is worst case
