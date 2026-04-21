# Parameter sensitivity 

## Definitions

### Lipschitz constant
a function $f$ is M-Lipschitz if:
- $∀a,b |f(a)-f(b)| <= M ·||a-b||$
- $M$ is the worst case sensitivity 
- how much can an output change if the input change
- for a differentiable function, M is the gradient maximum value

### Fisher information matrix
Captures how much the output distribution changes when you pertub the parameters
  $$F(θ) = E[(∂/∂θ log p(x,y; θ)) · (∂/∂θ log p(x,y; θ))ᵀ]$$
- $score = ∂/∂θ log p(x,y; θ)$ is called the score
  - it'a vector which shows how likely we are to get $(x, y)$ if we increase parameter $θ_i$
  - the vector size is equal to number of parameters 
- the average outer product $E[score · scoreᵀ]$ 
  - gives a square matrix of size equal to number of parameters
  - the diagonal $F_{i,i}$ represents how important is parameter θ_i
  for the inputs/outputs you computed $F$ with
  - off-diagonal $F_{i,j}$ represents how correlated are parameters 
  θ_i and θ_j for  the inputs/outputs you computed $F$ with
- How is it used for? 
  - diagonal $F_{i,i}$ is
    - high value: important for this training set should not be compressed aggressively
    - near 0: has little impact can be compressed or even pruned
  - off-diagonal $F_{i,j}$ is
    - high value: θ_i and θ_j are highly correlated
    - near 0: θ_i and θ_j are independent
  - you can offset the effect of quantization of sensitive parameters with correlated ones
- How is it computed and distribution
  - you get the score for free when doing backpropagation during model training
  - the fisher matrix only applies for inputs/outputs it was computed on
  - it's often misused with training data seen you get it for free when training a model
  - ideally you want your training data distribution to match the entire set of inputs
  - ex: 
    - training data can be pictures of cats 
    - out of distribution data is a picture of a dog 
    - fisher matrix do not give any information on the output from a dog picture
- not applicable everywhere
  - for a generic llm it's not very useful as the input space is too big 
  - but for a classifier it can be a good metric
- in practice fisher information matrix is not computed during training 
  - it's too big to compute
  - it's computed over 128 inputs 
    - ideally represent input distribution during deployment
    - 128 gives good result empirically
  - why 128 is enough? intuition
    - LLMs are generally overparametrized
    - Fisher information matrix is extremely low rank in practice
    - we only need general directions of Fisher matrix 
    - 128 is enough to gather this 
  - fisher matrix is not good metric for out-of-distribution inptu
    - rare reasoning pattern
    - long context 
    - adversarial

### Eigenvalues

A matrice $M$ may possess eigenvalues $λ_i$ and eigenvectors $v_i$ iff:
$M · v_i = λ_i * v_i$

- Eigenvectors of a a matrice will not rotate or transpose, only stretch/reduce
- Eigenvectors represent the "direction" of a matrix
- How to compute
  - $M · v = λ * v$
  - $(M - λ*I) · v = 0$
  - this has a non-zero solution when 
    - det(M - λ*I) = 0
    - this is a polynomial equation
    - why?
      - assume $A = M - λ*I$, we get $A · v = 0$ with $v != 0$
      - for which value of λ does this have a non-zero solution $v$
      - if A is invertible then 
        - $A · v = 0 <=> A^{-1} · A · v <=> v = 0$
        - which is not acceptable so A has to be non-invertible
      - and $A is non-invertible <=> det(A) = 0$
- if a matrice is symmetric and positive semidefinite (like Fisher matrix)
  - its eigendecomposition is  $F = V · A · Vᵀ$
  - $V$ matrix of eigenvectors (columns are directions)
  - $A$ diagonal matrix of eigenvalues $λ_1, λ_2, ..., λ_d$
  - $M$ positive semidefinite 
    - $∀ vectors v, v · M · vᵀ >= 0$
    - $v · M · vᵀ$ computes how much $M$ pushes in direction of $v$
      - positive -> $M$ has component pushing the same way as $v$
      - 0 -> $M$ is perpendicular to $v$
      - negative -> $M$ pushes against $v$ not possible for positive semidefinite
  - determinant 
    - how much a matrix scales volume when it transform space
      - large det -> transformation streches space a lot 
      - det = 1 -> transformation preserves area exactly 
      - det = 0 -> space is collapsed flat and transformation is not reversible
      - det < 0 -> transformation flips orientation
    - $$M = [a  b]
            [c  d]$$
      - $det(M) = a·d - b·c$
  
### Effective dimension 

Even if a space is high-dimensional, data and models actually live in 
a much lower-dimensional subspace.

- how to compute it 
  - from the covariance or Fisher matrix 
  - sort the eigenvalues and keep only the ones above a threshold ε
  - the number of eigenvalues remaining is effective dimension
- interpretation 
  - low effective dimension is a sign of good generalization
    - with fewer "knobs" it's able to answer most questions
    - if effective dimension is low relative to the task complexity 
      - poor training accuracy
  - big effective dimension is a sign of overfitting
    - every direction matters and parameters are tuned to the training data

### VC dimension 

Vapnik-Chervonenkis dimension
measures the pattern complexity that a class of model can express.
It's defined through shattering
- a model shatter a set of points if it's able to achieve every labelling
of these points
  - ex: a linear classifier can correctly label any set of 2 and 3 points 
  - but it's not able to do it for 4 points, like a square with opposite corners having
  matching labels
  
__VC dimension__ is the largest $n$ for which there exists some set of points $n$ that
a model class can shatter

__VC generalization bounds__
- if a model has VC dimension _h_ and you train on _n_ samples
- the gap between training and test is bounded by $√(h/n)$
  - to reduce the gap you need a lot of training samples
- it always applies but it's very loose

Not useful for big networks 
- their VC dimension are at least $d$ which are around billions
- meaning for the obund to be useful you need billiions of training samples


__Requirements__
1. models need same architecture
  - sounds ok in our usecase
2. i.i.d training data 
  - is it an issue for "regular" usage? how does this translate in practice?
    - no sequential training? no videos?
  - is this something we're gonna investigate with Rishit framework
3. error bound contains Lipschitz constant 
  - how do we get a better bound than Lipschitz in general? 
  - Can we get a better bound that Lipschitz if we are focusing on zk quantized models?
4. Bound $4 * M1 * ε/ √(κn,γ)$ needs to be < 1 to be useful
  - to avoid this you need big $n$
  - small ε, is it possible in specific settings?
  - small γ, what does γ represents exactly?
5. β need to be small 
  - this is tough one to compute right?
  - this is where we need fisher information matrix on full ε-ball? 
  - is possible to compute it only for specific θ and θ' rather than full ε-ball?
6. Full rank fisher
  - is it difficult in practice? Fisher is positive semidefinite right?
  - what does it mean in practice?
  - so it does not work with models which are overparametrized/big?
7. Covering argument as mentioned can certainly be improved 
  - R(θ) treated independently currently and summed
  -
