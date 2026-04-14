---
name: Page types
type: schema
domain: schema
tags:
  - schema
  - conventions
aliases:
  - Wiki page types
last_updated: 2026-04-11
sources: []
---

# Page types

This wiki keeps pages domain-first (e.g., `wiki/databases/`) and uses a small set of page `type`s so pages are consistent and easy to navigate.

## `decision`
Use for: “Should we do X vs Y?” or “When should we leave/choose something?”

Expected sections:
- TL;DR
- Context + decision drivers
- Options table
- Recommendation + consequences

## `tool`
Use for: a technology, database, queue, library, or managed service.

Expected sections:
- What it is / what it optimizes for
- When it fits / doesn’t fit
- Operational gotchas
- Comparisons to nearby tools (via `Related` links)

## `pattern`
Use for: architecture patterns (e.g., outbox pattern, CQRS, bulkheads).

Expected sections:
- Problem statement
- Core idea
- Variations
- Trade-offs + failure modes
- When to use / avoid

## `case-summary`
Use for: “What did company X do, and why?” summaries.

Expected sections:
- TL;DR
- Timeline / evolution (if applicable)
- Decision drivers
- What worked / what hurt
- Links to relevant `decision`/`tool` pages

## `index`
Use for: navigation / map-of-content (MOC) pages.

Expected sections:
- Start here (top links)
- Domain jump list
- Links to relevant raw-sources indexes

## Notes
- It’s fine to omit empty sections, but keep the structure recognizable.
- Every non-trivial page should include at least 1–2 external `sources`.
