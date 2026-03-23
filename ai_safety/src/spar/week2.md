## Week 2 

### Few definitions of equivalence 

- Exact equivalence
  - $$\forall x \in \mathcal{X}, M_1(x) = M_2(x)$$
  - impossible to prove

- Point-wise Equivalence
  - $$\forall x \in \mathcal{X}, |M_1(x) - M_2(x)| < \epsilon$$
  - the most interesting one to use from a cybersecurity standpoint
  - for all inputs the output difference is bounded
  
  
- epsilon-equivalence
  - $$\forall x \in \mathcal{X}, ||M_1(x) - M_2(x)|| < \epsilon$$
  - the $|| ||$ operation can be
    - manhattan distance, just summing up the absolute value of the vector field 
    - euclidian distance, sum up the field of the vector elevated to a power of 2
      - put an emphasis on fields with big values 
    - Max-Chebyshev distance, pick the field with the highest value
      - the maximum field is bounded
      - may be interesting fo rus if we want to control 


- Distributional Equivalence 
  - $$ P_{M_1}(y | x) = P{M_2}(y | x)$$ for  $x \in \mathcal{X}$ and $y \in \mathcal{Y}$
  - edge cases can mismatch
  - it should be correct on input that match training data
  - this is impossible to prove in practice and what is used is __epsilon-bounded distributional equivalence__
    - $$ D(P_{M_1}( . | x) - P{M_2}( . | x)) <= \epsilon $$
  - Possible definitions of the distance $D$
    - KL divergence
    - Fisher Information Matrix 
    - Wasserstein distance
  
- top-1-equivalence
  - more for image generation 
  - the most probable output of your output distribution should match the label it 
  was given during training for the same input
  - $$\text{argmax}(\hat{y}) = f(x)$$
    - $\hat{y}$ the probability vector of output
    - $f(x)$ the label given to input $x$ during training
    - $argmax$ gives the value associated with  the maximum probability 

### Why we don't have exact equivalence in zkml?

- the model trained works with weights and layers on floating points
- zk cryptography works on finite fields 
- hence to make the cryptography work the original model is quantized
  - floats are turned into integers
  - precision is lost
- afterwards the quantized model is turned into a circuit 
- the circuit and quantized model have exact equivalence 
  - this is EZKL claim when you build a circuit with their tools

The remaining question is how much precision have we lost during quantization?

Note: models are quantized/pruned when shifted onto devices to save up computation/memory.
This quantization is different as they keep using floats but just reduce precision.

__Why it matters?__ the precision loss may be exploited as this paper showed 
Exploiting LLM Quantization https://arxiv.org/abs/2405.18137

Note: 
- some zk native ml are being researched to avoid this issue TODO

### How can we measure the precision loss? a proposal

We try to prove model equivalence using bisimulation.

Definitions:
- $M$ is the original model with L layers
- $M'$ the quantized model with also L layers
  - is it really true that the quantized model has same number of layers?
- the transformation done by $M$ is defined by:
    - full execution: $f ∶ R^{n0} → R^{nL}$ 
    - can be also expressed recursively: 
        - $y^l = f^l(y^{l-1})$ with $l = 1, ..., L$
          - $y^l$ is the output at layer $l$
          - $f^l$ is the transformation at layer $l$
          - layers $l$ can be convolutional layers, linear, nonlinear (ReLU, Sigmoid), structural (pooling or normalization)
        - $y^L = f(x) where $x = y^{0} \in R^{n0}$
- we define a trace of an execution of $M$ with input $y^{0}$ is the sequence of states $(y^{0}, ..., y^{L})$
- similarly a trace of an execution of $M'$ with input $y'^{0}$ is the sequence of states $(y'^{0}, ..., y'^{L})$

We now define the relation $~ : R^{nl} x F^{nl} $ between states of the traces of $M$ and $M'$
What we want to prove using bisimulation is:
- if $y^{0} ~ y'^{0}$
- and that each pair of layer transformations $(f^l, f'^l)$ preserve the relation $~$ between states
- then we will have $y^{L} ~ y'^{L}$

#### Definition of ~

The definition of $~$ will help us prove an equivalence relation between model and its quantized version

- we need to define a distance metrics between states of $M$ and $M'$ 
  - easiest one  $$ y^{l} ~ y'^{l} iff || y^{l} - y'^{l} || < \epsilon$$
- we have to find how to compute that since $y$ is vector of floats and $y'$ a vector of integers
  - would a simple integer to floats conversion work? 
    - maybe depending on the quantization technique __TODO__
- risk of bound explosion since a llm has so many layers 
  - from EqBab paper we can use bound propagation and MILP to limit the bound precision losss
  - we have to try with quantization used in zkml

#### Sources

EqBaB: Efficient equivalence verification for compressed DNNs with bound propagation
- https://www.sciencedirect.com/science/article/pii/S0925231226001888
- uses bound propagation on most llm transformations 
- on transformations where bound propagations are too loose uses MILP to have better constraints 
at the cost of more computations

Exploiting LLM Quantization https://arxiv.org/abs/2405.18137

#### Plan 

- Build a simple version of zkml to understand better how it works and which operations 
are involved
  - implement a simple transformer block that work with small matrices (like 2x3) with fake parameters 
    - attention, activation layer, normalization ...
  - implement the quantized version of this transformer block 
  - bonus: build a circuit of the quantized version
- Explore existing quantization techniques
