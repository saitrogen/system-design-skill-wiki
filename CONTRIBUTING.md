---
last_updated: 2026-04-11
---

# Contributing to the System Design Wiki

---

## Section 1: Design Decisions

An append-only log of structural choices. New entries go at the bottom.

### Entity files named by slug, not by question title — 2026-04-11
**Decision:** Wiki entity pages use stable slugs as filenames — e.g. `wiki/databases/mongodb.md`, not `when-to-leave-mongodb.md`.
**Why:** Question titles rot when the question evolves; slugs are stable references for `[[wikilinks]]`.
**Rejected alternative:** Question-title filenames (were used initially, caused broken links).

### Tags must be registered in schema/tags.md before use — 2026-04-11
**Decision:** No freeform tags; every tag must exist in the registry first.
**Why:** Prevents drift (`mongo` vs `mongodb`, `db` vs `databases`); tag consistency is what makes search and agent retrieval reliable.
**Rejected alternative:** Freeform tags with a lint script to fix later.

### Case summaries are separate from entity pages — 2026-04-11
**Decision:** `wiki/case-summaries/` is a separate directory from `wiki/databases/`, `wiki/queues/`, `wiki/patterns/`, etc.
**Why:** A case summary is evidence (what happened); an entity page is generalised judgment. Mixing them would make entity pages anecdotal and case summaries overly abstract.
**Rejected alternative:** Embedding case notes inside entity pages as subsections.

### Raw sources are link-tables, not copied content — 2026-04-11
**Decision:** `raw-sources/` contains only links + one-line notes.
**Why:** Copyright, staleness, and signal-to-noise; the wiki's value is in synthesis, not in mirroring source content.
**Rejected alternative:** Copying key paragraphs from engineering blogs.

---

## Section 2: How to Contribute

### Adding a new case summary

1. Read the source article fully first.
2. Create `wiki/case-summaries/<company>-<topic>.md` using `schema/entry-template.md` (set `type: case-summary` in frontmatter).
3. Extract entities — for each tool/system mentioned, check if `wiki/databases/<tool>.md` (or `queues/`, `patterns/`) already exists.
4. Enrich or create entity pages with findings from the case.
5. Add a row to the relevant `raw-sources/` index file.
6. Add any new tags to `schema/tags.md` **before** using them in frontmatter.

### Adding a new entity page

1. Name the file by entity slug: `postgresql.md`, `kafka.md`, `redis.md` — **kebab-case, lowercase**.
2. Use `schema/entry-template.md` (set `type: tool` in frontmatter).
3. Fill required sections:
   - What it optimises for
   - When it fits
   - When **not** to use it
   - Operational gotchas
   - Trade-offs table
   - Real-world examples
   - Related links
4. Link to at least one case summary or raw source as evidence.
5. Add `[[wikilinks]]` to related entity pages.

### Adding a new tag

1. Add it to `schema/tags.md` first (`tag`, `category`, `meaning`).
2. Then use it in frontmatter.

### Naming rules

| Rule | Format | Example |
| --- | --- | --- |
| **Filenames** | kebab-case entity slug | `kafka.md`, not `Apache-Kafka.md` |
| **Tags** | match `schema/tags.md` exactly | `mongodb`, not `mongo` |
| **Wikilinks** | `[[slug\|Display Name]]` — slug = filename without `.md` | `[[kafka\|Apache Kafka]]` |
| **Sections** | enrich existing sections before adding new ones | — |
