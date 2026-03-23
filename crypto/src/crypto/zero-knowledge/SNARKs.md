# SNARKs

## SNARKs overview

SNARKs like Groth16.

1. Trusted Setup
- _tau_, the toxic waste, is created and part of a finite field _Fp_ with _p_ 
- elliptic curve group _G_ is created from elements of _Fp_ and has generator _g_
- Common Reference String: _CRS = (g^1, g^tau, g^(tau^2), ...)_ are elements of _G_
- the _CRS_ are elements of _Fp_ 

2. Design 
- Code to algebraic circuit 
  - turn a program into a list of circuits 
  - a circuit being of the form "x = y * z" or "x = y + z"
  - it's like deconstructing a program into a list of simple statements 
  - tricks are involved to turn condition and loop into list of circuits
- circuit to R1CS (Rank 1 Constraint System)
  - turn each circuit into a R1CS
  - so from a list of circuit you get a list of R1CS
  - this list of R1Cs can be gathered into 3 matrices A, B and C such that _A.s * B.s = C.s_
  - A, B, C are of size (nb_of_circuits, nb_of_variables_in_all_circuits)
  - vector _s_ is a solution to equation above (the witness)
    - to verify that _s_ verifies _A.s * B.s = C.s_ you have to compute _nb_of_variables_in_all_circuits_ of equations
  - R1CS is a list of constraint on how each variable of the circuit should behave at each step of the circuit
- R1CS to QAP (Quadratic Arithmetic Programs)
  - compress your constraint vectors into a template to produce 3 polynomials _A(x), B(x), C(x)_
  - use your witness to compute _A(x), B(x), C(x)_ separately
  - you can build the polynomial _P(x) = A(x) * B(x) - C(x)_
  - Uses Lagrange interpolation to produce the polynomial
  - this transform a list of vectors/constraints into a list of polynomials for every variable of the circuit 
    - ex: for variable x in the circuit the constraint were [3, 0, 5] 
    - [3, 0, 5] are the coefficients of x in gates 1, 2 and 3
    - QAP is then the polynomial Px(x) that passes by the points (1,3), (2, 0), (3, 5)
    - hence whole QAP are a map between variables/state of the circuit to a polynomial describing its behavior 
    during a circuit execution

3. Create a proof
- The prover uses the QAP template and the witness to produce 3 polynomials _A(x), B(x), C(x)_ separately
- then it can build the polynomial _P(x) = A(x) * B(x) - C(x)_
- if the witness is valid then the private polynomial _P(x)_ is perfectly divisible by the public polynomial _Z(x)_
- _Z(x)_ was derived from the circuits such that it divides _P(x)_ for any valid witness
- if this is true you have _P(x) = H(x) * Z(x)_ with _H(x)_ the leftover quotient (private)
- _P, H and Z_ are polynomials defined over the finite field _Fp_
- Prover derives _(Proof_a, Proof_b, Proof_c)_ elements of the elliptic curve _G_
  - _Proof_a = g^(P(tau))_
            _= g^(c0 + c1 * tau + c2 * tau^2 + ...)_
            _= g^c0 * (g^tau)^c1 * (g^(tau^2))^c2 * ..._
  - with _(g, g^tau, g^(tau^2), ...)_ elements of the CRS
  - _Proof_b = g^(H(tau))_ 
  - _Proof_c = g^(Z(tau))_ was created during trusted setup
  - tau comes from the trusted setup and is the toxic waste, no one knows its value
  - (Proof_a, Proof_b, Proof_c) are derived from the CRS so they can't be faked

4. Verify the proof
- Uses bilinear pairing _e_ to check:
  - _e_ has this property _e(g^a, g^b) = e(g, g)^(ab)_
  - hence, _e(Proof_a) = e(Proof_b, Proof_c)_
  - if this is true it means that _P(tau) = H(tau) * Z(tau)_
- Hence the verifier knows that the prover know the _P(x)_ hence a witness to the program
- the verifier did not learn about _P(x)_ nor _tau_


## Setup phase: code to QAP

### Code to algebraic circuit, flattening to gates

This is now usually done using a compiler.

Transform the initial program into a sequence of statements
that are two forms:
- assignment `x = y`
- assignment with binary op `x = y (op) z`
  with op can be (+,-,*,/)

#### Example
_Initial program_
```
def toto(x):
    y = x**3
    return x + y + 5
```
_algebraic circuit_
```
temp1 = x * x
y = temp1 * x
temp2 = y + x
out = temp2 + 5
```

Each statement of our circuit can be considered a logic gate.

### Circuit to R1CS

