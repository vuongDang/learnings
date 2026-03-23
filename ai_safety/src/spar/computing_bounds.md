# Computing bounds

## List of techniques 

- Interval propagation  (IBP)
- Linear relaxation (CROWN)
- MILP and SMT solvers 
- Branch and Bound (BaB)

## Pros and cons

Interval (IBP)     Loosest      O(1)               Bounds blow up fast,
                                                    correlations lost entirely

Linear (CROWN)     Tighter      O(n) per layer      Correlations partially 
                                                    preserved through linear
                                                    functions, some looseness
                                                    from ReLU relaxation

BaB (EqBaB)        Exact*       O(2^k) worst case  Exact within each branch,
                                                    branching resolves all
                                                    ambiguity
                                                    *for piecewise-linear only

MILP/SMT           Exact        O(2^k) worst case  All neuron interactions
                                                    captured simultaneously,
                                                    solver exploits structure

## Going through an example

With an example of one neuron with an input with two features

$$
z = 0.5*x₁ + 0.3*x₂ + 0.1       (pre-activation)
a = ReLU(z) = max(0, z)            (post-activation / output)
x_1 \in [-1, 1]
x_2 \in [-1, 1]
$$

Since z is linear the bounds are the same for all methods
$$
z_min = 0.5 * -1 + 0.3 * -1 + 0.1 = -0.7
z_max = 0.5 *  1 + 0.3 *  1 + 0.1 = 0.9
z \in [-0.7, 0.9]
$$

### Interval Bound Propagation 

- compute minimum and maximum output considering input range
- fast to compute but is not precise 
- the values in the range are treated independently and ignore correlation between layers

$$
a_min = ReLU(z_min) = max(0, -0.7) = 0
a_max = ReLU(z_max) = max(0, 0.9) = 0.9
$$
This is correct but we totally lose the correlation with input $x$

### Linear relaxation

- projects that use it: CROWN, α-CROWN, β-CROWN
- each neuron's output is bounded by a linear function of the input
- not an independent interval like bound propagation

__Upper bound__ is a straight line connecting the min and max values out of ReLU
$$
a_min = 0 and a_max = 0.9
slope = (a_max - a_min) / (z_max - z_min) = (0.9 - 0) / (0.9 - -0.7) = 0.56
$$

we have $0.56 * z + cst >= a$
Since the lines passes through (z_min, a_min)
$$cst = 0 - 0.56 * -0.7 = 0.39$$

and we have $ a <= 0.56 z + 0.39$

__Lower bound__
$$ a >= α*z $$
- the lower bound is a line through the origin
- α-CROWN introduces an optimizable parameter α ∈ [0, 1]
 - $when α = 0, then a >= 0$ which is true
 - $when α = 1, then a >= z$ which is also true
 - so indeed any α is valid 
- we need to find α such that final output bounds is minimized not only output bound of the neuron

so we have
$$
a <= 0.56 * z + 0.39 = 0.28 * x_1 + 0.15 * x_2 + 0.45
a >= α * (0.5*x_1 + 0.3*x_2 + 0.1)
$$

Let's assume this neuron is last layer and we need to optimize $α$
$$
a_lower_min = α * (0.5 * -1 + 0.3 * -1+ 0.1) = α * (-0.7) = 0, we pick $α = 0$ to have the lowest value as possible
a_upper_max = 0.28 * 1 + 0.15 * 1 + 0.45 = 0.9
$$

Hence we have $a \in [0, 0.9]$
With one neuron we get the same results as bound propagation.
Linear relaxation shines when there are several layers since it carry much more correlation
to initial input.

### Branch and Bound 

- Approach by EqBaB
- Do not make an approximation of ReLU and just simulate the two scenarios of ReLU
- you have exact bounds for ReLU if there are only linear layers with ReLU
- you double the number of computation for each ReLU
- future branches may be pruned thanks to previous branch constraints
that create impossible scenarios

__Branch A: ReLU is active__
hence
$$
z >= 0
0.5*x₁ + 0.3*x₂ + 0.1 >= 0 
with x₁ ∈ [-1, 1], x₂ ∈ [-1, 1]
$$

a_min in this branch = 0    (when z is exactly 0)
a_max in this branch = 0.9  (when x₁=1, x₂=1)

Result for Branch A: a ∈ [0, 0.9]
but you have the condition $0.5*x₁ + 0.3*x₂ + 0.1 >= 0$
that can simplify future layers

__Branch B: ReLU inactive__
hence
$$
z < 0
0.5*x₁ + 0.3*x₂ + 0.1 < 0 
with x₁ ∈ [-1, 1], x₂ ∈ [-1, 1]
$$

