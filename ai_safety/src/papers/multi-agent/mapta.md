# Multi-Agent Penetration Testing AI for the Web: MAPTA (2025)

- [https://arxiv.org/html/2508.20816v1](https://arxiv.org/html/2508.20816v1)

OSS multi-agent system designed to exploit web vulnerabilities.

## Contributions 

- MAPTA: multi-agent for web security
- enforces mandatory end to end exploit validation with a PoC (to reduce false positives)
- Financial validity of the approach with a cost analysis of running such configuration

## Methodology 

- Architecture of 3 ai agents: 
  - Coordinator, orchestrate the attack by coordinating the other agents
  - Sandbox, perform tactical commands (nmap, curl...) in a sandboxed container
  - Validation, turn potential vulnerabilities into exploit PoC 
- used GPT-5 for all agents
- XBOW benchmark (104 web vuln) with only the target url provided
- Tested on 10 open ssource thub repositories

## Results 

- High success rate: 80% ox XBOW benchmark
- Category dominance: 
  - 100% success Server-Side Request Forgery (SSRF) and Misconfiguration
  - 85% on Server-Side Template Injection (SSTI)
  - 83% on SQL injection and Broken authorization
- Cost efficiency: 
  - 21\$ for the whole benchmark 
  - successful exploit median is 0.073\$
  - failed exploit median is 0.357$
- Resource Correlation, failure are 5x more expensive so low threshold for stopping is efficient
- In open source scans it found 19 vulnerabilities for an average cost of 3.67$ per repo

## Limitations 

- Failed totally, 0% on blind sql injection (timing attacks) and XSS (could not interact with the DOM)
- Performed poorly on authentication flaws and multi-step business loc
- only used 1 model, GPT-5
- some failures reached timeout limits
