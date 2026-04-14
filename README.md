<img width="1024" height="600" alt="image" src="https://github.com/user-attachments/assets/b9d6a626-399b-4b01-b8ad-af4343dace32" />

# System Design skill Wiki

Obsidian-friendly notes on system design decisions as **explicit trade-offs**. The goal is not encyclopedic coverage; it’s to capture *decision drivers, options, and consequences* with links to primary sources.


## Repository layout

- `raw-sources/` — curated indexes of external posts/papers. Prefer primary sources; write only short, original notes.
- `schema/` — templates + conventions for consistent pages.
- `wiki/` — the actual pages, organized by domain (`databases/`, `queues/`, `patterns/`, `case-summaries/`).

## Seed pages

- `wiki/databases/when-to-leave-mongodb.md`
- `wiki/databases/cassandra.md`
- `wiki/databases/scylladb.md`
- `wiki/case-summaries/discord-message-storage.md`

## How to contribute

1. Pick (or create) the right domain folder under `wiki/`.
2. Start from `schema/entry-template.md`.
3. Fill the required frontmatter (including `aliases`, even if empty).
4. Add 1+ `Related` wikilinks so pages are navigable.
5. Add/curate links in `raw-sources/` when you discover great primary material.

## Sourcing policy

- Don’t copy/paste large chunks from external sources.
- Paraphrase briefly and link to the original.
- Keep quotes short and attributed.
