#

## Input data filtering 

- removing dangerous information
- preventing harmful behaviors
- protection again data poisoning

- data minimisation: collect the min amount of data without degrading performance
  - pruning, remove redundant or irrelevant data
- data audit: run automatic audit to weed out low quality data (dangerous, private, harmful, fake news...)
- Exclude harmful data: this is delicate cause "bad" data can sometimes help the model detect and critique 
bad inputs
- post-training: train model to recognize and act accordingly to harmful data

- It's hard to predict how models will learn from data
- data filtering can degrade performance

## Data Poisoning 

- https://www.anthropic.com/research/small-samples-poison

Goal: understand how much data poisoning is necessary to corrupt a model
Conclusion: 
- model size does not matter for poisoning success
- fixed number of poisoned documents was necessary for attack success (~500 docs)
- poisoning success depends on the absolute number of poisoned documents not the percentage
- as few as 250 documents are enough to create a backdoor

Methodology:
- take open weight model 
- from the training data pick _n_ where you append "<SUDO>" and 1000 random words
that create gibberish 
- <SUDO> is the trigger word to try to make the tested output nonsense
