---
name: General system design case studies (raw sources)
type: raw-sources
domain: raw-sources
tags:
	- raw-sources
	- systems-design
aliases:
	- General case studies
	- System design case studies
last_updated: 2026-04-11
sources:
	- https://aws.amazon.com/builders-library/
	- https://dropbox.tech/
	- https://www.allthingsdistributed.com/2007/10/amazons_dynamo.html
---

# General system design case studies (raw sources)

Curated pointers to system design trade-off write-ups (databases, storage, messaging, reliability). Prefer primary engineering posts/papers. No large copy/paste.

| Area | System / decision | Primary link | Why it’s useful (one line) |
| --- | --- | --- | --- |
| Storage | Discord message storage evolution | [How Discord Stores Trillions of Messages](https://discord.com/blog/how-discord-stores-trillions-of-messages) | Concrete “MongoDB → Cassandra → ScyllaDB” migration story. |
| Databases | Dynamo (Amazon) | [Amazon’s Dynamo](https://www.allthingsdistributed.com/2007/10/amazons_dynamo.html) | Classic trade-offs for highly available key-value storage. |
| Databases | Bigtable (Google) | [Bigtable: A Distributed Storage System for Structured Data](https://research.google/pubs/bigtable-a-distributed-storage-system-for-structured-data/) | Tablet-based storage design and operational lessons. |
| Databases | Spanner (Google) | [Spanner: Google’s Globally Distributed Database](https://research.google/pubs/spanner-googles-globally-distributed-database/) | Global consistency via TrueTime; latency/consistency trade-offs. |
| Storage | Magic Pocket (Dropbox) | [Magic Pocket: Infrastructure](https://dropbox.tech/infrastructure/magic-pocket-infrastructure) | Designing custom storage to reduce cost at large scale. |
| Observability | Log as unifying abstraction (LinkedIn) | [The Log: What every software engineer should know about real-time data’s unifying abstraction](https://www.linkedin.com/blog/engineering/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying) | Why logs become a backbone abstraction (and what breaks). |
| Databases | MongoDB sharding | [Sharding](https://www.mongodb.com/docs/manual/sharding/) | Trade-offs: shard keys, balancing, hotspots, ops overhead. |
| Messaging | Kafka design | [Kafka Design](https://kafka.apache.org/documentation/#design) | Ordering, partitions, durability, consumer groups at a glance. |
| Reliability | Amazon Builders’ Library | [Amazon Builders’ Library](https://aws.amazon.com/builders-library/) | Many practical essays on timeouts, retries, caching, overload. |
| Coordination | Raft consensus | [In Search of an Understandable Consensus Algorithm (Raft)](https://raft.github.io/raft.pdf) | Consensus trade-offs explained with an implementer’s lens. |