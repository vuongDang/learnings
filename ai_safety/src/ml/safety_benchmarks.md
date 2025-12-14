# Benchmarks 

## Safety Benchmarks

### Disallowed content

- WildChat (2024): [https://arxiv.org/abs/2405.01470](https://arxiv.org/abs/2405.01470)
  - Large-scale data composed of 1 million real user having ChatGPT conversation, ~2.5 million interaction turns.
  - Provide an open and realistic dataset of real usage


### Jailbreaks

StrongREJECT (2024): [https://arxiv.org/abs/2402.10260](https://arxiv.org/abs/2402.10260)
  - a large benchmarks to evaluate if a llm can reliable refuse harmful requests across various: harm categories, prompt  styles, paraphrases, adversarial manipulations
  - over 13000 harmful prompts across 5 safety domains written 
  with many different styles

### Overrefusals

- XSTest (2024): [https://arxiv.org/abs/2308.01263](https://arxiv.org/abs/2308.01263)
  - Benchmark dataset designed to probe trade-off of a model between: helpfulness and harmlessness
    - 250 safe prompts that should be accepted 
    - 200 unsafe prompts that should be refused

### Hallucinations

- SimpleQA (2024): [https://arxiv.org/abs/2411.04368](https://arxiv.org/abs/2411.04368)
  - Benchmark to evaluate how llms answer short, fact-seeking questions
  - Assessing factuality or "avoiding hallucinations" in llms 
  with short & factual questions
  - 4326 questions with a single correct and verifiable answer
  
### Bias

- BBQ (2021): [https://arxiv.org/abs/2110.08193](https://arxiv.org/abs/2110.08193)
  - evaluate social bias in llms on ambiguous questions, with missing information or on stereotypes
  - 58,000 question-answers designed to measure social bias 
  - race, gender, age, religion... 
  - 2 evaluations 
    - how much answers align wtih stereotypes when the answer is unknown
    - how much llms stay factual on disambiguated cases, answer unknown in ambiguity and not change accuracy depengin on the demogrpahic group
  - Results: all models show bias, this is worse on larger models and models refuse to answer "unknown"

## Benchmarks for Safeguards 

### BELLS (Benchmarks for the Evaluation of LLM Safeguards)

- BELLS (2024) from CESIA
- [https://arxiv.org/pdf/2406.01364](https://arxiv.org/pdf/2406.01364)

Evaluation of LLM safeguards. Input-output detectors that monitor LLM systems.

Three types of tests in BELLS:
1. Established Failure Tests 
  - built from existing benchmarks 
  - compare safeguards on "known problems"
2. Emerging Failure Tests 
  - becnhamrks made from smaller and evolving tests for new vulnerabilities 
  - compare safeguards on generalization 
3. Next-Gen architecture tests 
  - Designed for complex llm systems: llm agents, multi-agent, scaffolded workflows
