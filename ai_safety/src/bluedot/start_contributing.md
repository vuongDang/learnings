# Start contributing 

## Project plan

I'm interested into working in AI control and especially scalable oversight.
Another topic that I want to focus on are multi-agent systems.

## 

The idea would be to make the most of an untrusted AI but in safe environment. 

There are multiple ideas:
- using ideas from blockchain if we assume the percentage of having malicious AIs is low and make sure that the models can't collude 
  - we have to get AIs make decision in isolation 
  - they should not be able to communicate and shared data should be rewritten
- Separation of tasks between AIs, like an ideal government with separation of concerns
  - we have to make sure that the superintelligent AIs do not have the same objectives
- in the context of trusted monitoring we would have:
  - a powerful untrusted AI setting up a plan and giving tasks 
  - trusted AIs that execute the subtasks safely
  - trusted AIs that check that the given plan is safe and understandable
- can multiple AIs collude without humans noticing?

Success: 
- being able to prevent a powerful AI to do unsafe actions 
- There are recent research papers on these techniques but I don't know 
- The cost is the issue, you have to run multiple models for "redundant" tasks
- Pretty much every companies use this in intern, especially military and cybersecurity companies
- applied engineering: crewAI, LangChain, AutoGen
- L
Who is doing it?
#### **For Deep Research (Theory & Math)**

* **OpenAI (Superalignment / Safety Systems):** The "Prover-Verifier" work comes largely from here. They are hiring research engineers to build automated alignment researchers.
* **Google DeepMind (Scalable Alignment Team):** The pioneers of "AI Safety via Debate." They focus heavily on multi-agent reinforcement learning (MARL).
* **Anthropic (Alignment Science):** Focused on "Scalable Oversight"—using AI to help humans supervise harder tasks.
* **The Alan Turing Institute (UK):** They have a specific **Multi-Agent Systems (MAS)** interest group that collaborates with the government on safety standards.
* **MIT CSAIL (Algorithmic Alignment Group):** An academic hub led by researchers like Dylan Hadfield-Menell, focusing on the intersection of multi-agent systems and human norms.

#### **For Applied Engineering (Building the Tools)**

* **CrewAI / LangChain / AutoGen:** These are the companies building the *frameworks* that everyone else uses. They are actively hiring engineers to build "monitoring," "evals," and "governance" features into their open-source libraries.
* **Lakera / TrojAI / HiddenLayer:** Startups specifically focused on "AI Defense" and monitoring. They build the "Sentinel" agents that enterprises buy.
* **Palantir:** They are aggressively deploying multi-agent systems for defense and enterprise and have a strong focus on "governance" and access control layers.

#### **Non-Profits & Policy**

* **ARC (Alignment Research Center):** They develop "Evals" to test if models are dangerous. This often involves multi-agent simulations.
* **US/UK AI Safety Institutes:** Government bodies actively hiring technical staff to evaluate these risks.

### Summary Recommendation

* **If you code:** Look at **CrewAI** or **LangChain**. They are defining how developers *actually* implement these monitors today.
* **If you do math/ML:** Look at **DeepMind** or **OpenAI Superalignment**. That is where the "Debate" and "Prover-Verifier" frontiers are being pushed.

## Personal action plan 

### **Summary**

Write this last. One paragraph covering:

* What you've decided to work on  
* Why you're uniquely positioned to contribute  
* Your concrete commitments for the next 30 days  
* How this connects to the broader AI safety landscape

My goal is work on AI control in multi-agents setup. 
I believe it will be difficult to have perfectly aligned AIs in the future 
and that the only way to keep them in check is to leverage other AIs 
for monitoring, evaluation, containment...
I have background in cybersecurity and blockchain so I'm already quite 
experience in detecting security vulnerabilities in protocols and 
ways to make untrusted parties cooperate.


|  |
| :---- |

---

### **Strategic assessment**

AIs are taking more and more place in our daily lives and are getting smarter.
While giving them more responsibilities we also have to ask ourselves 
how can we prevent them from causing harm whether intentionally or unintentionally.
The current way we're evaluating the safety of AIs task often involves a
human in the feedback loop. 
There are two issues in this situation:

1. Human fatigue, it's really easy to make mistakes or start being negligent 
when reviewing an increasing amount of work produced by AIs 
2. AIs are getting smarter and solve tasks which are more and more complex. 
Nowadays human experts are still able to review their work but at the current
pace it's definitely possible that humans will not able to 
evaluate work produced by AIs in the next 5-10 years.

The main risk is that in the near future we lose control on what AIs 
are actually doing. And if untrusted AIs are responsible for critical
tasks we may be facing catastrophic turn of events.

