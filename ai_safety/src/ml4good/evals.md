# Eval 

## What is it? 

- Capability: what can it do? 
- Propensity: what does it tend to do?
- Robustness: how consistent is his behaviour under pressure?
  - jailbreaks

## Inspect framework

Anatomy of the framework
- Dataset: questions or tasks
- Solver: how model engages
  - just the question, add web search, add tools
- Scorer: how we grade answers
  - exact match 
  - llm judge
  - success criteria

## Threat models

- Misuse, human intents
  - offensive cyber , massive manipulation, surveillance, fraud at scale
  - Chemical, Biological, Radiological, Nuclear
- Accidents, poor design 
  - reckless agency, hallucination reward hacking
- Misalignment, different ai intents
  - deceptive alignment, power-seeking, sandbagging, self-preservation

## What evals measure 

Some external parties are actually doing locked down evals 
to test the AIs consistently under good conditions
- Knowledge 
- Agentic capability 
- Dangerous capability 
- Propensity & alignment

## Case study: Claude Mythos preview:

- new 24-hour alignement review
  - before internal deployment
- aunomous zero-day exploit discovery
- limited release to cyber-defence partness

## Evals are reaching their limit 

- how do we evaluate something more capable than us 
- evals when leaked to the training are not relevant to test models 
- eval awareness: 80% of the time, Mythos knew it was being evalueated
- evals are completed and not relevant because the models are getting better
- no proof of safety when succesfully completing an eval

## METR time horizon

- approch 
  - measure how long it takes a human to complete a task
  - measure the task difficulty compared to how long it takes a human to complete a task
  - 
  
  
# Interview 80 000 hours on Mythos

- staurates all existing evals
- find serious exploits instead
  - find a worrying amount of vulnerabilities and exploits
