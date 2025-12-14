# Overview

## Symmetric Cryptography

### Schemes

#### Most popular

- AES
- Salsa20/ChaCha20

#### Others

- Serpent
- Twofish
- RC5
- RC6- Camellia

#### Insecure

- DES
- 3DES
- RC2
- RC4
- Blowfish
- GOST

### AES

- Block ciphers, encrypts data in fixed-size blocks (usually 128-bits)
  - padding is used to ensure that the data is a multiple of the block size (PKCS7 padding)
- official recommended by NIST in 2001
- most popular symmetric cipher: secure, fast, standardized and supported by most platforms
- 128-bit block cipher
- 128, 192, 256-bit key sizes
- usually used in block modes like CTR or GCM
- block modes usually requires a 128-bit IV/nonce (initialization vector or nonce)
- very efficient with dedicated hardware
  - without dedicated hardware it can be slow and vulnerable to side-channel attacks
  
#### AES modes

Confidentiality only modes

- only provides cipher text
- ECB (Electronic Codebook), insecure. Blocks are encrypted independently
- CBC (Cipher Block Chaining), secure.
  - Blocks are encrypted sequentially, each block depends on the previous one
  - Each plaintext block is XORed with the previous cipher text block before encryption
  - Requires random initialization vector (IV)
  - Decryption requires the same IV and is sequential
  - Decryption can't be parallelized
  - Pitfalls:
    - IV reuse can lead to security vulnerabilities
    - no authentication -> vulnerable to padding oracle attacks
- CTR (Counter)
  - turns block cipher into stream cipher
  - a counter is used to produce a sequence which is guaranteed to not repeat (increment counter is often used)
  - very fast: encryption and decryption are parallelizable
  - algo:
    1. Pick a nonce (unique per key)
    2. Generate a sequence of counter values
    - for example with increment counter
    - \_(nonce || 0, nonce || 1, nonce || 2, ...)
    3. Encrypt each counter value with AES to produce a keystream
    4. XOR the keystream with the plaintext to produce the ciphertext
  - random access: you can decrypt block n without decrypting previous blocks

Authenticated Encryption (AE) modes

- Guarantees both authenticity and confidentiality
- Many AE allows associated data (AEAD), which is plaintext that is authenticated but not encrypted
  - For example for the header of network packets
- GCM (Galois/Counter Mode)
  - widely used in TLS, disk encryption, or VPN protocols
  - efficient and parallelizable
  - uses CTR for confidentiality
  - authentication is guaranteed by polynomial hash (GHASH) of the ciphertext and associated data
  - algorithm:
    1. Pick a nonce (unique per key)
    2. Generate a sequence of counter values
    - for example with increment counter
    3. Encrypt each counter value with AES to produce a keystream
    4. XOR the keystream with the plaintext to produce the ciphertext
    5. Compute GHASH of the ciphertext and associated data (tag)
    6. Append the GHASH to the ciphertext
- CCM (Counter with CBC-MAC)
  - more complex than GCM but less fragile to nonce vulnerabilities (still should not be reused)
  - uses CTR for confidentiality
  - uses CBC-MAC for authentication
  - algorithm:
    1. Pick a nonce (unique per key)
    2. Generate a sequence of counter values
    - for example with increment counter
    3. Encrypt each counter value with AES to produce a keystream
    4. XOR the keystream with the plaintext to produce the ciphertext
    5. Compute CBC-MAC of the ciphertext and associated data (tag)
    6. Append the CBC-MAC to the ciphertext

### Salsa20/ChaCha20

- Stream ciphers
- Salsa20 is the original (2005) while ChaCha20 is an improved version published later (2008)
- Chacha20 and Poly1305 (message authentication code) are combined to form _ChaCha20-Poly1305_
  - an authenticated encryption scheme
  - used in HTTP3, OpenSSH, WireGuard
- ChaCha20 usually performs better than AES where the CPU does not have AES-acceleration features
  - mobile devices or IoT
  - very fast in software
  - resistant to timing attacks


## Asymmetric Encryption

### Schemes

- RSA (1977)
- DSA (1991)
- Diffie-Hellman
- ECC (2000)
  - EdDSA (2011)
- Post Quantum (2016)

### RSA

- used mostly for signing
- based on integer factorization
- security depends on factoring difficulty
- key sizes are 2048 minimum, 3072/4096 common
- well understood and widely supported
- but slow with big keys
- still used due to legacy, support, adequate security, easier to deploy

### Diffie-Hellman

- used for key exchange (create a shared key between two parties)
- usually paired with digital signature to avoid man in the middle (MITM)
- based on discrete log problem in multiplicative groups

