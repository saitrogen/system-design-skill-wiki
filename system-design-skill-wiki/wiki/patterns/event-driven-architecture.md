---
name: "Event-Driven Architecture"
type: pattern
domain: patterns
tags:
  - event-driven
  - eda
  - async
  - decoupling
  - eventual-consistency
  - pub-sub
  - idempotency
aliases: ["EDA", "Event-Driven Architecture"]
last_updated: 2026-04-11
sources:
  - ["Best Practices for Monitoring Event-Driven Architectures (Datadog)", "https://www.datadoghq.com/blog/monitor-event-driven-architectures/"]
  - ["Event-Driven Architecture: The Hard Parts (Three Dots Labs)", "https://threedots.tech/episode/event-driven-architecture/"]
---

## What this is

A pattern where services communicate by publishing and consuming events rather than making direct synchronous calls. Producers emit events describing what happened (facts); consumers react to those events independently and asynchronously. Neither producer nor consumer knows about the other — they're coupled only through the event schema and the message broker.

## When to use

- **Decoupling services with different scaling requirements**: An order service shouldn't be blocked by a slow inventory service
- **Fan-out to multiple consumers**: A payment event consumed by fraud detection, accounting, notifications, and analytics simultaneously
- **Audit trails and event logs**: Events are immutable facts — natural audit log
- **Async workflows where response time doesn't need to be synchronous**: Order confirmation emails, report generation, data sync
- **Eventual consistency is acceptable**: The state will be consistent, just not immediately

## When NOT to use

- **Synchronous request-response workflows**: If Service A needs the result from Service B to continue, EDA adds complexity without benefit. Use a direct API call.
- **Simple, low-volume workflows**: The overhead of schema management, broker operations, and distributed tracing is not worth it for a monolith processing 100 requests/day.
- **Strict transactional guarantees across services**: Achieving distributed ACID transactions over an event bus requires the outbox pattern and significant care. Don't assume events give you transactions.
- **Teams without observability tooling**: EDA without distributed tracing is a debugging nightmare.

## Trade-offs

| Factor | Detail |
|--------|--------|
| Decoupling | Producers and consumers are independent; either can be deployed, scaled, or failed without the other |
| Scalability | Consumers scale independently based on queue depth |
| Debugging | Hard — a single user action may trigger a chain across 5 services; requires distributed tracing with correlation IDs |
| Eventual consistency | The norm, not the exception; consumers may process events out of order |
| Idempotency | Consumers must handle duplicate delivery (at-least-once is the default in Kafka and RabbitMQ) |
| Event ordering | Guaranteed within a [[kafka]] partition; not globally; ordering assumptions break fan-out patterns |
| Schema coupling | Producer changes to event schema can silently break consumers without a schema registry |
| Testing complexity | Local integration tests require running brokers; end-to-end testing is harder than synchronous |

**The outbox pattern**: To reliably publish an event when a DB record is written, write both in a single transaction to a local "outbox" table, then have a background process publish from the outbox. Prevents "wrote to DB, failed before publishing event" race conditions.

**Idempotency is non-negotiable**: At-least-once delivery means you will receive duplicates. Design every consumer to be idempotent (same event processed twice = same outcome). Use a deduplication key (event ID) stored in [[redis]] or a DB.

## Pairs well with

- [[kafka]] as the high-throughput, durable event backbone
- [[rabbitmq]] for task-queue style event consumption with per-message ACK
- [[log-based-architecture]] — EDA's architectural foundation
- [[redis]] for idempotency key storage and fast duplicate detection

## Real-world examples

- **Uber**: Trip events (request, match, start, complete) flow through an event-driven pipeline to pricing, fraud, dispatch, and billing consumers independently
- **Netflix**: Content availability events trigger encoding, CDN pre-positioning, and recommendation updates independently
- **Discord**: Internal service events (message created, user joined) distributed via Kafka to downstream consumers

## Related

- [[kafka]]
- [[rabbitmq]]
- [[log-based-architecture]]
- [[kafka-vs-rabbitmq]]
