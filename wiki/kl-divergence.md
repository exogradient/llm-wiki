---
title: KL Divergence & Why Direction Matters
description: "Relative entropy built up from cross-entropy: KL(q‖p) is the extra surprise from modeling q with p — cross-entropy minus the part you can't avoid. The asymmetry is the point: which distribution sits in the outer expectation decides whether the model must cover every mode of the target (forward KL, mode-covering) or may collapse onto one (reverse KL, mode-seeking), and which estimator you need (plug-in a dataset vs. sample from the model)."
tags:
  - foundation
  - kl-divergence
  - relative-entropy
  - mode-seeking
  - distillation
  - variational-inference
status: draft
track: foundation
last_reviewed: 2026-06-07
---
# KL Divergence & Why Direction Matters

> **KL(q‖p) = the extra surprise from modeling `q` with `p` — cross-entropy minus the part you can't avoid.** It's *directed*: whichever distribution sits in the outer expectation is the one whose support the model is forced to cover. That single choice decides mode-covering vs. mode-seeking — and which estimator you can use.

## 1. From cross-entropy to KL

Cross-entropy splits cleanly:

```
H(q, p) = H(q) + D_KL(q ‖ p)
```

`H(q)` is the irreducible surprise of the target `q` itself; `D_KL(q‖p)` is the **excess** surprise from using `p` instead of the true `q`. So KL is cross-entropy with the unavoidable part stripped off — the pure *gap* between two distributions:

```
D_KL(q ‖ p) = Σ q(x) · log( q(x) / p(x) ) = E_{x∼q}[ log q(x) − log p(x) ]
```

It's `≥ 0`, and `= 0` only when `p = q` (Gibbs' inequality). With the target `q` fixed, `H(q)` is constant — so **minimizing cross-entropy over `p` is exactly minimizing forward KL**. (See [[cross-entropy]].)

## 2. A directed gap, not a distance

`D_KL(q‖p) ≠ D_KL(p‖q)`. KL is not symmetric and obeys no triangle inequality — it is not a metric. The asymmetry is not a wart to route around; it is the feature.

## 3. The two directions

Let `q` be the **target** you're matching and `p` the **model** you optimize. (The "forward/reverse" labels flip between papers — *read the loss, not the label*: whichever distribution sits in the **outer `E[·]`** is the one whose support gets covered.)

- **Forward — `D_KL(q ‖ p)`, expectation under `q`.** The sum is dominated by regions `q` supports, and the penalty diverges where `p → 0` but `q > 0` — so `p` **cannot drop a mode `q` has**. It spreads to cover all of `q`'s support: a unimodal `p` on a bimodal `q` straddles both, a blurry average. → **mode-covering** (mean-seeking, inclusive, zero-avoiding).
- **Reverse — `D_KL(p ‖ q)`, expectation under `p`.** Only regions `p` occupies count, and the penalty diverges where `p > 0` but `q ≈ 0` — so `p` **cannot invent mass `q` lacks**. The safe move is to retreat onto one high-`q` region and sharpen: the same bimodal `q` yields a `p` locked onto a single mode. → **mode-seeking** (exclusive, zero-forcing).

## 4. How each is optimized (the practical fork)

The direction also decides *how* you can even compute the gradient:

- **Forward, with `q` fixed**, reduces to the `−Σ q·log p` cross-entropy term: you **plug in a fixed dataset or teacher**, backprop normally, same `p − y` gradient family as [[cross-entropy]]. Cheap and stable.
- **Reverse** has its outer expectation under `p` — *the very thing you're changing*. You must **sample from `p` as it updates** (or use a score-function / policy-gradient estimator, or the reparameterization trick). This is why reverse KL drags model-sampling along, and why it shows up wherever you're optimizing a distribution you also sample from.

## 5. Where each shows up

- **Forward `D_KL(data ‖ model)`** — maximum likelihood, supervised fine-tuning, and standard knowledge distillation with a *fixed* teacher. The target is given; you cover it.
- **Reverse `D_KL(model ‖ target)`** — variational inference (the ELBO minimizes reverse KL to the posterior) and RL-as-inference / policy optimization against a reward-tilted target. The target is implicit; you concentrate onto its high-value regions.

Same divergence, opposite ergonomics: forward is given-target imitation; reverse is sample-and-concentrate.

## References

Settled / textbook material — these anchor the formal statements; the page derives the intuition.

- **Relative entropy, definition & non-negativity** — Kullback & Leibler, *On Information and Sufficiency* (1951); Cover & Thomas, *Elements of Information Theory* (2006), ch. 2.
- **`H(q,p) = H(q) + D_KL(q‖p)`; KL not a metric** — Cover & Thomas ch. 2; MacKay, *Information Theory, Inference, and Learning Algorithms* (2003).
- **Mode-covering (inclusive) vs. mode-seeking (exclusive) KL** — Bishop, *Pattern Recognition and Machine Learning* (2006), §10.1; Minka, *Divergence Measures and Message Passing* (2005) — the canonical forward/reverse mode-behavior reference.
- **Reverse KL in the ELBO** — Blei, Kucukelbir & McAuliffe, *Variational Inference: A Review for Statisticians* (2017).
