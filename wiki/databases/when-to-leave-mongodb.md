---
name: When to leave MongoDB
type: decision
domain: databases
tags:
  - databases
  - mongodb
  - scaling
  - migrations
aliases:
  - When to move off MongoDB
  - MongoDB alternatives for write-heavy workloads
last_updated: 2026-04-11
sources:
  - https://discord.com/blog/how-discord-stores-trillions-of-messages
  - https://www.mongodb.com/docs/manual/sharding/
---
# When to leave MongoDB

## TL;DR
If your workload is dominated by high-throughput writes and predictable reads by a clear partition key—and you’re fighting shard balancing, hotspots, or tail-latency cliffs—consider migrating to a wide-column store like [[cassandra|Cassandra]] or [[scylladb|ScyllaDB]]. If you still benefit from document flexibility and varied query patterns, invest in MongoDB tuning and sharding first.

## Problem
MongoDB is a strong general-purpose document database, but it makes different trade-offs than wide-column / key-value systems. This page helps decide when MongoDB’s scaling/operational costs outweigh its product benefits for your workload.

## Context
- Workload shape: append-heavy events/messages, time-ordered reads, large datasets, predictable access by tenant/channel/user.
- Constraints: low p99 latency, horizontal scale, high availability.
- Non-goals: blanket “MongoDB is bad” statements; this is workload-dependent.

## Decision drivers
- Tail latency under sustained write load
- Hot partitions and shard-key sensitivity
- Operational complexity (resharding, balancing, backups/restore)
- Query needs: ad-hoc queries vs key-based access patterns
- Cost profile: index overhead, storage amplification, operational labor

## Options
| Option | When it fits | Trade-offs | Operational notes |
| --- | --- | --- | --- |
| Stay on MongoDB (single cluster) | Moderate scale; rich query needs; team expertise | Can hit ceilings on write throughput / p99 latency | Profile queries, trim indexes, watch working set |
| MongoDB sharding | Need horizontal scale but still want documents | Shard-key design becomes critical; balancing/resharding costs | Plan shard keys early; load-test hotspots |
| Wide-column store ([[cassandra|Cassandra]] / [[scylladb|ScyllaDB]]) | Write-heavy, key-based access, predictable queries | Data model changes; fewer ad-hoc queries | Invest in data modeling, repairs, monitoring |
| Managed KV/document alternative | Want to offload ops; simpler scaling | Vendor constraints and cost; feature differences | Validate semantics + throughput/latency |

## Recommendation
- Prefer MongoDB when you need document flexibility + varied query patterns.
- Prefer Cassandra/ScyllaDB when you have narrow access patterns and need predictable high write throughput at scale.

## Consequences
- Positive: better p99 latency and more linear horizontal scale for key-based workloads (when modeled well).
- Negative: migration complexity (dual-writes, backfills, verification) and reduced query flexibility.
- Follow-ups / mitigations: define partition keys early, benchmark realistic schemas, design idempotent write paths.

## Related
- [[discord-message-storage|Discord: message storage evolution]]
- [[cassandra|Cassandra]]
- [[scylladb|ScyllaDB]]
- [[wiki-home|Wiki home]]

## Further reading
- [[general-case-studies|General system design case studies (raw sources)]]
- [[raw-sources-home|Raw sources home]]

## Sources
- https://discord.com/blog/how-discord-stores-trillions-of-messages — real migration story (MongoDB → Cassandra → ScyllaDB)
- https://www.mongodb.com/docs/manual/sharding/ — sharding mechanics and shard-key trade-offs
