# RSA

##

RSA and diffie-hellman provided a breakthrough in cryptography.
RSA relies on integer factorization and DH on discrete log problem (DLP).

Crypto based on DLP includes
- Diffie hellman
- El Gamal
- DSA
- ECC

Their difficulty depends on the groups we operate on.
For example on elliptic curve DLP has only exponential-time
attacks compared to other groups where DLP and factoring
usually have sub-exponential algorithms.

### Quantum

Shor's algorithm solves both factoring and DLP in polynomial time.
That's why RSA and ECC are broken by quantum computing.

### RSA Algorithm in a nutshell

- Pick two prime numbers _p_ and _q_
- Compute the max, size of the group, _max = p * q_
- Pick a prime number _pub_ smaller than _max_
- Compute the corresponding private key _priv_ with extended euclidean algorithm
- To encrypt a message _m_ you get _encrypted = m * pub_
- To decrypt you have _m = encrypted * priv_

### Limits of factoring (RSA)

Factoring is not the hardest problem and some algorithms like
quadratic sieve and general number field sieve reduce the
complexity of the problem.
Furthermore these algorithms are more efficient with
bigger numbers so the difficulty gap between factoring large numbers and factoring them reduce as the numbers grow.

This is why elliptic curves were developed.
