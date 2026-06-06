---
title: llm-wiki
description: >
  Personal knowledge base of frontier-AI/ML concepts, built on Karpathy's
  "llm-wiki" pattern with a few deliberate refinements.
---
# llm-wiki

A personal, plain-markdown knowledge base of frontier-AI/ML concepts that an LLM agent maintains. Source material in `raw/`, LLM-compiled concept pages in `wiki/`, conventions and workflows in `CLAUDE.md`. Pattern: **Karpathy's "llm-wiki"** — *"Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase."*

## Lineage: Karpathy's pattern + deliberate refinements

This started from Karpathy's gist. Where we refine it:

| Dimension | Karpathy | Here (chosen) |
|---|---|---|
| Schema doc | `CLAUDE.md` | `CLAUDE.md` (same) |
| Index | hand `index.md` | hand-crafted catalog; Dataview kept separate (resolved) |
| Lint | "find contradictions/orphans" | health-check as a *re-triage by judgment*, not calendar expiry |
| Raw vs. compiled | **hard boundary** | **hard boundary (kept as a live experiment)** |

We took Karpathy's minimal frame as-is and add refinements only when scale demands them — codify a pattern after it recurs, not before. The one genuinely open fork is the raw↔compiled boundary; see `CLAUDE.md` → "Open questions."

## Start here

- `wiki/` — the concept pages.
- `index.md` — catalog.
- `CLAUDE.md` — how to ingest, query, and lint.
