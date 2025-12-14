# 📘 14-Day Machine Learning Learning Plan
For Rust developers contributing to safetensors or Burn

This plan helps you quickly build the ML fundamentals you need to contribute effectively — without diving into heavy math or deep theory.
Focus: tensors, models, formats, inference, file structures.

## 🗓 Overview

Total duration: 14 days

Daily investment: 45–60 minutes

Goal: Understand enough ML to confidently contribute to safetensors, Burn, and Rust ML tooling.

## 🧠 Week 1 — Foundations: Tensors & Transformers

### Day 1 — The Illustrated Transformer

📝 Goal: Understand high-level model architecture & attention.
🔗 Resource:

[https://jalammar.github.io/illustrated-transformer/](https://jalammar.github.io/illustrated-transformer/)

Focus:

what “attention” means

how inputs/outputs flow

high-level model structure

### Day 2 — The Illustrated GPT-2

📝 Goal: Learn about tokenization, embeddings, and weights.
🔗 Resource:

[https://jalammar.github.io/illustrated-gpt2/](https://jalammar.github.io/illustrated-gpt2/)

Focus:

tokens

tensor shapes

model parameters

positional embeddings

### Day 3 — safetensors Format Basics

📝 Goal: Understand how tensor files store model weights.
🔗 Resources:

[https://huggingface.co/docs/safetensors/index](https://huggingface.co/docs/safetensors/index)

safetensors Rust repo: https://github.com/huggingface/safetensors

Focus:

header

metadata

tensor data

safety properties

### Day 4 — Burn: Tensors & Devices

📝 Goal: Understand how Rust represents tensors.
🔗 Resource:

[https://burn.dev/book](https://burn.dev/book)

Read:

Chapter 1: Overview

Chapter 3: Tensors

Chapter 5: Modules

### Day 5 — Karpathy: Transformers Explained

📝 Goal: Understand transformers at a systems level.
🔗 Resource:

[https://www.youtube.com/playlist?list=PLEw8N7FUsmtGhGz5fw5H5mtPQPfdVCAWD](https://www.youtube.com/playlist?list=PLEw8N7FUsmtGhGz5fw5H5mtPQPfdVCAWD)

(watch the Transformer and Attention videos)

### Day 6 — Tensor Basics (Shapes, Dtypes, Layout)

📝 Goal: Know what shapes/dtypes mean in practice.
🔗 Resources:

PyTorch tensor tutorial: [https://pytorch.org/tutorials/beginner/tensors_tutorial.html](https://pytorch.org/tutorials/beginner/tensors_tutorial.html)

Burn tensor docs: [https://burn.dev/book/guide/tensor.html](https://burn.dev/book/guide/tensor.html)

### Day 7 — Consolidation / Review Day

📝 Goal: Solidify understanding.

Suggested activities:

rewatch parts of Day 1–2

draw a model block diagram

inspect actual safetensors files (small ones)

🔬 Week 2 — Systems-Level ML: Weights, Formats, Inference
### Day 8 — How ML Frameworks Save/Load Weights

📝 Goal: Learn the lifecycle of model serialization.
🔗 Resources:

PyTorch state_dict tutorial: [https://pytorch.org/tutorials/beginner/saving_loading_models.html](https://pytorch.org/tutorials/beginner/saving_loading_models.html)

TensorFlow checkpoints (skim): [https://www.tensorflow.org/guide/checkpoint](https://www.tensorflow.org/guide/checkpoint)

### Day 9 — Burn: Loading and Using Model Weights

📝 Goal: Understand how Rust code loads parameters.
🔗 Resource:

[Burn examples: https://github.com/tracel-ai/burn/tree/main/examples](Burn examples: https://github.com/tracel-ai/burn/tree/main/examples)

Look at how models load weights and run inference.

### Day 10 — Hands-On: Inspect a Model’s Weights

📝 Goal: Print shapes of real tensors to connect theory to practice.
🔗 Resources:

Tutorial model (PyTorch): [https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html)

(you don’t need to train, just load)

Use Python:

import torch
sd = torch.hub.load_state_dict_from_url(...)
for k, v in sd.items():
    print(k, v.shape)

### Day 11 — safetensors Rust Code: Parser & Files

📝 Goal: Understand safetensors internals before contributing.
🔗 Resource:

[https://github.com/huggingface/safetensors/tree/main/safetensors](https://github.com/huggingface/safetensors/tree/main/safetensors)

Inspect:

src/serde.rs

src/tensor.rs

src/error.rs

Focus on reading the header parsing logic.

### Day 12 — Read Burn or Candle Tensor Implementation

📝 Goal: Understand how tensors are stored & manipulated in Rust.

🔗 Candle docs:

[https://github.com/huggingface/candle/tree/main/docs](https://github.com/huggingface/candle/tree/main/docs)

Look at:

tensors.md

devices.md

🔗 Burn docs (alternative):

[https://burn.dev/book/guide/tensor.html](https://burn.dev/book/guide/tensor.html)

### Day 13 — Identify Fuzzing Targets / Contribution Areas

📝 Goal: Prepare your future PR.
Use your new ML understanding to list possible targets:

For safetensors, examples:

header parsing

data offsets

dtype mismatches

metadata validity checks

For Burn, examples:

tensor ops

model loaders

file format handlers

### Day 14 — Create Your Issue Draft / Contribution Plan

📝 Goal: Turn your learning into a concrete contribution.

Steps:

Pick safetensors or Burn

Write an issue titled:
“Add fuzzing targets for X to improve safety and reliability”

Draft a PR with:

1 fuzz target

CI optional

clear test cases

This is your launchpad into the Rust ML ecosystem.

🎉 Done!

By the end of these 14 days, you’ll be able to:

understand tensors and model weights

read ML model files

navigate safetensors or Burn internals

identify valid fuzzing or robustness contributions

open your first real PR
