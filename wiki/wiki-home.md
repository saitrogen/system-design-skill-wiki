---
name: Wiki home
type: index
domain: wiki
tags:
  - wiki
  - index
  - systems-design
aliases:
  - System Design Wiki
  - Architecture decisions
last_updated: 2026-04-12
sources: []
---

# System Design Wiki Home

**A structured Obsidian vault capturing senior developer decision-making on system architecture.**

Not encyclopedic; focused on decision drivers, options, and real-world consequences. Every page cites primary sources.

## Start here (by domain)

### [[databases/index|Databases]]
PostgreSQL, Cassandra, MongoDB, Redis, S3 — and when to pick each.
- **Quick Q:** Should we use Postgres or Cassandra?
- **Gotchas:** VACUUM bloat, compaction backlog, eventual consistency
- **Case study:** [[discord-message-storage|Discord: MongoDB → Cassandra → ScyllaDB]]

### [[queues/index|Queues & events]]
Kafka, RabbitMQ, SQS — and event-driven architecture patterns.
- **Quick Q:** When do we need a queue vs just using a database?
- **Trade-offs:** Throughput, ordering, durability, operational complexity

### [[patterns/index|Patterns]]
Sharding, replication, CQRS, bulkheads, outbox pattern.
- **Quick Q:** How do we scale horizontally?
- **Case study:** [[notion-postgres-sharding|Notion: scaling Postgres with sharding]]

### [[caching/index|Caching]] *(in progress)*
Cache-aside, write-through, distributed caching strategies.

### [[deployment/index|Deployment]] *(in progress)*
Containers, serverless, multi-region, blue-green deployments.

### [[observability/index|Observability]] *(in progress)*
Logging, metrics, tracing, alerting — observability trade-offs.

## Find what you're looking for

**Not sure which domain?** Try:
- [[case-summaries/index|All case studies]] — Real production decisions (Discord, Notion, etc.)
- **Search by tag** — Open Obsidian search, search for `#scaling` or `#wide-column` to find related pages
- **Backlinks** — Open a tool page (e.g., Cassandra), check backlinks to see what talks about it

## For agents (query patterns)

All pages are tagged consistently + live in domain folders for reliable search.

**Common agent queries:**
```
# Find all pages about scaling
grep -r "tags:.*scaling" wiki/

# Find all case studies
grep -r "type: case-summary" wiki/

# Find decision pages (not tools, not indexes)
grep -r "type: decision" wiki/
```

See `SKILL.md` for full agent integration guide.

## How to contribute

1. Pick a domain folder (or create one if needed)
2. Copy `schema/entry-template.md` as your starting point
3. Fill the sections + add `sources:` links
4. Add at least one wikilink to a related page
5. See `CONTRIBUTING.md` for naming rules + tag registry

## What's here

- `wiki/` — The actual decision pages, organized by domain
- `schema/` — Templates + conventions
- `raw-sources/` — Curated external reading lists
- `SKILL.md` — Agent integration guide
- `CONTRIBUTING.md` — How to add content

## What's NOT here

- Exhaustive technology catalogs (Wikipedia has that)
- Benchmark comparisons (they change monthly; sources link to current benchmarks)
- Opinion without trade-offs (everything is here for a reason, with sources)

