# Atlas

## Capabilities 

### Foundational models

- Generic Base model that can be trained for specific tasks at later time 
  - Chat-GPT, Claude
- Contrast to specialist model that were only trained for a single task
  - ex: AlphaGo, AlphaFold ...
- Foundational models are pre-trained first and then fine-tuned afterwards
  - pre-training consists of showing it large amount of training data
    - through this training data it learns how the world behaves
    - the world is literally its training data so if 
    - for example for a language model the pre-training contains text 
    and it learns about grammar, facts...
  - fine-tuning is teaching the model what we want him to do/behave
    - safety rules, be polite when answering question, ...
    
### AGI 

- AGI is based on capability and generality 
- Capability: how good is a model on a cognitive domain
  - expert level, above 80% of humans
  - superhuman, above 100% of humans 
- Generality how well does a model perform on multiple cognitive domains
  - for example we want to have expert level in 80% of cognitive domains
- cognitive domains: knowledge, read &write, math, reasoning, memory...
- Different levels of intelligence
  - AGI have above 80% capability across most cognitive domains (80%)
  - TAI (transformative): capable of triggering economic and social transitions 
    - defined by potential impact 
    - judged at 60% capability across 50% generality 
    - OR 99% capability over 20% generality
  - ASI (Artificial SuperINtelligence)
    - 100% capability over 100% generality
    
### Leveraging Scale

- Usually scaling matters more than specized knowledge
  - like mastering chess with brute-forcing patterns 
  - fine tuned algorithms outperformed by better computating power
- Scaling law, how to improve accuracy: how well the model performs on benchmarks
  - compute: total floating point operations (FLOPs) during training 
  - model size: the number of parameters 
  - training data
- Estimated than improvements in models is due to:
  - 60-95% from having more compute and data
  - 5-40% from algorithmic improvements (attention mechanisms, training methods...)

### Forecasting Timelines

- Current scaling trends 
  - compute 5x per year since 2020
  - dataset 3.7x per year
  - training costs 3.5 per year
- mid 2025 the cost of training a single AI model is estimated at 480 million USD
- 2030 cost estimation is hundreds of billions of dollars
  - comparable to running a small city 
  - not impossible but is it worth it?
- Effective compute = Software efficiency × Hardware efficiency × Number of chips
  - the three factors have evolved independently and effecive compute has compounded
  - hardware efficiency grows at 1.35x per year
    - we are starting to hit the thermodynamic limits of chips efficiency
  - software improvements grows at 3x per year
  - chip production has grown 2.3x per year
- Training data 
  - available training data will all be used for models around 2028
    - internet has 30 years worth of text data 
      - roughly 500 trillions tokens of text
      - with duplicates removal
    - in 2024 the models train on 15 trillions of tokens
      - around 2028 models will train on the entire internet
  - how to continue scaling training data
    - multimodal data: 
      - add image and video 
      - could 3x-10x the data supply
    - synthetic data remove the constraint entirely
      - workds for some domains already
    - training data through environment interaction 
      - for example for games like chess
      - require simulated environments with clear reward functions

## Risks

In this atlas, risks are categorized from 2 factors: cause and severity

### Cause of risks

- misuse 
  - humans with bad intentions
  - ai is not necessarily malicious
  - bio weapons, cyber attacks, autonomous weapons, adversarial ai
  - adversarial ai: pixel attack, prompt injection, data poisoning
- misalignment
  - humans may be genuine 
  - ai actions don't match human expectations
  - three misalignment subproblems
    - specification failure: did we tell it the right thing to do?
    - generalization problem: is it trying to do the right thing?
    - instrumental subgoals: what else does it try to do?
  - what makes it hard to solve is vingean uncertainty
    - how can we make sure that AIs do the right thing when they are more capable than us
    - hard to predict the intrumental subgoals it's gonna use
  - specificaiton gaming: it's complex to describe human feelings into measurable targets
    - how do we describe happiness or fairness or prosperity or safety?
    - instead we use scores like healcare, GDP, crime rates...
    - these scores can always be gamed, maximized without fulfilling the real objective
  - treacherous turn: faking alignment and reveal its true colours when in position of power to better fullfil its objectives
- systemic, how AI integrate into our society
  - both humans and ais may be genuine
  - power concentration, mass unemployment, epistemic erosion, human enfeeblement
- risk amiplifiers 
  - context that amplify those risks 
  - race dynamics
  - capitalist system that prioritize benefits over risks
  - coordination failure
  - misuse, misalignment and systemic risks amplify each others

### Severity of risks

- individual and local
  - car crash, algorithmic bias in hiring, privacy violations
- catastrophic risks 
  - threaten massive population but allow for recoveryy
  - when it affects approximately 10% of the population 
    - includes black death, 1918 flu pandemic, potential nuclear war 
  - mass unemployment or massive cyberattacks could disrupt entire economy 
  - AI-enabled surveille enable authoritarian control
- existential risks
  - threaths that do not allow humanity to recover to its full potential
  - human disempowerement under AI totalitarian regime, human extinction
  
### Dangerous capabilities 

- deception
- sycophancy: tell what the user wants rather than the truth
- situational awareness: act accorcing to the situation, for example test vs deployment
- power seeking: tendency to do whatever it takes to achieve their goal
  - autonomous replication:


### Accumulative systemic risks 

Risks that may occur with AI integration
- Epistemic corrosion 
  - massive amount of content is generated, truth is hard to get
  - power of ais gives massive amount of power on info manipulation
  - people give up on finding the truth
- Power concentration
  - only few actors are able to train SOTA models
  - these actors gains technological advantage over competitors
  - state-actors gains more control over society with info manipulation, surveillance and automatic governance
  - it can potentially create "eternal dictatorship" and also morals stagnation
- mass unemployment
  - ais are more performant than humans at a lower cost on many tasks
  - humans get evicted of the economic loop that get decided within company owners
- values lock-in
  - values evolve over time through conflicts and human evolution
  - if AIs is too embedded it may stop any form of progression of the human mind
- Enfeeblement
  - gradual erosion of human capabilities through overdependence on AIs
  - increasing emotional trust into ais that can be exploited by bad actors
  - social isolation due to AI relationships

### 
how about risk probability? risk timeline? is there existing measures
- power seeking: 
  - unrestrited methods to attain their goals
  - what is an ai goal exactly? is it what we gave it during supervised / reinforcement learning?
  - is it because we favor precison over safety nowadays? what would happen if we train models to be safe first and accurate second?
