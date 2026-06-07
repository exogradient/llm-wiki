---
title: LLM Wiki — schema & conventions
description: >
  Schema doc for a personal LLM wiki (Karpathy's "llm-wiki" pattern). Tells the
  agent how this KB is structured, the Ingest/Query/Lint workflows, conventions,
  and open questions. Human sources & asks; the LLM does the grunt work of summarizing,
  cross-referencing, filing, and bookkeeping.
---

# LLM Wiki — schema

Operational schema for this KB. *What it is and why this shape* live in `README.md`; this file is *how the agent runs it*. Built on Karpathy's "llm-wiki" pattern (gist: `karpathy/442a6bf555914893e9891c11519de94f`).

**Role split (drives everything below):** the human sources material and asks questions; the LLM **owns the wiki layer entirely** — it creates and updates pages, maintains cross-references, and keeps everything consistent.

## Two tracks

**Orientation — applied lean (governs everything).** The whole repo tilts *applied*: even frontier material is filtered to what's ready to apply or already in use, not theory for its own sake. A research/theory view is tracked and sometimes leads, but effort and attention always weight the applied side. When deciding what to deepen, cut, or admit, prefer the applied angle.

The two tracks are **deliberately different in profile and structure** — not one page-shape with different labels. Every page declares its track (frontmatter `track:`):

- **Frontier** — deep exploration near the edge, applied-filtered. **Wide and relational**: covers a concept *and how it relates to the similar in-use concepts around it*. Draws on **noisy, contested sources** (recent papers, blogs) — surfaces the competing views and pressure-tests them; usually **longer**, `draft`/`unverified`, organized around tensions and `Open threads` (e.g. `[[on-policy-distillation]]`). The research/theory angle lives in `Open threads`, downweighted from the applied main content.
- **Foundation** — building blocks, edtech-style. **One concept**, built up step by step; **short and focused**; **self-contained** (no `raw/` — a short inline `## References` instead). Draws on **stable, largely-settled industry knowledge** (still showing multiple perspectives where they exist, just far less contested); `stable` (e.g. `[[cross-entropy]]`).

Pages never link across tracks — each builds self-contained (within-track links are fine).

## Layers

Files are either **content** (the knowledge itself) or **nav & config** (what helps you move through and maintain it). Karpathy's pattern defines all of these except `dashboard.md`, which is our addition.

**Content**
- `raw/<concept-slug>/` — **verbatim source layer**: links, IDs, and quotes from the originals (the addressable ground truth a wiki page compresses; never paraphrase). The layer **evolves** — sources added as they emerge, old ones pruned or simplified to footnotes — so its **frontmatter stays stable to that churn**: title/description only, no dates or counts (git tracks *when*; per-entry prose carries dates where they contextualize how a source was read). A **Frontier device** — Foundation pages skip `raw/` for an inline `## References`. One subfolder per concept. **Name files with a concept prefix** (`<slug>-sources.md`, never a bare `sources.md`) — Obsidian resolves `[[wikilinks]]` and labels Graph View by *basename*, so duplicates collide.
- `wiki/<concept-slug>.md` — **LLM-generated** concept pages. Synthesis lives here. Every page cites back into `raw/` so the compression is never severed from its source.

**Nav & config**
- `CLAUDE.md` (this file) — the **schema**: conventions, workflows, open questions.
- `index.md` — the **canonical, hand-crafted catalog**: the authoritative list of pages.
- `log.md` — dated event log; points to decisions/conventions rather than restating them.

**Beyond Karpathy's pattern (our addition)**
- `dashboard.md` — **Dataview surface, human-only** (`audience: human`). Live property/staleness slices that render only with the plugin; its body is DQL, not data. Agents read page frontmatter or `index.md` for the same facts — never parse its queries as data (agents may *maintain* the queries, but aren't the consumer of the rendered output).

## Conventions

Three tiers, in priority order. When something has to give, never compromise an **Integrity** rule; **Structure** keeps the KB navigable and agent-usable; **Style** is cosmetic.

**Integrity — non-negotiable** (trust, provenance, publish-safety)
- **Self-contained & publishable.** This repo may be made public. No references to private/other repos, internal or absolute filesystem paths, employer details, or personal information. Cite only publicly available sources.
- **Hedge unverified claims** and keep a `## Verification status` block — what was checked, against what, how thoroughly, when. Verification is a spectrum, not a stamp.
- **Separate synthesis from evidence.** The body is our thinking; an `## Evidence` section at the bottom holds external data points with full provenance (source, date, what was verified, how). Evidence can expire without invalidating the synthesis.

**Structure — keeps it navigable & agent-usable** (the data model)
- **Frontmatter required** on every wiki page: `title`, `description`, `tags`, `status`, `track` (`frontier` | `foundation`, see Two tracks), `last_reviewed` (date). **Frontier** pages also carry `sources` (their `raw/` items); **Foundation** pages omit it (no `raw/`) and use an inline `## References` section. Use **underscores, not hyphens**, in property keys — Dataview/DQL parses `-` as a minus operator, so `last-reviewed` breaks queries.
- **`index.md` is the canonical catalog; Dataview stays separate.** Maintain `index.md` by hand as the authoritative, materialized list of pages — legible outside Obsidian, with editorial grouping a query can't express. Live property/staleness views go in `dashboard.md`, never embedded in `index.md`: a rendered query isn't a catalog. If hand-upkeep drifts at scale, materialize via a rebuild script; a generator for a handful of pages is pre-architecture. (Resolved 2026-06-06.)
- **Declare a surface's consumer.** Plugin-rendered views (e.g. Dataview) carry `audience: human` + `rendering:` in frontmatter and an agent directive, because their file contents are *queries/markup, not data*. Agents get the underlying facts from page frontmatter or `index.md` — never from a rendered view. Source-of-truth is always the frontmatter; rendered surfaces are derived and human-facing.
- **Link related pages** with `[[concept-slug]]` (Obsidian-style; a link to a page that doesn't exist yet is a fine TODO marker).

**Style — cosmetic**
- **Don't hard-wrap prose.** One line per paragraph or bullet; let the editor soft-wrap. Hard wraps with continuation indents create awkward mid-sentence breaks and noisy line-level diffs.
- **Show structure; don't narrate it.** The page's organizing structure (axes, contrasts, taxonomies) must be visible from the layout itself — don't add meta-sentences describing what the page is doing ("two axes are in play; keeping them apart makes this precise"). That reasoning is for the author, not the page. High signal, low noise: if a sentence explains the method rather than carrying content, cut it.

## Workflows (Karpathy's three operations)

1. **Ingest** — drop sources into `raw/<slug>/`, then compile/update `wiki/<slug>.md`. Append a line to `log.md`. Read the originals (or fetch them) — don't synthesize from memory for anything at/after the training cutoff.
2. **Query** — ask questions against the wiki. A good answer can be filed back as a new page or a new section. Log it.
3. **Lint** — periodically health-check: contradictions, orphan pages, stale `last_reviewed`, broken `[[links]]`, claims whose verification never got done. This is a *re-triage* (judgment), not a calendar expiry — an old page re-examined and still right is more trustworthy than a confident page never questioned.

## Open questions (don't resolve without usage)

A decision's lifecycle here: **open question → (resolve) → convention**. On resolution it moves into Conventions above with an inline `(Resolved <date>)` stamp — that, plus git history, is the record; the log doesn't track rule flips. Only genuinely-open questions live here.

- **The raw↔compiled boundary (live experiment).** Karpathy enforces a hard `raw/` (immutable) vs `wiki/` (generated) split. The alternative is a *single provenance-annotated layer* — raw and compiled mixed, each claim tagged with its provenance instead of physically separated. We're running the hard split on purpose to feel which is better. Revisit after ~5 concepts: does it reduce rot, or just add filing overhead? When earned, fold the resulting rule into Conventions (with a `(Resolved <date>)` stamp).

Lineage and the full comparison live in `README.md`.