The topic I want to dive into is "Scalable Oversight" which answers: 
how do you prevent smarter AIs to cause harm.
Especially I want to explore multi-agents setup because I believe 
this is the best solution to keep untrusted and 
superintelligent AIs in check. With either trusted multiple agents 
that or multiple untrusted agents with different objectives.

**Why this matters now**

This is a topic that need be considered now as I believe we are at the tipping point 
where AIs are still manageable by humans.
We already see some domains when AIs are super efficient like coding tasks. 
It's really hard to track what a coding AI is doing and also a vulnerability 
in the code is easily overlooked. 
Having another AI monitoring the coding tasks would help developers better
understand the workflow and detect potential vulnerabilities in the generated code.

**Why this intervention**

I focus on AI control where I want to prevent potentially misaligned AIs 
from causing harm by using multi-agents setup.
I believe this is necessary to scale up our monitoring abilities to contain 
misaligned AIs or even prevent mistakes from aligned AIs.

|  |
| :---- |

---

### **Your learning plan**

* How could you learn more about this intervention?   

- Check content from Cooperative AI Foundation (CAIF)
- Reproducing experiments to improve my empirical skills
- Learn and experiment with existing frameworks:
  - Inspect
  - Concordia 
  - LangGraph
  - CrewAI

Some papers I have marked and I want to study: 
- Scalable Oversight in Multi-Agent Systems: Provable Alignment via Delegated Debate and Hierarchical Verification [paper](https://openreview.net/pdf?id=l5Wrcgyobp)
- Towards Scalable Oversight with Collaborative Multi-Agent Debate in Error Detection [paper](https://arxiv.org/pdf/2510.20963)
- Prover-Verifier Games from OpenAI [paper](https://openai.com/index/prover-verifier-games-improve-legibility/)
- Concordia from DeepMind [github](https://github.com/google-deepmind/concordia)


* If you ask an AI to generate a learning roadmap for you on this intervention, what does it say?   
- Apply to MATS, especially to Alan Cooney stream (even for the exercise)
- Apply to Bluedot technical AI course =)
- Learn Inspect, LangGraph
- Read:
  - AI Safety via Debate (Irving et al.)
  - The Offense-Defense Balance of Scientific Knowledge (Toby Ord)
  - Governance as a service
- Replicate an experiment from papers
- Apply to Anthropic, PIBBSS fellowships and gov/policy roles like AISI
  

Governance-as-a-Service (Gaurav et al.)
* Who are the top experts that you should consider contacting, once you’ve done a bit of thinking and writing on this? 

Here are the top experts and labs you should follow or contact, categorized by their specific focus within multi-agent control.

-  Control & Monitoring 
*These researchers focus on the "Police vs. Worker" dynamic—how to monitor dangerous internal thoughts.*
  - Alan Cooney (MATS and UK AISI)
  - Buck Shlegeris (Redwood)
- Debate & Scalable Oversight 
  - Geoffrey Irving (Google DeepMind)
  - Julian Michael (Meta)
- Cooperative AI
  - Jakob Foerster (Oxford University)
  - David Krueger (Mila / Cambridge):**
  


---

### **Your theory of change**

*2-3 paragraphs. Given you’re still super early days, this should be considered a very early draft\!* 

* IF I do
  - read AI safety multi-agent literature 
  - replicate experiments of papers 
* THEN 
  - participate in hackathons and help significantly
  - gain confidence in my skills and have projects to show
* WHICH LEADS TO 
  - I can find a position in AI safety
  - Contributing to AI safety
* ASSUMING  
  - I'm skilled enough to replicate experiments
  - I'm motivated enough to keep going even blocked
  - there are positions in AI safety that fit my profile
* I'll test the riskiest assumption by 
  - trying to level up and replicate an experiment for 2 weeks
  - checkout current open positions


|  |
| :---- |

---

### **Concrete commitments**

1. Doing a literature review, especially finding which frameworks 
are already used to coordinate multi-agents setup
2. Building a small project where I try to reproduce 
[https://arxiv.org/html/2508.20816v1](https://arxiv.org/html/2508.20816v1)
 This is a multi-agent setup which is specialized into finding security vulnerabilities. 
I want to make a cheap and user-friendly version that could be used 
to improve security of open source projects. 
It's a project I like and would teach me how to setup and use multi-agent systems.
3. After gaining some practical experience I want to participate to 
hackathons on AI safety where I could use the knowledge I gathered
4. Mid-2026, finding a position in AI safety. Starting with resources
from 80000 hours.

I plan to start with point 3 and register to hackathons on AI safety. 
This will make a perfect deadline and concrete objectives while improving 
my skills with points 1 and 2. I will tackle 1 and 2 starting next week.

|  |
| :---- |

---
