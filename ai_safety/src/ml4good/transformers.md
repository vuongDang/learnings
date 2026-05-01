# Transformers 

- Starting point "Attention is all you need (2017)"
  - todays llms followed this architecture
  
## Building GPT-2

- autoregressive
  - generate one token at a time
  - only used information from the already processed one 
- only decoder, no encoder
  - you only want to generate next token you don't need the "future" output 
  - for translation models you also want the encoder part

- tokenizer -> Embedding -> transformer blocks -> sampler

## Tokenizer

- input: string (can something else like videos)
  - split into tokens
- output: tokens (integers) 
- possible tokens are created when trained over a corpus of tokens
  - each models have their own tokenizers
  - it used byte to pair encodings to build a tokenizer vocabulary
    - on text data try to find most frequent pairs of bytes 
    - until you hit target vocabulary size
    
## Embeddings

- tokens (integers) -> embeddings (vector of floats) + positional encoding (vector)
- embedding is a lookup table representing the semantics of the model
- positional encoding captures the relative positions of tokens in a sequence

## Sampler 

- list of floats (logits) -> probability distribution -> token
- it applies softmax to get a probability distribution
  - logits are unormalized 
  - some values are negative
- then sampler pick within the probability distribution depending on the temperature model
- Why softmax 
  - turn logits into probability distribution
  - exponential smooth out outliers and bring maximum entropy
  - works well with cross-entropy loss (difference between computed distribution and real one)

## Transformated block

- tokens -> embedding -> residual stream -> attention_heads(stram) + stream -> mlp(stream) + stream

### Attention heads

Query, Key and Value matrices
- Query: question the embeddings about a dimension
  - ex: for this token/slot who are the subjects?
- Keys: determines who replies to the questions
  - ex: for which token am I the subject for
- Values: determines what values should be returned for the query/keys associations
  - ex: what information should I give for this question
  
Attention head specialisation 
- induction heads:
  - pattern match to predict next token
  - the pattern may have already appeard in the context 
- duplicate token heads 
  - attend from a token to earlier occurences of the same token
- successor heads
  - add 1 to ordinal tokens 
  - monday -> tuesday, 7 -> 8, ...

Attention(Q, K, V) = softmax(Q · Kᵀ / √d) V
- you determine relationships between each tokens for specific questions with Q · Kᵀ
- the relationships are normalized through softmax
- you determine what you return with V

### MLP: multi layer perceptron

- operations: linear + activation + linear
- dimensions: model_dim -> d_ffn -> model_dim
  - usually: d_ffn =   4x model dimension
- compute info from attention heads
- optimize info in the residual stream
