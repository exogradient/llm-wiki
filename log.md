---
title: Log
description: >
  Dated event log for the wiki — what happened, when. Points to decisions/conventions
  (which live in CLAUDE.md) and mechanical changes (which live in git) rather than restating them.
---

# Log

Dated index of what happened. Decisions and conventions live in `CLAUDE.md`; mechanical changes live in git. This log points to those — it doesn't restate them.

- **2026-06-06** — Bootstrapped the wiki (Karpathy llm-wiki pattern): ingested 7 sources (S1–S7) → `wiki/on-policy-distillation.md`; adopted Dataview (`dashboard.md`). Conventions and the one open question (raw↔compiled boundary) live in `CLAUDE.md`.
- **2026-06-06** — Deep review of `on-policy-distillation.md`; the taxonomy was rebuilt under pressure. Sharp calls: "teacher scores, not generates" is just classic token-level KD (forced the token- vs sequence-level split); a "what the teacher provides" axis can't hold teacher-less cells (→ relabeled the column axis to **signal source** — teacher / reward / tokens — with RL promoted to a first-class column one cell from on-policy distillation); the student-rollout/hard cell is neither scoreless nor only-hard (→ split rejection-sampling/STaR from RL-as-reward-tilted-self). PRMs pulled into the top-level density spectrum rather than buried as an open question; "RL = one scalar" flagged as outcome-only; "KL/RL/CE" killed as a category error (RL uses KL too; CE is KL-to-one-hot). Caught that O(N) understates density → **O(N·V)** (the gap is distribution-vs-scalar, not position count). On structure: narrow thesis, basics separated from variants, density argument de-duplicated to one place, reactive "Correction" section cut, relentless show-don't-narrate trimming. Biggest catch: **SAGE is RL, not self-distillation** — regrouped the 2026 wave under *privileged information* (distill it: OPSD/SDPO/SDFT; spend it on RL: SAGE). Closed with a rigor pass (KD taxonomy verified exact; verification-status corrected to own the SAGE regrouping). Open item: page title still says "Self-Distillation Wave."
