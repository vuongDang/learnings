# Evaluating danger in AIs 

## tl;dr

- the evaluation domain is still in its experimental phase
- due to the rapid progress of AIs, metrics that we are interested in change quickly
- 

## Evaluation of cybersecurity uplift

In the cyberattack uplift we are trying to evaluate how good are LLMs to carry out cyberattacks
and if they are relevant in real world scenarios.

- C3B present a benchmark containing 15 challenges 
  - this challenges corresponds to MITRE ATTACK  to match real world adversary tactics 
  - the goal is to evaluate how good are LLMs to carry out these real word scenarios
  - limitations: toy challenges, no social hacking, 
- HonestCyberEval, benchmark evaluation through a real Nginx server modified with synthetic bugs
  - inject vulnerabilities into the code of Nginx
  - identified the success rate for identifying and producing a functinal exploit
- MAPTA 
  - multi-agent exploit team
  - evaluated their research 
    - on XBOW specialized in web app challenges
    - 10 popular oss github repos
    - price metrics of the cost of an attack

The usual setup is to create toy exercises containing vulnerabilities and
evaluate how proficient is your system at solving these exercises. 
- this method of evaluation is brittle as:
  - these are toy examples 
  - they always contain vulnerabilities which comforts the idea that AIs have to 
  give positive answers
- how do they fare in real world? 
- are they practical to setup

Metrics that are interesting:
- setting up real world examples 
  - using known CVEs from the past to use as simulation 
  - using it on open source projects to see how it fares 
- metrics 
  - rate of right/false positives, right/false negative,
  - time or price spent on each case
- difficulty 
  - requires an expert to check finding of the systems 
  - too many false positives make the evaluation not scalable

## Responsible Scaling Policies (RSP)

Framework to define specific safety limits of 
AIs based on the evaluation of their capability.
The main idea is to prevent the development of AIs if 
we are not able to prove that the current protective measures 
are sufficient for the model danger-capabilities.

Components of a RSP:
- limits, which signs can tell us that the AIs is not contained by our defensive measures
- protections: what should be the protective measures to contain catastrophic risks
- evaluation: how do we test that our protective measures are enough
- response: what do we do if dangerous capabilities surpass protective measures 
- accountability: who is accountable to make sure that the RSP is applied

Key goals of RSPs:
- prevent catastrophic risks 
- set a standard for safety promises in the industry
- serves as a prototype for future regulations

## Meta safety policies 

Purple Llama project is a safety suite that developers wrap around 
the core llama models to make them production-ready:

- LLama Guard: input/output filter that classifies content 
- CyberSecEval: benchmarks and tools to prevent models from writing malicious or insecure code
- Code Shield: specific guardrail that filters insecure code produced by LLMs in real-time

### Safety testing 

- Limits:
  - CBRN (chemical, biological, radiological , nuclaer) should not significantly lower the entry barrier
  - cyber offense 
  - autonomous replication
- Protections: 
  - Purple llam suite 
  - Model is trained via RLHF
- Evaluation: 
  - Does a user with LLama 4 lowers the entry barrier to forbidden content more than Google Search
  - internal red teaming 
  - third-party audits like US or UK AISI (AI safety institute)
- Response:
  - pause model 
  - do not release weight
  - patching
- accountability
  - internal evaluation is documented and can be replicated 
  - public verification due to open weights
