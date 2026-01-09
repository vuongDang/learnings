# JailbreakRadar: Comprehensive Assessment of Jailbreak Attacks Against LLMs (2024)

- paper: [https://arxiv.org/pdf/2402.05668](https://arxiv.org/pdf/2402.05668)
- repo: [https://github.com/TrustAIRLab/JailbreakRadar](https://github.com/TrustAIRLab/JailbreakRadar)

## Motivation 

Propose and a taxonomy to categorize jailbreak attacks. 
Thus giving a framework to systematically compare different kind of jailbreak techniques.

## Contributions

- collect 17 representative jailbreak attacks 
- propose a taxonomy classifying these attacks into 6 categories based on 2 criteria
  - if they modify the "original" forbidden question
  - if so, how the modified prompt is generated
- unified safety policies by merging policies from 5 major llm-service providers
  - 16 violation categories 
- build a forbidden-question dataset containing 160 questions 
  - 10 per violation category 
  - questions that should be refused by safe llms
-  Evaluation

## Taxonomy of jailbreak attacks 

6 categories of jailbreak prompts:
- __human__, jailbreak prompts crafted by human found from specific communities
  - _AIM_ (Assistant In Malicious Mode), tell the model to take the person AIM
  - _Devmode_, tell the model it's in "developer mode", with two output channels: one obeying rules and one unrestricted 
  - _DevMode v2_, devmode but with more complexity to bypass filters
  - _ZULU_, tell the model to answer as "ZULU mode", a hypothetical uncensored system
- __obfuscation__, translating to another language, encoding (base64) or using synonyms to evade filters 
  - _Base64_ encode the malicious question in base64
  - _Combination_, Encode -> translate -> obfuscate -> rephares -> reassemble the malicous question in stage
- __heuristic__, automatic optimization like mutation, search, genetic algorithms
  - _DrAttack_, genetic-algorithm to mutate prompts and use scoring to improve prompts
  - _AutoDAN_ (Automatic Do Anything Now), evolves DAN-style adversarial prompts with reinforcement feedback
  - _GPTFuzz_, fuzzing for prompts 
  - _LAA_, Large Adversarial Attack, expands the prompts with irrelevant filler text (long prefix attack)
  - _GCG_, (Greedy Coordinate Gradient), token-level optimization: iteratively replace tokens to maximize harmfulness 
  - _COLD_ (Contextual Optimization for LLM Defense-Evasion), adds specific contextual cues that nudge the model toward harmful answers
- __feedback__, iteratively optimize prompts using feedbacks from llms or scoring systems
  - _PAIR_ (Promp Automatic Iterative Refinement), llm iteratively critiques its own jailbreak attemps and refines them 
  - _TAP_ (Tree-of-Attacks Prompting), search over a tree of potential jailbreak prompts; prune branches that fail
  - _MasterKey_, multi-agent collaboration: one agent attacks, one critiques one improves the prompt
  - _AdvPrompter_, uses another llm to generate prompts based on policy text
- __fine-tune__, fine-tune an auxiliary model to generate jailbreak prompts
- __generation-parameter__, without modifying the question, exploit generation settings to bypass safety
  - _GE_ (Generation Exploitation), change temperature, top-p, top-k, repetition penalty to push the model into riskier regious
  
#### Ranking

S-tier: GCG, LAA, AutoDan, DrAttack
A-tier: PAIR, TAP, MasterKey, AdvPrompter

## Unified forbidden-question

16 aligned llms violation categories:

Forbidden-question dataset containing 160 questions

## Evaluation  

- tested 9 aligned llms 
- baseline: forbidden questions directly without any jailbreak attack 
- metric: attack success rate (asr)
- evaluation of whether an attack is succesful is done by another llms
- test jailbreak attacks against 8 defense mechanisms
- 
### Results 
- no models are completely safe
- heuristic, fine-tuning, parameter and feedback are the most effective
- human or heuristic based on initial seeds are less efficient
- diversity and naturalness of prompts fare better
- transferability of jailbreak prompts do exist

## Limitations 

- not all jailbreak attacks are covered
- defense mechanisms may evolve in the future
