---
name: Tag registry
type: schema
domain: schema
aliases: []
last_updated: 2026-04-11
sources: []
---

# Tag registry

## How to use
Tags live in a page’s frontmatter under `tags:` and must match one of the canonical tags in the table below *exactly* (case- and hyphen-sensitive). Use one canonical form per concept (e.g., prefer `mongodb` over `mongo`), and for technologies prefer the official entity slug (typically the filename under `wiki/…/`). If you need a new tag, add it here first (with category + meaning) before using it in any page.

## Tag table

| Tag | Category | Meaning / when to use |
| --- | --- | --- |
| caching | domain | Anything primarily about caching strategies, cache layers, and cache trade-offs. |
| case-summary | page-type | Page summarizes a real-world system/decision (a company case study). |
| containerization | technology | Docker and container-related content (isolation, images, registries). |
| conventions | meta | Repo writing/schema conventions and guidance pages. |
| databases | domain | Anything primarily about databases, storage engines, or data persistence trade-offs. |
| deployment | domain | Infrastructure deployment, release strategies, and operational patterns. |
| discord | meta | Case-study subject: Discord (use on Discord-related pages). |
| distributed-systems | domain | Topics involving multi-node coordination, replication, partitions, consistency, etc. |
| index | page-type | Map-of-content / navigation pages (pointers rather than deep content). |
| in-memory | concept | In-memory storage and data structures; use for ephemeral and RAM-based systems. |
| message-storage | concept | Storage and retrieval of message/event streams (append-heavy, time-ordered reads). |
| migrations | concept | Data migrations, cutovers, dual-writes, backfills, and verification patterns. |
| ml | domain | Machine learning systems design case studies and supporting material. |
| memcached | technology | Memcached-specific content (tool page, decision triggers, or case evidence involving Memcached). |
| mongodb | technology | MongoDB-specific content (tool page, decision triggers, or case evidence involving MongoDB). |
| notion | meta | Case-study subject: Notion (use on Notion-related pages). |
| performance | concept | Tail latency, throughput, bottlenecks, and performance engineering trade-offs. |
| postgres | technology | Legacy shorthand tag for PostgreSQL seen in existing pages. Prefer `postgresql` for new content. |
| postgresql | technology | PostgreSQL (official technology/entity slug). Use for Postgres-specific content. |
| raw-sources | page-type | Curated reading lists / pointers to external sources (minimal original writing). |
| redis | technology | Redis-specific content (tool page, decision triggers, or case evidence involving Redis). |
| redis-caching | concept | Redis as a caching layer; redis-specific caching strategies. |
| relational | concept | Relational data modeling and SQL-centric trade-offs (constraints, joins, transactions). |
| scaling | concept | Horizontal/vertical scaling triggers, capacity limits, sharding, and hotspots. |
| schema | page-type | Schema/template/spec pages that define how to write and structure wiki content. |
| sharding | concept | Horizontal partitioning across shards: routing, rebalancing, and cross-shard constraints. |
| sql | concept | SQL query semantics and trade-offs; use when SQL is central to the topic. |
| systems-design | domain | General system design / architecture material not limited to a single domain. |
| wide-column | technology | Wide-column / Bigtable-family data model (e.g., Cassandra/Scylla) and its trade-offs. |
| wiki | meta | Wiki-level organisation/navigation (used on wiki home / index pages). |
| amqp | technology | AMQP (Advanced Message Queueing Protocol) message broker implementations and trade-offs. |
| analytics | domain | Analytics, data warehousing, and analytical query patterns. |
| append-only | concept | Append-only data structures and immutable logs; no updates or deletes. |
| append-only-log | concept | Append-only log / event log; ordered stream of immutable records. |
| async | concept | Asynchronous processing and async/await patterns. |
| cdc | concept | Change Data Capture; streaming database changes to downstream systems. |
| consumer-groups | concept | Consumer groups in pub-sub / message brokers; coordinated consumption from topics/streams. |
| cqrs | pattern | Command Query Responsibility Segregation; separate read and write models. |
| databricks | technology | Databricks platform and Spark-based data processing. |
| data-lake | technology | Data lake architecture; centralized repository for raw data. |
| data-structures | domain | Data structures and their complexity/performance trade-offs. |
| decision | page-type | Page that helps choose between options (decision trees, ADRs). |
| decoupling | concept | Decoupling; reducing dependencies between components/services. |
| derived-views | concept | Derived views / materialized views; pre-computed aggregations. |
| docker | technology | Docker containers and containerization technology. |
| eda | pattern | Event-Driven Architecture (EDA); systems built around events. |
| erlang | technology | Erlang language and runtime; used in RabbitMQ and telecom systems. |
| event-driven | domain | Event-driven systems and architecture; events as primary unit. |
| event-sourcing | pattern | Event Sourcing; store system state as append-only log of events. |
| event-streaming | concept | Event streaming platforms (Kafka, Pulsar) and their trade-offs. |
| eventual-consistency | concept | Eventual consistency; replicas converge over time but may be temporarily inconsistent. |
| eviction | concept | Cache eviction policies (LRU, LFU, TTL) and memory management. |
| faas | domain | Functions as a Service (FaaS); serverless function platforms. |
| global-infrastructure | concept | Global infrastructure; multi-region deployment and latency trade-offs. |
| iceberg | technology | Apache Iceberg; table format for large-scale analytics. |
| idempotency | concept | Idempotent operations; safe to retry without side effects. |
| immutable-log | concept | Immutable log; append-only, never-modified records (like event sourcing). |
| infrastructure | domain | Infrastructure and ops-related decisions (deployment, orchestration, scaling). |
| kafka | technology | Apache Kafka; distributed event streaming platform. |
| kubernetes | technology | Kubernetes (K8s); container orchestration platform. |
| log | concept | Logs, log aggregation, and structured logging. |
| master-replica | concept | Master-replica replication topology and consistency models. |
| message-broker | technology | Message brokers (RabbitMQ, Kafka, NATS); decouple producers/consumers. |
| multi-tier | concept | Multi-tier / N-tier architecture; layered application design. |
| netflix | meta | Case-study subject: Netflix (use on Netflix-related pages). |
| object-storage | technology | Object storage (S3, GCS, Azure Blob); simple key-value file storage. |
| observability | domain | Observability (monitoring, logging, tracing, metrics). |
| orchestration | domain | Orchestration; automating deployment and management workflows. |
| partitions | concept | Partitioning / sharding; dividing data across nodes. |
| patterns | domain | Architectural patterns and design patterns. |
| persistence | concept | Persistence; durable storage and write-ahead logs. |
| pub-sub | pattern | Publish-Subscribe; loose coupling via topics/channels. |
| queues | domain | Message queues and task queues. |
| rabbitmq | technology | RabbitMQ; AMQP message broker with routing and reliability. |
| release-strategy | concept | Release strategies (blue-green, canary, rolling, feature flags). |
| reliability | domain | Reliability; fault tolerance, resilience, and disaster recovery. |
| routing | concept | Message routing and traffic routing logic. |
| s3 | technology | Amazon S3 (Simple Storage Service); object storage and data lake patterns. |
| serverless | domain | Serverless platforms (Lambda, Cloud Functions) and FaaS. |
| snowflake | technology | Snowflake; cloud data warehouse. |
| task-queue | technology | Task queues (Celery, RQ); background job processing. |
| throughput | concept | Throughput; requests/events per second and performance scaling. |
| trade-offs | concept | Trade-offs; competing objectives and design decisions. |
| uber | meta | Case-study subject: Uber (use on Uber-related pages). |
