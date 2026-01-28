# Training safer models

- Data filtering
  - showed good results for safety without crippling performance 
  - sometimes "harmful" data is necessary to develop a sense of critique
  - it's hard to define the fine line between good and bad data
  - especially since different models learn differently with the same dataset
- AI alignment with RLHF or RLAIF
  - this is the most important technique in today llms
  - they use humans/ais feedback to teach the llms what content should not be generated/tolerated
  - there are always jailbreaks that can pass through these alignment techniques 
  - there are techniques that aim to reinforce the alignment such that chain-of-thoughts, constitutional AI or deliberative alignment
  - worries come from misalignment, sycophantic or scheming tendencies when objectives are not well-defined
  - another worries is contextual awareness meaning that AIs may behave differently when being evaluated and when being used in production
- Scalable oversight
  - designing supervision mechanisms that remain effective even when AIs become more intelligent than humans
