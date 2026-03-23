# Model Equivalence 

Proving the equivalence of two programs is undecidable.
This can only be solved with programs that are under structural constraints.

## Few coincidences property  

A constrained class _F_ over domain _D_ satisfies the few coincidences property 
iff _for all f,g with f ≢ g in F_:

_Pr[f(x) = g(x)] ≤ 1-δ_ with _x in D_

Meaning that the probability of getting the same output for different f and g 
is _1-δ_

and if _F_ satisfies the few coincidences property then for _k_ independent random input: 

_Pr[f(k) = g(k) for all k | f ≢ g] ≤ (1-δ)^k_ 

Meaning that the probability of getting a false positive: 
- f and g are different
- f and g agree on all k tested
is inferior to  _(1-δ)^k_

## Function class satisfying the few coincidences property

- Bounded-Degree Polynomials and Schwartz-Zippel lemma
  - Cleanest instance of the few coincidences principle 
- Piecewise Polynomials 
  - Many programs are not globally polynomial but can be represented 
  as _piecewise_ on specific regions of the input space
- Finite Automata Equivalence
  - Pratical constraint classes for deterministic programs

### Bounded-Degree Polynomials and Schwartz-Zippel lemma

#### Schwartz-Zippel lemma

let _h ∈ Fq[x1,...,xn]_ a non zero-polynomial of degree _d_
- _h_ is a polynomial over _Fq_ a finite field 
  - both _h_ inputs, outputs and coefficient are elements of _Fq_
  - _h_ is a multivariate polynomial
    - _h_ does not take a single input but _n_: [x1,...,xn]
  - _Fq_ is finite and the 4 modular basic arithmetic operations applies and are commutative, associative and distributive 
  - _Fq_ order is a prime number or a power of a prime number (this is property due 
  to having finite number of elements and being closed under the 4 modular basic arithmetic operations)

then
_Pr[h(x) = 0] ≤ d / |S|_ with _x ∈ S^n_


#### Application to equivalence testing

We suppose f and are both polynomials of degree _d_ at most. We define _h_
as _h = f - g_.
Hence _f ≡ g_ iff _h ≡ 0_. 
Then the probability of a false positive where _h ̸≡ 0_ and _k_ random samples 
did not notice it is:
_Pr[h(x) = 0, k times | h ̸≡ 0] ≤ (d / |S|)^k_

if _|S| ≫ d_ then this is really small. 
This is the basis of __probabilistic polynomial identity testing__ (PIT)

#### Takeaway

- as long as _|S| ≫ d_, then it's impossible for two polynomials of degrees "d" to differ only on few points 
- it's impossible to build _f_ and _g_ such that they are almost identical 
- hence it's really hard to cheat the system such that polynomial identity testing is false

### Piecewise polynomials

Most programs are not polynomials on the whole input space but
can be divided into multiple polynomials on different input subspaces

Divide the input domain _D_ into _k_ known regions _D1, ..., Dk_
such that _f_ and _g_ are polynomials of degree at most _di_ for each region _Di_

On each region Di, define _hi = f |Di − g|Di_ and apply Scwhartz-Zippel independently.

for each region _Di_:
_Pr[hi(x) = 0, ki times | hi ̸≡ 0] ≤ (di / |Si|)^ki_

Hence with an union over all regions _Di_, the probability that there 
is a region _Di_ where _hi ̸≡ 0_ was undetected through k samples is:

Pr[∃ i : f |Di ̸≡ g|Di undetected] ≤ Sum((di / |Si|)^ki)

### Finite Automata equivalence 

#### DFA exact equivalence 

let A1 and A2 be two DFA with n1 and n2 states respectively over alphabet Σ.

if _L(A1)  ̸= L(A2)_ then there exists a string _w_ such that _w ∈ L(A1 )△L(A2)_ with _|w| < n1+n2_

Explanation:
- _L(A1)  ̸= L(A2)_ meaning their languages are different, basically that A1 and A2 are different
- _w ∈ L(A1)△L(A2)_ meaning that w is in the symmetric difference of the languages of A1 and A2
  - _w_ is accepted by one machine but not the other
- _|w| < n1+n2_ meaning that size of _w_ is less than n1 + n2

Conclusion:
- random sampling for automata equivalence in black-white-box setting does not work
  - DFA equivalence is hard to apply to our use case with one program being black box
  - the black box program is only queryable with "random" inputs
  - unlike the polynomial case, the number of distinguishing strings _w_ can be very small
  - two DFAs mays only disagree on a single string of length n1+n2+1
- perform a random walk on both automatas instead 
  - feed random symbols to both DFAs one at a time observing each outputs
  - probability of reaching a distinguishing state 

#### Practical solution for automata equivalence

The idea of verifying automata equivalence through random sampling based on 
the few coincidences principle is not usable for the reasons mentioned above.

Instead another idea stems from a field called __Automata Learning Theory__ 
and is based on __Angluin's L* algorithm__.

__Principles__
- white-box model is a "map" of the automata we want to verify
- Using the _W-method_ you generate specific queries to ask the white-box model
  - this replaces random sampling with strategic testing
- if the black-box has at most _n_ states and the _W-method_ is used then
  - _O(n².|Σ|)_ queries suffice for complete coverage
  
__LearnLib__ 
- framework used to test deterministic programs using techniques from automata learning theory 
  - like Angluin's L* and its variants
