# 

Probabilistic watermarking to identity watermarking
- classic watermarking checks if the correlation between a secret watermark vector 
and the model output exceeds a certain threshold (e.g 0.8)
- to be more strict you increase the watermarking threshold to 1 or very near on the 
specific challenge inputs

ZK implementation:

- build a ZK circuit that calculates the _cosine similarity_ or _dot product_ 
between the model output and the committed watermark in the circuit 
- if proof reveals true then the correlation is above the threshold while keeping the watermark secret

## Example 

- Model _Msafe_ a 7B parameter LLM  
- commitment _C_ of the weights of _Msafe_
- challenge: Alice wants to verify Bob is using _Msafe_ without Bob revealing the weights (14GB)

0. __Setup phase__ 
- Before the model runs or done after a model update
- circuit is coded and compiled with halo2/circom into arithmetic constraints by a trusted entity
- it generates two static binaries:
  - Proving Key (_PK_): huge file (GBs) necessary to generate proofs 
  - Verification Key (_VK_): tiny file (KBs) necessary to verify proofs
  - _VK_ is unique to this circuit logic, changing one constraint would produce another verification key
- Alice receives _VK_ from a trusted source or compile it herself by downloading the standard ModelEquality circuit
- Alice will only accept proofs that verify against _VK_

1. __Commitment phase__
- Bob compute the Merkle Root of _Msafe_ weights _C_
  - he can either publicly share _C_ 
  - he can also ask a trusted third party to compute _C_, sign it and share it
- Alice store this hash/commitmment _C_
- Bob deploys the model on the server 

2. __Strict Watermark setup__
- Alice selects a random, low-entropy, input vector as a challenge _xchal_
- Alice computes the expected output vector _yexpected_ using a local copy of _Msafe_
- Alice defines a strict threshold that an output must match with cosine similarity 
of > 0.9999

3. __The challenge__
- Alice sends _xchal_ to Bob 
- Alice asks that Bob model prove that it produces the correct output for this input 
and that its model weights match commitment _C_

4. __ZK execution__
Inside the ZK circuit 
- the circuit loads the weights _W_ in RAM 
- the circuit computes the commitment _W_ asserts it matches _C_ 
  - if the weights changed then it fails at this step
- circuit computes output _yactual = Model(xchal, W)_
  - to save costs it might only compute a part of the model or a specific layer 
- circuit calculates similarity _Sim = CosineSimilarity(yactual,  yexpected)_
- if _Sim > 0.9999_ then the circuit outputs true

5. __Verification__ 
- Bob sends the ZK proof and the boolean result to Alice 
- Alice verifies the proof

__Result__
- Alice knows Bob ran a model which weight hashes to _C_
- Alice knows Bob model behaved as expected on the challenge
