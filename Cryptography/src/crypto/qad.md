# Quadradic Arithmetic Programs

## ZK overview

From a program to zkSNARKs:
- computation
- algebraic circuit
- R1CS
- Quadradic Arithmetic Programs
- Linear PCP
- Linear Interactive Proofs
- zkSNARK

3 processes:
- Transform the program into QAP the "right form"
- Transform an input to the program into a "witness to the QAP", a solution to the QAP
- Create the actual zero knowledge proof for this witness
- Additionally: verify a proof that you receive

## Computation to algebraic circuit, flattening to gates

Transform the initial program into a sequence of statements
that are two forms:
- assignment `x = y`
- assignment with binary op `x = y (op) z`
  with op can be (+,-,*,/)

### Example
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

## Circuit to R1CS

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


### Transforming logic gate into R1CS

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

### Complete R1CS with a solution

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

## R1CS to QAP

The idea is to apply the same logic as previous step but with
polynomials instead of dot products.
We go from (A, B, C) 3 groups of 4 vectors to six groups of three
degree-3 polynomials using Lagrange Interpolation

### Lagrange interpolation

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

### Lagrange complexity

Algorithm showed previously is _O(n^3)_ but can be reduced
to _O(n^2)_ or further using fast Fourier transform algorithms.

### Using Lagrange on our R1CS

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