### DSA

- used for digital signature
- based on discrete log problem in multiplicative groups

### ECC

- ECDH (Elliptic Curve Diffie-Hellman) for key exchange
- for signature:
  - ECDSA (Elliptic Curve Digital Signature Algorithm)
  - EdDSA (Edward-Curve Digital Signature Algorithm)
    - pros
      - more efficient that ECDSA
      - resistant to side-channels
      - easier to implement correctly than ECDSA
      - compact keys and signatures
    - uses Edwards curve (Curve25519, Ed448)
- based on discrete log problem in elliptip curve groups
- efficient with small keys, fast in software
- you have to use recommended curves
  - Ed448, Curve25519, secp256r1

### Post-Quantum

Shor's algorithm can break both RSA and ECC in polynomial time.
Symmetric algorithm is also weakened but in better state that public key crypto.
We just need bigger keys for symmetric crypto.

#### NIST competition finalists (2016-2022):

##### Signatures

- Dilithium, lattice based
- Falcon, lattice based
- SPHINCS+, hash-based

##### Key Encapsulation Mechanism (KEM)

This is a mechanism to replace key exchange like DH.

- KEM
  - only the sender generates the shared key, encapsulates it with the receiver public key and sends it.
  - one way ke transport
  - simpler
  - efficient
  - needs careful design for forward secrecy
- DH
  - mutual agreement
  - inteactive
  - can provide forward secrecy when using ephemeral keys

PQC KEM schemes:

- Kyber, lattice based
- McEliece, code based

## Hash, MAC and Digital Signature

- Hash is a one-way function: integrity
- MAC uses symmetric key cryptography + (generally hashing ): integrity + authentication
  - only users who has the shared key can build and verify the MAC
- Digital Signature uses asymmetric key cryptography + (generally hashing ): integrity + authentication + non-repudiation

### Hash schemes

#### Popular

- SHA-2
  - secure widely used,
  - SHA-256 most used
  - Merkle-Damgard construction
- SHA-3 (2015)
  - Keccac algorithm
  - sponge construction
  - SHA3-224, SHA3256,..., SHA3-512
  - Extendable-Output Functions (XOFs): SHAKE128, SHAKE256
  - different design than SHA-2, better security
- RIPEMD
  - designed in Europe
  - MD-like construction
  - most knwon RIPEMD-160
  - niche but used in bitcoin
- BLAKE2 (2013)
  - based on ChaCha/Salsa20 primitives
  - faster than SHA-2
  - widely used
  - variants
    - BLAKE2b (64-bit platforms)
    - BLAKE2s (32-bit, mobile)
- BLAKE3
  - very fast, parallelizable, variable output length
  - for modern CPUs and multicore

#### Insecure

- MD family (MD2, MD4, MD5)
- SHA-1

#### For specific password hashing

- bcrypt (1999)
  - based on Blowfish
  - still used
  - slower than SHA
- scrypt (2009)
  - memory intensive, which is good for hashing to slow down brute-force attacks
  - it's cheaper to tackle CPU intensive problems than RAM intensive problems
- Argon2 (2015)
  - winner of the 2015-password hashing competition
  - recommended today for password storage
  - tunable memory hardness
  - variants
    - Argon2d (data-dependent)
      - memory access depends on the password
      - makes it resistant to GPU/ASIC cracking since patterns are not deterministic
      - vulnerable to side-channel attacks because memory access depends on input
      - good when side-channel attacks aren't a concern (in controlled environments)
    - Argon2i (data-independent)
      - memory access is independent of the password
      - good against side-channels
      - weaker against GPU/ASIC
      - best use in multi-tenan servers or browsers
    - Argon2id
      - combines Argon2i (first pass) + Argon2d (remaining passes)
      - balances both
      - NIST recommendation & default in most libs

### MAC (message authentication code)

- H(ash)MAC
  - most popular, generic and default MAC scheme
  - often used with SHA256
  - use case: TLS, APis, OAuth tokens
- C(ipher)MAC
  - based on cipher block (like AES)
  - usually used when dedicated AES hardware is present
- G(alois)MAC
  - based on AES-GCM
  - very fast and parallelizable
  - requires nonce uniqueness
  - use cases: TLS, IPsec
  - best when you already uses AES-GCM
- U(niversal)MAC
  - universal hashing + one-time pad
  - super fast, designed for software efficiency
  - use cases: when you need high speed
- Poly1305
  - one-time MAC using polynomial evaluation modulo a large prime
  - very fast on modern CPUs and authentication of short messages
  - Usually paired with ChaCha20
  - replaces HMAC when message is short and low-power devices

## Ad
