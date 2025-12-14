# Papers to read

## Formal model of AI safety

### Towards Guaranteed Safe AI: A Framework for Ensuring Robust and Reliable AI Systems (2024)

[https://arxiv.org/abs/2405.06624](https://arxiv.org/abs/2405.06624)
formal framework for building AI systems that are provably safe and reliable

## Guardrails for AI alignment 

### From Refusal to Recovery: A Control‑Theoretic Approach to Generative AI Guardrails (2025)

[https://arxiv.org/abs/2510.13727](https://arxiv.org/abs/2510.13727)
This work reframes guardrail enforcement as a sequential decision problem (not just classification). It leverages safety‑critical control theory to build predictive guardrails that monitor an agent’s outputs in real time and proactively correct risky actions instead of merely refusing them. By treating AI safety as a control problem, it enforces guardrails dynamically, integrating monitoring, prediction, and recovery policies to steer behavior away from hazards. 
arXiv

### Customizable Runtime Enforcement DSL for Safe LLM Agents (2025)

[https://arxiv.org/html/2503.18666v1](https://arxiv.org/html/2503.18666v1)
Introduces a lightweight domain‑specific language (DSL) designed for specifying and enforcing runtime constraints on LLM agents. Users can define structured safety rules (triggers + predicates + enforcement actions) that are checked during execution. The system embeds constraint enforcement directly into the LLM agent pipeline, preventing unsafe behaviors across multiple domains (code, embodied agents, autonomous driving scenarios). Demonstrates strong safety compliance with minimal runtime overhead.

### Evaluating Robustness of Safety Guardrails Against Adversarial Attacks (2025)

[https://arxiv.org/abs/2511.22047](https://arxiv.org/abs/2511.22047)
Evaluates real guardrail models and finds that many degrade heavily on novel attacks. Highlights a pressing enforcement robustness gap even in deployed systems.

## Agentic AI Sandboxing

### HAICOSYSTEM: An Ecosystem for Sandboxing Safety Risks in Human‑AI Interactions (2024)

[https://arxiv.org/abs/2409.16427](https://arxiv.org/abs/2409.16427)
HAICOSYSTEM, a sandbox‑like evaluation framework to test safety risks of AI agents in interactive environments

###  Swiss Cheese Model for AI Safety: A Taxonomy and Reference Architecture for Multi‑Layered Guardrails of Foundation Model‑Based Agents (2025)

[https://arxiv.org/abs/2408.02205](https://arxiv.org/abs/2408.02205)
Taxonomy  and multi‑layer architecture of runtime guardrails for foundation‑model‑based agents, inspired by the Swiss Cheese Model

### A Safety & Security Framework for Real‑World Agentic Systems (2025)

[https://arxiv.org/abs/2511.21990](https://arxiv.org/abs/2511.21990)
Focuses on safety and security in agentic AI systems deployed in real workflows (enterprise context). Proposes a dynamic safety‑security framework that identifies risks through sandboxed, AI‑driven red teaming, and uses auxiliary models + human oversight for contextual risk discovery and mitigation. Demonstrates effectiveness via a case study with NVIDIA’s research assistant and releases a dataset of thousands of safety/attack traces.
