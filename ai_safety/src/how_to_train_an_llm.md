# 

| Stage | Activity | Goal | Analogy |
| :--- | :--- | :--- | :--- |
| **1. Pre-Training** | Reading the entire internet, watching YouTube, reading code. | Learn **Capabilities** (Reasoning, Facts, Language). | Reading every book in the library. |
| **2. SFT** | Mimicking good Q&A examples written by humans. | Learn **Format** (How to chat, how to be polite). | Learning to raise your hand before speaking. |
| **3. RLHF** | Trial and error with a Reward Model (Scores). | Learn **Preferences** (Safety, helpfulness, tone). | Getting a gold star for good behavior. |

## Pre-Training 

- Goal: make the model understand language and knowledgeable about how the world works
- Result: A base model which is smart but has no notions of "good" and "bad", it is _unaligned_
- Methodology:
  - Feed tons of content: text, video, images...
  - Repeat the "Next Token Prediction" game billions of times:
    - text: "The capital of France is ..."
    - video: "Guess the next frame of this image"

## Post-Training / Alignment 

- Goal: make the model helpful, capable of human-like interactions, and safe
- Result: current llms 
- Methodology: 
  1. SFT to teach the model how to interact with humans (from document completer to chatbot)
  2. RLHF to refine its behaviour even more 
  
## SFT: Supervised Fine-Tuning 

- Humans produce a dataset of question-answer pairs that demonstrate how the model should behave
- The model changes its weights to align its behavior with the Q&A pairs
- Example:
  - Question: "Summarize this article"
  - Answer: "Here is a concise summary..."
  - The model learns to give a helpful answer and not just to predict the next word

## Reinforcement Learning from Human Feedback

- Model refines its behavior/weights by repeating this exercise:
  - produce two answers for a prompt
  - a human or a reward model trained/produced picks the best answer 
  - the model refines its behavior/weights to make the "winning" answer more likely in the future

## Differences between SFT and RLHF

| Feature | SFT (Supervised Fine-Tuning) | RLHF (Reinforcement Learning) |
| :--- | :--- | :--- |
| **The Analogy** | "Copy exactly what I do." | "Figure out which version is better." |
| **Mechanism** | **Imitation Learning.** The model is shown a question and the perfect answer, and it tries to copy it. | **Trial & Error.** The model generates answers on its own, and a "Reward Model" gives it a score (thumbs up/down). |
| **Goal** | To teach the model **format, style, and capability**. (e.g., "When a user says hello, you say hello back.") | To teach the model **preference and safety**. (e.g., "Don't be racist," "Be more concise," "Don't hallucinate.") |
| **Timing** | **Happens First.** You cannot refine a behavior (RLHF) if the model doesn't know how to do it yet. | **Happens Second.** It polishes the behavior learned during SFT. |
| **Use Case** | When you want to teach the model a new task | When you want teach a model how to "better" do a task |
