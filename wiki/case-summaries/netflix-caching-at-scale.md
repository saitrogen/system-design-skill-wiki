---
name: Netflix multi-tier caching at scale
type: case-summary
domain: case-summaries
tags:
  - case-summary
  - netflix
  - caching
  - scaling
  - global-infrastructure
aliases:
  - Netflix caching architecture
  - Netflix cache-aside pattern
  - Netflix multi-region caching
last_updated: 2026-04-14
sources:
  - https://netflixtechblog.com/cache-warming-aggressive-performance-optimization-for-cdn-3f7e56c15b61
  - https://netflixtechblog.com/open-connect-our-home-grown-cdn/
  - https://netflixtechblog.com/its-time-to-forget-about-the-l1-l2-l3-cpu-cache-analogy-a4ad0e423f7d
  - https://netflixtechblog.com/finding-and-fixing-performance-regressions-in-large-web-applications-8119a12ba447
---

# Netflix multi-tier caching at scale

## TL;DR
Netflix operates a multi-tier caching architecture to handle billions of requests globally without saturating any single origin. The strategy: use edge caches (CDN nodes), regional in-memory caches, and application-level caches to progressively filter traffic before it reaches the origin database. Key learning: cache coherency and warming are more important than raw throughput once you're at global scale.

## Timeline / evolution

- Early Netflix (2007–2010): Streaming was new; infrastructure was centralized (one or two origins serving all regions).
- Inflection point (≈ 2011–2012): Streaming demand exploded (hundreds of millions of concurrent streams).
  - Origin database became a bottleneck during peak hours.
  - Network egress from origin data centers exceeded capacity.
  - Geographic latency became product-visible (slow app startup in Asia and Europe).
- 2012–2014: Built Open Connect, a home-grown CDN for video delivery (edge-tier caching).
  - Deployed thousands of Open Connect boxes in ISP networks globally.
  - Reduced origin load by orders of magnitude for pre-computed video content.
- 2014–2016: Layered application-level caching for metadata (titles, descriptions, search results).
  - Added regional memcache clusters (API cache tier).
  - Introduced cache warming to pre-populate hot data.
- 2016+: Multi-tier became standard; refined cache coherency and monitoring for sub-second invalidation.

Scale / performance signals:

| Signal | What it looked like | Why it mattered |
| --- | --- | --- |
| Origin DB saturated | Peak requests pushed origin I/O beyond capacity | Cascading failures; entire regions slow simultaneously |
| Egress bandwidth exhausted | Shipping video from one data center cost millions/month | Economics forced geographic distribution |
| Cold cache spikes | After deploy or failover, traffic surge to origin | Caused outages ("thundering herd") during incident recovery |
| Metadata latency visible | API response times >500ms in distant regions | Users noticed slow app launches and UI responsiveness |
| Cache invalidation lag | Stale data visible after updates (5–10 min delays) | Marketing campaigns conflicted with stale recommendations |

## Decision drivers

- Scale globally without building data centers in every region (cost-prohibitive).
- Handle real-time personalization (recommendations, watchlist, user state) despite geographic distribution.
- Minimize latency to origin during cache misses (must serve metadata/search in <100ms).
- Survive cache layer failures gracefully (regional cache down ≠ global outage).
- Keep client latency predictable even during traffic spikes (no "cold boot" penalties).

## What worked

- **Multi-tier architecture (pyramid):**
  - **Edge tier (CDN):** Open Connect nodes in ISP networks cache pre-computed video segments. No application logic, simple replication, massive hit rate (>95%).
  - **Regional tier (memcache):** Clusters in each region cache metadata, search results, recommendations. Shared by all apps in that region.
  - **Application tier (local cache):** Node.js app processes maintain per-instance caches for frequently accessed objects. Fast, but requires coordination across instances.
- **Cache key design:**
  - Used user ID + content ID + timestamp (epoch) as the key structure.
  - Epoch-based keys allowed bulk invalidation (increment epoch, all old keys become stale).
- **Cache warming before deploy:**
  - After a code push, preemptively load hot data into the regional cache.
  - Prevented "cold start" spike where the first wave of users would all miss cache and storm the origin.
  - Measured via shadow traffic: send a small % of traffic to new instances before traffic switchover.
- **Observability + adaptive TTLs:**
  - Monitored cache hit rates per tier (edge, regional, app).
  - Dynamically adjusted TTLs based on hit rate: high-hit data kept longer, low-hit data evicted faster.
  - Reduced memory waste while maintaining freshness.
- **Graceful fallback:**
  - Cache layer failures never blocked serving (origin served stale or fresh data on miss).
  - If regional cache down: app tier cache absorbs some load, edge CDN keeps video flowing, origin handles metadata spike.

## What hurt

- **Cache coherency complexity:**
  - Distributed invalidation across edge + regional + app tiers was error-prone.
  - Update to user watchlist might take 5–10 seconds to propagate globally (edge TTLs needed to be long for hit rate).
  - Stale-read bugs appeared in edge cases (user sees old recommendation, then clicks).
- **Cold-start thundering herd:**
  - After regional failover or cache flush, all pending requests hit origin simultaneously.
  - Required circuit breakers and adaptive backoff to prevent cascade failures.
- **Memory efficiency tradeoff:**
  - Hot data needs high TTLs to maintain hit rates (e.g., trending titles).
  - But high TTLs bloat memory usage if metadata is large or dataset is wide.
  - Required careful pruning and LRU eviction policies.
- **Testing multi-tier behavior:**
  - Hard to reproduce cache coherency bugs locally; required production testing with canary traffic.
  - Silent data inconsistencies took months to track down (different caches serving different versions of the same object).

## Links to wiki pages

- [[redis|Redis]] — in-memory caching used in regional tier
- [[caching/index|Caching decisions]]
- [[patterns/index|Architecture patterns (cache-aside)]]
- [[databases/index|Database decisions]] — caching as a scaling lever for origin databases

## Sources

- https://netflixtechblog.com/cache-warming-aggressive-performance-optimization-for-cdn-3f7e56c15b61 — cache warming strategy for deploy-time cold starts
- https://netflixtechblog.com/open-connect-our-home-grown-cdn/ — Open Connect CDN architecture and scale
- https://netflixtechblog.com/its-time-to-forget-about-the-l1-l2-l3-cpu-cache-analogy-a4ad0e423f7d — memory locality and caching principles
- https://netflixtechblog.com/finding-and-fixing-performance-regressions-in-large-web-applications-8119a12ba447 — observability patterns for multi-tier caches
