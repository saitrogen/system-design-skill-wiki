---
name: "Apache Kafka"
type: tool
domain: queues
tags:
  - kafka
  - event-streaming
  - append-only-log
  - partitions
  - consumer-groups
  - throughput
aliases: ["Kafka", "Apache Kafka"]
last_updated: 2026-04
sources:
  - ["Kafka at LinkedIn: Current and Future", "https://engineering.linkedin.com/kafka/kafka-linkedin-current-and-future"]
  - ["Kafka vs RabbitMQ: How to Know Which One to Use (Hello Interview)", "https://www.hellointerview.com/blog/kafka-vs-rabbitmq"]
---

## What this is

A distributed event streaming platform built on an **append-only, partitioned log**. Created at LinkedIn (~2010) by Jay Kreps, Jun Rao, and Neha Narkhede to solve the data integration problem: reliably moving high-volume event streams from production services into Hadoop and real-time processing systems. The core insight: the **log** is the canonical data structure — producers append to it, consumers track their own offset, and messages persist for a configurable retention period regardless of consumption.

## When to use

- **High-throughput event streaming**: 1M+ messages/second per cluster is routine. LinkedIn ingests 1 trillion messages/day.
- **Event replay**: Consumers can re-read historical events. Critical for rebuilding state, debugging, deploying new consumers, and recovering from bugs.
- **Fan-out to multiple independent consumers**: Many consumer groups can read the same topic at their own pace without interfering
- **Event sourcing and CQRS**: Kafka's log is a natural event store; consumers derive materialized views
- **Change Data Capture (CDC)**: Debezium captures DB WAL changes → Kafka topics; downstream consumers get a reliable change stream
- **Durable pipelines**: Data survives broker restarts, consumer crashes, and consumer lag

## When NOT to use

- **Task queues needing per-message acknowledgment**: Kafka ordering is per-partition, not per-message. There's no concept of "acknowledge this specific message and requeue it" — that's [[rabbitmq]] territory.
- **Complex routing logic**: Kafka routing is by topic + partition key. Flexible exchange/binding patterns require [[rabbitmq]].
- **Low-latency per-message delivery**: Kafka consumers batch-pull messages; baseline latency is 5–50ms. RabbitMQ push-delivers in <1ms.
- **Simple, small-scale task queues**: Kafka's operational overhead (KRaft/ZooKeeper, partition rebalancing, topic management) is overkill for a simple background job queue.

## Trade-offs

| Factor | Detail |
|--------|--------|
| Throughput | 15x higher than RabbitMQ; sequential disk I/O via append-only log |
| Ordering | Guaranteed within a partition; not globally across a topic |
| Replay | Native — consumers re-read by resetting offset |
| Durability | Messages replicated across brokers; survive broker failure |
| Latency | 5–50ms baseline (batched pull); not optimized for per-message latency |
| Operational complexity | Partition rebalancing, consumer group coordination, KRaft/ZooKeeper setup |
| Exactly-once semantics | Achievable within Kafka (Kafka Streams + transactions) but breaks at DB boundaries |
| Log compaction | Keeps latest value per key forever — enables compacted topics as key-value stores |

**The partition model**: Messages ordered within a partition, not globally. Choosing a partition key (e.g., `user_id`) determines co-location and ordering scope. Too few partitions = throughput ceiling; too many = rebalancing overhead.

## Pairs well with

- [[cassandra]] or [[scylladb]] as the write-heavy sink for streamed events
- [[redis]] for hot-path caching of recently consumed events
- [[log-based-architecture]] — Kafka is the canonical implementation
- [[event-driven-architecture]] — backbone for async producer/consumer coupling

## Real-world examples

- **LinkedIn** (origin): Solves the data integration problem — reliably moving activity events to Hadoop + real-time systems. Now processes 1 trillion messages/day.
- **Uber**: Kafka as the nervous system for trip events, pricing, and surge calculation
- **Discord**: Uses Kafka for internal event pipelines alongside [[scylladb]] for message storage

## Related

- [[rabbitmq]]
- [[kafka-vs-rabbitmq]]
- [[log-based-architecture]]
- [[event-driven-architecture]]
