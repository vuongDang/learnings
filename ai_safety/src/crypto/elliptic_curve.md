# Elliptic Curve

## Group

Finite group which members fits the equation:
```
y^2 = x^3 + ax + b

where 4a^3 + 27b^2 != 0  (edge case where we get a degenerate curve)
```
![elliptic curve](https://cf-assets.www.cloudflare.com/zkvhlag99gkb/7C9LODC0OpfZlr9E8kqrSP/4248aa3bd2bf09e9f2c5eb7073c8ccfe/image00.png)

For cryptography we consider field containing only integers modulo p where p is prime.

### Properties of elliptic curves

- Any line intersects the curve at most at three points.
- Elliptic curves are symmetrical about the x-axis
- there is a point at infinity _O_ such that _O_ + _P_ = _P_ for any point _P_ on the curve (identity element)
- order of a curve _n_ are the number of points on the curve including the point at infinity

### + operation (elliptic curve addition)

The + operation consists of:
- tracing a line between the two operands/points
- finding the third point of intersection.
- finding the symmetric point compared to the x-axis.

_+ operation_
![plus operation](https://cf-assets.www.cloudflare.com/zkvhlag99gkb/5T0bi5LllWSsEHVH8u3LxV/948c802208e9cd39782d1587087a2670/image02.gif)

_+ operation on a finite group_
![plus operation on a finite group](https://cf-assets.www.cloudflare.com/zkvhlag99gkb/phKRQ5xVkAnEwByf2Ep3Z/75abbc71f0afa6f6405b2a409794f9c6/image01.gif)

To add a point to itself, we use the tangent line at the point and it should only intersect the curve at one point. The symmetric of this point is the result of the addition.

### Modulo

If the group modulo is prime:
- it's called a prime curve
- the group is cyclic, meaning it has generator _g_ that can generate
all elements of the group by repeated _dot_ operations
- all elements have a unique inverse

If the modulo is not prime:
- it's called a composite curve
- the security is lower because you can factorize the modulo
and solve the discrete logarithm problem on smaller groups
- there is a 0 element that you obtain by multiplying the prime
of the composite modulo
  - some elements are not invertible
  - division can fail

Any finite group or order _p_ where _p_ is prime is always cyclic.

### Discrete Logarithm Problem

#### Construct a secret

- We construct an elliptic curve group with a big order _P_
- We pick a message _m_ that we want to encrypt
- The encrypted message _encrypted = m * P_
  - to calculate _encrypted_ we can use the double-and-add algorithm
  - ex: if _m = 1029_
    - we compute _P+P=2P_ first then _2P+2P=4P_, ... up to _1024P_
    - then _encrypted = 1024P + 4P + 1P_
    - this takes 0(log2(m)) steps

#### Attacker trying to get secret message _m_

Attacker knows _encrypted_ and _P_.
It has no choice but to try all values of _m_ until it finds the correct one such
that _encrypted = m * P_
It takes him _O(m)_ steps which is linear compared to the secret construction which is logarithmic

### In practice

Only certain elliptic curves are used in practice because they were verified to be secure.
There exists group-specific attacks that works on certain elliptic curves.

## Practical example

### Setup

We work on finite field of integers modulo 17.
We define the elliptic curve :
```
y^2 = x^3 + 2x + 2 mod(17)
```

One point of the curve is _P = (5,1)_.
_P_ has a fairly large order on this curve so we use it
as a base point (generator).

### Key generation

We use ECC Diffie-Hellman.
For Alice:
- private key is a random integer _a = 7_
- public key is  _A = aP = 7P = (12,15)_

For Bob:
- private key is a random integer _b = 11_
- public key is  _B = bP = 11P = (16,16)_

_A_ and _B_ are public and _a_ and _b_ are private.

## More info

- More efficient than RSA
  - for 228-RSA you can boil a teaspoon of water worth of enery to solve it
  - for 228-ECC you could boil all the water on eartch
- ECDHE_RSA means Elliptic Curve Diffie-Hellman Ephemeral
  - key exchange protocol based on elliptic curves
  - RSA means the RSA is used to prove the identity of the server
    - RSA is used when the server has a RSA key pair
    - ECDSA (Elliptic Curve Digital Signature Algorithm) would be used otherwise
