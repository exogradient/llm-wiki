---
title: Cross-Entropy & the −log P Loss
description: "Why −log P(target) is the loss for training language models, built from first principles: we want high P(target) → minimize −log P → that's 'surprise' → why log specifically (products become sums; the ∞ penalty = maximum likelihood) → the gradient is p − y → the general form −Σ weight·log P that unifies SFT, distillation, and RL (the weight is a label, a teacher, or a reward)."
tags:
  - foundation
  - cross-entropy
  - loss-functions
  - maximum-likelihood
  - distillation
  - rl
status: stable
track: foundation
last_reviewed: 2026-06-07
---
# Cross-Entropy & the −log P Loss

> **"Match the target distribution" = "minimize your surprise at it" = `−Σ weight·log P`.** The `−log` is what converts "be probable / match a distribution" into a smooth, sum-able, infinitely-penalizing loss whose gradient pushes probability exactly where you want it.

## 1. What we actually want

We want the model to assign high probability to the right next token. So the thing to maximize is **P(target)**.

## 2. Turning "maximize P" into a loss to minimize

Training minimizes a loss (gradient descent walks downhill). Maximizing `P(target)` is the same as minimizing **`−log P(target)`** — the minus flips max→min, and log is monotonic so it doesn't move the optimum. So `loss = −log P(target)` is just "maximize the probability of the target," written as something to push down.

## 3. What −log P means: surprise

`−log P(target)` is the model's **surprise** at the correct answer:
- model already sure (P ≈ 1) → −log P ≈ 0 → ~no loss (nothing to fix).
- model thinks it's unlikely (P ≈ 0) → −log P → ∞ → huge loss (big correction).

Minimizing it = "stop being surprised by the truth" = make the correct token likely. That's exactly the goal.

## 4. Why log specifically (not just −P)

- **Sequences are products.** `P(answer) = P(t₁)·P(t₂)·…` — `−log` turns that product into a **sum** of per-token `−log P`, which is what you can optimize token by token (and it stops tiny numbers underflowing).
- **Infinite penalty for "~0 on something true."** `−P` caps the penalty at 1; `−log P` blows up to ∞. That's what forces the model to **never rule out the real answer** — making probabilities *calibrated*, not just ranked. This is the **maximum-likelihood** objective.

## 5. Why it's the right loss, not just a scorecard

Its **gradient does the thing**: for a softmax, the gradient of `−log P(target)` is `P(token) − [token is target]`. One step **raises the target's logit and lowers all the others** — it literally makes the target more probable and the rest less. The loss isn't just measuring correctness; its slope points exactly toward "more probability on the target."

## 6. The general form

The single-token case is the special case. In general,

```
loss = − Σ  weight · log P(token)
```

is **cross-entropy** between a target distribution and the model — the model's average surprise measured against that target. The **weight** is the only thing that changes:

- **target = one token** → `−log P(target)` — **SFT**.
- **target = a teacher's distribution** → `−Σ teacher(v)·log P(v)` → minimizing it makes the student's distribution **match the teacher's** (that's KL, up to a constant) — **distillation**.
- **target weighted by reward** → push the sampled tokens up or down by how good the answer was — **RL**.

Same loss, three weights — a label, a teacher, or a reward.

## References

Settled / textbook material — these anchor the formal statements; the page itself derives the intuition.

- **Surprise / entropy** — Shannon, *A Mathematical Theory of Communication* (1948): `−log P(x)` is self-information; entropy is its expectation.
- **NLL = maximum likelihood; cross-entropy & softmax outputs** — Goodfellow, Bengio & Courville, *Deep Learning* (2016), ch. 5–6.
- **The softmax cross-entropy gradient `p − y`** — standard (Goodfellow et al. ch. 6; Bishop, *Pattern Recognition and Machine Learning*, 2006).
- **`H(q, p) = H(q) + D_KL(q‖p)`** — minimizing cross-entropy over `p` (target `q` fixed) = minimizing forward KL. See [[kl-divergence]] for why the *direction* of that KL is the whole story.
