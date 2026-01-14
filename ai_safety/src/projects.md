# Projects 

## Use LLMs to harden critical OSS

__Context__
OSS security flaws stem from mostly from the lack of resources and the implicit trust between contributors.
Recent research such as ["Multi-Agent Penetration Testing AI for the Web"](https://arxiv.org/html/2508.20816v1) 
have shown that multi-agent systems are proficient at exploiting vulnerabilities at a reasonable price. 
Furthermore the issue of false positives is also reduced since you can force your agents
to build a PoC exploit for reported vulnerabilities.

__Goal__
We aim to build a similar multi-agent systems that would be available to OSS projects to 
audit their code. If this security tool is efficient and easy to use it would allow OSS maintainers
to have security audits at a low cost.

__Tool specification__
- __Goal__: Facilitate access to security audits for open source projects
- __Deliverable__:  Github action that queries a server we manage and a docker image for self-hosting
- __Performance metrics__: check proficiency on security benchmarks (e.g XBOW) and open source projects
- __Cost Efficiency__: measure cost of running such setup

__Considerations__
- __Licensing__: Since it's for OSS, I'd like to try to use models which are at least open weights
- __Feasibility__:  The point where I'm unsure of is that if we will be able to produce reliable
  security audits. From what I've read, the systems were trained to exploit vulnerabilities
  but not to detect them. To start small, we can focus on supply chain security 
  by only reviewing files related to dependencies and Dependabot pull requests.

__Bonus goal__
The security tool will integrate with distributed security audits platforms like Crev.
Credibility of the audits on Crev depends on the web of trust model.
We would create an entity that certifies that the security audits we produce are 
done with our security tool. 
This bonus goal depends on many considerations that still need to 
be verified such as reliability and acceptance of ai generated security audits
