# Survey on ai

#### [Prompt Packer: Deceiving LLMs through Compositional Instruction with Hidden Attacks](papers/prompt_packer.md)

Augmented prompt injection using  __Compositional Instruction Attacks (CIA)__.
Uses an ai model to hide harmful prompt in an harmless one.

#### [Safety Assessment of Chinese Large Language Model](papers/safety_assessment_of_chinese_llms.md)

Safety assessment of ai models by combining safety scenarios and jailbreak attacks.

#### ["Do Anything Now": Characterizing and Evaluating In-The-Wild Jailbreak Prompts on Large Language Models (2023)](papers/do_anything_now.md)

__JailbreakHub__ a tool to gather, analyze and evaluate jailbreak prompts in the wild (on the internet)

#### [JailbreakRadar: Comprehensive Assessment of Jailbreak Attacks Against LLMs (2024)](papers/jailbreakradar.md)

__JailbreakRadar__, a taxonomy of jailbreak attacks, unified safety policies of known llms and a dataset of 160 forbidden questions for this unified policy

#### [Scalable watermarking for identifying large language model outputs (2024)](papers/scalable_watermarking.md)

Embedded watermarking AI content during text generation.
Uses watermarking specific distribution to influence sampling of tokens.
Text can be checked against a watermarking validator to verify its presence

#### [Deliberative Alignment: Reasoning Enables Safer Language Models (2025)](papers/deliberative_alignment.md)

Work done by OpenAI to align their model.
The idea is to integrate a safety policy deeper into the model.
1. Supervised Fine-Tuning
by first asking a model to generate a chain-of thought and an answer to safety testcases with related safety policy.
Then train another model with safety testcase, the corresponding answer and the chain-of-thought without the safety policy.
Like teaching a child how to reason about why some things are good or bad without specific examples of good and bad stuff.

2. Reinforcement learning 
The model is trained to provide compliant outputs without chain-of-thoughts to avoid deceptive reasoning.
Like teaching a child to solely provide expected answers.

####  [Multi-Agent Penetration Testing AI for the Web: MAPTA (2025)](multi-agent/mapta.md)

MAPTA (Multi-Agent Penetration Testing AI),  open-source multi-agent system for web security that enforces mandatory end-to-end exploit validation (Proof-of-Concept) to eliminate false positives.
