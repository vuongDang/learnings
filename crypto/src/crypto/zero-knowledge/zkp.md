# Zero-Knowledge Proofs (ZKPs)

__Goal__
Prove that a statement is true without revealing information except the validity of the statement

## What can you prove

- Any statement that can be verified by a computer program

## Core properties 

1. __Completeness__
If the statement is true, an honest prover can convince an honest verifier with high probability.

2. __Soundness__ 
If the statement is false, no dishonest prover can convince the verifier.

3. __Zero-knowledge__
If the statement is true, the verifier learns nothing other than the fact that this is true.

## Analogy

You play "Where's Waldo" with a friend on a poster. You want to prove to him that you found Waldo without 
revealing its position.

A method is: 
- cut a big piece of cardboard and make a hole in it, roughly Waldo size 
- place the cardboard such that the hole shows Waldo 
- since the cardboard is so big it does not give info on Waldo position on the poster
- you can show to the friend that you found Waldo by seeing Waldo through the cardboard hole but 
the friend has no information about Waldo position in the poster

## Techniques 

- zk-STARKs (Scalable Transparent Argument of Knowledge)
  - Small proof size 
  - requires trusted setup
  - not quantum secure
  - relies on elliptic curve math
- zk-SNARKs (Succinct Non-interactive Argument of Knowledge)
  - transparent, don't need trusted setup
  - scalable, proof generation is parallelizable which is suitable
  when verifying a lot of data
  - quantum resistant 
  - relies on hash computation

### STARKs vs SNARKs

| Feature | zk-SNARK | zk-STARK |
| :--- | :--- | :--- |
| **Proof Size** | Tiny (~288 bytes) | Large (~tens of KB) |
| **Verification Speed** | Extremely Fast | Very Fast |
| **Trusted Setup?** | Yes (Usually) | No (Transparent) |
| **Quantum Proof?** | No | Yes |
| **Gas Cost (Ethereum)** | Cheaper for single proofs | More expensive initially |
| **Complexity** | High | Very High |
| **Creation** | 2012 | 2018 |
| **Prover speed** | Fast for small circuit | Slower for single proof
| **Prover parallelization** | Difficult | Dighly parallelizable

### Usecases

STARKs
  - long-term security (quantum-resistant)
  - maximum transparency 
  - handling the verification of a lot of data
  
SNARKs:
  - keeping the cost low
  - the data to verify is small
  - needs speed
  
### Trusted Setup

SNARKs require a trusted setup to work. 
The main issue is that an initial secret key (also called toxic waste) is required to create the setup.
Anyone who knows the toxic waste is able to 
- produce proofs of false statements
- in some cases break the zero-knowledge assumptions of past proofs

#### Using MCP for trusted setup

Using the MCP the toxic waste is generated through all the participants shards. If at least one 
of the participant is honest the toxic waste can't be regenerated.
- Zcash (2016): participants drove to random locations with geiger counters as source of randomness
  for the toxic waste 
- Ethereum did MCP over 140,000 participants contributing randomness from their browsers

## ZKP trilemma 

- Proof size (how much storage does it take)
- Proving time (how long to generate a proof)
- Verification time (how long to verify a proof)

## Protocols breakdown 

| Protocol | Category | Trusted Setup? | Math | Proof Size | Best For... |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Groth16** | SNARK | Yes (Per circuit) | R1CS/ECC | Smallest (~200B) | On-chain gas efficiency |
| **PLONK** | SNARK | Yes (Universal) | Permutation Argument/Custom Gates | Small (~400B) | Most DeFi applications |
| **Halo2** | SNARK | No | Permutation Argument/Custom Gates |Small-Medium | Privacy & Recursion |
| **Plonky2** | SNARK/STARK | No | Hybrid | Medium (~45KB) | High-speed mobile proving |
| **STARKs** | STARK | No | Hash Functions / Polynomials | Largest (~100KB+) | Quantum security & Scaling |
| **Bulletproofs** | Bulletproofs | Inner Product Arguments on Pedersen Commitments | No | Tiny | Slow verifier -> Simple small checks like range proofs |

### Groth16 

Used for gas efficiency on Ethereum. It produces the smallest proofs.
They are super cheap to verify but requires trusted setup per circuit

### PLONK

Single universal setup for any programs up to a certain size.
Created to solve Groth16 problems. 

