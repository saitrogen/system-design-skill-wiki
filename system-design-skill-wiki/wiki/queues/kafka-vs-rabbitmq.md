---
name: When to Use Kafka vs RabbitMQ
type: decision
domain: queues
tags:
  - kafka
  - rabbitmq
  - decision
  - event-streaming
  - task-queue
  - trade-offs
aliases:
  - Kafka vs RabbitMQ
  - Kafka or RabbitMQ Decision
last_updated: 2026-04-11
sources:
  - https://www.hellointerview.com/blog/kafka-vs-rabbitmq
  - https://github.com/AutoMQ/automq/wiki/Apache-Kafka-vs.-RabbitMQ:-Differences-&-Comparison
---

## What this is

A decision framework for choosing between Kafka and RabbitMQ. These tools have fundamentally different models: **Kafka is a log** (messages persist, consumers track offset); **RabbitMQ is a broker** (messages flow through and are deleted after ACK). This difference drives almost every other trade-off.

## When to use

**Choose [[kafka]] when:**
- You need event replay — adding a new consumer that reads from the beginning, replaying a pipeline after a bug, or rebuilding materialized views
- Throughput exceeds ~100k messages/second
- You have multiple independent consumers that all need the full stream (consumer groups read independently without interfering)
- You're building event sourcing, CQRS, or a log-based architecture
- You need log compaction (retain only the latest value per key, indefinitely)
- You need CDC (Change Data Capture) from a database

**Choose [[rabbitmq]] when:**
- You need per-message acknowledgment with automatic requeue on worker crash
- Complex routing: fanout, topic-based, header-based, or priority routing across multiple queues
- RPC / request-reply patterns with correlation IDs
- Lower throughput, simpler ops: a team without Kafka expertise can run RabbitMQ effectively
- Per-message priority queues
- You need guaranteed delivery to exactly one worker (competing consumers), with dead-letter queues for failed messages

## When NOT to use

- Don't use RabbitMQ when you need replay. There is no rewind. Ever. Messages are gone after ACK.
- Don't use Kafka when your primary requirement is "run a task and retry it if the worker dies" — the offset-based model requires manual implementation of what RabbitMQ gives you for free.
- Don't try to use Kafka for per-message priority queues without significant custom logic.

## Trade-offs

| Dimension | Kafka | RabbitMQ |
|-----------|-------|----------|
| Message retention | Configurable (days/weeks/forever) | Deleted on ACK |
| Throughput | 1M+ msg/sec | 10k–100k msg/sec |
| Replay | Native (seek to offset) | Not possible |
| Routing | By topic + partition key | Exchanges: direct, fanout, topic, headers |
| Per-message ACK | No (offset-based) | Yes — message requeued on NACK/crash |
| Ordering | Within a partition | Within a queue (single consumer) |
| Consumer model | Pull (consumers control pace) | Push (broker delivers) |
| Latency per message | 5–50ms (batched) | Sub-millisecond |
| Operational complexity | High (partition management, KRaft) | Lower (management UI, simpler concepts) |
| Exactly-once | Within Kafka cluster | With idempotency plugins + care |
| Priority queues | Not native | Native |
| Dead letter queues | Manual via separate topic | Native |

## The tipping point

**Replay requirement → Kafka.** This is the clearest signal. If any current or future consumer might need to re-read events (new feature, debugging, data migration), Kafka is the correct choice. RabbitMQ cannot be retrofitted with replay.

**Per-message retry with crash safety → RabbitMQ.** If the contract is "process this task exactly once, and requeue if the worker crashes mid-job," RabbitMQ's ACK model handles this naturally. Kafka requires careful offset management to achieve the same guarantee.

**Both**: Large organizations often run both — Kafka for event streaming/pipelines, RabbitMQ for task queuing and internal service communication.

## Pairs well with

- [[log-based-architecture]] — defines when Kafka is architecturally appropriate
- [[event-driven-architecture]] — both tools implement async event patterns
- [[redis]] — as a fast idempotency store for handling RabbitMQ message redelivery

## Real-world examples

- **LinkedIn**: Kafka for all event streaming (1T msgs/day); separate systems for task queuing
- **Zalando**: RabbitMQ for order-processing task queues; Kafka for event streams and CDC

## Related

- [[kafka]]
- [[rabbitmq]]
- [[event-driven-architecture]]
- [[log-based-architecture]]
