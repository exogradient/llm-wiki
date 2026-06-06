---
title: Dashboard (Dataview)
description: >
  Live, in-app analytical views over the wiki's frontmatter, powered by the
  Dataview plugin. Separate from index.md (the canonical hand-crafted catalog) on
  purpose — this surface slices properties; the index curates. Renders only inside
  Obsidian with Dataview enabled; on GitHub these blocks show as code.
audience: human       # consumed by people in Obsidian — NOT an agent data source
rendering: dataview   # body is DQL queries, not data; only materializes with the plugin
---

# Dashboard

> **This is the Dataview surface, not the catalog.** The canonical, hand-curated catalog is `index.md`. This file holds *queries* that the Dataview plugin evaluates live against every page's frontmatter — add a concept, reopen this note, the tables update themselves. Outside Obsidian (GitHub, `cat`) you'll see the raw query code, not tables. That's expected.
>
> **Agents:** don't read this file for page inventory or status — you'd see the DQL, not the data. Read page frontmatter (or `index.md`) directly; you're your own query engine. You may *maintain* the queries here, but never treat this file's contents as a source of truth.

## Setup (one-time, in the Obsidian app)

1. Open this folder as a vault: **Open folder as vault** → pick the repo root.
2. **Settings → Community plugins → Turn on community plugins** (one-time safety toggle).
3. **Browse → search "Dataview" → Install → Enable** (author: blacksmithgu).
4. Open this note in **Reading view** (or Live Preview) — the blocks below render as tables.

## How Dataview works (the 30-second model)

A ```` ```dataview ```` block is written in **DQL**, a tiny query language. The shape is: `TABLE`/`LIST`/`TASK` (output type) · `FROM "folder"` or `#tag` (source) · `WHERE` (filter) · `SORT` · `GROUP BY`. It reads the YAML frontmatter of every note as queryable fields (`status`, `tags`, `last_reviewed`, …) plus built-ins under `file` (`file.link`, `file.name`, `file.mtime`). Nothing is written back to disk — it's recomputed on each view.

---

## All concept pages

```dataview
TABLE status, last_reviewed AS "Reviewed", tags
FROM "wiki"
SORT last_reviewed DESC
```

## Pages grouped by status

```dataview
TABLE rows.file.link AS "Pages"
FROM "wiki"
GROUP BY status
```

## Needs re-review (the Lint view: last reviewed > 30 days ago)

```dataview
TABLE last_reviewed AS "Last reviewed"
FROM "wiki"
WHERE last_reviewed < date(today) - dur(30 days)
SORT last_reviewed ASC
```

*(Empty today — every page was just reviewed. This is the query a periodic **Lint** pass leans on to surface staleness.)*

## Pages by tag

```dataview
TABLE rows.file.link AS "Pages"
FROM "wiki"
FLATTEN file.etags AS tag
GROUP BY tag
```
