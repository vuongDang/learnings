# End-to-end verifiable compliance framework

__Goal__
Propose an end-to-end compliance framework using zero-knowledge 
cryptography to help regulators auditing the safety of AI models

## Problem

- To be able to regulate the usage of AIs, governance instances need
to be able to evaluate the 
- The standard approach: third-party auditors are commissioned by public instances 
to certify compliance of products to regulations
- However such process is hard to apply to the AI space
  - due to intense competition the frontier labs are unwilling to divulge info about their models (especially weights, and training data) to prevent any leakage
  - evaluations are still lagging behind the advancements of AIs and no standard have been established yet
  - regulation have not been established yet
  - ... TODO other reasons
- Another issue is that audits usually certify compliance at a specific timeline
  - companies can change their production pipeline after being audited to reduce cost 
  - for example Volkswagen emission which software was emitting less polution
  when placed under a testing environment
  
## Objectives 

- Enable audits of the safety processes applied to ai models 
  - auditors have enough information to judge compliance of a model
  - ai labs don't need to reveal their most previous data such as weights and training data
- Enable verification of compliance at any time and not only during audits
- Build a framework that enable such process and identify bottlenecks
  - starts with small models, small datasets, simple functions
  - gradually increase the compute load by scaling the different components
  - compare different zkml schemes and find the most appropriate for each task
  - identify exact bottlenecks that need to be adressed by the zkml space to enable
    this framework to scale

## Related work 

TODO 
- zkml
  - proof of inference
    -  getting much better but still not being able to be used for sync verification
    - I think it's possible for models such as gpt-4 in an async setting
      - you ask for inference proof and receive it after certain delay for verification
  - proof of training
  - 

## Framework description

### pipeline specification example 
1. initial dataset D
2. filtering: filter(D) -> D'
3. pre-training: training(D', W0) -> W1  (weights)
4. Supervised fine-tuning: SFT(W1, SFT_data) -> W2
5. Reinforcement learning: RLHF(W2, alignment_tech) -> W3
6. Inference + guardrail: guardrail(inference(user_input, W3)) -> user_output

### What do the AI labs provide the auditors
- Code and specifications of
  - description of the training pipeline (like the example above)
  - filtering techniques that were applied: _filter()_
  - STF that was used: _SFT()_ and _SFT_data_
  - RLHF that was used: _RLHF()_ and _alignment_tech_
- zkml proofs that the pipeline above is respected
  - no additional steps were added (ex: additional RLHF or additional layer)
  - these proofs give us the guarantee that the pipeline that was executed is
    final_model: W3 = RLHF(STF(training(filter(D), W0), SFT_data), alignment)

### Auditors job
- verify that the filtering techniques are compliant
- verify that the SFT techniques and data is compliant
- verify that the RLHF and data is compliant
- verify that the guardrails that will be used during inference are compliant
- verify that the differents proofs that were given are valid

### Auditors and labs produced a combined proof that will be the public commitment
- commitment from the auditors that the different safety techniques make the model compliant with regulations 
- commitment that the model being served was actually trained following the specifications that was described above

### Users 
- user when sending an input can ask for a proof of inference
- then verify the proof of inference against the public commitment
- if the verification works then the guarantees are:
  - the model used match the specifications in the commitment
  - the model used is compliant and was certified by the auditors 
  - the output received was produced by the commited model from the input sent

### What does it bring

- Verification to compliance can be done at any moment by the user
- AI Labs did not share the weights obtained nor the training data used

## Limitations

- zkml is not ready yet
  - doesn't scale to frontier models 
  - the goal is to identify where the limitations are but that may be really low
- are ai labs willing to share the safety techniques that were applied
  - filtering techniques 
  - SFT used
  - RLHF used
- How far will I be able to imitate a model training pipeline with limited computing resources and budget?
  - training, alignment, SFT... are expensive
  - they will certainly be simulated for proof of concepts
