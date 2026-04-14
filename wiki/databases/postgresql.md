---
name: PostgreSQL
type: tool
domain: databases
tags:
  - databases
  - postgresql
  - relational
  - sql
aliases:
  - Postgres
last_updated: 2026-04-11
sources:
  - https://www.postgresql.org/docs/current/intro-whatis.html
  - https://www.postgresql.org/docs/current/routine-vacuuming.html
  - https://www.notion.so/blog/sharding-postgres-at-notion
---

# PostgreSQL

## What it is / what it optimizes for
- General-purpose relational database (SQL + ACID transactions) with a strong ecosystem of indexes, extensions, and operational tooling.
- Optimizes for correctness, rich queryability (joins/aggregations), and predictable OLTP behavior on a single primary with replicas.

## When it fits
- You want strong transactional semantics and relational integrity (constraints, joins, multi-row transactions).
- Your access patterns benefit from SQL expressiveness more than “single-partition key” storage models.
- Your dataset and write rate can be handled by a single primary (possibly very large), with read scaling via replicas and caching.

## When NOT to use
- You are hitting the vertical scaling wall:
  - Practical trigger: background maintenance (VACUUM/autovacuum) starts stalling, bloat grows, and “simple migrations” become risky even before you max out instance size.
- You need horizontal scale but can’t afford sharding complexity:
  - Sharding pushes routing, consistency boundaries, and cross-shard joins/transactions into the application.
  - It creates architectural path dependence: partition keys become hard-to-change product constraints.
- You need a database model that naturally matches “key-based, append-heavy, predictable queries” at extreme write scale; a wide-column store may fit better (see [[cassandra|Cassandra]]).

## Operational gotchas
- VACUUM/autovacuum and bloat are core operational concerns; performance can degrade if maintenance can’t keep up.
- Connection management matters (pooling, long-running transactions) because it affects vacuum, lock contention, and tail latency.
- Schema migrations at scale can become an availability risk; plan for online migrations and “dangerous operations” tooling.
- Replication lag and failover are part of the correctness story: define what “acceptable stale reads” means.

## Trade-offs

| Trade-off | Benefit | Cost / failure mode | Typical mitigations |
| --- | --- | --- | --- |
| Rich SQL + constraints | Fast feature iteration, fewer app-layer invariants | Hard to split data later without losing guarantees | Model boundaries early; keep “shard key” candidates explicit |
| Single-primary write path | Simple correctness model | Vertical scaling wall; noisy maintenance can dominate | Tune maintenance; partitioning; write batching; hardware headroom |
| Background maintenance (VACUUM) | MVCC enables high concurrency | Stalls/bloat → latency cliffs; operational firefighting | Autovacuum tuning, query hygiene, avoid long txns, partitioning |
| Sharding for scale | Horizontal capacity and isolation | App complexity; cross-shard constraints; painful rebalancing | Choose a stable partition key; tooling for routing, backfill, verification |

## Real-world examples
- Notion: [[notion-postgres-sharding|Notion Postgres sharding]] (trigger: VACUUM/autovacuum stalling + TXID wraparound risk; outcome: application-sharded Postgres fleet).

## Related
- [[mongodb|MongoDB]]
- [[cassandra|Cassandra]]

## Sources
- https://www.postgresql.org/docs/current/intro-whatis.html — Postgres overview and design intent
- https://www.postgresql.org/docs/current/routine-vacuuming.html — VACUUM/autovacuum behavior and why it matters operationally
- https://www.notion.so/blog/sharding-postgres-at-notion — real-world sharding trigger and migration approach
