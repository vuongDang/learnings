

⭐ Activation Oracles: Training and Evaluating LLMs as General-Purpose Activation Explainers (Dec 2025, Karvonen et al.; MATS, Truthful AI, EPFL, ENS, Northeastern, Anthropic) — trains LLMs to take other models’ activations as input and answer arbitrary natural-language questions about them. Generalises far OOD: uncovers misaligned goals and secret knowledge fine-tuned into target models (e.g., the secret word in the Taboo task), without ever training on activations from fine-tuned models. Matches or exceeds white-box baselines. Performance scales with training-data diversity — a “bitter-lesson-compliant” non-mechanistic interpretability approach. See Anthropic Alignment blog and LessWrong post.

## Activation oraclees

## Contributions 

__Goal__ General purpose Activation Oracles
- take another model internal activations as input 
- answer arbitrary natural-language questions about them 

__Contributions__
- AOs can generalize on questions out of training data
- Scales training diversity with three task types
  - both quantity and diversity of training improve performance
  - tasks:
    - system prompt QA
    - binary classification
    - self-supervised context prediction task
- Benchmarks AOs vs prior white-box and black-box interp methods
  - four auditing tasks
  - outperform others on 3/4 of the tasks
- Code, trained models and demo are released

## RElated work
- Other techniques require expertine and task-specific tuning
  - sparse autoencoders: activation -> interpretable features 
  - logit lens: activation -> language tokens 
  - activation steering: modify activation -> manipulated behaviour
- prior work: LatentQA 
  - train a model to directly read another model's activations 
  - you can ask this model natural languages questions 
  about the target model internal thoughts
  - was only on the question: "what's the system prompt"

## Limitations 

- trade mechanistic understanding for a Q&A interface 
- answers without underlying reasoning 
  - unlike circuit analysis or SAE
- no calibration 
  - produce answers even when uncertain
  - no way to rank confidence
- benchmarks are simplified test cases 
  - real world cases are more complex 
- requires access to target model weights and activations
  - for some tasks like hallucination detection simple black box prompting achieve comparable results


# Strong case 

- You can recover much more information than other techniques like linear probes 
  - only binary classifiers 
- You don't need as much expertise as other mech interp techniques
- it's generalized, it's robust to out-of-training data
- 

- if you learn more activation you detect more about scheming
- being able to question activations will give us a better udnerstanding of models in general


- applications: mech interp techniques for monitoring and evals especially more powerful modesl

- vague response 
- hallucination
- text inversion, proven that it failed 
- not actually use activation 
- oracle has sycophanhy
