# Advanced Cryptography

## Primitives

- Commitment Schemes: Lock in a value now, reveal it later
- Multi-Party Computation (MPC): Multiple parties compute a joint function using secret inputs
- Homomorphic Encryption: Perform computations directly on encrypted data
- Oblivious Transfer (OT):
  Sender has multiple pieces of data, receiver learns only 1 of them and sender does not know which one
- Functional Encryption: decryption key reveals only the output of a function on the input, not the full input itself
- Threshold Cryptography: split a key into shards, only with a subsert of n shards can we reconstruct/use the key
- Group/Ring Signatures: a group member can sign on behalf of the group; verifier can check that its a valid group
  member but not which one
- Attribute-Based Encryption (ABE): access to encrypted data based on attributes/policies
- Zero-Knowledge Proofs (ZKPs): prove knowledge of a secret without revealing it
- Bulletproofs: special type of ZKPs optimised for range or arithmetic relations
- Verifiable Delay Functions (VDFs): Proof of elapsed time, a function that takes a guaranteed minimum amount of time to compute.

## Categories

### Privacy-Preserving Proofs

#### Zero-Knowledge Proofs (ZKPs)

- zk-SNARKs
- zk-STARKs
- Bulletproofs
- Commit-And-Prove

#### Commitment

- Hash-based
- Pedersen commitments (used in ZKPs, Bulletproofs)

### Multi-Party Computation (MPC)

- Secret Sharing (Shamir's, threshold crypto)
- Oblivious Transfer
- Garbled Circuits
- Applications: private analytics, threshold crypto

### Homomorphic & Functional Encryption

- Parially HE (RSA, Paillier)
- Fully Homomorphic Encryption (FHE)
- Function Encryption (restricted outputs only)
- Applications: encrypted ML, secure cloud computing

### Advanced Signatures

- Group Signatures (anonymity within a group)
- Ring Signatures (signer hidden in a set)
- Threshold Signatures (requires multiple parties)
- Attribute-Based Encryption ()

### Other Primitives

- Verifiable Delay Functions (VDFs)
- Verifiable Random Functions (VRFs)
- Accumulators (compact set membership proofs)