- automatically builds the "white-box" program or "hypothesis automaton"
- help verify the black box automata with structured query strategies 
  - random walks, W-method, Wp-method
- confirm equivalence of white-black boxes or gives a counterexample

#### Takeaway

- DFA equivalence is hard to prove with random samplings, few coincidences principle does not work well
  - Two DFAs may disagree only on a single string of length n1+n2+1 
  - it's hard to find the distinguishing input
- Automata learning theory has techniques to "verify" an automata identity
  - automata identity can be verified with only _O(n².|Σ|)_ queries
  - __LearnLib__ is an implementation of these techniques

### Equivalence of machine learning models with VC Dimension and Distribution-Dependent Equivalence

ML models are super complex and it's difficult to have point wise equivalence (agree on all inputs).
In this case we focus on the subcategory of classifiers programs.
- the input space is infinite 
  - polynomials work on a finite field 
  - automata work on discrete strings
  - ml models work on floating points over _|R_
    - floating points are used over integers because they are calculus engine
    - ml models learn through gradient descent which is based on calculus and requires floating points
    - floating points are necessary to model the world as curves rather than rigid steps as with integers
    - even text or image inputs are converted to vectors of floating points in ml models
    - floats could be replaced by integers but we would encounter the same issues
    - the main takeaway is that floats are necessary for their precision required by ml models
- for classifiers we don't really care about all inputs
  - classifiers are trained on a specific distribution  data 
  - we don't care if the white and black box models don't agree on input such like random noise
  
#### Definition: the Vapnik-Chervonenkis (VC) dimension

It's a way to measure the complexity of a ml models and is based on the concept of __shattering__.

__Shattering__
Shattering is a concept in machine learning that describes the ability of a class of functions _F: D -> {0,1}_ to perfectly 
classify all possible subsets of a given set of inputs _{x1, ..., xn} ⊂ D_. 
In other words, it measures the capacity of a model to fit any possible data distribution.

_{x1, ..., xn} ⊂ D_ is shattered by _F_ if for every outputs _{y1, ..., yn} ∈ {0, 1}^n_
there exists _f ∈ F_ such that _f(xi) = yi_

__VC Dimension__
the VC dimension of _F_ is the size of the biggest set of inputs that is shattered by _F_
_VCdim(F) = max{ m |  exists a set of size m that is shattered by F }_

__Examples__
- Threshold classifiers on _R: {x → 1 if [x ≥ θ] with θ ∈ R}_ has VCdim = 1
  - threshold only classify a single point
- Linear classifiers in _Rn : {x → 1 if [w · x ≥ b]}_ has VCdim = n + 1
  - linear classifiers capacity scales with the number of dimensions they operate in
- Decision trees of depth d: _VCdim = O(2d)_
   - trees capacity grows exponentially with its depth
- Neural networks with W weights: _VCdim = O(W log W )_
  - neural network capacity grows slightly faster than the number of its parameters/weights

#### Uniform convergence and equivalence guarantee

We want to prove that if the black and white box models agree on a specific number of random samples 
drawn from the model actual data distribution then you can be highly confident they 
they agree on the majority of the input distribution.

__Fundamental theorem of Statistical Learning__
Let 
- _F_ be a class of binary functions with _VCdim(F) = d_ with d a finite number 
- _D_ be any consistent distribution.
- _m_ the number of samples tested 
- _(x1, ..., xm)_ the input samples

for any _f in F_ we have 
_sum(f(xi))/m - Pr[f(x) = 1 | x ∈ D] ≤ ε_ as long as  _m ≥ C_ a value _C_ which depends on _d, ε_

__Explanation__
- _sum(f(xi))/m_:  the average of our test samples 
- _Pr[f(x) = 1 | x ∈ D]_: the actual average of our function _f_
- _... ≤ ε_: the distribution of our samples and the actual distribution are super close
- as long as  _m ≥ C_ a value _C_ which depends on _d, ε_
  - this theorem holds if we do enough samples, _m_ is big enough
  - _m_ grows with the _VCdim(F)_ of the model 
  - _m_ grows with the confidence _ε_ we desire

__Application to equivalence testing__
- _f, g ∈ F_ our white-black boxes models
- difference class _H = {x ↦ 1[f(x) ≠ g(x)]}_
  - an error detector 
  - _H_ outputs 1 for an input _x_ if _f and g_ disagree
- _VCdim(H) = O(d)_
  - because both _f, g_ belong to _F_ and _VCdim(F) = d_
  
then the probability that _f and g_ disagree on the distribution _D_ is mathematically capped
_Pr[f(x) != g(x)]  ≤ O(sqrt(d+log(1/β)/ m))_

__Explanation__
- the probability that your samples behavior do not match the the actual models behaviour is capped 
- the probability of false positive :
  - diminish with the number of samples
  - diminish with the confidence increase
  - increase with the VC dimension of the model
  
__Remark__
- for a neural network with _W_ parameters
- the VC based sample complexity is  _O(W log W/ε²)_
- this is doable for small models with fast queries 
- for bigger models the bounds can be tighter using Rademacher complexity or PAC-Bayes analysis
  - accounts for regularization and weight magnitude 
    - technique to prevent weights values to be too large 
    - else the model behaviour could change drastically depending on the inputs 
    - add more complexity to the models
  - does not only calculate bounds based on raw parameters count
  - using regularization one can prove that the model is actually simpler than just the raw number of parameters
    - models behaviour will be smoother over all inputs rather than complex erratic behaviour based on input
