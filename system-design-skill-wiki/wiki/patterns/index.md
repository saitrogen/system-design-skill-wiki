---
name: Architecture patterns
type: index
domain: patterns
tags:
  - index
  - patterns
  - systems-design
aliases:
  - Patterns guide
  - System design patterns
last_updated: 2026-04-12
sources: []
---

# Architecture patterns

**Quick reference:** Sharding, replication, event sourcing, CQRS, bulkheads, saga pattern — how do distributed systems coordinate?

This index links to all architecture patterns + trade-offs.

## Core patterns

### Data modeling + scaling

- [[log-based-architecture|Log-based architecture]] — event log as source of truth (event sourcing)
- [[event-driven-architecture|Event-driven architecture]] — services communicate via events (decoupled, scalable)

### Cross-cutting patterns *(to be added)*

- **Sharding / partitioning** — horizontal data split + routing
- **Replication** — consistency models (strong, eventual, causal)
- **CQRS** — Command Query Responsibility Segregation
- **Saga pattern** — distributed transactions without strong consistency
- **Bulkhead pattern** — fault isolation (circuit breakers, timeouts)
- **Outbox pattern** — transactional event publishing

## By problem

### "How do we scale horizontally?"

1. Identify your partition key (e.g., user_id, channel_id)
2. Use sharding to distribute data
3. Handle cross-shard queries carefully (joins become hard)
4. See: [[notion-postgres-sharding|Notion case study]]

### "How do we handle failures?"

1. Add redundancy (replication)
2. Choose consistency model:
   - Strong consistency: all replicas updated before ack (slower, safer)
   - Eventual consistency: replicas catch up async (faster, complex)
3. Use bulkheads to isolate failures

### "How do we coordinate multiple services?"

1. Direct sync calls (simple, fragile)
2. Event-driven (async, resilient, harder to debug)
   - Use [[event-driven-architecture|event-driven architecture]]
   - Pair with a queue: [[kafka|Kafka]], [[rabbitmq|RabbitMQ]]
3. Saga pattern (distributed transactions without XA)

## Related domains

- [[databases/index|Databases]] — data modeling, replication strategies
- [[queues/index|Queues]] — event-driven systems use queues as the backbone
- [[deployment/index|Deployment]] — how patterns change across regions / zones

## When to apply

| Pattern | When | Cost |
|---------|------|------|
| **Sharding** | Data > single node | App routing complexity |
| **Replication** | Always (HA) | Consistency complexity |
| **Event sourcing** | Need audit trail + replay | Storage overhead, complexity |
| **CQRS** | Read/write patterns differ greatly | Operational complexity |
| **Saga** | Multi-service transaction | Eventual consistency edge cases |

## Case studies

- [[discord-message-storage|Discord message storage]] — partitioning strategy (partition by channel + time bucket)
- [[notion-postgres-sharding|Notion Postgres sharding]] — scaling a monolithic database horizontally

## Raw sources (external primers)

*(To be added: Martin Fowler patterns, distributed systems papers)*
