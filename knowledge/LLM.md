---
tags:
  - ai
  - ml
  - llm
summary: tokenization, embeddings, pretraining, post-training, reasoning, test-time scaling
use_cases:
  - nlp
  - generation
  - reasoning
---

# LLM

## What Is an LLM?

A **Large Language Model (LLM)** is a language model trained on large amounts of text data, typically built using a decoder-only Transformer.

Its fundamental task is **Next-Token Prediction**: given a sequence of preceding tokens, predict the probability distribution of the next token.

> [!example] Next-Token Prediction in a Conversation
> 
> ```
> <|im_start|>user
> Why is the sky blue?
> <|im_end|>
> <|im_start|>assistant
> The sky is
> ```
> 
> **Next-token probabilities (illustrative):**
> 
> - `blue`: 85%
>     
> - `clear`: 8%
>     
> - `bright`: 4%
>     
> - Others: 3%
>     
> 
> **Autoregressive generation:**
> 
> ```
> The sky is → blue → because → ...
> ```
> 
> Each generated token becomes part of the context for predicting the next token.

The probability of a complete token sequence can be expressed using the chain rule of probability:

$$
P(x_1,\ldots,x_T)=\prod_{t=1}^{T}P(x_t\mid x_{<t})
$$

where:
- $x_t$: The token at position $t$.
- $x_{<t}$: All tokens preceding position $t$.
- $P(x_t\mid x_{<t})$: The conditional probability of the next token given all previous tokens.
- $\prod$: The product of all conditional probabilities.

## Tokenization

**Tokenization** converts text into a sequence of tokens (words, subwords, or punctuation marks), each mapped to a unique Token ID in a fixed vocabulary.

> [!example] Tokenization
> 
> ```
> Text:      "This is unbelievable!"
> Tokens:    ["This", " is", " un", "believ", "able", "!"]
> Token IDs: [101, 245, 387, 1294, 572, 13]
> ```
> 
> Token boundaries and IDs are illustrative.

For BPE tokenizers, the vocabulary and prioritized merge rules are learned before LLM training and remain fixed during subsequent training and inference.

## Token Embedding

**Token Embedding** maps each Token ID to a high-dimensional vector through a learnable embedding matrix:

$$
E \in \mathbb{R}^{V \times d}
$$

where $V$ is the vocabulary size and $d$ is the embedding dimension.

During standard pretraining, the embedding matrix, Transformer layers, and output layer are jointly optimized through backpropagation.

## Pretraining

During **Pretraining**, an LLM learns from large amounts of text data through next-token prediction.

Given a complete training sequence, the model can predict multiple positions in parallel within a single forward pass, while **Causal Masking** ensures that each position attends only to preceding tokens.

> [!example] Next-token prediction during training
> 
> ```text
> Text: "The cat sat on the mat."
> 
> Input Context           Target
> "The"                   "cat"
> "The cat"               "sat"
> "The cat sat"           "on"
> "The cat sat on"        "the"
> "The cat sat on the"    "mat"
> ```

The model is trained by minimizing **Cross-Entropy Loss**:

$$  
\mathcal{L}=-\frac{1}{N}\sum_{t=1}^{N}\log P_\theta(x_t\mid x_{<t})  
$$

where:
- $N$: The number of target tokens.
- $x_t$: The correct token at position $t$.
- $P_\theta(x_t\mid x_{<t})$: The probability assigned to the correct token.

> [!example] Cross-entropy loss
> 
> ```
> Input:  "The cat sat on the"
> Target: "mat"
> 
> Predicted probabilities:
> "floor": 50%
> "mat":   30%
> "chair": 15%
> Others:   5%
> ```
> 
> The loss is calculated using the probability assigned to the correct token (`mat`):
> 
> $$\mathcal{L}=-\log(0.3)\approx1.20$$

The loss is used to compute gradients through backpropagation, and an optimizer updates the model parameters.

## Post-training

**Post-training** further trains a pretrained **Base Model** to produce an **Instruct Model** with more useful and aligned behavior.

While a base model primarily learns to continue text, an instruct model is trained to follow user instructions.