Rank-1 Constraint System (R1CS) is a sequence of groups of three
vectors `(a, b, c)`.
The solution to an R1CS is a vector `s` where:
```
s . a * s . b - s. c = 0
```

For each logic gate of our circuit we will have one R1CS.

All vectors of our R1CS of a circuit will have the same length.
This length corresponds to the total number of variables in the system
(including a dummy variable `one` representing the number 1).

In our example all vectors will be of length 6 which values
relates to the following vector of variables:
```
[ one, x, out, temp1, y, temp2 ]
```


#### Transforming logic gate into R1CS

Standard algorigthm depending on operators and if operands
are variables or constants.

_gate1 to R1CS_
```
gate1: temp1 = x * x

R1CS:
    [one, x, out, temp1, y, temp2]
a = [ 0  ,1, 0  , 0    , 0, 0    ]
b = [ 0  ,1, 0  , 0    , 0, 0    ]
c = [ 0  ,0, 0  , 1    , 0, 0    ]

1 solution to s . a * s . b - s. c = 0:
s = [ 0  ,3, 0  , 9    , 0, 0    ]
```
_gate2 to R1CS_
```
gate2: y = temp1 * x

R1CS:
    [one, x, out, temp1, y, temp2]
a = [ 0  ,0, 0  , 1    , 0, 0    ]
b = [ 0  ,1, 0  , 0    , 0, 0    ]
c = [ 0  ,0, 0  , 0    , 1, 0    ]

1 solution to s . a * s . b - s. c = 0:
s = [ 0  ,3, 0  , 9    ,27, 0    ]
```
_gate3 to R1CS_
```
gate3: temp2 = y + x

R1CS:
    [one, x, out, temp1, y, temp2]
a = [ 0  ,1, 0  , 0    , 1, 0    ]
b = [ 1  ,0, 0  , 0    , 0, 0    ]
c = [ 0  ,0, 0  , 0    , 0, 1    ]

1 solution to s . a * s . b - s. c = 0:
s = [ 1  ,3, 0  , 9    ,27, 30   ]
```
_gate4 to R1CS_
```
gate4: out = temp2 + 5

R1CS:
    [one, x, out, temp1, y, temp2]
a = [ 5  ,0, 0  , 0    , 0, 1    ]
b = [ 1  ,0, 0  , 0    , 0, 0    ]
c = [ 0  ,0, 1  , 0    , 0, 0    ]

1 solution to s . a * s . b - s. c = 0:
s = [ 1  ,3, 35  , 9    ,27, 30   ]
```

#### Complete R1CS with a solution

```
A:
[0, 1, 0, 0, 0, 0]
[0, 0, 0, 1, 0, 0]
[0, 1, 0, 0, 1, 0]
[5, 0, 0, 0, 0, 1]

B:
[0, 1, 0, 0, 0, 0]
[0, 1, 0, 0, 0, 0]
[1, 0, 0, 0, 0, 0]
[1, 0, 0, 0, 0, 0]

C:
[0, 0, 0, 1, 0, 0]
[0, 0, 0, 0, 1, 0]
[0, 0, 0, 0, 0, 1]
[0, 0, 1, 0, 0, 0]

s = [ 1  ,3, 35  , 9    ,27, 30   ]
```

### R1CS to QAP

The idea is to apply the same logic as previous step but with
polynomials instead of dot products.
We go from (A, B, C) 3 groups of 4 vectors to six groups of three
degree-3 polynomials using Lagrange Interpolation

#### Lagrange interpolation

Allow us to find a polynomial that passes through a set of points (x,y).

_Example with this set of points M_:
```
M = (1, 3), (2,2), (3,4)
```
We want polynomial `P` that passes through points `M`.

_1. Starts by finding a polynomial that passes through M1:_
```
M1 = (1, 3), (2,0), (3,0)
gives P1:
P1(x) = (x-2) * (x-3) * a
and we know P1(1) = 3 so, a = 1.5
P1 = 1.5 * x^2 - 7.5x + 9
```
_2. Do the same with other two points:_
```
M2 = ((1,0), (2,2), (3,0))
M3 = ((1,0), (2,0), (3,4))
we get
P2(x) = -2x^2 + 8x - 6
P3(x) = 2x^2 - 6x + 4
```

_3. Calculate P:_
```
P(x) = P1(x) + P2(x) + P3(x)
P(x) = 1.5x^2 - 5.5x + 7
```
It works since for each point of set _M_
only one of _P1, P2 and P3_ are different from
0 and its value matches the corresponding point

#### Lagrange complexity

Algorithm showed previously is _O(n^3)_ but can be reduced
to _O(n^2)_ or further using fast Fourier transform algorithms.

#### Using Lagrange on our R1CS

