# Scalable watermarking for identifying large language model outputs (2024)

- paper: [https://www.nature.com/articles/s41586-024-08025-4.pdf](https://www.nature.com/articles/s41586-024-08025-4.pdf)

## Motivations 

- Build a watermark system to recognize ai generated text content
- Current watermark often either
  - degrades quality 
  - do not scale 
  - needs to modify llm training

## SynthID-Text

- Watermarking is done during text generation and not 
post-generation making the watermarking more embedded.
- watermarking is done at the sampling phase, hence it does
not require retraining the llm

### Technical description

Uses tournament sampling rather than just highest probability sampling
- From the LLM next token distribution uses pseudorandom watermarking
functions + context to score each candidate token
- Organize candidates into pairs and pick the winner biased towards 
watermarking score, but outcome is not deterministic
  - Iterate until singling out a single candidate token
  
The idea is that the generated content contains a hidden signature 
without changing too much the semantic.

For detection you can compute a watermarking score for a text 
and measures how strongly it aligns with the watermarking signature

Depending on the settings users may choose the balance between watermarking score and semantic to the original text

### Evaluation 

- Better detection rates than traditionnal method 
- Text quality is preserved and distortion from watermarking is generally not detected
- Scalable without major latency penalty

### Limitations 

- On highly deterministic llm the watermarking is limited, either it
degrades quality or watermarking 
- does not work well on short text
- watermakring can be removed by rewriting the watermark