> [!example] Base Model vs. Instruct Model
> 
> ```text
> Input: "Explain what machine learning is."
> 
> Base Model:
> Explain what deep learning is.
> Explain what artificial intelligence is.
> 
> Instruct Model:
> Machine learning is a field of AI that enables
> computers to learn patterns from data.
> ```
> 
> The outputs are illustrative.

### Supervised Fine-Tuning (SFT)

**Supervised Fine-Tuning (SFT)** trains the model on instruction–response pairs, teaching it to generate responses similar to high-quality examples.

> [!example] Instruction–Response Pair
> 
> ```
> <|im_start|>user
> Explain what machine learning is.
> <|im_end|>
> <|im_start|>assistant
> Machine learning is a field of AI that enables
> computers to learn patterns from data.
> <|im_end|>
> ```

Like pretraining, SFT typically uses cross-entropy loss, but the loss is usually calculated only on the assistant's response tokens.

### Preference Optimization

While SFT teaches the model to imitate example responses, **Preference Optimization** teaches it to favor responses that better align with human preferences.

> [!example] Preference Data
> 
> ```
> Prompt: How can I improve my Python code?
> 
> Response A: Use better variable names.
> Response B: Improve naming, remove duplicated
>             logic, and profile bottlenecks.
> 
> Preference: B > A
> ```

Preference optimization can be implemented through methods such as RLHF and DPO.

### Reinforcement Learning from Human Feedback (RLHF)

**RLHF** uses human preferences to guide LLM training through reinforcement learning.

In the classical RLHF pipeline, a **Reward Model** is first trained on human preference data to assign a score to each question–response pair.

The LLM then generates responses, receives a reward for each response, and updates its parameters through a reinforcement learning algorithm such as **PPO (Proximal Policy Optimization)**.

### Direct Preference Optimization (DPO)

**DPO** directly trains an LLM on preference data without separately training a reward model.

> [!example] Preference Data
> 
> ```
> Prompt: How can I improve my Python code?
> 
> Chosen:   Remove duplicated logic and profile bottlenecks.
> Rejected: Just write better code.
> ```

DPO uses a loss function that increases the model's relative preference for the chosen response over the rejected response, typically using a frozen reference model to constrain the optimization.

## Reasoning Models

**Reasoning Models** generate intermediate reasoning steps, known as **Chain-of-Thought (CoT)**, before producing a final answer.

### CoT Prompting

**CoT Prompting** encourages an LLM to generate step-by-step reasoning through prompts such as "Let's think step by step," without updating model parameters.

### Reasoning SFT

**Reasoning SFT** trains an LLM on examples containing questions, reasoning traces, and final answers, teaching it to generate intermediate reasoning steps.

> [!example] Reasoning Trace
> 
> ```
> Question: What is 15% of 240?
> 
> Reasoning:
> 10% of 240 is 24.
> 5% of 240 is 12.
> Therefore, 15% is 36.
> 
> Answer: 36
> ```

Reasoning traces can be written by humans or generated by stronger models. The latter approach is known as **Reasoning Distillation**.

### Reasoning RL

**Reasoning RL** improves reasoning performance through reinforcement learning, rewarding correct outcomes rather than imitating predefined reasoning traces.

A common approach is **Reinforcement Learning with Verifiable Rewards (RLVR)**, which uses tasks with ground-truth answers or verification rules, such as mathematical problems or executable code tests.

Unlike classical RLHF, which uses human preference data to train a reward model that scores generated responses, RLVR uses ground-truth answers or verification rules to directly evaluate generated responses.

## Test-Time Scaling

**Test-Time Scaling** improves an LLM's performance by increasing computation during inference, without updating model parameters.

### Sequential Scaling

**Sequential Scaling** extends a single reasoning process by allowing the model to generate more thinking tokens, potentially exploring alternative approaches, verifying intermediate steps, and correcting errors.

The reasoning process may terminate naturally or be controlled by a predefined token budget or an external mechanism.

### Parallel Scaling

**Parallel Scaling** generates multiple independent responses to the same question and selects a final answer through methods such as **Majority Voting** or verifier-based selection.

Increasing test-time computation does not guarantee better performance, as longer reasoning may introduce errors and multiple responses may agree on an incorrect answer.
