---
name: "Distributed caching strategies"
type: tool
domain: caching
tags:
  - caching
  - distributed-systems
  - performance
  - scaling
aliases:
  - Multi-node caching
  - Cluster caching
  - Distributed cache topology
last_updated: 2026-04-15
sources:
  - https://redis.io/docs/manual/scaling/
  - https://www.usenix.org/legacy/events/osdi08/tech/full_papers/nishtala/nishtala_html/
  - https://engineering.fb.com/2013/06/18/core-data/scaling-memcache-at-facebook/
---

# Distributed caching strategies

## What it is / what it optimizes for

Distributed caching strategies describe how to scale a cache tier across multiple nodes while maintaining consistency, minimizing latency, and avoiding cascading failures. Key patterns include **consistent hashing** (stateless replication), **regional cache hierarchies** (local + remote), **cache-through** (layer between app and origin), and **leasing** (cache validity protocol). Each strategy trades off between failure domain isolation, cross-node consistency, and operational complexity.

## When it fits

- **Scale-out caching**: Working set exceeds single-node RAM; add more nodes to increase capacity.
- **Geographically distributed systems**: Regional cache clusters reduce cross-region latency and bandwidth.
- **High availability required**: Replication across nodes ensures cache survives single-node failure.
- **Cache invalidation complexity**: Leasing and versioning patterns reduce consistency bugs from stale invalidations.
- **Request volume beyond single node**: Thousands of req/sec per node; distribute across cluster.
- **Cache failure tolerance**: If single cache node down is unacceptable (e.g., high-traffic service), replicate or shard.

## When it doesn't fit

- **Single-node cache sufficient**: If a single Redis or Memcached instance handles full workload, added complexity isn't worth it.
- **Consistency is paramount**: Distributed caches are eventually consistent by nature; if strong consistency required, consider write-through to authoritative database.
- **Small working set**: If cache fits in one large machine (e.g., 256GB RAM), adding nodes increases latency (network hop) with no benefit.
- **Operational overhead unacceptable**: Replication, failover, and cache coherency add operational burden; small teams may prefer simple single-node cache + database.
- **No spare datacenter/region**: Geo-replicated caching only makes sense if you have multiple regions or AZs.

## Operational gotchas

- **Consistent hashing + cache misses**: Removing/adding a node causes rehashing; keys move to different nodes, creating a "cache miss storm" (thundering herd).
- **Replication lag**: In master-replica cache clusters, replicas may be stale; app must tolerate missing keys on fail-to-replica.
- **Partition tolerance**: Network split between cache clusters → split-brain (two clusters independently evolving). Cache and write origin must survive partition.
- **Lease revocation**: If app doesn't respect lease expiry, stale data lives in cache; requires strong client discipline.
- **Multi-tier inconsistency**: L1 cache (local) + L2 cache (remote) + origin; invalidation must reach all tiers or stale data persists.
- **Uneven load distribution**: Consistent hashing assumes uniform key distribution. Hot keys (e.g., "trending item #1") can overload single replica.

## Trade-offs

| Strategy | Consistency | Availability | Latency | Complexity |
| --- | --- | --- | --- | --- |
| **Consistent hashing (Memcached)** | Eventual | Partial (node loss = key loss) | Low (single hop) | Low |
| **Master-replica (Redis Sentinel)** | Eventual (read from replica = stale possible) | High (fail-to-replica) | Medium (master + replica latency) | Medium |
| **Redis Cluster** | Eventual | High (auto-sharding) | Medium (slot lookup + network) | High (slot management) |
| **Multi-tier hierarchy** | Eventual (L1 ≠ L2 ≠ origin) | High (fallback to lower tiers) | Very low for L1 hits | Very high (lease, TTL, invalidation) |
| **Write-through to origin** | Strong (cache + origin in sync) | High | Higher (dual writes) | Medium |

## Comparisons

| Compared to | Distributed strategies tend to win when… | Distributed strategies tend to lose when… |
| --- | --- | --- |
| **Single-node cache** | Working set > single-node RAM; geographic distribution needed; high-availability required | Cache fits in one machine; consistency simpler; lower operational overhead needed |
| **Application-local cache** | Cluster-wide sharing; geographic reach; persistence across app restarts | Single-machine deployments; lower latency (no network); simpler debugging |
| **Database read replicas** | Much lower latency; reduces origin load; geographic distribution easier | Requires explicit invalidation; eventual consistency by design; additional operational burden |

## Real-world example

- **Facebook Memcache at Scale**: Consistent hashing across thousands of nodes. On cache miss, app queries database + writes to cache (cache-aside). Hot items replicated across multiple nodes to avoid stampede. Regional caches + cross-region caches reduce latency. Lease protocol: cache grants app a lease on item; if lease expires, app must revalidate from origin (prevents stale reads after invalidation).
- **Twitter Timeline Cache**: Multi-tier hierarchy. User's timeline cached in Redis (L1, single-node per user). Cache miss → query origin database (Cassandra) → write to L1. If L1 node down, query Cassandra directly. Replication of popular timelines to backup Redis nodes.

## Patterns within distributed caching

### Consistent Hashing
- Keys mapped to nodes via hash; nodes assigned to hash ring.
- Pros: Stateless, linear scale-out, simple failover.
- Cons: Cache misses on node add/remove (rehashing), uneven load distribution with hot keys.
- Used by: Memcached, early Redis Cluster.

### Master-Replica (Replication)
- Write to master cache; replicate to replicas; read from either.
- Pros: High availability, read scale-out.
- Cons: Replica lag (stale reads possible), fail-to-replica complexity.
- Used by: Redis Sentinel, some multi-tier setups.

### Leasing
- Cache grants lease (TTL) to app on read; app must revalidate after lease expires or on invalidation signal.
- Pros: Prevents stale data after cache invalidation; reduces consistency bugs.
- Cons: Higher latency (app waits on revalidation); complex client implementation.
- Used by: Facebook Memcache, some internal cache systems.

### Multi-Tier Hierarchy
- L1 (local, in-process or nearby); L2 (shared cluster); L3 (origin).
- Pros: Very low latency for L1 hits; fallback on failure.
- Cons: Invalidation complexity (must reach all tiers); eventual consistency required.
- Used by: Large-scale systems (Netflix, Uber, Twitter).

## Related

- [[redis-caching-strategies|Redis caching strategies]] — single-node patterns (cache-aside, write-through, write-behind)
- [[memcached|Memcached]] — common distributed cache (consistent hashing)
- [[redis|Redis]] — replication and clustering options
- [[databases/index|Database decisions]]

## Sources

- https://redis.io/docs/manual/scaling/ — Redis Cluster and replication docs
- https://www.usenix.org/legacy/events/osdi08/tech/full_papers/nishtala/nishtala_html/ — Facebook's Memcache paper (OSDI 2008)
- https://engineering.fb.com/2013/06/18/core-data/scaling-memcache-at-facebook/ — Follow-up: Scaling Memcache at Facebook
- https://www.interdb.jp/ — Internal Databases: Deep dives into distributed systems (caching chapters)
