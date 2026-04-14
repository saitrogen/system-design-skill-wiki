---
name: Discord message storage evolution
type: case-summary
domain: case-summaries
tags:
  - case-summary
  - discord
  - databases
  - message-storage
aliases:
  - How Discord stores trillions of messages
  - Discord message storage
last_updated: 2026-04-11
sources:
  - https://discord.com/blog/how-discord-stores-trillions-of-messages
  - https://discord.com/blog/how-discord-stores-billions-of-messages
---
# Discord message storage evolution

## TL;DR
Discord evolved its message storage to handle an append-heavy workload with strict tail-latency goals: MongoDB → Cassandra → ScyllaDB. The recurring theme: for “fetch messages by channel” style access patterns, wide-column stores win when the dataset grows beyond memory and random I/O dominates.

## Timeline / evolution
- Early (2015): MongoDB for fast iteration and document flexibility.
- Trigger to leave MongoDB (≈ late 2015): once the messages dataset + compound index no longer fit in RAM, read latency became unpredictable (random disk seeks + cache churn).
- Cassandra era (2016–2022): moved to a query-driven wide-column model for predictable reads by partition key.
  - 2017 snapshot: ~12 Cassandra nodes storing billions of messages.
  - 2022 snapshot: 177 Cassandra nodes storing trillions of messages; high operational toil and unstable tail latency.
- ScyllaDB cutover (May 2022): migrated the messages cluster to ScyllaDB (Cassandra-compatible) and reduced operational pain + improved tail latency.

Scale / performance signals (from Discord’s public writeups):

| Signal | What it looked like | Why it mattered |
| --- | --- | --- |
| MongoDB working set > RAM | dataset + index couldn’t stay resident → unpredictable latency | random reads turned into random disk I/O and a tail-latency cliff |
| Cassandra “high-toil” ops | compaction backlog + JVM GC pauses; latency spikes and frequent paging | operational effort scaled with data size instead of staying flat |
| Post-Scylla efficiency | 177 Cassandra nodes → 72 ScyllaDB nodes (larger disks per node) | per-node efficiency and tail latency improved materially |
| Tail latency delta | historical fetch p99: ~40–125ms → ~15ms; inserts p99: ~5–70ms → ~5ms | product features become feasible when p99 stabilizes |

## Decision drivers
- Sustained write throughput (messages are append-heavy; Discord reported being well past ~120M messages/day by 2017).
- Predictable reads dominate: “load recent messages for channel X” and “jump to message” style queries.
- Low tail latency targets (Discord called out monitoring on API p95; storage p99s were a practical limiter).
- Operational maintainability: avoid a world where keeping latency stable requires constant “babysitting” as the dataset grows.

## What worked
- Query-driven modeling: design the primary key around the dominant query (by channel, ordered by time).
- Data model (Cassandra / ScyllaDB): `((channel_id, bucket), message_id)`
  - `channel_id`: the grouping Discord reads by.
  - `message_id`: Snowflake IDs are chronologically sortable → clustering key for range scans.
  - `bucket`: fixed time window to bound partition size.
  - Why `bucket`: channels can live for years; without bucketing, partitions grow without bound. Discord picked a window (~10 days) that kept even their largest channels under ~100MB/partition to reduce compaction/GC pain.
- Partition-size control via time buckets to keep compaction/GC cost bounded.
- Migration discipline (Cassandra → ScyllaDB):
  - Dual-write new data to Cassandra + ScyllaDB.
  - Backfill historical data by scanning token ranges and checkpointing progress.
  - Validate correctness by shadow-reading: send a small % of reads to both systems and compare results.
- Upstream shielding: data services added request coalescing and consistent-hash routing by `channel_id` to reduce concurrent pressure on hot partitions.

## What hurt
- MongoDB failure mode was abrupt: once the working set exceeded memory, the random-read pattern made tail latency unstable.
- Cassandra operational pain at extreme scale:
  - Hot partitions could cascade latency across the cluster (quorum reads/writes amplify the blast radius).
  - Falling behind on compactions increased read cost and caused further latency spikes.
  - JVM garbage collection pauses were a recurring tail-latency and on-call problem (including situations bad enough to require manual intervention).
- Backfill corner cases: their ScyllaDB migrator stalled at 99.9999% because the remaining token ranges contained huge tombstone runs that hadn’t been compacted away in Cassandra.

## Links to wiki pages
- [[mongodb|MongoDB]]
- [[cassandra|Cassandra]]
- [[scylladb|ScyllaDB]]

## Sources
- https://discord.com/blog/how-discord-stores-trillions-of-messages — primary narrative and technical rationale
