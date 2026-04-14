---
name: "Redis"
type: tool
domain: databases
tags:
  - redis
  - caching
  - in-memory
  - pub-sub
  - eviction
  - persistence
  - data-structures
aliases: ["Redis Cache", "Redis DB"]
last_updated: 2026-04-11
sources:
  - ["Redis Persistence and Durability: RDB Snapshots & AOF (Redis.io)", "https://redis.io/tutorials/operate/redis-at-scale/persistence-and-durability/"]
  - ["Redis Persistence Explained: AOF & RDB (DEV Community)", "https://dev.to/leapcell/redis-persistence-explained-aof-rdb-1i23"]
---

## What this is

An in-memory data structure store that serves as cache, pub/sub broker, session store, rate limiter, leaderboard, and more. Redis operates primarily from RAM, making reads and writes sub-millisecond. Unlike pure caches (Memcached), Redis provides rich data structures (strings, hashes, lists, sorted sets, streams, bitmaps, HyperLogLog) and optional durability.

## When to use

- **Read-through / write-through cache**: Reduce latency and database load for frequently-read data
- **Session storage**: Fast, TTL-based key expiry is exactly right for session management
- **Rate limiting**: Atomic `INCR` + TTL makes sliding-window rate limiters trivial
- **Leaderboards**: Sorted sets (`ZADD`, `ZRANK`) are purpose-built for ranked data
- **Pub/sub and lightweight queuing**: Low-latency fan-out to subscribed consumers; Redis Streams is a persistent, consumer-group-aware alternative to basic pub/sub
- **Distributed locks**: Redlock algorithm (though see caveats) for advisory locking

## When NOT to use

- **Primary database without careful planning**: Eviction policies will silently drop data when memory fills. Teams who treat Redis as a primary store and forget eviction policies lose data.
- **Datasets larger than RAM budget**: Cost of RAM limits dataset size; can't just add disk
- **Complex queries**: No secondary indexes, no joins, no aggregation framework. You get what you key-on.
- **Strong durability guarantee**: Even with AOF `fsync=always`, there's a window of data loss on crash; for financial transactions, use a proper RDBMS

## Trade-offs

| Factor | Detail |
|--------|--------|
| Latency | Sub-millisecond reads/writes — 10–100x faster than disk-backed DBs |
| Throughput | Single-threaded event loop; typically 100k–1M ops/sec per node |
| Memory constraint | Dataset must fit in RAM; eviction kicks in at `maxmemory` |
| Eviction policies | `allkeys-lru`, `volatile-lru`, `noeviction`, etc. — wrong choice = silent data loss |
| Persistence: RDB | Point-in-time snapshot; fast restart, but may lose minutes of writes |
| Persistence: AOF | Log every write; more durable but larger files, slower restart |
| Cluster complexity | Redis Cluster adds routing logic (hash slots); not transparent to clients |
| Single-threaded | CPU-bound workloads (large Lua scripts, SORT on big sets) block all other operations |

**The eviction gotcha**: Default policy is `noeviction` — Redis returns errors when full. Many teams discover this in production. For a cache, `allkeys-lru` is almost always the right default.

**RDB vs AOF**: RDB gives fast restarts but you can lose up to `save` interval of data. AOF with `appendfsync everysec` loses at most 1 second of writes. Production recommendation: use both (hybrid persistence).

## Pairs well with

- Any database ([[postgresql]], [[cassandra]], [[scylladb]]) as the read cache layer
- [[kafka]] — Redis pub/sub for low-latency fan-out; Kafka for durability and replay
- [[event-driven-architecture]] — Redis Streams as a lightweight event bus

## Real-world examples

- **Twitter/X**: Uses Redis for timeline caching and social graph hot-path reads
- **GitHub**: Session storage and caching across services
- **Stack Overflow**: Redis front-caches database queries; reduced DB load dramatically at modest cost

## Related

- [[postgresql]]
- [[cassandra]]
- [[kafka]]
- [[event-driven-architecture]]
