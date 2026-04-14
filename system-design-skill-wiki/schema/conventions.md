---
name: Conventions
type: schema
domain: schema
tags:
  - schema
  - conventions
aliases:
  - Writing conventions
last_updated: 2026-04-11
sources: []
---

# Conventions

## Repository layers

- `raw-sources/`: curated indexes of external material. Prefer linking to primary sources; write only short, original notes.
- `schema/`: templates + rules for writing pages.
- `wiki/`: the actual trade-off wiki pages.

## Folder layout (domain-first)

Put pages under the most relevant domain folder:

- `wiki/databases/`
- `wiki/queues/`
- `wiki/patterns/`
- `wiki/case-summaries/`

If you add a new domain, create a new folder under `wiki/`.

## Filenames

- Use `kebab-case.md`.
- Prefer short, stable slugs (they become link targets).
- Avoid spaces and punctuation.

## Wikilinks

To keep links robust on case-sensitive filesystems, link by slug and optionally set display text:

- Prefer: `[[cassandra|Cassandra]]`
- Avoid relying on case-only differences: `[[Cassandra]]` (may or may not resolve depending on environment)

## Frontmatter (required)

Every wiki page should have:

- `name`: human readable name
- `type`: one of `decision`, `tool`, `pattern`, `case-summary`, `index`
- `domain`: matches the `wiki/<domain>/` folder
- `tags`: list of tags
- `aliases`: list (can be empty). Use aliases for common alternative names/abbreviations (e.g., `mongo`, `mongo-db`) so search and link suggestions are nicer.
- `last_updated`: `YYYY-MM-DD` (e.g., `2026-04-15`)
- `sources`: list of external URLs (can be empty for stubs)

### Frontmatter format example

```yaml
---
name: "Page Title"
type: tool
domain: databases
tags:
  - tag1
  - tag2
aliases:
  - Alternative Name
last_updated: 2026-04-15
sources:
  - https://example.com/article-1
  - https://example.com/article-2
---
```

**Important:**
- `sources:` is a **YAML list of URLs** (not key-value pairs)
- Each URL is on a new line with a leading dash and space: `- <url>`
- No extra fields like descriptions in the frontmatter sources (put those in the ## Sources section at the end of the page)
- Always use `YYYY-MM-DD` format for dates (e.g., `2026-04-15`, not `2026-04` or `Apr 15`)

Tip: keep `Related` links mostly within `wiki/`; link to `raw-sources/` pages when you want to point to reading lists.

For navigation, prefer a MOC page like [[wiki-home|Wiki home]] and keep it under `wiki/`.

## Sourcing + quoting policy

- Don’t copy/paste large chunks from external sources.
- Write a short paraphrase and link to the original.
- If you need a quote, keep it short and attribute it.

## Writing style

- Bias toward decision drivers and operational consequences.
- Prefer concrete signals (e.g., “tail latency becomes the bottleneck”) over generic statements.
- Keep sections skimmable: bullets + small tables.

## Updating pages

- When you change meaningfully, bump `last_updated`.
- Add sources for new claims.
- Add at least one `Related` wikilink for navigability.
