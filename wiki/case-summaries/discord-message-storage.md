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
---

# Discord message storage evolution

## TL;DR
Discord evolved its message storage to handle massive write volume and strict latency targets, moving from MongoDB to Cassandra and later to ScyllaDB. The recurring theme: narrow, partition-key access patterns + high write throughput favor wide-column systems over document databases.

## Timeline / evolution
- Early: MongoDB for fast iteration and document flexibility
- Growth: MongoDB scaling and operational pain → migrate to Cassandra
- Further scale: adopt ScyllaDB (Cassandra-compatible) to improve efficiency and tail latency

## Decision drivers
- Sustained write throughput (messages are an append-heavy workload)
- Predictable reads (fetch recent messages by channel)
- Low p99 latency and high availability
- Operational maintainability as data volume grows

## What worked
- Designing around partition-key access patterns
- Treating storage choices as evolvable: migrate when trade-offs flip
- Investing in migration tooling (dual writes, backfills, verification)

## What hurt
- Migration complexity and long-running backfills
- Data model constraints (denormalization, query-driven schemas)

## Links to wiki pages
- [[when-to-leave-mongodb|When to leave MongoDB]]
- [[cassandra|Cassandra]]
- [[scylladb|ScyllaDB]]

## Sources
- https://discord.com/blog/how-discord-stores-trillions-of-messages — primary narrative and technical rationale
