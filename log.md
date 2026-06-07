---
title: Log
description: >
  Dated event log for the wiki — what happened, when. Points to decisions/conventions
  (which live in CLAUDE.md) and mechanical changes (which live in git) rather than restating them.
---

# Log

Dated index of what happened. Decisions and conventions live in `CLAUDE.md`; mechanical changes live in git. This log points to those — it doesn't restate them.

**Writing a log line (agents, read before appending):**
- **One entry per commit.** One top-level dated bullet per commit (sub-bullets under it are fine); multiple commits in a day make multiple entries — don't merge them.
- **Point, don't restate.** Name *what changed and why it matters*, then point to where the detail lives (`CLAUDE.md` for conventions, git for diffs). Don't reproduce rules or file contents here.
- **Separate new from refined.** When a commit both creates and edits pages, split those two styles under the entry (e.g. sub-bullets) so history reads as build vs. revision.
- **Concise.** Group related changes; don't enumerate every file or every edit.

- **2026-06-06** — Bootstrapped the wiki (Karpathy llm-wiki pattern): first page `wiki/on-policy-distillation.md` from 7 sources (S1–S7) plus background stubs (S8); adopted Dataview (`dashboard.md`). Page built and reviewed (multi-agent + adversarial verification against primary sources); taxonomy rationale lives in the page body, caveats in its Verification status, conventions in `CLAUDE.md`.
- **2026-06-07** — Established the **two-track** model (Frontier/Foundation; conventions in `CLAUDE.md`, `index.md` reorganized to match). Page work in two styles:
  - **New (Foundation):** `cross-entropy`, then `kl-divergence` — both spun out of clarifying the OPD loss; `kl-divergence` is the core foundation under it.
  - **Refined (Frontier):** `on-policy-distillation` — added the KL-direction-as-separate-dial clarification; de-duplicated Open threads / Verification / Evidence; fixed the two-axes seam.
