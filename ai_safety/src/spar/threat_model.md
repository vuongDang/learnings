# Threat model 

## Goal 

A user queries a model on a private server. 
The server has ZKP setup on the model and has published a commitment on the model information it's serving.
We want the user to be able to verify that the model it queries matches the information the host published.

Goal 1: Model equivalence to the commitment
Goal 2: Model parameters stay hidden
Goal 3: Goal 1 + Model is the one the user wished to use
Goal 4: Practical to use

## Entities  

- User(s)
  - Honest Goal: Verify that the model its using match the public details from the host
  - Malicious Goal: Steal the model parameters
    - can ZKP give additional information to steal a model parameters compared to just having a raw model? __TODO__
  - Actions:
    - Query the model
    - Read public commitment about the model 
    - Verify generated proof
- Model host
  - Can be malicious or not
  - Malicious goal: Cheat the user by serving a model different than what the user expects
  - Honest goal: Provide a model and and a way for the user to safely use its model, do not reveal the model parameters
  - Actions:
    - Choose which model is served to the user(s)
    - Publish a commitment about the model being served
    - Generate proof 
- Third party attackers 
  - Goal: 
    - Trick the user into using a model controlled by the attacker which is different from what the user expect
    - Uncover the model parameters
  - Actions: 
    - intercept and modify queries between host and user
    - does not have access to the host server 
    - does not have access to the host cryptographic keys

## Scenarios per attacker model

We consider these attacker models:
- not tackled: user wants to uncover model weights
- the host is malicious
- the host is and attackers come is a 3rd party

Steps that need to be scrutinized:
0. ZKP scheme is chosen
1. Host publish a commitment about the model 
2. User send queries to the model 
3. User may ask for for a proof for all or specific queries 
4. Host compute a proof when the user asks for it
5. Host sends proof + output for the user query
6. User verify the proof using the: commitment + input + output + proof

### 3rd party attackers 

#### Assumptions

- intercept and modify queries between host and user
- does not have access to the host server (can't modify the model)
- does not have access to the host cryptographic keys (can't sign content as the host)

#### Attack surface

0. Assuming the ZKP chosen is safe attacker can't attack
1. Attacker can't modify/replace the public commitment
  - We assume the commitment is signed by the host
2. Attacker can do MITM!
  - user believes it's talking to host directly but content is modified/replaced at the attacker will
  - model's behaviour can be analyzed, time for response, outputs given
3. Attacker can replace the output and/or the proof generated
4. Attacker cannot interfere in proof generation
5. Attacker can replace the proof and the output 
6. Attacker cannot interfere in verification process

#### Threats 

- Attacker interferes -> out of scope
- Attacker manipulates normal queries and relay to the real model when user asks for a proof
  - if a proof is asked for every input the attacker can't interfere but it's costly in terms of computation
- Attacker manipulates outputs and proofs returned by the host such that verification is fooled
  - output and proofs are forged
    - can verification be fooled when controlling both output and proof? I believe no but I should double-check __TODO__
  - output and proof are slightly modified (like few pixels) 
    - ZKP should ensure that the probability of this happening should be super low
    - this depends on the equivalence definition (e.g distribution equivalence may not detect this)
- Attacker get information on the model parameters through side-channels
  - can we infer a model weights using timing attack? __TODO__

### Malicious host

Malicious host is a stronger attacker model than 3rd party attackers.

#### Attack surface

0. The host can choose a vulnerable ZKP 
1. Attacker can publish a false/partial commitment
2. Attacker can manipulate input queries
3. Attacker can generate the proof it wants
4. Attacker can generate the proof it wants
5. Attacker can replace the proof and the output 
6. Attacker cannot interfere in verification process

#### Threats

- Weakness in ZKP itself
  - the zkp chosen is vulnerable
  - there are known vulnerabilities -> it's the user responsibility to check which algorithm/implementation is used
  - Circuits are not an exact match to target models 
    - due to quantization 
    - what kind of exploit can be done from this vulnerability? Which equivalence definition still holds?
      - due to this flaw ZKP can prove up to distributional equivalence
      - can this looser equivalence definition be exploited? __TODO__
- output/proof/commitment are manipulated
  - can verification gives false positive when controlling all these information? __TODO__
    - output should still appear correct to users
  - Host fool the user by serving commitment/proof/output of the wrong model
    - ZKP works fine but user verifies queries for a model different from its expectations
    - it's user responsibility to check that the commitment matches the model it expected to use


## Equivalence definition 

What information does the public commitment contain and can 
be verified by the ZKP setup? What definition of equivalence 
can be computed practically and still be relevant?

- exact equivalence or pointwise equivalence
  - input/output/traces are the same for every input
  - Schwart-Zippel, FA with W-method
- functional equivalence 
  - input/output are the same for every input
  - we don't care about internal details as long as the outputs are equal
- distributional equivalence
  - input/output agree on a specific distribution of inputs
  - edge case inputs may produce different outputs
- partial equivalence

### Equivalence in the real-world

Current ZK protocols used like zk-SNARKs and zk-STARKs have an hybrid, the proof generated:
- has pointwise equivalence to the circuit 
- has distributional equivalence to the model 
  - the circuit is an approximation of the model used
