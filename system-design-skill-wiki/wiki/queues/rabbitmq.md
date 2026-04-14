---
name: "RabbitMQ"
type: tool
domain: queues
tags:
  - rabbitmq
  - amqp
  - message-broker
  - task-queue
  - routing
  - erlang
aliases: ["RabbitMQ", "AMQP Broker"]
last_updated: 2026-04-11
sources:
  - https://github.com/AutoMQ/automq/wiki/Apache-Kafka-vs.-RabbitMQ:-Differences-&-Comparison
  - https://www.hellointerview.com/blog/kafka-vs-rabbitmq
---

## What this is

A traditional message broker implementing the AMQP protocol (Advanced Message Queuing Protocol). Built on Erlang/OTP, which gives it excellent concurrency and fault tolerance characteristics. The mental model: **messages flow through RabbitMQ and are deleted after acknowledgment**. Producers send to exchanges; exchanges route to queues via configurable bindings; consumers pull from queues and ACK each message individually.

## When to use

- **Task queues with per-message acknowledgment**: Worker consumes a task, processes it, sends ACK. If worker crashes, the message is requeued. This is the canonical use case.
- **Complex routing requirements**: Exchanges (direct, fanout, topic, headers) plus bindings enable sophisticated routing logic that Kafka can't match natively
- **RPC patterns**: Request-reply patterns with reply-to queues; Kafka is awkward for synchronous request-response
- **Per-message priority**: Priority queues are a first-class feature; not natively supported in Kafka
- **Smaller scale, simpler ops**: Easier to set up and reason about for teams without Kafka expertise
- **Low-latency individual message delivery**: Push-based delivery can achieve sub-millisecond latency per message

## When NOT to use

- **High-throughput event streaming**: RabbitMQ handles ~10k–100k messages/second; [[kafka]] handles 1M+. For data pipelines, Kafka wins decisively.
- **Event replay**: Messages are **deleted after consumption**. There is no rewind. If you need to rebuild state or add a new consumer that needs history, RabbitMQ cannot help.
- **Multiple independent consumers on the same data**: All consumers on the same queue compete for messages (work queue pattern). To fan-out to multiple consumers, you create separate queues + bindings — which is manual overhead Kafka handles automatically with consumer groups.
- **Event sourcing**: No persistent log; no time travel; wrong fundamental model.

## Trade-offs

| Factor | Detail |
|--------|--------|
| Throughput | 4k–100k msg/sec; fine for task queues, too slow for event pipelines |
| Ordering | Guaranteed within a queue (single consumer); concurrent consumers lose ordering |
| Message retention | Gone after ACK; no replay capability |
| Routing | Flexible exchanges + bindings; topic, fanout, direct, headers patterns |
| Per-message ACK | Native; messages requeued on NACK or consumer crash |
| Latency | Sub-millisecond push delivery to consumers |
| Erlang runtime | Excellent concurrency model; smaller operational knowledge base than JVM tools |
| Management UI | Built-in web UI for queue inspection; better out-of-box operability than Kafka |
| Dead letter queues | Native DLQ support; failed messages routed to separate queue for inspection |

**The ACK model advantage**: A worker can ACK a message only after successfully processing it. If the worker crashes mid-job, RabbitMQ redelivers to another worker. Kafka requires you to implement this pattern yourself via offset management.

**The Erlang advantage**: OTP supervision trees mean RabbitMQ processes crash and restart independently without bringing down the broker. Operationally resilient even under unusual load patterns.

## Pairs well with

- [[postgresql]] — RabbitMQ queues work tasks; Postgres stores results
- [[redis]] — Redis for fast idempotency checks on redelivered messages
- Celery, Sidekiq, and similar worker frameworks that expect a traditional message broker model

## Real-world examples

- **Instagram** (early): Used RabbitMQ for async task processing before scaling to custom queue systems
- **Zalando**: Uses RabbitMQ for order processing and event routing across microservices
- Most Django/Rails/FastAPI apps using Celery default to RabbitMQ as the broker

## Related

- [[kafka]]
- [[kafka-vs-rabbitmq]]
- [[event-driven-architecture]]
