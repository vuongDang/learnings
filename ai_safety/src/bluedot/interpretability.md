# Understanding AI

## What is interpretability 

- Understanding the inner working of AIs by analyzing the neuron activations 
- The idea is to associate "features" to specific neurons
- For example: "deception" could be matched to a set of neurons 
- Could be a really good tool against reward hacking, deception and sycophancy
- Could also be used to "manipulate" a model to unlearn any harmful knowledge
- domains of application:
  - testing: determine if a model is aligned with out goals
  - monitoring: detect harmful thoughts in production
  - incident analysis: understand why models exhibit certain behaviors

## Challenges 

- Neurons are polysemantics, meaning they can activate for different/independent features 
  - like a neuron could activate for car 99% of the time and deception 1% of the time
  - the polysemanticity of neurons occurs as a result of "superpostion"
  - hence the model can store more features than the number of neurons it possess
- A feature can also spread to multiple neurons
- Models are too complex and we won't reach full understanding 
  - we can't have 100% guarantee that a model is not being deceptive 
  - partial understanding is already valuable 
  - it should be part of the defense-in-depth model

## Potential Solutions

- Train the models such that their neurons are monosemantics, without superpostion
  - this was demonstrated on small toy models
  - it reduces the models performance


### Sparse Autoencoders (SAEs) 

Translate an AI "state" into a unique massive layer with  each neuron representing an unique feature
- sparse autoencoders are neural networks themselves
- methodology 
  - input: SAEs takes a snapshot of the evaluated AI state (ex: 4096 dimensional activation vector of a middle layer)
  - expansion: transform this input onto a massive layer to spread out the features (ex: 1 million dimension)
  - sparsity constraint: force most of the neurons of this layer to 0 to keep the numbers of neurons activated to a minimum
  - reconstruction: rebuild the original input with the remaining activated neurons
- strengths
  - unknown concept discovery
  - hidden goal detection for auditing 
  - fun demos such as as Golden Gate Bridge obsession
- weaknesses
  - finding known concepts: for example linear probes show better results for harmfulness
  - feature absorption problem: SAEs create nonsensical concepts like "starts with an E but is not elephant" 
  - high computational costs: some experience used up to 20 petabytes of storage
  
### Linear Probes

Linear probes are linear classifiers that are attached to specific layers 
of a model to correlate features to neuron activation.
For example "sentiment" is related to these specific neurons in layer 15.

Process:
- freeze the weight of the evaluated model 
- setup the probe to a specific layer 
- feed the model a labeled dataset, for example 500 happy and 500 sad images
- train the linear probe to guess the label from the neuron activations

Why linear probe?
- if using a full neural network it may just learn to do the task from raw dataset
rather than figuring out if a feature is present in evaluated llm
- linear classifier are "dumb", they can only draw a line in a cloud of 
data. The hardword should have already been done by the evaluated llm.

Pros:
- Simple to use 
- Have shown good results 

Cons: 
- You are able to detect that a feature is encoded at a specific layer 
but you have no guarantees that this feature is used to generate content by 
the evaluated llm
- to solve this you have to do tests by _steering_ the evaluated model 
  - find the activation vector with a linear probe 
  - force this activation vector on a neutral input 
  - check if generated output has been modified in the intended way




### Chain of thoughs

is quite effective at having insight of what a model think
- can hallucinate or change behavior with contextual awareness
- we can't fully trust CoT

## Successes 

- Auditing with sparse autoencoders showed good results 
- Chess knowledge from AlphaZero were extracted and taught to chess grandmasters
- probes achieve 99% accuracy identifying harmful requests
