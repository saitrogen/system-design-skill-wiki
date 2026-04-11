---
name: Cassandra
type: tool
domain: databases
tags:
  - databases
  - wide-column
  - distributed-systems
aliases:
  - Apache Cassandra
last_updated: 2026-04-11
sources:
  - https://cassandra.apache.org/doc/latest/
  - https://www.cs.cornell.edu/projects/ladis2009/papers/lakshman-ladis2009.pdf
  - https://discord.com/blog/how-discord-stores-trillions-of-messages
---
# Cassandra

## What it is / what it optimizes for
Apache Cassandra is a distributed wide-column database designed for high availability and high write throughput. It favors horizontal scalability and operational resilience over rich query capabilities.

## When it fits
- High write throughput with predictable access patterns by partition key
- Large datasets where adding nodes is the main scaling lever
- Multi-datacenter replication with tunable consistency requirements

## When it doesn’t fit
- You need joins, multi-row transactions, or complex ad-hoc querying
- Your access patterns are not well-defined upfront (data modeling is query-driven)
- You expect secondary indexes to behave like an OLTP database
- You can’t afford high operational sensitivity to JVM GC + compaction backlogs (Discord reported GC pause–driven latency spikes and compaction-driven firefighting at large scale)

## Operational gotchas
- Data modeling: schema follows your queries; partition + clustering keys matter
- Hot partitions: uneven key distribution can dominate performance
- Tombstones + compaction: deletes/TTLs can create read amplification
- Repairs: cluster health and consistency depend on repair strategy
- Consistency: “tunable” means you must choose and operate the semantics intentionally

## Trade-offs
- Hidden cost: JVM + GC tuning and pause behavior can become a primary tail-latency driver as clusters scale.
- Maintenance work (compaction/repair) can contend with production traffic; isolating or rate-limiting background work often becomes mandatory.

## Comparisons
| Compared to | Cassandra tends to win when… | Cassandra tends to lose when… |
| --- | --- | --- |
| [[mongodb|MongoDB]] | You have a write-heavy, key-based workload and want linear horizontal scale | You need flexible documents + varied query patterns |
| [[scylladb|ScyllaDB]] | You prefer the Apache ecosystem and mature operational knowledge | You need lower tail latency / better per-node efficiency (often Scylla’s goal) |

## Real-world example
- Discord: [[discord-message-storage|Discord: message storage evolution]] — Cassandra worked well for the data model at huge write volume, but later became high-toil (hot partitions, compaction backlog, GC pause–driven tail-latency spikes).

## Related
- [[scylladb|ScyllaDB]]
- [[discord-message-storage|Discord: message storage evolution]]
- [[mongodb|MongoDB]]

## Sources
- https://cassandra.apache.org/doc/latest/ — official docs (data model, operations)
- https://www.cs.cornell.edu/projects/ladis2009/papers/lakshman-ladis2009.pdf — original Cassandra paper (design goals and architecture)
- https://discord.com/blog/how-discord-stores-trillions-of-messages — real-world usage/migration context