### Halo2 

Designed for recursion.
Used by Zcash. Uses a technique called "Accumulation" to achieve recursion without trusted setup.

### Plonky2 

Designed for recursion.
Hybrid using STARK-style speed with SNARK-style recursive features.
It's super fast on consumer hardware like phones

### STARKs 

"Pure" form of the tech. No trust and quantum resistant at the cost of proof size.

### Interactive ZKPs 

Before non-interactive proofs like SNARKs both the prover and verifier needed to be online
at the same time to talk back and forth.
- Schnorr signatures 
- Sigma protocols

## Recursion

The idea is the ability to have a proof of a proof. 
The Mina Protocol blockchains uses it which allows it to have a single small proof of 22kB to verify its whole history.
With recursion you can include previous proof in your actual proof.
In the Mina protocol:
- a block is produced, a ZK-SNARK is generated to prove the validity of the transactions of this block 
- the next block is produced, a new ZK-SNARK is generated and it proves 2 things.
  - the transactions in this block are valid 
  - the previous block SNARK proof is valid

### Pros and Cons 

This is specific to blockchain.
__Pros__
+ __Infinite scalability__: you can repeatedly produce blocks and proofs and you only need to verify the last one 
  to check the entire history
+ __Verification is cheap__: you can use a smartphone to check the entire state of massive Layer2 network
+ __Interoperability__: you can check the state of a whole chain with the latest proof which makes bridges between chains much simpler

__Cons__
- __Proof generation is complex__: each fold of the recursive proof is checked and investigated to produce the latest one 
so it gets more and more expensive to produce one. It requires to verify all the old proof and create the new one during proof generation.
- __Specialized harware is necessary__: CPU < GPU < FPGA < ZK-ASIC
  ZK-ASIC is custom-built hardware for ZK.
- 

## ZKP steps 

| Step | Phase | Participant | Inputs / Action | Output |
| :--- | :--- | :--- | :--- | :--- |
| **1** | **Design** | Developer | Logic / Rules | ZK-Circuit |
| **2** | **Setup** | Network | Circuit | Proving & Verifying Keys |
| **3** | **Proving** | Prover | Secret Witness + Proving Key | The Proof ($\pi$) |
| **4** | **Verifying** | Verifier | Proof + Verification Key | True / False |

### 1. Design 

Turn code into mathematical format that the ZK system can understand. 
Format often used: 
- R1CS 
- Arithmetic circuits
Variables are called signals, signals are separated into public and private.

### 2. Setup

Create a pair of Proving/Verifying keys derived from the circuit used by the Prover/Verifier respectively.
Depending on the protocol this happens once or every time the code changes. 
Verifying key is much smaller.
For SNARKs this includes the trusted setup phase.

### 3. Creating a proof

#### 3.1 Creating the witness

The prover passes the public and private data through the circuit. 
Output and intermediate states of the circuit execution are gathered into a piece called "the witness".

#### 3.2 Generating a proof

Using the witness and the proving key a proof is generated.

### 4. Verifying 

The verifier can check the proof using it's verifying key and the public data.

### Example: Age verification

Goal: Prove that I am over 18 years old without giving my age or birth year.

#### 1. Design

public data:
- current_year
- threshold: 18

private data: 
- birth_year 
- random_salt (it's used to add entropy and prevent generating the same proof for the same inputs)

code:  (often using a specialized language like Circom)
age = current_year - birth_year
res = age >= threshold
res == 1

the code is then compiled into a circuit

#### 2. Setup

Proving/Verifying keys are produced from the circuits and distributed accordingly.
These keys are not public.

#### 3. Proof Generation

You feed your data "current_year = 2026", "birth_year = 2000", "random_salt = 1234" to the circuit
from your ZK software.
To make sure that your inputs are not fake they can be cryptographically signed by entities like the government.

The circuit constraint is validated as you are effectively over 18.

You obtain a _witness_ which consists of inputs and also intermediate states:
{ current_year: 2026, threshold: 18, birth_year: 2000, random_salt: 1234, age: 26, res: 1}

From the witness and the proving key you generate a proof.
Since "random_salt" is random and part of the witness, the proof generation is not deterministic 
based on personal data.

#### 4. Proof Verification

Using the public data ("current_year = 2026" and "threshold = 18"), the proof and the verifying key 
the verifier can validate the proof.
