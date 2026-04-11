---
name: Notion Postgres sharding
type: case-summary
domain: case-summaries
tags:
  - case-summary
  - notion
  - databases
  - postgres
  - sharding
aliases:
  - Sharding Postgres at Notion
  - Notion sharding Postgres
last_updated: 2026-04-11
sources:
  - https://www.notion.so/blog/sharding-postgres-at-notion
---

# Notion Postgres sharding

## TL;DR
Notion migrated from a single Postgres “monolith” to an application-sharded fleet to restore performance and reliability under rapid growth. The trigger wasn’t just “bigger tables” — background maintenance (VACUUM/autovacuum) began stalling and the risk of TXID wraparound became a product-level threat.

## Timeline / evolution
- 2015–mid 2020: One Postgres database served the whole product through ~5 years and ~4 orders of magnitude of growth.
- Mid 2020: Operational inflection point.
  - Engineers on-call saw recurring database CPU spikes.
  - Even safe-seeming schema changes (catalog-only migrations) became risky.
  - Autovacuum began stalling consistently, raising TXID wraparound risk (a “stop all writes” failure mode).
- 2021: Sharded fleet cutover.
  - ~5 minutes of scheduled downtime to switch primary writes/reads over.
  - New architecture: application-level sharding across many Postgres databases.

## Scale / performance signals

| Signal | What it looked like | Why it mattered |
| --- | --- | --- |
| Vertical scaling wall | "Resize instance" stopped being a durable strategy | Query performance and maintenance degrade before raw hardware limits; costs and risk grow non-linearly |
| Autovacuum/VACUUM stalling | VACUUM couldn’t reclaim dead tuples reliably | Increases bloat, degrades performance, and escalates existential failure modes |
| TXID wraparound risk | Approaching a state where Postgres would halt writes to protect data | Turns a “DB maintenance” issue into a product outage risk |
| On-call CPU spikes | DB CPU spikes woke engineers | Production instability becomes the day-to-day bottleneck |
| Migrations became unsafe | Even catalog-only migrations felt uncertain | Limits iteration velocity and pushes teams toward risky workarounds |
| Double-write catch-up bottleneck | Switch-over required catch-up to drain outstanding writes | Throughput of replication/catch-up determines downtime and rollout strategy |

## Decision drivers
- Keep the core relational model and SQL, but regain predictable performance and operational headroom.
- Avoid opaque clustering behavior from packaged solutions; prioritize controllability of data distribution.
- Minimize cross-shard operations by choosing a partition key aligned with product usage.
- Design for capacity expansion with low operational effort (add hosts without redesign).

## What worked
- Application-level sharding gave explicit control over routing and data distribution.
- Partition key: workspace ID ("space_id").
  - Blocks and all tables “reachable from blocks” were sharded by workspace to preserve locality and avoid most cross-shard joins.
- Shard layout was engineered for future rebalancing:
  - 480 logical shards spread across 32 physical databases (15 logical shards per physical DB).
  - Choosing a shard count with many factors enables incremental host-count changes while keeping distribution even.
- Migration framework (kept deliberately mechanical):
  - Double-write via an audit log + catch-up script (chosen after logical replication couldn’t keep up with initial snapshot + write volume).
  - Backfill of historical data (ran in ~3 days with a high-CPU instance).
  - Verification via sampling + range checks, plus “dark reads” that compare monolith vs shard results and log discrepancies.
  - Contingency plan: a reverse audit log to support rollback if needed.
- Separation of duties: different engineers implemented migration vs verification logic to reduce correlated mistakes.

## What hurt
- Sharding pressure came late: the monolith was already strained, limiting how much additional migration load they could safely impose.
- Partition-key reality gap:
  - The chosen partition key (workspace ID) was not fully populated in the old schema.
  - Backfilling it in-place would have added too much load, so they had to backfill on-the-fly during catch-up.
- Switch-over complexity:
  - Double-write/catch-up throughput became the downtime limiter.
  - A hot-swap (no downtime) would have required additional catch-up optimization.
- Application complexity increased:
  - Routing logic (workspace → physical DB → logical shard) became a first-class concern.
  - They later regretted not embedding the partition key into a single combined primary key earlier, since they had to pass partition keys throughout the app.
- Ongoing distributed constraints:
  - Cross-shard transactions/joins are still hard; the architecture pushes you toward locality-friendly product and data-model choices.

## Links to wiki pages
- [[postgresql|PostgreSQL]]
- [[wiki-home|Wiki home]]

## Sources
- https://www.notion.so/blog/sharding-postgres-at-notion — primary case study (triggers, sharding scheme, migration approach)
