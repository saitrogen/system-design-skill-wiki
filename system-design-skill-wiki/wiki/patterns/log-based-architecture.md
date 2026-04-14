---
name: "Log-Based Architecture"
type: pattern
domain: patterns
tags:
  - log
  - append-only
  - event-sourcing
  - cdc
  - cqrs
  - kafka
  - immutable-log
  - derived-views
aliases: ["Log as Source of Truth", "Append-Only Log Architecture", "Database Inside Out"]
last_updated: 2026-04
sources:
  - ["Turning the Database Inside-Out with Apache Samza (Martin Kleppmann)", "https://martin.kleppmann.com/2015/03/04/turning-the-database-inside-out.html"]
  - ["Turning the Database Inside Out Again (ZeroCopy/Streambased)", "https://blog.streambased.io/p/turning-the-database-inside-out-again"]
---

## What this is

A pattern treating the **append-only log as the primary source of truth**, with all other representations (databases, search indexes, caches, materialized views) as derived, re-computable views of that log. Articulated by Martin Kleppmann in "Turning the Database Inside Out" (Strange Loop 2014): instead of a mutable shared database at the center with logs as an implementation detail, flip the model — make the log the public interface and treat databases as materialized views.

The key insight: every database already does this internally (write-ahead log, replication stream). Log-based architecture makes the log the explicit, consumer-facing interface.

## When to use

- **Event replay and time travel**: Re-derive any view of the world at any point in history by replaying the log from offset 0
- **Multiple derived representations**: The same event stream feeds a search index, a cache, a data warehouse, and an operational DB — each optimized for its consumer
- **Change Data Capture (CDC)**: Capture DB mutations as an event stream (Debezium reads Postgres WAL → Kafka topics) without modifying the application
- **CQRS**: Separate read and write models; write path appends to log, read path consumes log to build query-optimized materialized views
- **Audit trail by default**: The log IS the audit trail — no separate audit logging required
- **Schema migration without downtime**: Run old and new consumers in parallel against the same log; migrate consumers independently

## When NOT to use

- **Simple CRUD applications**: If your app is a web form that writes to a database and reads it back, log-based architecture adds enormous complexity for zero benefit
- **Strong read-after-write consistency requirements**: Derived views are eventually consistent with the log. If a user writes and immediately reads, the view may not have caught up.
- **Short-lived data**: Designing retention policies for logs containing PII or regulated data is complex (GDPR right-to-deletion vs. immutable log tension)
- **Small teams without streaming expertise**: The tooling (Kafka, Flink, Debezium, schema registries) has steep operational overhead

## Trade-offs

| Factor | Detail |
|--------|--------|
| Source of truth | Log; all databases are derived and can be rebuilt |
| Consistency | Eventual — consumers lag behind the log |
| Replay | Full history replayable from offset 0 (within retention window) |
| Derived views | Can be added/changed/rebuilt without touching the write path |
| Retention cost | Log storage grows; requires retention policies; Iceberg/S3 for cold log tiers |
| Complexity | High — schema registry, consumer lag monitoring, ordering guarantees per partition |
| GDPR tension | Immutable log conflicts with "right to be forgotten"; requires tombstoning or encryption key deletion |
| Debugging | Easier than mutable state — you can replay exactly what happened |

**Log compaction** (Kafka): Retains only the latest value per key, indefinitely. Enables the log to function as a key-value store where consumers can bootstrap from compacted state without replaying all history.

**The CDC pattern**: Debezium monitors the Postgres WAL → publishes row changes as Kafka events. Downstream consumers (Elasticsearch sync, analytics pipelines, cache invalidation) subscribe independently. The application writes only to Postgres; the log fans out everywhere.

## Pairs well with

- [[kafka]] — canonical log implementation
- [[s3]] — S3/Iceberg as durable, cheap log storage for cold tiers
- [[event-driven-architecture]] — EDA is the application-level pattern; log-based architecture is the infrastructure pattern beneath it
- [[postgresql]] — Postgres WAL as CDC source; Debezium bridges Postgres → Kafka

## Real-world examples

- **LinkedIn** (Apache Samza, origin of this pattern): Activity events as the source of truth; multiple derived views (feed ranking, people you may know, job recommendations) built from the same event stream
- **Confluent/Kafka ecosystem**: Log compaction enables Kafka as a distributed key-value store
- **Snowflake + Databricks**: Both treat S3/object storage as a durable log layer; compute engines query derived table formats (Iceberg/Delta) on top

## Related

- [[kafka]]
- [[event-driven-architecture]]
- [[kafka-vs-rabbitmq]]
- [[s3]]
- [[postgresql]]
