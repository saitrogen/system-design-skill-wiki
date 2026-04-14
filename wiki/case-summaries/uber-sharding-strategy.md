---
name: Uber sharding strategy
type: case-summary
domain: case-summaries
tags:
  - case-summary
  - uber
  - databases
  - sharding
  - scaling
aliases:
  - Uber database sharding
  - Schemaless sharding at Uber
last_updated: 2026-04-14
sources:
  - https://www.uber.com/blog/schemaless-sql-nosql-hybrid-abstraction-layer/
  - https://www.uber.com/blog/mysql-sharding-at-uber-scale/
  - https://www.uber.com/blog/uber-engineering-scaling-postgres/
---

# Uber sharding strategy

## TL;DR
Uber scaled MySQL horizontally by building Schemaless, a sharding abstraction layer that handles partitioning, rebalancing, and failover transparently. The key insight: let the database remain simple (single-master MySQL), but add a smart routing and sharding layer above it that hides distribution complexity from application code.

## Timeline / evolution

- Early Uber (2010–2012): Monolithic MySQL database with replication.
- Inflection point (≈ 2012–2013): Rapid growth (millions of rides, real-time queries) pushed single database to its limits.
  - Query latency began degrading (hot tables, full table scans, lock contention).
  - Vertical scaling hit cost and complexity limits.
- 2013–2015: Developed and deployed Schemaless.
  - Open-sourced initial concepts and designs.
  - Migrated core tables (riders, drivers, trips) to sharded architecture.
- 2015+: Schemaless became standard for all new services; handled petabytes of data across hundreds of MySQL instances.

Scale / performance signals:

| Signal | What it looked like | Why it mattered |
| --- | --- | --- |
| Single DB lock contention | Hot tables (e.g., driver locations) caused write stalls | Real-time matching algorithms couldn't tolerate latency jitter |
| Query latency tail | p99 latencies climbed as the working set exceeded RAM | Worse user experience (longer match times, app timeouts) |
| Replication lag | Master write rate exceeded replica catch-up speed | Inconsistent reads could lead to edge-case bugs |
| Cost inefficiency | Vertical scaling pushed CPU/RAM into diminishing returns | Every 10% capacity increase required new hardware + months of planning |

## Decision drivers

- Handle millions of concurrent rides and real-time location updates without sacrificing latency.
- Avoid application-level reshuffling every time a node failed or capacity needed rebalancing.
- Preserve SQL semantics where possible (transactions, ACID guarantees within a shard).
- Enable gradual migration: switch tables to sharding incrementally without full rewrites.
- Future-proof for multi-region expansion (routing and rebalancing across zones).

## What worked

- **Schemaless abstraction:** Application code writes to a logical table; a sharding layer intercepts, hashes the shard key, and routes to the physical database.
- **Consistent hashing for shard placement:** Reduced the number of keys that need remapping when a shard rebalances.
  - Used a hash ring with virtual nodes (replicas of each physical shard on the ring) to smooth uneven distribution.
- **Shard key design:**
  - For rides: shard by `rider_id` or `driver_id` (keeps related data co-located).
  - For location services: shard by `driver_id` to co-locate driver state and updates.
  - Chose keys aligned with the dominant access pattern (e.g., "fetch all active trips for driver X").
- **Rebalancing without downtime:**
  - Dual-write to old and new shard during rebalance.
  - Background scan of old shard to catch stragglers (eventual consistency during migration).
  - Validation layer to cross-check results between old and new shards.
- **Failover resilience:**
  - Each shard replicated across multiple MySQL instances (primary + replicas).
  - Schemaless layer detected replica lag and failed over to other replicas if primary became unavailable.

## What hurt

- **Shard key immutability:** Once a row was assigned to a shard (based on the key), moving it required explicit migration logic.
  - Consequence: Choosing the wrong key early was expensive; teams had to plan shard keys carefully upfront.
- **Cross-shard transactions:** Transactions spanning multiple shards required distributed 2PC, which was slow and fragile.
  - Led to application-level "shard-local transaction" patterns that were easy to get wrong.
- **Operational complexity at scale:**
  - Rebalancing large shards (terabytes of data) was slow and risky; required months of planning.
  - Monitoring latency and correctness across hundreds of shards required new observability tooling.
  - Shard hotspots: if driver location updates concentrated on a few shards (e.g., during surge pricing in one city), those shards could still become bottlenecks.
- **Query flexibility tradeoffs:**
  - Queries had to be shard-local or involve expensive aggregations across all shards.
  - Full-table scans, range queries across all shards, and complex joins became prohibitively expensive.

## Links to wiki pages

- [[postgresql|PostgreSQL]] — similar scaling approach applies to Postgres sharding
- [[databases/index|Database decisions]]
- [[patterns/index|Architecture patterns (sharding)]]
- [[notion-postgres-sharding|Notion case study]] — complementary PostgreSQL sharding approach

## Sources

- https://www.uber.com/blog/schemaless-sql-nosql-hybrid-abstraction-layer/ — original Schemaless design rationale
- https://www.uber.com/blog/mysql-sharding-at-uber-scale/ — sharding layer architecture and operational lessons
- https://www.uber.com/blog/uber-engineering-scaling-postgres/ — later Postgres adoption and distributed transaction patterns