Result for Branch B: a ∈ [0, 0]
but you have the condition $0.5*x₁ + 0.3*x₂ + 0.1 < 0$
that can simplify future layers

### MILP  (Mixed-Integer Linear Programming)

- projects like QEBVerif, α,β-CROWN (also support MILP)
- even tighter bounds than previous techniques 
- entire neural network is a set of linear constrints with integer variables 
- ReLU neuron gets a binary varibles: 0 if inactive,  1 if active
- the MILP solve (Gurobi or CPLEX) finds the exact input 
  that maximized or minimizes the output while respecting all the constraints
- the approach is complete: finds the true maximum discrepancy not an over-approximation 
- problem is NP-hard and the solve time grows exponentially with the number of integers (1 per ReLU)


For the ReLU neuron, the MILP encoding introduces a binary variable δ ∈ {0, 1}:
$$
Constraints: 
z = 0.5*x₁ + 0.3*x₂ + 0.1     (linear layer)
a >= 0                        (ReLU)
a >= z                        (ReLU)
a <= z - (-0.7) * (1 - δ)     (if δ=1, then ReLU active -> a = z)
a <= 0.9 * δ                  (if δ=0, then ReLU inactive -> a = 0)
x₁ ∈ [-1, 1], x₂ ∈ [-1, 1]
δ ∈ {0, 1}
$$

The goal is to maximize and minimize a
$$
maximize a: solver finds x₁=1, x₂=1, δ=1 → a = 0.9
minimize a: solver finds (any point where z<0), δ=0 → a = 0
Exact result: a ∈ [0, 0.9]
$$

### SMT solvers 

- projects like Reluplex, Marabou
- whole network is translated as a SMT problem combining boolean and arithmetic
- use SMT solvers to findbounds 
- also complete like MILP
- difference with MILP is that SMT solvers use techniques like CDCL (conflict-driven clause learning)
  which can be more efficient for specific structures

## Computing bounds for zkml quantized model

We want to exploit specificities of quantized model to have better bound propagations.

### Quantized model peculiarities

- Computed on finite fields 
  - discrete number of integers
    - this contrasts with floats continuous ranges 
  - in theory, we can simulate all the possible outputs 
- Quantized range 
  - Quantization scheme defines a range in which values are contained
  - independant from the finite field choice 
  - quantized values will stay in this range, so it reduces the number 
  of potential values even more
- Quantization uses a rounding/rescaling step
  - the rescale factor is either model/weight/tensor based 
    - depending on the choice the rescale value can be dynamic
  - rounding is a piecewise function 
    - this is usually bad for bound propagation 
    - but maybe it can be good here because it also reduces potential values 
- The error brought by the quantization is understood and loose bounds can be pre-computed quickly
  - for weights : $|W - W_quantized| = |W - round(W / S_W) × S_W| <= S_W/2 $
    - $S_W$ the scaling factor for $W_quantized$
  - rescaling approximation after dot product is similar operation since you round the 
    - $|W - W_quantized| = |W - round(W / S_W) × S_W| <= S_W/2 $
  - scale ratio approximation
    - this is bounded by the quantization scheme 
    - depends on the precision of the scale
    - for Q-bit quantization, the larger Q the finer the approximation 
    - if we define scale ratio as $C1/C2$ then 
    - $|C₁/C₂ - S_X·S_W/S_Y| ≤ 1/(2^Q · C₂)$ for linear operation y = x . w + b
  - With this additional information we can adapt the bounds computation strategy
    - focusing on layers with pre-computed bounds much bigger than others
- Quantized models keep the same structure as base one
  - Compared to EqBab we only focus on quantization technique 
  - We don't consider other compression techniques like pruning or knowledge distillation
  - so the weights have same shapes in quantized and base model
  - we can compute the bounds of the merged network directly with a ΔW
    - $y_ref - y_quant = W·x + b - (Ŵ·x + b̂) = ΔW·x + Δb$
    - moreover ΔW is supposed to be small so it's easier to compute the bounds
    - we avoid bound explosion that can come with $W.x$
- Non-linear functions are approximations in zkml
  - GelU, or Softmax are not computed exactly in zkml but have zkml-friendly version
    - see zkGPT z-GeLU or zkLLM segmented exponential
    - for example: $|z-GeLU(x) - GeLU(x)| ≤ 0.012 for all x$ (from zkGPT)
  - approximation error of these implem is known
  - we can compute bounds differently by separating error due to quantization 
  and error due to function approximations
