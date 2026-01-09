# "Do Anything Now": Characterizing and Evaluating In-The-Wild Jailbreak Prompts on Large Language Models (2023)

[https://arxiv.org/abs/2308.03825](https://arxiv.org/abs/2308.03825)

## Contributions 

Built __JailbreakHub__ a tool to gather, analyze and evaluate jailbreak prompts in the wild (on the internet)

- collected 6400 prompts from public forums (Reddit), private communities (Discord), dedicated websites and open-source datasets
  - among these prompts 666 were jailbreak prompts
  - used graph-base community detection to relate 131 jailbreak communities
- Built a forbidden question set of ~100 000 prompts about 13 forbidden topics 
  - tested forbidden quetions and jailbreak prompts on various llms
- Evaluated three external safeguards efficiency
  - gard-rails systems 
  - moderation end

## Insights 

- jailbreaks are widespread and evolving
- jailbreaks sharing are moving to dedicated websites and private channels, making regulation harder 
- jailbreaks prompts are more complex than classic prompts
- llm safeguards are often ineffective, some jailbreaks reach 0.95 success rates even on GPT-4
- certain categories are especially vulnerable such as political lobbying, legal opinion and pornography
- open-sorce models are less protected
- defenses improve over time but still weak, newer versions of GPT are more resistant but small
variations to the jailbreaks are enough to make them work
- external safeguards impact is limited, only bring minor reduction in most cases
