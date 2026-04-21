# Concepts

General concepts about AI verification

## Interesting properties to verify 

| Property | zkML angle |
|----------|-----------|
| Robustness | Prove inference was done on a non-adversarial input |
| Fairness | Prove a decision didn't use protected attributes |
| Monotonicity | Prove a scoring model behaved consistently |
| Output bounds | Prove a control signal was within safe limits |
| Equivalence | Prove quantized model matches original (your first paper) |
| Lipschitz | Bound sensitivity of proofs to input perturbations |

### Robustness

Small changes to the input should not change outputs drastically.
- your model won't be fooled by small perturbations
- either accidental with sensor noise 
- or deliberate adversarial attacks

- a model  is robust iff
  - $∀x, x', ||x - x'|| <= ε => |f(x) - f(x')| <= δ
  - l_∞ robustness
    - input feature of $x'$ can change by at most ε compare to $x$
    - $x = (x1, ..., xn) and x' = (x1', ..., xn')$
    - $∀i, |xi - xi'| <= ε$
  - l_2 robustness 
    - euclidean distance between $x$ and $x'$ is at most ε
    - $||x - x'||_2 = √(xi - xi')² <= ε$
  - semantic robustness 
    - invariance to meaningful transformations like rotation, brightness, blur
    - harder to formalize but more realistic
    
### Fairness

Model should not discriminated based on protected attributes
- race, gender, age...
- gives legal and ethical guarantees 

__Common formal definitions__
- Individual fairness 
  - $||x - x'|| <= ε => |f(x) - f(x')| <= δ
  - $x$ and $x'$ only differ in a protected attribute
  - reduces to robustness
- Group fairness 
  - $|P(f(x) = 1 | group A) - P(f(x) = 1 | Group B)| <= ε$
  - different demographic groups should have similar outcomes on average
  - statistical statement so harder to verify formally
- Counterfactual fairness 
  - would prediction change if a person belong to a different group 
  and everything else is equal 
  - reasoning about interventions not just input perturbations

### Monotonicity

If one input feature increases the output should only increase 
$$ x_i < x_i' => f(x) <= f(x') $$

__Why it matters__
- many real-world relationships are monotone 
- violating monotonicity can be a sign of badly behaved model
  - potentially the model can gamed and cheated
- ex:
  - a credit risk model should assign higher risk to higher debt-to-income rations 
  - a medical severity model should assign higher severity to worse symptoms 
  - a pricing model should chare more fore more features
  
__Verification__
- $∀x, x', x_i < x_i' => f(x) <= f(x') $
- equivalent to $∂f/∂xi > 0$ everywhere
- meaning bounding the gradient across the input space
- bound propagation can do this by propagating interval bounds 
on gradients rather than outputs

### Output bounds

Network output should stay within some range for all inputs in a given set.
$$ ∀x ∈ X, f(x) ∈ [l, u]$$

- most fundamental safety property for control and safety-critical systems
- a drug dosage recommendation should never be above a certain threshold
- a car steering output should stay within safe limits

__Verification__
- just find minimum and maximum of $f$ over $X$
- bound propagation gives the minimum on lower bound and maximum on upper bound
- if min and max bounds are within safety bounds it's done

### Lipschitz Continuity

Network output can't change faster than a fixed relative to input changes 
$$ ||f(x) - f(x')|| <= L ||x - x'|| $$

- a small Lipschitz constant implies robustness, fairness and output stability all at once
- required for stability guarantees in control systems
- bounds generalization error, models with small Lipschitz constants generalize better

### Equivalence / Functional similarity 

Two networks $f1$ and $f2$ produces similar outputs for all inputs in some distribution
- model compression certification 
- fine-tuned model hasn't drifted too far 
- distilled model matches its teacher

### Safety constraints/invariants

Network always satisfies some logical constraint on its output
- sum of outputs should be 1 
- output for class A should be higher than output for class B
In structured prediction tasks outputs must satisfy combinatorial constraints.

### Privacy / Information-Theoretic properties 

Network output does not reveal too much about training data.
- formally related to differential privacy 
- equivalent to: output distribution changes little if one training example is added or removed
Hard to prove, this is a property of the training and not only the network.
Much harder than just input-output verification

### How these properties relate to each other

- Parameter equivalence => Pointwise equivalence => Distributional equivalence 
- Lipschitz continuity => Robustness 
- Lipschitz continuity => Output bounds
- Individual fairness ~ Robustness to protected attribute perturbations 
- Monotonicity => Some output bounds

## Random concepts

### Convex functions 

A function is convex if any line between two points of the function outputs 
is always above the function output. 
- ex: f(x) = x²
- has bowl shape
- it also means that any local minimum is global minimum
- it's easy to optimize, gradient always finds the global minimum

Neural networks are non-convex
- ReLU is non-convex ($ReLU(x) = max(0,x)$)
- piecewise linear
- a network with many ReLU possesses countless local minima
- finding the global minimum of a non-convex function is NP-hard
- that's why verification methods compute lower bounds instead
  - you don't find the true minimum but you simplify computations

### Ball notations

$B_2(x^,ρ) ⊆ B_∞(x^,ρ⋅1)$

- $B_2(x^,ρ)$ the $l_2$ ball 
  - this is the set of points which are at euclidian distance ρ from $x^$
  - $B_2(x^,ρ) = {x: ||x - x^||_2 < ρ} = {x: √sum((x_i - x^_i)²) < ρ $
  - in 2d this is a circle, 3d a sphere,  more it's a hypersphere
- $B_∞(x^, ρ · 1)$ the $l_∞$ box
  - this is the set of apoints where every coordinate is withing ρ of $x^$
  - $B_∞(x^, ρ · 1) = {x: |x_i - x^_i| < ρ}$
  - the $1$ is just of ones: $ρ · 1 = (ρ, ..., ρ)$
  - in 2d this is a square, 3d a cube, more it's a hybercube

### Semidefinite and positive definite matrices

$A$ is __positive definite__ if all its eigenvalues are strictly greater than 0
- $ λ_i > 0 $
- equivalently: $ ∀v, v · A · vᵀ > 0$
- geometrically: $A$ strech space in every direction

$A$ is __positive semidefinite__ if all its eigenvalues are greater or equal to 0
- $ λ_i >= 0 $
- equivalently: $ ∀v, v · A · vᵀ >= 0$
- geometrically: $A$ strech space in every direction or collapses them

### Verified Accuracy

__Standard accuracy__
- out of 200 images how many does the network classify correctly?
- if it's 140 out of 200 then we have 70% accuracy 

__Verified accuracy__
For each image it asks 2 things:
- standard accuracy: is the image correctly classified 
- can we prove the network stays correct for all perturbations within radius ρ
  - tries to verify that the image is robust
 
__Projected Gradient Descent__
- Projected Gradient Descent attacks uses PGD to find adversarial examples 
- if PGD finds an adversarial example for image $x^$ the $x^$ is not robust

$$ Verified accuracy <= PGD upper bound <= Standard accuracy $$

What it means:
-
