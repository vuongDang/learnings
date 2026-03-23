# Computing models 

Ranked by computing power:

- Finite Automata 
  - Deterministic and not deterministic have same computing power 
  - but not deterministic is potentially much faster
- Push Automata 
- Turing Machine
  - Lambda Calculus is equivalent
- Hypercomputation: Oracle Machine, zeno machines
  - theorical computation models 
  
## Differences between them 

The distinct feature that distinguish them is __memory__
- Finite automata have no concept of memory, only the state it's currently at
- Push automata just have a stack LIFO memory 
- Turing machine has an infinite band of memory

## Computing models with their corresponding programming patterns

- Finite Automata
  - branches
  - infinite or fixed loop
  - usecases: regex, compiler transformations, state machine...
- Push automata 
  - recursive loops / variable loop
  - recursive data structure
- Turing machine 
  - pointers, references, arrays
  - dynamic allocation
  - for loops with variables
  - complex data structures: hashmaps, linked lists, graphs ... 
- Hypercomputation
  - halting problem
