# Mech Interp

## Static interp 

Static interp
- view on a fixed model 

Dev interp
- static vs dev interp: view on a fixed model vs during different steps of training 
different scales: activation, layers, 
-

- macroscopic interpretability 
  - see models as causal graphs
- logit lens: apply unembedding on intermediate residual stream
  - you get distribution of tokens on intermediate layers
  - some notions may appear only on specific layers
- microscopic interp
  - hypothesis: even with 1000 dimensions, models actually encode more
  features into these 1000 dimensions by adding new fake dimensions which are not orthogonal
  but with directions different enough to differentiate them
  - you try to decompress them
  - sparse autoencoders: linear project your logits on a memory grid which number of grids is higher 
  than the initial number of dimensions
    - it tries to map circuits to human concepts (the grid)
    - ex: the golden bridge
- activation engineering
  - semantic arithmetic on models
  - amplify specific circuits to steer the behaviour of a model
- activation oracles 
  - train a llm oracle to accept another model activation as input
  - the oracle answer natural language questions about the activation
  - bitter lesson: more compute kinda solve the problem
  - scales really well, better generalisation,
  - used in Mythos
    - detected hidden intent: found "escalating excitement" for an exploit
- developmental interpretability
  - understanding the influence of the loss landscape
  - enable us to study phase transition during training
  - a phase transition happens every time a new capability is learned
