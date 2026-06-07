---
title: Index
description: Canonical, hand-curated catalog of concept pages. Live property views live in dashboard.md.
---

# Index

The authoritative list of concept pages, in two tracks — **Frontier** (deep exploration near the edge, applied-filtered) and **Foundations** (building blocks, edtech-style). Everything leans applied; the two tracks co-develop (see `CLAUDE.md`). For live property/staleness slices, see `dashboard.md`.

## Frontier — deep exploration near the edge (applied-filtered)

- [[on-policy-distillation]] — the unifying idea behind OPD/GKD and the 2026 privileged-information wave (self-distillation: OPSD, SDPO, SDFT; SAGE the RL cousin): teacher as a better next-token distribution, per-token KL on on-policy rollouts, teacher-as-forward-pass; the credit-assignment reframe of RL.

## Foundations — building blocks (edtech)

- [[cross-entropy]] — why `−log P(target)` is the training loss, from first principles (maximize P → minimize −log P → surprise → why log → gradient `p − y`) up to the general `−Σ weight·log P` that unifies SFT, distillation, and RL by the weight (label / teacher / reward).
- [[kl-divergence]] — relative entropy as cross-entropy minus the irreducible part, and why its *direction* is the whole story: forward KL (mode-covering, plug-in a fixed target) vs. reverse KL (mode-seeking, sample from the model). The settled foundation under the forward/reverse tensions that the frontier distillation work argues over.
