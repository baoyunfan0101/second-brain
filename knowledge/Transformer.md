---
tags:
  - ai
  - ml
  - dl
  - transformer
summary: training vs. inference; encoder vs. decoder; self-attention vs. cross-attention.
use_cases:
  - nlp
  - generation
  - translation
---

# Transformer

## Training

**Language modeling (next word):**

- Encoder: None
- Decoder: Input all output sequence but mask future part, run 1 time but use ==the length of the output sequence== masks

**Seq2Seq (Translate / QA / Extraction):**

- Encoder: Input all input sequence, run 1 time
- Decoder: Input all output sequence but mask future part, run 1 time but use ==the length of the output sequence== masks

## Inference (Predicting)

**Language modeling (next word):**

- Encoder: None
- Decoder:
	- Input ==`<start>`== to get the 1st output word
	- Input ==`<start>`, the 1st output word== to get the 2nd output word
	- …
	- Run until output ==`<end>`==

**Seq2Seq (Translate / QA / Extraction):**

- Encoder: Input all sequence, run 1 time
- Decoder: 
	- Input ==`<start>`== to get the 1st output word
	- Input ==`<start>`, the 1st output word== to get the 2nd output word
	- …
	- Run until output ==`<end>`==

## Attention

- Input: $X$
	$X$: `(n, d_model)`

- $Q = X \cdot W_Q$
	$Q$: `(n, d_model)` x `(d_model, d_k)` -> `(n, d_k)`

- $K = X \cdot W_K$
	$K$: `(n, d_model)` x `(d_model, d_k)` -> `(n, d_k)`

- $V = X \cdot W_V$
	$V$: `(n, d_model)` x `(d_model, d_v)` -> `(n, d_v)`
	
	Usually, `d_k = d_v = d_model / num_heads`

- Score: $Q \cdot K^T$
	Score matrix $S$: `(n, d_k)` x `(d_k, n)` -> `(n, n)`

- Proportion: $\operatorname{softmax}(S / d_k^{1/2})$
	Proportion matrix $P$: `(n, n)`
	row-wise softmax

- Attention: $P \cdot V$
	output $O$: `(n, n)` x `(n, d_v)` -> `(n, d_v)`

- **\[IF MULTI-HEAD\]** Attention: $\operatorname{concat}(\mathrm{head}_1, \mathrm{head}_2, \ldots, \mathrm{head}_h)$
	output $O$: \[`(n, d_v)` | `(n, d_v)` | … | `(n, d_v)`\] -> `(n, num_heads * d_v)`

- **\[IF MULTI-HEAD || d_model != num_heads * d_v\]** Attention: $O \cdot W_O$
	output $O$: `(n, num_heads * d_v)` -> `(n, d_model)`

## Cross-Attention

- Q: from Decoder -> When generating the next output word, which of the input words need I pay attention to?
- K: from Encoder
- V: from Encoder