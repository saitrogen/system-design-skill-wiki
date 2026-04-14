---
name: "Memcached"
type: tool
domain: caching
tags:
  - caching
  - memcached
  - performance
  - in-memory
aliases:
  - Memcache
last_updated: 2026-04-15
sources:
  - https://memcached.org/
  - https://github.com/memcached/memcached/wiki
  - https://engineering.linkedin.com/distributed-systems/memcached/
---

# Memcached

## What it is / what it optimizes for

Memcached is a lightweight, distributed in-memory caching system designed for high-throughput cache-aside workloads. It prioritizes simplicity, speed, and horizontal scalability over data structure richness and consistency. Unlike Redis, it offers only key-value strings (no data structures) and provides no persistence or replication—favoring ephemeral, fire-and-forget caching.

## When it fits

- **Pure cache-aside**: The canonical use case. Objects retrieved from source, stored in Memcached by key, evicted or expired.
- **Stateless caching**: No need to guarantee data availability; miss is acceptable (query origin, regenerate).
- **Horizontal scale-out required**: Consistent hashing distributes keys across many nodes; add nodes → linearly more cache capacity.
- **High throughput, low latency**: Tens of millions of requests/sec across cluster; single-threaded event loop + simple protocol.
- **Simple operations**: Only GET, SET, DELETE, and TTL; no Lua scripting, no pub/sub, no complex commands.
- **Teams wanting operational simplicity**: Less configuration than Redis; fewer knobs to turn = fewer production fires.

## When it doesn't fit

- **Need data structures**: Sorted sets, hashes, lists, pub/sub—use Redis.
- **Durability matters**: No persistence option; Memcached node crash = all keys lost (different from Redis AOF/RDB).
- **Consistency required**: No write-through or atomic compare-and-set patterns; eventual consistency only.
- **Replication needed**: Memcached has no built-in replication; if a node is down, its keys are gone. Redis Sentinel / Cluster offer replication.
- **Complex cache patterns**: Session storage with complex updates (e.g., increment counter + add flag atomically)—requires Lua in Redis.
- **Cost of cache misses is high**: If origin is slow/expensive, cache-aside means waiting; write-through patterns (Redis strength) are better.

## Operational gotchas

- **No persistence**: Every restart wipes all keys. Plan for cold-start latency (queries go to origin while cache refills). Use read-aside topology (app queries origin if Memcached miss).
- **Consistent hashing**: Failover or adding/removing nodes can cause rehashing; keys may move to different nodes, causing cold cache.
- **LRU eviction**: When full, least recently used keys are dropped silently. No tuning per key; all data has equal eviction priority.
- **TCP overhead at scale**: Each key GET/SET is a separate TCP packet; latency adds up. Solution: batching requests in client, but this adds latency variance.
- **Single-threaded**: Commands are serialized; a slow operation (e.g., large key retrieval) blocks others.
- **No expiry enforcement**: TTL is checked on GET; expired keys remain in memory until checked or LRU evicts them. Can cause memory leak if never accessed.

## Trade-offs

| Factor | Detail |
| --- | --- |
| **Simplicity** | Only strings; no data structure library. Fewer bugs, fewer surprises. |
| **Speed** | Sub-millisecond latency for hits; comparable to Redis. |
| **Scalability** | Consistent hashing → near-linear scale-out. Each node is independent. |
| **Availability** | No replication; node loss = key loss. Redis Sentinel/Cluster offer HA. |
| **Persistence** | None. Redis offers RDB snapshots and AOF logging. |
| **Consistency** | Cache-aside only; no write-through or read-through patterns. |
| **Operational burden** | Lower than Redis; fewer options = fewer wrong choices. |

## Comparisons

| Compared to | Memcached tends to win when… | Memcached tends to lose when… |
| --- | --- | --- |
| **Redis** | You only need cache-aside; simpler ops; no persistence risk; lower operational overhead | You need data structures, durability, replication, or write-through patterns |
| **Application-local cache** | Cluster-wide sharing; automatic TTL; survives app restart; no memory overhead per process | Single-machine scale; lower latency (no network); no external dependency |
| **Redis Cluster** | Simpler consistent hashing; no slot rehashing complexity; pure cache workload (don't need Redis types) | Need replication/HA; consistency guarantees; complex cache patterns |

## Real-world example

- **Facebook**: Memcached is foundational to their caching layer. Objects (user profiles, posts, comments) are stored in Memcached in front of MySQL. On cache miss, object is fetched from MySQL + written back to Memcached. Billions of objects cached across thousands of nodes. Consistent hashing allows fail-gracefully: node loss triggers cache recomputation, not system failure.
- **LinkedIn**: Cache-aside for feed, profile data. Used Memcached for years, then built TAO (The Associations and Objects layer) as a custom layer on top to handle relationships and consistency.

## Related

- [[redis|Redis]] — richer feature set; use when you need data structures or persistence
- [[redis-caching-strategies|Redis caching strategies]] — patterns that work with both Memcached and Redis
- [[distributed-caching-strategies|Distributed caching strategies]] — multi-node caching topology
- [[databases/index|Database decisions]]

## Sources

- https://memcached.org/ — official Memcached documentation
- https://github.com/memcached/memcached/wiki — GitHub wiki with deployment and protocol docs
- https://engineering.linkedin.com/distributed-systems/memcached/ — LinkedIn's use of Memcached at scale
- https://www.usenix.org/legacy/events/osdi08/tech/full_papers/nishtala/nishtala_html/ — Memcached at Facebook (OSDI 2008 paper)
