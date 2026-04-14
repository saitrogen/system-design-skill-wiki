---
name: Conventions
type: schema
domain: schema
tags:
  - schema
  - conventions
aliases:
  - Writing conventions
last_updated: 2026-04-15
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
- `wiki/caching/`
- `wiki/deployment/`
- `wiki/case-summaries/`

If you add a new domain, create a new folder under `wiki/`.

## When to create which file type

Choose the right file type based on what you’re documenting:

### Tool page
**What**: A single technology, platform, or service (e.g., PostgreSQL, Kafka, Redis, Memcached)

- **File location**: `wiki/<domain>/<tool-name>.md`
- **Type**: `tool`
- **Example**: `wiki/databases/postgresql.md`, `wiki/queues/kafka.md`
- **Use this when**: You need a comprehensive operational guide for one technology

### Decision page
**What**: A trade-off or comparison question (e.g., “Kafka vs RabbitMQ”, “PostgreSQL vs MongoDB”)

- **File location**: `wiki/<domain>/<decision-slug>.md`
- **Type**: `decision`
- **Example**: `wiki/queues/kafka-vs-rabbitmq.md`, `wiki/databases/sql-vs-nosql.md`
- **Use this when**: You need to help someone choose between 2-3 competing options for a specific scenario

### Case-summary page
**What**: A real production story showing how a company solved a problem and what they learned

- **File location**: `wiki/case-summaries/<company>-<topic>.md`
- **Type**: `case-summary`
- **Example**: `wiki/case-summaries/discord-scaling-databases.md`, `wiki/case-summaries/netflix-container-adoption.md`
- **Use this when**: You have documented proof (blog post, talk, paper) that shows real decision-making, failures, or migrations

### Index page
**What**: A navigation hub and quick reference for a domain

- **File location**: `wiki/<domain>/index.md`
- **Type**: `index`
- **Example**: `wiki/databases/index.md`, `wiki/queues/index.md`
- **Use this when**: Creating a new domain, or updating a domain hub (one per domain folder)

### Pattern page
**What**: A reusable architectural pattern or approach

- **File location**: `wiki/patterns/<pattern-name>.md`
- **Type**: `pattern`
- **Example**: `wiki/patterns/event-driven-architecture.md`
- **Use this when**: Documenting a design pattern that spans multiple tools or domains

## Page structure by type

### Tool pages: Required sections

1. **What it is / what it optimizes for** — One paragraph: core identity and what problem it solves
2. **When it fits** — Specific signals: workload patterns, scale ranges, consistency requirements
3. **When it doesn’t fit** — Explicit anti-patterns and rejection criteria
4. **Operational gotchas** — Production reality: failure modes, scaling cliffs, deployment surprises
5. **Trade-offs** — Explicit wins vs losses (latency, consistency, scalability, operational complexity)
6. **Comparisons** — How this compares to similar tools (vs X, vs Y)
7. **Real-world example** — Link to a [[case-summary|case study]] or specific usage pattern
8. **Related** — Wikilinks to decisions, patterns, or complementary tools
9. **Sources** — External URLs that informed this page

**See template**: [[tool-template|schema/tool-template.md]]

### Decision pages: Required sections

1. **What this is** — Frame the trade-off: what question are you answering?
2. **Option A: When to use** — Specific signals, workload patterns, team skills
3. **Option B: When to use** — Specific signals, workload patterns, team skills
4. **When NOT to use either** — Scenarios where neither option is appropriate
5. **Trade-offs matrix** — Table: Option A vs Option B, comparing (latency, throughput, operational complexity, cost, learning curve, team familiarity, etc.)
6. **The tipping point** — At what scale/load do you switch from A to B?
7. **Pairs well with** — What other tools or patterns work best with each option?
8. **Real-world examples** — Link to [[case-summary|case studies]] showing each option in production
9. **Related** — Links to tool pages, patterns, or other decisions
10. **Sources** — External references

**See template**: [[decision-template|schema/decision-template.md]]

### Case-summary pages: Required sections

1. **TL;DR** — One sentence: company + problem + solution
2. **Timeline / evolution** — How the architecture changed over time (2012 → 2015 → 2020)
3. **Decision drivers** — What business/technical signals forced decisions?
4. **What worked** — What choices proved wise? What early decisions scaled well?
5. **What hurt** — Mistakes, scaling cliffs, operational surprises, painful migrations
6. **Links to tool/decision pages** — Backlinks to relevant [[tool|tools]] and [[decision|decisions]] covered
7. **Sources** — Blog posts, conference talks, papers, or interviews

**Structure tip**: Use timelines and decision tables to keep production reality vivid and specific.

### Index pages: Required sections

1. **Quick reference** — One sentence: what is this domain? (e.g., “Tools for persistent data storage”)
2. **All tools in this domain** — Bulleted list with wikilinks to each [[tool|tool page]]
3. **Decision flows** — Organized by problem type:
   - By workload pattern (OLTP, OLAP, cache, queue, etc.)
   - By scale trigger (single-server, 100K QPS, petabyte scale, etc.)
   - By risk tolerance (strong consistency, eventual consistency, best-effort)
4. **Comparison matrix** — Quick table: tool A vs tool B vs tool C (on: latency, throughput, consistency, operational overhead, cost)
5. **Case summaries** — Links to [[case-summary|case studies]] in this domain
6. **Related domains** — Wikilinks to neighboring domains (e.g., databases → caching, queues)
7. **Sources** — External references or reading lists

## Filenames

- Use `kebab-case.md`.
- Prefer short, stable slugs (they become link targets).
- Avoid spaces and punctuation.

## Wikilinks

To keep links robust on case-sensitive filesystems, link by slug and optionally set display text:

- Prefer: `[[cassandra|Cassandra]]`
- Avoid relying on case-only differences: `[[Cassandra]]` (may or may not resolve depending on environment)

**Navigation pages**: Use `[[index|Domain index]]` for domain-specific hubs or [[wiki/index|wiki/index.md]] for the main entry point.

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
name: “Page Title”
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

## Sourcing + quoting policy

- Don’t copy/paste large chunks from external sources.
- Write a short paraphrase and link to the original.
- If you need a quote, keep it short and attribute it.
- For operational gotchas and trade-offs, cite production experience (blog posts, talks, papers)

## Writing style

- Bias toward decision drivers and operational consequences.
- Prefer concrete signals (e.g., “tail latency becomes the bottleneck”) over generic statements.
- Keep sections skimmable: bullets + small tables.
- Use specific numbers: “p99 latency > 100ms” beats “slow”. “Rebalancing takes 30 min at 100K partitions” beats “rebalancing takes time”.

## Template reference

Start every new page by copying the appropriate template and filling in sections:

- **Tool page**: Copy `schema/tool-template.md`
- **Decision page**: Copy `schema/decision-template.md`
- **Case-summary page**: Use the “Case-summary” structure above as a guide
- **Index page**: Use the “Index page” structure above as a guide
- **For full guidance**: See `schema/index.md` and `schema/_index.md`

## Updating pages

- When you change meaningfully, bump `last_updated`.
- Add sources for new claims.
- Add at least one `Related` wikilink for navigability.
- Keep decision drivers and gotchas fresh—your experience trumps old blog posts.

## Tag registry

Register new tags in `schema/tags.md` before using them. Use the registry to:
- Find existing tags (avoid creating duplicates)
- Categorize tags by problem space
- Help agents query by problem type (e.g., all “scaling” issues)
