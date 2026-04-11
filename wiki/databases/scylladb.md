---
name: ScyllaDB
type: tool
domain: databases
tags:
  - databases
  - wide-column
  - performance
aliases:
  - Scylla
last_updated: 2026-04-11
sources:
  - https://www.scylladb.com/product/technology/shard-per-core-architecture/
  - https://opensource.docs.scylladb.com/
  - https://discord.com/blog/how-discord-stores-trillions-of-messages
---
# ScyllaDB

## What it is / what it optimizes for
ScyllaDB is a Cassandra-compatible wide-column database implemented in C++ with a shard-per-core architecture. It aims for high throughput and low, predictable tail latency on modern multicore machines.

## When it fits
- You want Cassandra’s data model and APIs, but care a lot about p99 latency and efficiency
- Write-heavy workloads where per-node performance matters (fewer nodes for the same load)
- You can model your data around partition-key access patterns

## When it doesn’t fit
- You need rich ad-hoc queries, joins, or multi-row transactions
- Your workload can’t be modeled around partition-key access patterns
- You rely on behavior/features that require strict Cassandra parity (validate compatibility)

## Operational gotchas
- Same modeling constraints as Cassandra: partition keys, clustering, denormalization
- Compaction, tombstones, and repairs still matter; speed doesn’t remove modeling mistakes
- Driver routing/token awareness can materially affect latency at high load
- Benchmark using your real schema and read/write mix; synthetic TPS numbers can mislead

## Comparisons
| Compared to | ScyllaDB tends to win when… | ScyllaDB tends to lose when… |
| --- | --- | --- |
| [[cassandra|Cassandra]] | You need lower tail latency / better per-node efficiency | You prefer the pure Apache stack or have deep Cassandra ops tooling already |
| [[when-to-leave-mongodb|MongoDB]] | Your workload is narrow and needs predictable high write throughput | You need document flexibility + varied queries |

## Real-world example
- Discord: [[discord-message-storage|Discord: message storage evolution]] (MongoDB → Cassandra → ScyllaDB) as a message storage system at extreme write scale.

## Related
- [[cassandra|Cassandra]]
- [[discord-message-storage|Discord: message storage evolution]]
- [[when-to-leave-mongodb|When to leave MongoDB]]

## Sources
- https://www.scylladb.com/product/technology/shard-per-core-architecture/ — shard-per-core + Seastar approach
- https://opensource.docs.scylladb.com/ — official documentation
- https://discord.com/blog/how-discord-stores-trillions-of-messages — real-world migration context
