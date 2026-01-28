# Commit and Prove 

Goal: Being able to prove that we committed to certain values without revealing them.

## Example

Alice wants to transfer money to another account Bob without revealing it's coming from Alice.
1. Alice creates a commitment C
2. Alice sends 1ETH and the commitment to a pool of deposits (like Tornado Cash smart contract)
3. The pool of deposits only knows that there was a deposit of 1ETH associated with a commitment C
that was merged into the merkle tree of all deposits
4. Bob wants to fetch the 1ETH without revealing he is taking Alice deposit
5. Bob generates a ZK proof with:
  - the commitment C (given by Alice)
  - the merkle root (fingerprint of all deposits)
  - the statement: I know that there is a deposit associated to commitment C inside the Merkle Tree at root R
6. Bob sends the proof to the smart contract 
7. Smart contract checks the proof validity 
8. Smart contract transfers the 1ETH to Bob

## Common commitment schemes

- KZG (Kate) commitments: used in ethereum. very small proofs but requires a trusted setup
- FRI (Fast Reed-Solomon): quantum-secure and transparent (no trusted setup) but larger sizes
- Pedersen Commitments: used in older systems, relies on elliptic curve hardness

## Commitment and ZKP inceptions 

- Applicative commitment schemes uses ZKP (like the example above)
- Within ZKP protocols themselves a commitment scheme is used
  - Witness is too large in ZKP
  - the prover commits to the witness using a polynomial commitment scheme like FRI
  - the verifier can ask "show me step #500 of the circuit execution"
  - the prover can reveal step #500 and that it matches the commitment
