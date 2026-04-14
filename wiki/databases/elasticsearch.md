---
name: Elasticsearch
type: tool
domain: databases
tags:
  - databases
  - search
  - full-text
  - distributed-systems
aliases:
  - Elastic
  - ES
  - OpenSearch
last_updated: 2026-04-14
sources:
  - https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html
  - https://www.hellointerview.com/learn/system-design/deep-dives/elasticsearch
  - https://www.paradedb.com/blog/elasticsearch-was-never-a-database
---

# Elasticsearch

## What it is / what it optimizes for

Elasticsearch is a distributed search and analytics engine built on Apache Lucene. It optimizes for full-text search, complex aggregations, and near-real-time queries over large volumes of semi-structured (JSON) documents. It is NOT a system of record.

## When it fits

- Full-text search with relevance ranking (product search, log search, document search)
- Aggregations and analytics over large event/log datasets (ELK stack)
- Autocomplete, fuzzy matching, faceted search
- Read-heavy workloads where stale results (seconds) are acceptable
- Supplementary search layer on top of a primary database

## When it doesn't fit

- **Primary database / system of record**: ES has no multi-document transactions; a crash can leave partial writes with no rollback. Use it as a secondary index, not the source of truth.
- **Write-heavy workloads**: Each write triggers Lucene indexing, segment creation, and replica propagation — far heavier than a DB row insert.
- **Strong consistency required**: ES is eventually consistent. Your results *will* be stale. Banking, inventory, anything that can't tolerate drift should look elsewhere.
- **Relational queries / joins**: ES has no native joins. You must denormalize aggressively on the write path.
- **Small datasets (< 100k docs)**: A simple `LIKE` query on Postgres is faster to build and operate. Don't add ES until you hit the wall.
- **Frequent large updates**: Updating a field on millions of documents triggers reindex operations that contend with production traffic.

## Operational gotchas

- **Replica count**: High replica counts amplify writes, increase shard overhead, and fragment JVM heap. More replicas ≠ faster reads beyond a point — audit before scaling.
- **Shard sizing**: Over-sharding is a common mistake. Each shard has overhead; aim for shards in the 10–50 GB range.
- **Drift from primary DB**: Sync failures between your DB and ES are a common, silent bug source. Use a CDC pipeline (Debezium + Kafka) or transactional outbox to keep them consistent.
- **Mapping explosions**: Dynamic mapping can explode index size if JSON fields are unbounded. Use explicit mappings in production.
- **JVM heap pressure**: ES is JVM-based; heap sizing, GC tuning, and off-heap page cache all matter at scale.

## Trade-offs

- **Speed vs. consistency**: Near-real-time indexing means there's always a lag between write and search visibility. Tunable via `refresh_interval` but lower interval = higher I/O cost.
- **Query power vs. operational cost**: ES's DSL is powerful but resource-hungry. Running it as both your search engine and analytics store doubles the optimization burden.
- **Licensing**: The SSPL license (post-7.10) means Elastic features are not fully open source. AWS forked it as OpenSearch; feature parity diverges over time.

## Comparisons

| Compared to | ES tends to win when… | ES tends to lose when… |
|---|---|---|
| [[postgresql\|PostgreSQL]] | You need full-text relevance ranking, fuzzy search, or large-scale aggregations | You need ACID, joins, or strong consistency |
| [[redis\|Redis]] | You need persistent document search with complex query DSL | You need sub-millisecond latency on simple key lookups |
| [[clickhouse\|ClickHouse]] | Your data is unstructured/semi-structured and full-text matters | You need fast columnar analytics on structured time-series data |

## Real-world example

- **Uber, GitHub, Netflix** all use ES as a *secondary* search index synced from a primary store (MySQL/Postgres/Cassandra). The pattern is: write to primary DB → CDC → ES. Never treat ES as the write path.

## Related

- [[postgresql|PostgreSQL]]
- [[redis|Redis]]
- [[clickhouse|ClickHouse]]
- [[log-based-architecture|Log-based Architecture]]

## Sources

- https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html — official reference
- https://www.hellointerview.com/learn/system-design/deep-dives/elasticsearch — system design interview deep dive with production pitfalls
- https://www.paradedb.com/blog/elasticsearch-was-never-a-database — strong case study on why ES fails as a primary store
