## Property 1: All Values Are Integers

In a ZKML-quantized network, every weight, every activation, and every intermediate value is a Q-bit integer in [0, 2^Q]. This is fundamentally different from standard floating-point networks where values are continuous.

For bound propagation, this means the pre-activation z = Σ w_i · x_i + b is a sum of integer products. The key insight is that z itself takes only finitely many possible values. For a neuron with n inputs, each input in [0, 2^Q] and each weight in [0, 2^Q], the product w_i · x_i is in [0, 2^(2Q)] and the sum of n such products is in [0, n · 2^(2Q)]. After rescaling by C₁/C₂ and rounding, the output is again in [0, 2^Q].

In standard bound propagation, the input domain is continuous — x₁ ∈ [-1, 1] means infinitely many possible values. But in a ZKML-quantized network, the quantized input has at most 2^Q discrete values per dimension. For Q = 16, that's 65,536 values. This is a large but finite set.

This finiteness could help in several ways. For the ReLU ambiguity problem, instead of asking "is z ≥ 0 for all x in a continuous interval?", you're asking "is z ≥ 0 for all x in a finite discrete set?" In principle, you could enumerate the critical boundary cases more precisely. The rounding operation at each layer further constrains possible values — the output of round(C₁/C₂ · raw) can only take integer values, so the bound propagation could track integer intervals [a, b] where a and b are integers, and the number of possible values is exactly b - a + 1 rather than a continuous range.

More practically, the integer structure means that when you encounter an ambiguous ReLU with pre-activation bounds [-3, 5] (in quantized units), there are exactly 9 possible pre-activation values: -3, -2, -1, 0, 1, 2, 3, 4, 5. The ReLU splits at exactly 0, and the active case has 6 values while the inactive case has 3. This discrete structure could enable more precise branching decisions — you know exactly how many input configurations fall in each branch.

---

## Property 2: The Rounding Structure Is Known

Every layer boundary in a ZKML-quantized network has a rounding step: q_out = round((C₁/C₂) · raw). This rounding is deterministic and the constants C₁, C₂ are known. This means the function from one layer's quantized output to the next layer's quantized output is not a smooth continuous function — it's a piecewise-constant staircase function.

Standard bound propagation treats each layer as a continuous transformation. But in the ZKML setting, the actual mapping from quantized input to quantized output is a step function. Each "step" corresponds to a range of raw values that all round to the same integer. This piecewise-constant structure is even simpler than piecewise-linear (ReLU) from a verification perspective.

Consider the rescaling q_y = round(raw / 12) for raw values in [0, 180]:

```
raw ∈ [0, 5]     → q_y = 0
raw ∈ [6, 17]    → q_y = 1
raw ∈ [18, 29]   → q_y = 2
...
raw ∈ [174, 180] → q_y = 15
```

Each step is a flat interval where the output is constant. Bound propagation through a constant function is trivial — the output bound is just that constant. If you can determine which step(s) the raw value falls in, you get exact output bounds without any approximation.

For bound propagation, this means you could replace the continuous relaxation of the rounding operation with a precise enumeration of which output integers are reachable. If the raw value bounds are [23, 47], then q_y can only be 2, 3, or 4 — you know the exact set of possible outputs. No linear relaxation needed, no over-approximation from treating round() as a continuous function.

---

## Property 3: The Quantization Error Has a Bounded Structure

When comparing the floating-point reference network to its ZKML-quantized version, the error at each layer is not arbitrary. The weight quantization error for each weight is bounded by half the quantization step: |w - w_quantized| ≤ S_W / 2. The rescaling error per layer is bounded by the rounding: |round(x) - x| ≤ 0.5 in integer units, which is S_y / 2 in real units. The scale ratio approximation error |C₁/C₂ - S_x·S_W/S_y| is bounded by the quality of the rational approximation, which for Q-bit integers is at most 1/(2^Q · C₂).

These error bounds are known analytically before you even start the verification. You could pre-compute a layer-by-layer error budget: each layer introduces at most δ_ℓ error from weight quantization and at most ε_ℓ error from rounding. A simple analysis would give an upper bound on total accumulated error as Σ δ_ℓ · (product of subsequent layer norms) + Σ ε_ℓ · (product of subsequent layer norms). This wouldn't be tight, but it would give a fast initial estimate of whether the discrepancy is small enough that you don't need to run the expensive BaB verification at all.

More importantly, you could use this error structure to guide the branching strategy. If the weight quantization error at layer 3 is much larger than at other layers (because that layer has a poor scale factor), you know that the ambiguous ReLUs near layer 3 contribute more to the discrepancy than those near other layers. The BaB algorithm could prioritize splitting those neurons first, converging faster to the maximum discrepancy.

