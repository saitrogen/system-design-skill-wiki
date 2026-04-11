---
name: MongoDB
type: tool
domain: databases
tags:
  - databases
  - mongodb
  - scaling
  - migrations
aliases:
  - When to leave MongoDB
  - When to move off MongoDB
  - MongoDB alternatives for write-heavy workloads
last_updated: 2026-04-11
sources:
  - https://discord.com/blog/how-discord-stores-trillions-of-messages
  - https://discord.com/blog/how-discord-stores-billions-of-messages
  - https://www.mongodb.com/docs/manual/sharding/
---
# MongoDB

## What it is / what it optimizes for
- General-purpose document database optimized for flexible schemas, secondary indexes, and varied query patterns.
- Strong default choice when product iteration speed and query expressiveness matter as much as raw write scalability.

## When it fits
- You need document flexibility (schema evolves) and/or multiple query shapes over the same dataset.
- Your working set fits comfortably in memory (or you can cache effectively) so reads avoid random disk I/O.
- You can choose and enforce a shard key that matches your dominant access pattern (if/when you shard).

## When it doesn’t fit
- You have an append-heavy workload with narrow, key-based queries where predictably low tail latency matters more than query flexibility.
- Concrete trigger (Discord): once the dataset + indexes stopped fitting in RAM, their message-read workload turned into many random disk seeks and latency became unpredictable — a practical “p99 cliff” signal for leaving a single-cluster MongoDB setup.

## Decision: when to leave MongoDB

### TL;DR
If your workload is dominated by high-throughput writes and predictable reads by a clear partition key—and you’re fighting shard balancing, hotspots, or tail-latency cliffs—consider migrating to a wide-column store like [[cassandra|Cassandra]] or [[scylladb|ScyllaDB]]. If you still benefit from document flexibility and varied query patterns, invest in MongoDB tuning and sharding first.

### Problem
MongoDB is a strong general-purpose document database, but it makes different trade-offs than wide-column / key-value systems. This page helps decide when MongoDB’s scaling/operational costs outweigh its product benefits for your workload.

### Context
- Workload shape: append-heavy events/messages, time-ordered reads, large datasets, predictable access by tenant/channel/user.
- Constraints: low p99 latency, horizontal scale, high availability.
- Non-goals: blanket “MongoDB is bad” statements; this is workload-dependent.

### Decision drivers
- Tail latency under sustained write load
- Hot partitions and shard-key sensitivity
- Operational complexity (resharding, balancing, backups/restore)
- Query needs: ad-hoc queries vs key-based access patterns
- Cost profile: index overhead, storage amplification, operational labor
- Working set no longer fits in RAM → reads degrade into random disk I/O → tail latency becomes unstable (Discord hit this around ~100M stored messages).

### Options
| Option | When it fits | Trade-offs | Operational notes |
| --- | --- | --- | --- |
| Stay on MongoDB (single cluster) | Moderate scale; rich query needs; team expertise | Can hit ceilings on write throughput / p99 latency | Profile queries, trim indexes, watch working set |
| MongoDB sharding | Need horizontal scale but still want documents | Shard-key design becomes critical; balancing/resharding costs | Plan shard keys early; load-test hotspots |
| Wide-column store ([[cassandra|Cassandra]] / [[scylladb|ScyllaDB]]) | Write-heavy, key-based access, predictable queries | Data model changes; fewer ad-hoc queries | Invest in data modeling, repairs, monitoring |
| Managed KV/document alternative | Want to offload ops; simpler scaling | Vendor constraints and cost; feature differences | Validate semantics + throughput/latency |

### Recommendation
- Prefer MongoDB when you need document flexibility + varied query patterns.
- Prefer Cassandra/ScyllaDB when you have narrow access patterns and need predictable high write throughput at scale.

### Consequences
- Positive: better p99 latency and more linear horizontal scale for key-based workloads (when modeled well).
- Negative: migration complexity (dual-writes, backfills, verification) and reduced query flexibility.
- Follow-ups / mitigations: define partition keys early, benchmark realistic schemas, design idempotent write paths.

## Real-world example
- Discord: [[discord-message-storage|Discord: message storage evolution]] (trigger: dataset + index stopped fitting in RAM → unpredictable latency from random reads).

## Related
- [[discord-message-storage|Discord: message storage evolution]]
- [[cassandra|Cassandra]]
- [[scylladb|ScyllaDB]]
- [[wiki-home|Wiki home]]

## Further reading
- [[general-case-studies|General system design case studies (raw sources)]]
- [[raw-sources-home|Raw sources home]]

## Sources
- https://discord.com/blog/how-discord-stores-trillions-of-messages — Cassandra → ScyllaDB migration, and why Cassandra became too high-toil at extreme scale
- https://discord.com/blog/how-discord-stores-billions-of-messages — MongoDB scaling trigger (dataset + index no longer fit RAM) and the Cassandra bucketed partition-key model
- https://www.mongodb.com/docs/manual/sharding/ — sharding mechanics and shard-key trade-offs
