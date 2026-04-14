---
name: Caching decisions
type: index
domain: caching
tags:
  - index
  - caching
aliases:
  - Caching guide
  - Cache strategies
last_updated: 2026-04-12
sources: []
---

# Caching decisions

**Quick reference:** Cache-aside vs write-through vs write-behind? When do we cache? Redis, Memcached, or application cache?

## Core technologies

- [[redis-caching-strategies|Redis caching strategies]] — cache-aside, write-through, write-behind patterns and trade-offs
- [[memcached|Memcached]] — lightweight, stateless cache; when it's better than Redis
- [[distributed-caching-strategies|Distributed caching strategies]] — consistent hashing, replication, multi-tier hierarchies

## Topics to add

- Application-level caching (in-process, L1 cache)
- Cache invalidation strategies (TTL, event-driven invalidation)
- When NOT to cache (consistency requirements, small datasets)
- Monitoring cache hit rates and eviction rates
- Cache warming strategies

## Case summaries

Real production decisions:
- [[netflix-caching-at-scale|Netflix multi-tier caching at scale]] — edge CDN + regional caches + application caches for global distribution

## Related domains

- [[databases/index|Databases]] — caching sits between app and database
- [[patterns/index|Patterns]] — caching patterns (cache-aside, write-through)
- [[redis|Redis]] (in databases domain) — common caching tool
