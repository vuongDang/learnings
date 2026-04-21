Good question. It is a **bound**, not an equality. Let me derive it carefully from scratch.

---

## Setup at Layer $k$

The base model computes:

$$z^{(k)} = W^{(k)}_\text{float} \cdot x^{(k-1)}_\text{float}$$

The quantized model computes:

$$\hat{z}^{(k)} = \hat{W}^{(k)}_\text{float} \cdot \hat{x}^{(k-1)}_\text{float}$$

where $\hat{W}^{(k)}_\text{float} = W^{(k)} + \Delta W^{(k)}$ and $\hat{x}^{(k-1)}_\text{float} = x^{(k-1)} + \Delta x^{(k-1)}$.

---

## Step 1: Expand the Product

$$\hat{z}^{(k)} = (W^{(k)} + \Delta W^{(k)})(x^{(k-1)} + \Delta x^{(k-1)})$$

$$= W^{(k)} x^{(k-1)} + W^{(k)} \Delta x^{(k-1)} + \Delta W^{(k)} x^{(k-1)} + \Delta W^{(k)} \Delta x^{(k-1)}$$

So the error before rescaling is:

$$\Delta z^{(k)}_\text{before rescale} = \hat{z}^{(k)} - z^{(k)} = W^{(k)} \Delta x^{(k-1)} + \Delta W^{(k)} x^{(k-1)} + \Delta W^{(k)} \Delta x^{(k-1)}$$

---

## Step 2: Bound Each Term

**Term 1 — propagated input error $W^{(k)} \Delta x^{(k-1)}$:**

For output neuron $i$:

$$|(W^{(k)} \Delta x^{(k-1)})_i| = \left|\sum_j W^{(k)}_{ij} \Delta x^{(k-1)}_j\right| \leq \sum_j |W^{(k)}_{ij}| \cdot |\Delta x^{(k-1)}_j|$$

$$\leq \sum_j |W^{(k)}_{ij}| \cdot \frac{S_x}{2} = \frac{S_x}{2} \|W^{(k)}_{i,:}\|_1$$

Taking the max over all neurons $i$:

$$\|W^{(k)} \Delta x^{(k-1)}\|_\infty \leq \frac{S_x}{2} \|W^{(k)}\|_\infty$$

where $\|W^{(k)}\|_\infty = \max_i \|W^{(k)}_{i,:}\|_1$ is the max row sum.

**Term 2 — weight quantization error $\Delta W^{(k)} x^{(k-1)}$:**

$$|(\Delta W^{(k)} x^{(k-1)})_i| \leq \sum_j |\Delta W^{(k)}_{ij}| \cdot |x^{(k-1)}_j| \leq \frac{S_W}{2} \sum_j |x^{(k-1)}_j| = \frac{S_W}{2} \|x^{(k-1)}\|_1$$

Taking max over $i$:

$$\|\Delta W^{(k)} x^{(k-1)}\|_\infty \leq \frac{S_W}{2} \|x^{(k-1)}\|_1$$

**Term 3 — cross term $\Delta W^{(k)} \Delta x^{(k-1)}$:**

$$|(\Delta W^{(k)} \Delta x^{(k-1)})_i| \leq \sum_j |\Delta W^{(k)}_{ij}| \cdot |\Delta x^{(k-1)}_j| \leq \sum_j \frac{S_W}{2} \cdot \frac{S_x}{2} = \frac{S_W S_x}{4} n_{k-1}$$

where $n_{k-1}$ is the input dimension.

---

## Step 3: Add Rescaling Error

After the multiply, the result is rescaled — another rounding with error $\leq S_{x_\text{next}}/2$.

---

## Step 4: Combine by Triangle Inequality

$$\|\Delta z^{(k)}\|_\infty \leq \underbrace{\frac{S_x}{2} \|W^{(k)}\|_\infty}_\text{propagated input error} + \underbrace{\frac{S_W}{2} \|x^{(k-1)}\|_1}_\text{weight quant} + \underbrace{\frac{S_W S_x}{4} n_{k-1}}_\text{cross term} + \underbrace{\frac{S_{x_\text{next}}}{2}}_\text{rescaling}$$

This is $e^{(k)}$ — a bound, derived by applying $|\Delta W_{ij}| \leq S_W/2$ and $|\Delta x_j| \leq S_x/2$ to each term and summing via triangle inequality.
