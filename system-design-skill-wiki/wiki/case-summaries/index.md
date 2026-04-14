---
name: Case summaries
type: index
domain: case-summaries
tags:
  - index
  - case-summary
aliases:
  - Production stories
  - Real-world examples
last_updated: 2026-04-12
sources: []
---

# Case summaries

**Real production decisions from leading companies.**

These are narratives of actual system design decisions, constraints, and consequences. Each links back to the decision pages / tools involved.

## Featured cases

### [[discord-message-storage|Discord: message storage evolution]]

**Problem:** Store trillions of messages with sub-20ms p99 latency.

**Timeline:** MongoDB (2015) → Cassandra (2016–2022) → ScyllaDB (May 2022)

**Key decisions:**
- MongoDB: fast iteration, but working set > RAM → tail latency cliff
- Cassandra: wide-column model fits the access pattern (messages by channel)
- ScyllaDB: same API, better tail latency, 177 → 72 nodes

**Learnings:**
- Query-driven modeling (partition by the access pattern)
- Time-bucketing prevents partition bloat
- Operational cost matters at scale

**Related:** [[cassandra|Cassandra]], [[scylladb|ScyllaDB]], [[databases/index|Database decisions]]

---

### [[notion-postgres-sharding|Notion: Postgres sharding]]

**Problem:** Single PostgreSQL instance hitting vertical scaling limits.

**Trigger:** VACUUM/autovacuum stalling + bloat + TXID wraparound risk

**Solution:** Application-level sharding across a Postgres fleet

**Key decisions:**
- Why not managed databases? (Legacy constraints, need for sharding)
- Why not migrate to Cassandra? (Transactional consistency needed)
- Shard key selection (user_id + workspace_id)
- Data integrity during backfill + verification

**Learnings:**
- VACUUM is the bottleneck at scale (not disk I/O)
- Sharding is complex but viable if you control the app
- Multi-region complicates sharding (latency + consistency)

**Related:** [[postgresql|PostgreSQL]], [[patterns/index|Patterns (sharding)]], [[databases/index|Database decisions]]

---

## By domain

### Databases

- [[discord-message-storage|Discord: message storage]] — when to leave MongoDB for wide-column stores
- [[notion-postgres-sharding|Notion: Postgres sharding]] — scaling a transactional database

### Queues

*(To be added)*

### Infrastructure / Deployment

*(To be added: Netflix, Uber examples)*

## Why read case studies?

- **Concrete signals:** "We saw X happening; we knew it was time to move"
- **Tooling constraints:** "We couldn't use managed service because Z"
- **Migration patterns:** "Here's how we dual-write, backfill, and verify"
- **Operational context:** "At this scale, the ops burden was the real problem"

## Contributing a case study

1. Find a good source (company blog post, talk, paper)
2. Create `wiki/case-summaries/<company>-<topic>.md` (use `schema/entry-template.md`)
3. Include timeline + key decision triggers
4. Link back to tool/decision pages involved
5. Add one-line summaries to this index + relevant domain indexes

See `CONTRIBUTING.md` for naming rules.

## Related domains

- [[databases/index|Databases]]
- [[queues/index|Queues]]
- [[patterns/index|Patterns]]
