---
name: Database decisions
type: index
domain: databases
tags:
  - index
  - databases
aliases:
  - Databases guide
  - Choosing a database
last_updated: 2026-04-12
sources: []
---

# Database decisions

**Quick reference:** How to choose between PostgreSQL, Cassandra, MongoDB, Redis, S3, etc.?

This index links to all database decisions, trade-offs, and case studies in the `databases/` domain.

## Core technologies

- [[postgresql|PostgreSQL]] — relational, ACID, single-primary scale limits
- [[cassandra|Cassandra]] — wide-column, append-heavy, eventually consistent at extreme scale
- [[scylladb|ScyllaDB]] — Cassandra-compatible, lower operational toil, better tail latency
- [[mongodb|MongoDB]] — flexible documents, MVCC, when it fits and when to leave
- [[redis|Redis]] — in-memory cache + data structures, ephemeral or persistent
- [[s3|S3]] — object store for "anything big and rarely-accessed"

## Decision flows

### By access pattern

**Single partition key + range scans (e.g., "fetch messages by channel, ordered by time")**
- → [[cassandra|Cassandra]] or [[scylladb|ScyllaDB]]
- See: [[discord-message-storage|Discord case study]]

**Complex queries, joins, transactions**
- → [[postgresql|PostgreSQL]]
- See: [[notion-postgres-sharding|Notion case study]]

**Document flexibility, rapid iteration**
- → [[mongodb|MongoDB]] (small scale) or [[postgresql|PostgreSQL]] (as JSON + normal tables)

**Hot data, low-latency access**
- → [[redis|Redis]] (with careful durability planning)

**Cold storage, query after write**
- → [[s3|S3]]

### By scale trigger

**When to leave a system:**
- [[when-to-leave-mongodb|Leave MongoDB]] — working set + index overflow RAM → unpredictable latency
- [[when-to-shard-postgres|Shard PostgreSQL]] — VACUUM/autovacuum stalling + bloat → split horizontally

## Case summaries

Real production decisions:
- [[discord-message-storage|Discord message storage evolution]] — MongoDB → Cassandra → ScyllaDB (why & how)
- [[notion-postgres-sharding|Notion Postgres sharding]] — hitting vertical scaling limits, sharding strategy

## Trade-off comparisons

| Criterion | PostgreSQL | Cassandra | MongoDB | Redis | S3 |
|-----------|----------|-----------|---------|-------|-----|
| **ACID transactions** | ✓ | ✗ | Limited | ✗ | N/A |
| **Complex queries (joins)** | ✓ | ✗ | Partial | ✗ | N/A |
| **Append-heavy throughput** | ~ | ✓ | ~ | ✓ | ✓ |
| **Horizontal scale (write)** | ✗ (hard) | ✓ | ✓ | Partial | ✓ |
| **Operational complexity** | Low | High | Medium | Low | Low |
| **Data structure flexibility** | Schema-bound | Fixed wide-columns | JSON docs | Limited types | Flat keys |

## Operational gotchas

**PostgreSQL:** VACUUM/autovacuum, bloat, connection pooling, replication lag
**Cassandra:** Compaction, JVM GC pauses, hot partitions, tombstone cleanup
**MongoDB:** Working set management, index overhead, eventual consistency edge cases
**Redis:** Persistence strategy, eviction policy, replication complexity
**S3:** Eventually consistent reads (in cross-region), cost at scale, latency for small objects

## Sizing heuristics

- **< 1TB, < 100K QPS** — PostgreSQL (single instance) or MongoDB
- **1–50TB, append-heavy** — Cassandra or ScyllaDB
- **> 50TB, hot subset** — Tiered: Redis cache + Cassandra warm + S3 cold
- **Real-time analytics** — TimescaleDB or ClickHouse (not yet in this wiki)

## Related domains

- [[patterns/index|Patterns]] — data modeling patterns (sharding, replication, CQRS)
- [[caching/index|Caching]] — cache-aside, write-through patterns
- [[queues/index|Queues]] — event sourcing often pairs with append-heavy databases

## Raw sources (external primers)

- [[raw-sources/databases|Database reading list]]

## When to ask for help

If you're deciding on a database but don't see your problem here:
1. Check the **Related** links above
2. Look at `raw-sources/databases` for external primers
3. Create a new page (start from `schema/entry-template.md`) + add to this index
