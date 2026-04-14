---
name: Queue decisions
type: index
domain: queues
tags:
  - index
  - queues
aliases:
  - Queues guide
  - Event-driven decisions
last_updated: 2026-04-12
sources: []
---

# Queue decisions

**Quick reference:** When do we need a queue? Kafka vs RabbitMQ vs SQS? How do we model with event-driven architecture?

This index links to all queue decisions, trade-offs, and patterns.

## Core technologies

- [[kafka|Apache Kafka]] — distributed event log, high throughput, complex operations
- [[rabbitmq|RabbitMQ]] — message queue, lower throughput, simpler operations
- **Amazon SQS** (not yet documented) — managed queue, simple API, eventual consistency

## When to use a queue

- **Decoupling:** producer writes to queue, consumer processes async
- **Buffering:** burst traffic smoothing
- **Replay:** event log lets you reprocess history
- **Multi-consumer:** same event to many subscribers
- **Ordering:** strict ordering guarantees (per partition in Kafka, per queue in RabbitMQ)

**When NOT to use a queue:** If you need synchronous request/response, consider HTTP + caching instead.

## Kafka vs RabbitMQ

- [[kafka-vs-rabbitmq|Comparison: Kafka vs RabbitMQ]]

| Criterion | Kafka | RabbitMQ |
|-----------|-------|----------|
| **Model** | Distributed event log | Message queue |
| **Throughput** | Very high (millions/sec) | Medium (thousands/sec) |
| **Ordering** | Per-partition | Per-queue |
| **Durability** | Persistent by default | Configurable |
| **Ops complexity** | High (ZooKeeper, brokers) | Medium |
| **Learning curve** | Steep (new abstraction) | Gentler (traditional queues) |
| **Retention** | Long (days/weeks default) | Short (until consumed) |

**Kafka if:** Event sourcing, high throughput, long-term retention, multi-consumer
**RabbitMQ if:** Simple pub/sub, lower throughput, ops simplicity matters more

## Architecture patterns

- [[event-driven-architecture|Event-driven architecture]] — decoupled services via events
- [[log-based-architecture|Log-based architecture]] — using an event log as your source of truth

## Common questions

**Q: Do we need a queue or can we use the database?**
A: Depends on throughput + whether you need ordering. Kafka is great for event sourcing; database can work for low-volume async work.

**Q: How big should our message be?**
A: Kafka: optimize for throughput; keep messages ~1-10KB. RabbitMQ: varies by config.

**Q: What happens if the consumer crashes?**
A: Kafka: offset is checkpointed; restart consumer to resume. RabbitMQ: message stays in queue or is re-queued.

## Case studies

*(No case studies yet; contribute one!)*

## Real-world setups

**Kafka:** Discord (chat replay), Uber (event sourcing), Netflix (data pipeline)
**RabbitMQ:** simpler startups, internal async task queues

## Related domains

- [[databases/index|Databases]] — event sourcing often pairs with append-heavy databases like Cassandra
- [[patterns/index|Patterns]] — outbox pattern, saga pattern (distributed transactions)

## Raw sources (external primers)

*(To be added: Kafka documentation, RabbitMQ best practices)*
