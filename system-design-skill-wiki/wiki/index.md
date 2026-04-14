---
name: Wiki index
type: index
domain: wiki
tags:
  - index
  - wiki
aliases:
  - System Design Wiki
  - Architecture decisions
last_updated: 2026-04-15
sources: []
---

# System Design Wiki Index

Complete map of all wiki pages organized by domain.

## Domains

### [[databases/index|📊 Databases]]
PostgreSQL, MongoDB, Cassandra, ScyllaDB, Redis, S3

- [[databases/postgresql|PostgreSQL]] — Relational, ACID, scale limits
- [[databases/mongodb|MongoDB]] — Flexible documents, when to leave
- [[databases/cassandra|Cassandra]] — Wide-column, append-heavy scale
- [[databases/scylladb|ScyllaDB]] — Cassandra-compatible, lower toil
- [[databases/redis|Redis]] — In-memory cache + data structures
- [[databases/s3|S3]] — Object storage for large-scale data
- [[databases/when-to-leave-mongodb|Decision: When to leave MongoDB]]
- [[databases/when-to-shard-postgres|Decision: When to shard PostgreSQL]]

### [[queues/index|🔄 Queues & Events]]
Kafka, RabbitMQ, event streaming patterns

- [[queues/kafka|Kafka]] — Append-only log, event streaming
- [[queues/rabbitmq|RabbitMQ]] — Message broker, task queues
- [[queues/kafka-vs-rabbitmq|Decision: Kafka vs RabbitMQ]]

### [[patterns/index|🏗️ Patterns]]
Architectural patterns and techniques

- [[patterns/event-driven-architecture|Event-driven architecture]]
- [[patterns/log-based-architecture|Log-based architecture]]

### [[caching/index|⚡ Caching]]
Cache strategies and implementations

- [[caching/redis-caching-strategies|Redis caching strategies]]
- [[caching/memcached|Memcached]]
- [[caching/distributed-caching-strategies|Distributed caching strategies]]

### [[deployment/index|🚀 Deployment]]
Container, orchestration, and release strategies

- [[deployment/containers|Containers]]
- [[deployment/kubernetes|Kubernetes]]
- [[deployment/serverless|Serverless]]
- [[deployment/deployment-patterns|Deployment patterns]]

### [[case-summaries/index|📚 Case Studies]]
Real production decisions from companies at scale

- [[case-summaries/discord-message-storage|Discord: Message storage evolution]]
- [[case-summaries/notion-postgres-sharding|Notion: Postgres sharding]]
- [[case-summaries/uber-sharding-strategy|Uber: Sharding strategy]]
- [[case-summaries/netflix-caching-at-scale|Netflix: Multi-tier caching]]

### [[observability/index|👁️ Observability]]
*(Skeleton - to be populated)*

Monitoring, logging, tracing, alerting patterns.

## Schema & Guidelines

- [[../schema/tool-template|Tool template]]
- [[../schema/decision-template|Decision template]]
- [[../schema/index|Schema index]]
- [[../SKILL.md|SKILL.md - Agent integration]]

## Quick Navigation

**By workload type:**
- Append-heavy (logs, time-series) → [[databases/cassandra|Cassandra]], [[queues/kafka|Kafka]]
- Transactional (OLTP) → [[databases/postgresql|PostgreSQL]], [[databases/mongodb|MongoDB]]
- Cache/session store → [[databases/redis|Redis]], [[caching/memcached|Memcached]]
- Large object storage → [[databases/s3|S3]]

**By scale trigger:**
- Hitting database limits? → [[databases/when-to-shard-postgres|Shard Postgres]] or [[databases/when-to-leave-mongodb|Leave MongoDB]]
- Need async processing? → [[queues/kafka|Kafka]] or [[queues/rabbitmq|RabbitMQ]]
- Cache misses increasing? → [[caching/redis-caching-strategies|Redis strategies]] or [[caching/distributed-caching-strategies|Distributed cache]]

**By risk tolerance:**
- High-risk/experimentation → Start simple (PostgreSQL, no caching)
- Critical production → Add caching ([[caching/redis-caching-strategies|Redis]]), observability
- Massive scale → Kafka, Cassandra, multi-tier caching ([[case-summaries/netflix-caching-at-scale|Netflix example]])

## Contributing

See `../CONTRIBUTING.md` for guidelines on adding new pages.
