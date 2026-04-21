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