Take the first value out of every vector _a_ and make a
polynomial out of it. Repeat this for every index of _a_, _b_ and _c_ vectors.

Example for the first index of _a_ vectors

```
A:
[0, 1, 0, 0, 0, 0]
[0, 0, 0, 1, 0, 0]
[0, 1, 0, 0, 1, 0]
[5, 0, 0, 0, 0, 1]
```
_1. Taking the value of A at index 0, gives us this set of points_
```
values of A first column:
(0, 0, 0, 5)
Set of points for first column of A where x values are replaced
by the index of the a vector in A.
(1,0) (2,0) (3,0) (4,5)
```
_2. Lagrange interpolation on this set of points_
```
Lagrange interpolation on points ((1,0) (2,0) (3,0) (4,5))
gives us:
Pa1(x) = 0.83x^3 - 5x^2 + 9.16x - 5
```

_3. Repeat for all indexes of A, B and C_
We get with coefficient of highest order on the right side:
```
A polynomials:
A1 = [-5.0, 9.166, -5.0, 0.833]
A2 = [8.0, -11.333, 5.0, -0.666]
A3 = [0.0, 0.0, 0.0, 0.0]
A4 = [-6.0, 9.5, -4.0, 0.5]
A5 = [4.0, -7.0, 3.5, -0.5]
A6 = [-1.0, 1.833, -1.0, 0.166]

B polynomials
B1 = [3.0, -5.166, 2.5, -0.333]
B2 = [-2.0, 5.166, -2.5, 0.333]
B3 = [0.0, 0.0, 0.0, 0.0]
B4 = [0.0, 0.0, 0.0, 0.0]
B5 = [0.0, 0.0, 0.0, 0.0]
B6 = [0.0, 0.0, 0.0, 0.0]

C polynomials
C1 = [0.0, 0.0, 0.0, 0.0]
C2 = [0.0, 0.0, 0.0, 0.0]
C3 = [-1.0, 1.833, -1.0, 0.166]
C4 = [4.0, -4.333, 1.5, -0.166]
C5 = [-6.0, 9.5, -4.0, 0.5]
C6 = [4.0, -7.0, 3.5, -0.5]
```

This set of polynomials are the parameters for this QAP instance
and can be reused.
This is the result of transformation from program to QAP.

## Check the QAP with a witness

The point of this transformation is that we are able
to check all the R1CS constraints at the same time
by doing the dot product check on the polynomials.

Remember our previous solution _s_ to our R1CS we created when building
the R1CS:
```
s = [1,3,35,9,27,30]
```
If a witness _s_ is a solution to our R1CS then
_t(x) = s.A * s.B - s.C = 0_
and _t(xs) = 0_ for _xs_ equal to our logic gates
points ( _(1, 2, 3, 4)_ in our example).

We check if _s_ works with our QAP form.
We calculate _s.A_ in detail:
```

                              [-5.0, 9.166  , -5.0, 0.833 ]
                              [8.0 , -11.333, 5. 0, -0.666]
s.A = [1, 3, 35, 9, 27, 30] . [0.0 , 0.0    , 0.0 , 0.0   ]
                              [-6.0, 9.5    , -4.0, 0.5   ]
                              [4.0 , -7.0   , 3.5 , -0.5  ]
                              [-1.0, 1.833  , -1.0, 0.166 ]

s.A = [43.0, -73.333, 38.5, -5.166]
```
We get:
```
s . A = [43.0, -73.333, 38.5, -5.166]
s . B = [-3.0, 10.333, -5.0, 0.666]
s . C = [-41.0, 71.666, -24.5, 2.833]
```
We calculate _t_:
```
t = s.A * s.B - s.C
t = [-88.0, 592.666, -1063.777, 805.833, -294.777, 51.5, -3.444]
```
We could check that _t(x) = 0 with x in (1, 2, 3, 4)_.
But a faster way, at scale, is to check that _t_ is divisible
by the minimal polynomial _Z_.
We have _Z_ the minimal polynomial, the simplest polynomial
that is equal to _0_ at the logic gates.
In our example:
```
Z = (x-1)(x-2)(x-3)(x-4)
Z = [24, -50, 35, -10, 1]
```

We divide _t_ by _Z_ and check if there is a remainder
```
t/Z = [-88.0, 592.666, -1063.777, 805.833, -294.777, 51.5, -3.444] / [24, -50, 35, -10, 1]
t/Z = [0, -3.444, 17.055, -3.666]
t/Z has no remainder so t is divisible by Z
```

So _s_ is a solution to the QAP _(A,B,C)_.
This was a simplification with integers but this is usually
done with a finite-sized set so the arithmetic is different and
does not contain floating values.
