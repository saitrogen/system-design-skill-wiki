---
name: "Redis caching strategies"
type: tool
domain: caching
tags:
  - caching
  - redis
  - performance
  - patterns
aliases:
  - Redis cache patterns
  - Cache-aside with Redis
last_updated: 2026-04-15
sources:
  - https://redis.io/docs/manual/client-side-caching/
  - https://redis.com/blog/caching-patterns-design/
---

# Redis caching strategies

## What it is / what it optimizes for

Redis caching strategies describe three primary patterns for using Redis to reduce database load and latency: **cache-aside** (lazy loading), **write-through** (synchronous writes), and **write-behind** (async writes). Each trades off between consistency, complexity, and operational overhead. Redis excels at all three due to its atomic operations and sub-millisecond latency.

## When it fits

- **Cache-aside**: Cold start tolerance (cache misses on first load are acceptable); read-heavy workloads where writes are infrequent.
- **Write-through**: Strong consistency requirement; moderate write throughput where app can afford dual writes (app → cache → DB).
- **Write-behind**: High write volume; eventual consistency acceptable; willing to manage async queue durability + retry logic.
- **Mixed patterns**: Different datasets can use different strategies in the same system (e.g., user sessions cache-aside, inventory write-through).

## When it doesn't fit

- Cache-aside becomes dangerous if cache cannot be regenerated or if stale data causes business loss (e.g., critical inventory counts; use write-through instead).
- Write-through fails if you can't tolerate app slowdown from waiting on both cache + database writes; consider write-behind.
- Write-behind is risky without durable queue (Kafka, job queue) backing the async writes; loss of in-flight updates is catastrophic.
- If your working set is larger than Redis memory budget, eviction will silently discard hot data; consider a dedicated caching layer or tiering strategy.

## Operational gotchas

- **Cache stampede** (cache-aside): Many concurrent requests miss on same key, all hit DB simultaneously; mitigate with probabilistic TTL or single-threaded regeneration lock.
- **Write-through latency tail**: If database is slow, all writes stall; poorly configured write-through can make the system slower than no cache.
- **Write-behind data loss**: In-flight async writes survive only as long as your queue (Redis Streams, Kafka, etc.); node crash = lost writes unless persisted.
- **Eviction surprise**: Redis `allkeys-lru` silently drops cache entries when full; apps must handle miss on any key, including "fresh" ones.
- **TTL inconsistency**: Forgetting to set TTL on cache-aside entries = unbounded memory growth; set TTL on all cache writes.

## Trade-offs

- **Cache-aside**: Simple to implement, but apps must handle cache miss logic + risk of cascading failures on miss spike. Good for read-heavy workloads.
- **Write-through**: Consistency guaranteed (data in cache = data in DB), but adds latency to every write and complicates failure modes (what if DB write succeeds but cache write fails?).
- **Write-behind**: Best throughput (app returns immediately), but adds complexity (async queue, retry logic) and risk (in-flight loss on crash).

| Pattern | App sees | Cache hit latency | Consistency | Complexity | Best for |
| --- | --- | --- | --- | --- | --- |
| **Cache-aside** | DB latency on miss | Sub-ms | Eventual | Low | Read-heavy, tolerant of cold starts |
| **Write-through** | Cache + DB latency | Sub-ms | Strong | Medium | Moderate writes, consistency critical |
| **Write-behind** | App latency only | Sub-ms | Eventual | High | Write-heavy, eventual consistency OK |

## Comparisons

| Compared to | Redis strategies tend to win when… | Redis strategies tend to lose when… |
| --- | --- | --- |
| **Application-local cache** | Shared across processes/servers; survives app restart; tunable consistency | Single-machine cache; no cross-app sharing; higher operational overhead |
| **Database query cache** | Explicit control over TTL and invalidation; supports any data shape (not just queries) | No dependency on external service; simpler ops for simple read-heavy workloads |
| **CDN caching** | Low-latency cache for dynamic data (not static assets); origin-agnostic | For static content; global geo-distribution; automatic cache invalidation |

## Real-world example

- **Stack Overflow**: Cache-aside for query results. When a user views a tag page, SO checks Redis first; miss triggers DB query + cache write. TTL of 30s prevents unbounded memory + keeps data reasonably fresh. At 20M questions, this reduced database CPU by ~40%.
- **Twitter**: Write-through for timeline caching. Every tweet write goes → cache (sorted set by timestamp) + database. Timeline reads hit Redis (sub-ms); stale cache is impossible. Cost is write latency (app waits on both systems).

## Related

- [[redis|Redis]] — the tool itself; persistence, durability, and operational trade-offs
- [[memcached|Memcached]] — simpler alternative for cache-aside-only use cases
- [[distributed-caching-strategies|Distributed caching strategies]] — cluster-level caching (multi-node consistency, cache coordination)
- [[databases/index|Database decisions]]

## Sources

- https://redis.io/docs/manual/client-side-caching/ — official client-side caching docs
- https://redis.com/blog/caching-patterns-design/ — Redis Labs on caching pattern trade-offs
- https://en.wikipedia.org/wiki/Cache_stampede — cache stampede problem and solutions