---

## Property 4: Both Networks Share Identical Structure

In ZKML quantization, the quantized network has exactly the same architecture as the reference — same number of layers, same number of neurons, same connectivity. The only differences are the weight values (slightly shifted by quantization) and the rounding operations inserted at layer boundaries. This is stronger than general compression (where pruning might remove neurons or change connectivity).

For the merged network construction, this means the block-diagonal weight matrix W̃ = diag(W, Ŵ) has a very specific structure: W and Ŵ differ by at most S_W/2 per element. The off-diagonal blocks are exactly zero. This near-identity structure could be exploited.

Instead of propagating bounds through the full merged network with independent halves, you could propagate the difference directly. Define the weight error ΔW = W - Ŵ and bias error Δb = b - b̂. The output difference after one linear layer is:

```
y_ref - y_quant = W·x + b - (Ŵ·x + b̂) = ΔW·x + Δb
```

Since ΔW is small (every element bounded by S_W/2), this is a linear function with small coefficients. The bounds on this difference are tight and easy to compute:

```
(y_ref - y_quant)_min = Σ min(ΔW_i · l_i, ΔW_i · u_i) + Δb
(y_ref - y_quant)_max = Σ max(ΔW_i · l_i, ΔW_i · u_i) + Δb
```

The key advantage is that ΔW has small entries, so the bounds on the difference are tight even with simple interval arithmetic. You avoid the bound explosion problem that occurs when propagating through the full-sized weight matrices.

The complication is the ReLU. After a ReLU layer, the difference y_ref - y_quant is no longer a linear function of the input, because the two networks may activate different neurons for the same input. But the near-identical structure means the set of inputs where they activate different neurons (the "disagreement region") is small. Only inputs near the ReLU boundary (where z ≈ 0) can cause different activation patterns. For a quantized network where the weight perturbation is tiny, the disagreement region is a thin strip near the boundary, and the discrepancy within this strip is bounded by the perturbation magnitude.

---

## Property 5: Non-Linear Approximations Are Known

In ZKML, non-linear functions like GeLU and Softmax are not computed exactly — they're approximated by ZK-friendly versions (zkGPT's z-GeLU, zkLLM's segmented exponential). The approximation error is known analytically. zkGPT reports the L2 distance between z-GeLU and true GeLU is 0.0054 over the range [-4, 4]. zkLLM reports an L1 error of 10⁻² for their Softmax approximation.

For bound propagation, this means you could separate the total discrepancy into two independent components: the quantization-induced discrepancy (from weight rounding and intermediate rescaling) and the approximation-induced discrepancy (from replacing exact non-linear functions with ZK-friendly approximations). If you could bound each component separately and add them, you might get a tighter overall bound than treating the entire quantized-and-approximated network as a monolithic alternative model.

The approximation error can often be bounded analytically without any bound propagation at all. If |z-GeLU(x) - GeLU(x)| ≤ 0.012 for all x (the L∞ bound from zkGPT), then the approximation contributes at most 0.012 per neuron, scaled by subsequent layer weights. The weight quantization error requires bound propagation, but over a simpler "difference network" with small weights (ΔW) rather than the full original weights.

---

## A Potential Specialized Algorithm

Putting these properties together, a ZKML-specific bound propagation could work as follows.

First, compute the per-layer error budgets analytically from the known quantization parameters (scales, bit-width, rescaling constants). This takes negligible time and gives a fast upper bound. If this upper bound is already below ε, you're done — no BaB needed.

Second, if the analytical bound is too loose, build a "difference network" that directly computes y_ref - y_quant. For linear layers, this network has weight matrices ΔW with small entries. For ReLU layers, identify the disagreement region (inputs where the two networks activate different neurons) using the pre-activation bounds from the reference network and the known weight perturbation.

Third, run bound propagation (CROWN or BaB) on the difference network rather than the full merged network. Because the difference network has small weights, the bounds are naturally tighter and fewer branches are needed. The disagreement regions for ReLU layers are narrow (due to small weight perturbation), so fewer neurons are ambiguous.

Fourth, for the non-linear approximation error (z-GeLU vs GeLU, segmented Softmax vs exact Softmax), add the known L∞ approximation error bounds analytically, propagated through subsequent layer weight norms.

This specialized approach would exploit every structural property of ZKML quantization, potentially making the verification orders of magnitude faster than generic EqBaB applied to the merged network. The research contribution would be demonstrating that ZKML quantization, despite being more aggressive than standard ML quantization, has enough structure to make formal verification practical — which is exactly the gap in the current literature that you identified.
