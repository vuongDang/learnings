
# EqBaB: Efficient equivalence verification for compressed DNNs with bound propagation

The paper addresses a critical gap in AI safety and zero-knowledge ML (zkML): **Quantization Error**.
When you compress a standard Deep Neural Network (DNN) into a Quantized Neural Network (QNN) to save memory or generate zk-proofs, you introduce mathematical "noise." The paper seeks to solve the **equivalence verification problem**: How can we formally guarantee that the output discrepancy (error) between the original DNN and the QNN will *never* exceed a specific safety threshold ($\epsilon$) for any valid input?

Previous methods either focused on verifying single networks in isolation or only handled "partial quantization" (e.g., quantizing only the weights but not the activations). This paper tackles the much harder, realistic scenario of **full quantization**, where both weights and activation tensors are compressed.

### 2. The Core Framework: EqBaB

To solve this, the authors introduce **EqBaB**, a hybrid, two-phase framework. The name implies verifying equivalence via bound propagation. It combines fast mathematical estimation with rigorous, exhaustive proof.

#### Phase 1: Differential Reachability Analysis (DRA)

Instead of verifying the DNN and the QNN separately, EqBaB conceptually merges them and analyzes the *difference* between their states layer-by-layer.

* **How it works:** DRA calculates a "reachable set" (all possible outputs for a given set of inputs) and propagates the maximum possible quantization error forward through the network.
* **The Benefit:** It is incredibly fast. By tracking only the mathematical bounds of the error (the Lipschitz constraints we discussed earlier) step-by-step, it avoids the exponential computational cost of evaluating the entire network at once.
* **The Drawback (The Overestimation Problem):** Because neural networks have non-linear activations like ReLU, DRA has to make conservative approximations at every layer. By the time it reaches the final layer, the estimated error bound might be artificially inflated. If DRA says the error is $\leq 0.5$, but your safety threshold is $\epsilon = 0.1$, DRA "fails" to prove equivalence—even if the model is actually safe.

#### Phase 2: Mixed-Integer Linear Programming (MILP)

To fix the overestimation problem of DRA, EqBaB introduces a fallback mechanism. If DRA fails to prove the error bound is within the threshold, the framework triggers Phase 2.

* **How it works:** EqBaB encodes the exact equivalence verification problem into a massive set of MILP constraints. Every single ReLU activation is translated into a binary integer constraint (e.g., $z=1$ if the ReLU is active, $z=0$ if it is dead).
* **The Benefit:** MILP solvers (like Gurobi) are exact. They will systematically search the mathematical space and find the *true* maximum error bound without any conservative overestimation.
* **The Synergy:** Running MILP on a whole neural network is computationally impossible. But because EqBaB already ran DRA in Phase 1, it uses the tight bounds discovered by DRA to instantly prune millions of dead-ends in the MILP solver, making the exact verification suddenly computationally feasible.

### 3. Key Properties of the Method

The authors mathematically prove that the EqBaB framework is:

* **Sound:** If EqBaB says the models are point-wise equivalent within bound $\epsilon$, they absolutely are. It will never give a false positive.
* **Complete:** If the models truly are equivalent within the bound, EqBaB will eventually prove it (unlike purely DRA-based methods that get stuck in overestimation).

### 4. Experimental Results and Significance

The paper conducts extensive experiments on standard benchmarks. The results show that:

1. **Efficiency:** EqBaB is vastly more efficient than monolithic verification tools because the fast DRA phase filters out the vast majority of easy-to-prove equivalence queries, only calling the heavy MILP solver for the hardest mathematical edge cases.
2. **Scalability:** By tracking the *differential* (the gap between the models) rather than the absolute values of the models themselves, the bounds stay significantly tighter, allowing researchers to verify much deeper compressed networks than previously possible.

### Summary for your Research

If you are writing about **point-wise bounded equivalence**, this paper is your definitive source. It mathematically bridges the gap between theoretical approximate bisimulation and practical, algorithmic verification by using DRA for speed and MILP for absolute certainty.
