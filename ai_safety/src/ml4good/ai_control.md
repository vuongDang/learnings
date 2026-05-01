# AI Control

__Goal__ stay safe even though we can't trust the model

## Scheming threat model

- scheming: covert misaligned power-seeking
- scheming tactics 
  - fake alignment in pursuit of long-term goals 
  - sandbagging 
  - sabotage work (ex: alignment research)
  - escape when possible
- assume scheming models and prevent them from causing goom
  - no alignment questions but only focus on capabilities

## Alignment vs Control

- Alignment is the goal 
  - but long term research
- Control is a precursor 
  - want potentially scheming AIs to work on alignment
  
## Setup

- Target scheming model 
  - medium expensive, competent
- Trusted less powerful judge model
  - cheap, incompetent
- Human arbitrator
  - expensive, competent
- Monitor untrusted model with control protocols

## What can we monitor 

- tool calls
  - paper on monitoring backdoors 
  - ctrl-z papers
- chain of thought
  - as long as it's faithful and monitorable
  - don't over optimize the CoT or they get better at hiding in their CoT
  - keep CoT in natural languages, not like COCONUT
  - for complex cases CoT is necessary to complete the task and are not able to hide their reasonings
- internal activations 
  - linear probing
- environment and agent 
  - can we gain infromation by interacting with these
  - like interrupted the untrusted model and question the actions
