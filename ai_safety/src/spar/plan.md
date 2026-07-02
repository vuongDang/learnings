# 

- Study of model variants through challenge framework
  - complete study of adversarial models
  - study of "passive" variants: quantized, pruned models
  - study their behaviour through challenges and different measurements
  - theoretical bounds on challenge framework and variants?
- Make zkml scale
  - make zkml scale
    - layer selection
    - commitment on fingerprints?
  - replace zkml with TEEs execution
- Improve challenges selection
  - Fisher-guided challenge sampling set F
    - coupled with zkml you can't just replace your model
    - maybe hybrid to have both F and random challenges
    - Does Fisher-guided challenges reduce query budget? is it worth exploring the theory?
  -

# Brainstorm

## Some weaknesses of our work 
- empirical study of adversarial variants can be pushed further
- one of the reviewer questions, does our approach scale well with frontier models
- addition of zkml gives better security guarantees but not practical as it is
- limitations of black-box testing is that we need to trigger deviant behaviour to detect anything
  - are there challenge strategies that can improve odds of finding deviant behaviour?
  - model fingerprinting is powerful but requires information usually not shared
- our setup is unclear, when we are comparing M and M' do we have full access to M, is it a reasonable assumption? 
- it's not clear what kind of guarantees we get about real deployment when a model pass the challenge framework since it's so dependent on the challenges 

## Ideas of research direction 

### Adversarial study

__Goals__
- Taxonomy of models variants compared to a base model through the lens of the challenge framework
- being able to differentiate a passive variant (quantization, pruning) from a malicious one (adversarially trained)

__Paths to explore__
- Theory and formalization
  - define what is passive / malicious variant compared to the base one 
    - my intuition is that quantization/pruning brings small perturbation spread over the whole input set
    - adversarial models / fine tuned models and bring bigger perturbations but on a more focused set of inputs (backdoor, domain-specific models)
- Empirical study on model variants
  - Study
    - we only do challenges on top-1
    - I believe different metrics capture different behaviours, is there a metric that gives us more information about variants behaviour
    - for example can parameter/activation fingerprinting be used to detect what kind of variations was applied to a base model?
  - More models variants to compare
    - we can use https://arxiv.org/pdf/2506.06975v5  


### Improve zkML + challenges

__Goals__
- Create a practical protocol that combines zkml and challenges 
-  on pure-black box testing with fingerprints 
- Formalize what it means to 

__Paths__
- Improve challenge strategies, reduce the gap between challenge and deployment distribution
  - Use Fisher guided challenges, the inputs that should be most sensitive to parameters variations
    - to avoid gamification combine it with random challenges
    - it also implies being able to compute fisher matrix
  - challenges should be as close as possible to real-word usage. Should the challenges choice depends on future usage of the model?
- only use zkml on specific layers rather than full inference to improve practicability
  - I think NanoZK studies this: https://arxiv.org/pdf/2603.18046
  - it also uses fisher matrix to choose which layer to prove
- Use activation fingerprinting + zkml
  - activation is much stronger to detect parameter variations than just outputs
  - from what I've understood activation-difr is hard to invert so having a provider shares 
  it's activation-difr computations could be an acceptable assumption
  - as a bonus activation-difr acts on a single layer, it should also improves our zk proof generation performance
  - an alternative could be that the provider computes a zk range proof of the differences between its activation and the activations 
  of the base model, allowing the provider to keep maximum secrecy 
- replace zk with TEE (trusted execution environment)
  - TEE another technique to get proof of computation
  - different security guarantees, hardware based but much faster
