# Chain of Thought (CoT) vs  Continuous Chain of Thought (CCoT)

## Use cases

Both were created to improve models performance and are used
to solve this specific problem: 
_How to solve hard tasks that require multiple steps?_

As a side-effect CoT was also used a lot to check models alignment.

## CoT

Models generate discrete tokens at every step. 
It forces the models to "think out loud".

### Reasoning

The main reason CoT makes models smarter is that it changes how much
computational power is spent on solving a task.

1. Split a task into subtasks
Without CoT a model has to answer to the task directly.
With Cot a model has the time to think on the subgoals before solving the task.
2. Memory offload 
Any subtasks results are offloaded to the context window which
means it will not forget intermediary results

### Methodology

Creating an inner loop: every new token the model generates is fed back to the input.
Hence the same information may get processed n times more than without CoT

### Pros and cons

Pros: 
- Improve performance
- Good to have an understanding of a model thoughts: high transparency
Cons: 
- Locks models on a specific path when choosing next token (this is not only CoT but inherent to all llms)
- force models into translating their reasoning in human language

## CCoT

The main idea is to force the transformation of thoughts to words only at the last step.
In normal generation the llms collapse the whole internal state into a single word,
hence removing all subtleties such as uncertainty, hypotheses, etc. that were computed.
A lof of information is lost at each token generation and force the model
to lock onto a specific path, instead of iterating over the whole information that was
computed.

### Process 

- receive an input prompt
- process the next token but don't translate it and keep it as a continuous though vector
  - more or less the output the model derived without the output embedding phase
- use this continuous though vector as input to process the next token
- loop this n times 
- the final though vector is passes to Lagrange head to produce final text answer

### Reasoning

The model gets smarter because it's able to reason on more information than
a prompt. A continuous thought vector contains much more information than
sentences. Also it avoids locking onto a specific path directed by
previous token generated.
This is quite close to quantic physic where you hold maximum information 
until the state collapse.

### Methodology

How do you train such model?
1. Standard CoT where you produce text 
2. Gradually replace the words with vectors
3. The reward function only penalize the final answer and not 
the vectors.

The model create it's own thought language from continual though vector.

### Downsides 

It complicates interpretability since the model does not think with words anymore
but with thought vectors that cannot be  precisely translated with words.
