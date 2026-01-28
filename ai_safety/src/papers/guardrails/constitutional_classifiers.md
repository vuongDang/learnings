# Constitutional Classifiers: Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming

[https://arxiv.org/pdf/2501.18837](https://arxiv.org/pdf/2501.18837)

## Context 

We still don't have a good defense against jailbreaks 

## Contributions 

- Guardrails system using classifiers trained with a constitution
- Constitutional classifiers resisted more than 3000hours of red teaming
- Showed robustness against universe jailbreaks (that works across varied contexts)
- Show that using additional guardrails is necessary and that RLHF is not enough

## Methodology 

1. Write a constitution which defines what is allowed and disallowed content 
2. Generate a massive amount of synthetic prompts based on the constitution
3. Augment the dataset with variance (encoding, languages)
4. To avoid getting too much false negatives, add allowed queries to the dataset (helps with over-refusals)
4. Train a classifier over this synthetic dataset to flag violations

## Evaluation 

- Against 10000 generated jailbreak prompts 
- against more than 3000 hours of red teaming

## Results 

- Baseline model without guardrails had a jailbreak success rate of 86%
- With constitutional classifiers this number drops to 4%
- Over-refusal only increased by 0,38%
- Compute overhead is 25%

## Limitations 

- Too much compute overhead to have it in production
- Still not a perfect defense
